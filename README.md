# nerves-devcontainer

Nerves development workspace with Visual Studio Code dev-container (Remote - Containers extension)

## Requirements

- [Docker Desktop](https://docs.docker.com/get-docker/)
  - Docker has some requirements for host PC to be operated.
  - See detailed requirements: [Windows](https://docs.docker.com/docker-for-windows/install/) / [macOS](https://docs.docker.com/docker-for-mac/install/)
- [Visual Studio Code](https://code.visualstudio.com/download)
  - [Remote - Containers](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-containers) extensions

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

4. Note that you need to generate your SSH keys, only for the first time to login Docker image.
```Shell
root@ebc5e1a19ae3:/workspaces/nerves-devcontainer# ssh-keygen -t rsa -N "" -f .ssh/id_rsa`
```

## Tips

### Manual build of Docker image locally

This repository will clone [pre-built image on Docker Hub](https://hub.docker.com/r/nervesjp/nerves).
Original Dockerfile is maintained at  
https://github.com/NervesJP/docker-nerves/blob/main/Dockerfile.  

You can also build Docker image locally by locating above Dockerfile to `./devcontainer` and uncommenting `//"dockerFile": "Dockerfile",` on `./devcontainer/devcontainer.json`.
It means you can customize your own Nerves development environment as you want.

### Mounted files about Nerves settings

Each time an image is execute, a filesystem into Docker image will disappear. So Nerves related setting directories are mounted in the current (this) directory on the host.

First directory is `${PWD}/.ssh` that is expected to contain `id_rsa` and `id_rsa.pub` SSH key pairs. 
They are used for the construction of Nerves firmware and secure connection to Nerves devices.  
It is mounted to `~/.ssh/` on Docker image. 
To generate your SSH keys, you need to operate `ssh-keygen -t rsa -N "" -f /workspaces/nerves-devcontainer/.ssh/id_rsa` after logging into Docker image for the first time.

Second is `${PWD}./nerves` that is mounted to `~/.nerves`. It keeps archives of Nerves toolchains and nerves_system_br.  
If it disappeared when Docker container was restarted, you need to download archives every time you operate `mix deps.get`.  
So we decided to mount it on this directory. 
Although `mix deps.get` at first will take a while to unzip archives to `./nerves/artifacts` due to poor performance about bindings between host and container filesystems, we think it is acceptable compared that we always have to wait when running mix `deps.get` for a long time.

In addition, your Nerves project can be kept on the current directory by the VS Code automatic feature.

### burn Nerves firmware to microSD card

Docker has restrict policies to avoid effecting host environment. Therefore, `mix burn` cannot be operated from Docker image because there is no right to access `/dev` to on host as a root user.

One way to burn Nerves firmware is just operating `fwup` on the host. `fwup` is an utility for constructing/burning Nerves firmware.  
https://github.com/fhunleth/fwup

After installing `fwup` on the host according to [this step](https://github.com/fhunleth/fwup#installing), please do following command on the host terminal (e.g., PowerShell as Admininistrator, Terminal.app).

```Shell
$ cd <your_nerves_project_dir>
$ fwup _build/${MIX_TARGET}_dev/nerves/images/<project_name>.fw
```

Please let us know if you have a cool solution! ([issue#1](https://github.com/NervesJP/docker-nerves/issues/1))

## Contributors

- [@matsujirushi](https://github.com/matsujirushi) : original (awesome!) contributor
- [@takasehideki](https://github.com/takasehideki) : main maintainer
