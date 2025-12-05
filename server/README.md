## How to build and run

At the repo root (so `cd ..` first)


### Build variants

All builds require access to the private [`ceno-gpu`](https://github.com/scroll-tech/ceno-gpu/) repo, so forward SSH key:

```bash
DOCKER_BUILDKIT=1 docker build \
  --secret id=sshkey,src=$HOME/.ssh/<PRI_KEY_FILE_PATH> \
  --build-arg GIT_HOST=github.com \
  -t reth-server:latest \
  .
```

Select features via `--build-arg FEATURES=...`:

- GPU build (default): `--build-arg FEATURES="metrics,jemalloc,gpu"`
- CPU-only build: `--build-arg FEATURES="metrics,jemalloc"` (omit GPU extras)

### Run

```bash
docker run --gpus all \
  -p 8000:8000 \
  -v /path/on/host/jobs:/app/jobs \
  -e ETH_RPC_URL="<RPC URL>" \
  reth-server:latest
```

Mounting `/app/jobs` lets you persist `block_data` and logs between runs. For CPU-only images drop `--gpus all` and use the CPU build flag. Set any other env vars (APP_PK_URI, AGG_PK_URI, JOBS_DIR, etc.) as needed.
