## How to build and run

At the repo root (so `cd ..` first)


```
DOCKER_BUILDKIT=1 docker build . -t reth-server:latest  --secret id=sshkey,src=$HOME/.ssh/<PRI_KEY_FILE_PATH>  --build-arg GIT_HOST=github.com
```

> need to pass `--secret id=sshkey,src=$HOME/.ssh/<PRI_KEY_FILE_PATH>` to be able to access private [ceno-gpu repo](https://github.com/scroll-tech/ceno-gpu/)

Then start server
```
docker run --gpus all -p 8000:8000 reth-server:latest -e ETH_RPC_URL="<RPC URL>"
```
