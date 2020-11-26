# nerves-devcontainer

Nerves development workspace with Visual Studio Code dev-container (Remote - Containers extension)

## Requirements

- Docker ready host PC
    - See detailed requirements: [Windows](https://docs.docker.com/docker-for-windows/install/) / [macOS](https://docs.docker.com/docker-for-mac/install/)
- [Visual Studio Code](https://code.visualstudio.com/download)
    - [Remote - Containers](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-containers) extensions
- [Docker Desktop](https://docs.docker.com/get-docker/)

## Quick start

1. Clone this repository  
```Shell
git clone https://github.com/NervesJP/nerves-devcontainer
```
2. Start VS Code, run the **Remote-Containers: Open Folder in Container...** command from the Command Palette (F1) or quick actions Status bar item, and select the project folder you cloned at Step 1.
3. After a while, you can enjoy Nerves/Elixir development with Bash on Docker image and VS Code like as follows.  
```Shell
root@ebc5e1a19ae3:/workspaces/nerves-devcontainer# ls 
LICENSE.txt  README.md
root@ebc5e1a19ae3:/workspaces/nerves-devcontainer# ls ~/.mix/*
/root/.mix/rebar  /root/.mix/rebar3

/root/.mix/archives:
hex-0.20.6  nerves_bootstrap-1.10.0
root@ebc5e1a19ae3:/workspaces/nerves-devcontainer# elixir --version 
Erlang/OTP 23 [erts-11.1.3] [source] [64-bit] [smp:4:4] [ds:4:4:10] [async-threads:1] [hipe]

Elixir 1.11.2 (compiled with Erlang/OTP 23)
root@ebc5e1a19ae3:/workspaces/nerves-devcontainer# iex 
Erlang/OTP 23 [erts-11.1.3] [source] [64-bit] [smp:4:4] [ds:4:4:10] [async-threads:1] [hipe]

Interactive Elixir (1.11.2) - press Ctrl+C to exit (type h() ENTER for help)
iex(1)> 
```
4. Only for the first time to login Docker image, you need to generate your SSH keys, as the follow.
```Shell
ssh-keygen -t rsa -N "" -f .ssh/id_rsa`
```

## Tips

### Mounted files about Nerves settings

Since a filesystem into Docker image will disappear when an image is execute, Nerves related setting files are mounted to current (this) directory on the host.

First files are `${PWD}/.ssh/id_rsa,id_rsa.pub` that are mounted to `~/.ssh/` on Docker image. They are used for the construction of Nerves firmware and secure connection to Nerves devices.  
To generate your SSH keys, you need to operate `ssh-keygen -t rsa -N "" -f /workspaces/nerves-devcontainer/.ssh/id_rsa` after logging into Docker image for the first time.

Second is `${PWD}./nerves` that is mounted to `~/.nerves`. It keeps archives of Nerves toolchains and nerves_system_br.  
If it disappeared whenever Docker container is restarted, you need to download archives every time you operate `mix deps.get`.  
So we decided to mount it on this directory. `mix deps.get` at first will take a while to unzip archives to `./nerves/artifacts` due to poor performance about bindings between host and container filesystems. However we think it is acceptable compared that we always have to wait when running mix `deps.get` for a long time.

First files is `${PWD}/.nerves`. It keeps 
 mix deps.get at first will take a while to unzip archives to ./nerves/artifacts due to poor performance about bindings between host and container filesystems. However I think it is acceptable compared to the usual wait when running mix deps.get.


 -v ${PWD}:/workspace can mount current directory on host to Docker image.

Note that your Nerves project can be kept on current directory thanks to the VS Code feature.

### Manual build of Docker image locally

Original Dockerfile is maintained at https://github.com/NervesJP/docker-nerves/blob/main/Dockerfile

You can also build Docker image locally by locating above file to here and uncomment `//"dockerFile": "Dockerfile",` on `./devcontainer/devcontainer.json`.

## Contributors

- [@matsujirushi](https://github.com/matsujirushi) : original (awesome!) contributor
- [@takasehideki](https://github.com/takasehideki) : main maintainer
