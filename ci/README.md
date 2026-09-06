## Summary

Before building the docker image, fetch a fresh registration token from the GitHub [runner page](https://github.com/scroll-tech/ceno-reth-benchmark/settings/actions/runners/new?arch=x64&os=linux) and decide on a `SERVER_NAME` label for this runner.

Build the image (enable BuildKit so we can forward an SSH key for private Git clones):
```shell
DOCKER_BUILDKIT=1 docker build \
  --ssh sshkey="$HOME/.ssh/id_ed25519" \
  -t ceno-reth-gpu:v1 \
  -f ci/Dockerfile .
```

Spin up the docker image by supplying the token and runner name at runtime:
```shell
docker run --gpus device=0 --name ceno-reth-runner -d \
  -e RUNNER_TOKEN="$TOKEN" \
  -e RUNNER_NAME="$SERVER_NAME" \
  -e RUNNER_URL="https://github.com/scroll-tech/ceno-reth-benchmark" \
  ceno-reth-gpu:v1
```

You may optionally set `RUNNER_LABELS` (defaults to `gpu`). The container’s entrypoint configures the runner via the provided token on startup and then launches `RUNNER_ALLOW_RUNASROOT=1 /home/docker/actions-runner/run.sh`, so no manual SSH is required.

For a dual-GPU runner, expose both host GPUs to the same container and register a dedicated label:

```shell
docker run --gpus '"device=0,1"' --name ceno-reth-runner-dual -d \
  -e RUNNER_TOKEN="$TOKEN" \
  -e RUNNER_NAME="$SERVER_NAME" \
  -e RUNNER_LABELS="dual-gpu" \
  -e RUNNER_URL="https://github.com/scroll-tech/ceno-reth-benchmark" \
  ceno-reth-gpu:v1
```

Docker exposes the selected GPUs to processes in the container as logical devices `0` and `1`. When dispatching `run-benchmark-v2.yml`, keep the default `gpu_devices=0` for one GPU. For the dual runner above, set `gpu_runner_label=dual-gpu` and `gpu_devices=0,1`. The selected runner must expose every requested logical device.
