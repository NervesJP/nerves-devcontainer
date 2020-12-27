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

   ```bash
   $ git clone https://github.com/NervesJP/nerves-devcontainer
   ```

2. Start VS Code, run the **Remote-Containers: Open Folder in Container...** command from the Command Palette (F1) or quick actions Status bar item, and select the project folder you cloned at Step 1.

3. After a while, you can enjoy Nerves/Elixir development with Bash on Docker image and VS Code like as follows.

   ```bash
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

   ```bash
   root@ebc5e1a19ae3:/workspaces/nerves-devcontainer# ssh-keygen -t rsa -N "" -f .ssh/id_rsa
   ```

## Tips

### Manual build of Docker image locally

This repository will clone [pre-built image on Docker Hub](https://hub.docker.com/r/nervesjp/nerves).

Original Dockerfile is maintained at
https://github.com/NervesJP/docker-nerves/blob/main/Dockerfile.

The number of Docker Tag and the Release number of this repository are
associated with [the Release of GitHub repository for
Dockerfile](https://github.com/NervesJP/docker-nerves/releases).

You can also build Docker image locally by locating above Dockerfile to
`./devcontainer` and uncommenting `//"dockerFile": "Dockerfile",` on
`./devcontainer/devcontainer.json`.

It means you can customize your own Nerves development environment as you want.

### Mounted directories about Elixir/Nerves settings

Each time an image is execute, a filesystem into Docker image will disappear.
So Elixir/Nerves related setting directories are mounted in the current (this)
directory on the host.

First directory is `${PWD}./hex` that will be mounted to `~/.hex`. It keeps Hex archives.

Second, `${PWD}./nerves` will be mounted to `~/.nerves`. It keeps archives of
Nerves toolchains and `nerves_system_br`.

If they disappeared when Docker container was restarted, you need to download
archives every time you operate `mix deps.get`.

So we decided to mount it on this directory.

Although `mix deps.get` at first will take a while to unzip archives to
`./nerves/artifacts` due to poor performance about bindings between host and
container filesystems, we think it is acceptable compared that we always have
to wait when running mix `deps.get` for a long time.

The last one is `${PWD}/.ssh` that is expected to contain `id_rsa` and
`id_rsa.pub` SSH key pairs.

They are used for the construction of Nerves firmware and secure connection to
Nerves devices.

It is mounted to `~/.ssh/` on Docker image.

To generate your SSH keys, you need to operate `ssh-keygen -t rsa -N "" -f
/workspaces/nerves-devcontainer/.ssh/id_rsa` after logging into Docker image
for the first time.

In addition, your Nerves project can be kept on the current directory by the VS
Code automatic feature.

### Set environment variables for Nerves development

You can set your own environment variables with `remoteEnv` option in
`./devcontainer/devcontainer.json`.

By default, `${MIX_TARGET}` is set to `rpi3` when running the dev-container.

Please change the value accordingly if your [target of Nerves](https://hexdocs.pm/nerves/targets.html)
is already determined by something other than `rpi3`.

In addition to this, here we define environment variables for WiFi settings.

You may set these values of `${WIFI_SSID}` and `${WIFI_PSK}` according to the
WiFi access point. See ["Connect to a target device" on this
article](https://dev.to/mnishiguchi/elixir-nerves-get-started-with-led-blinking-on-raspberry-pi-2l1i)
for details.

### Burn Nerves firmware to microSD card

Docker has restrict policies to avoid effecting host environment. And also, it
is not possible to pass through a USB device (or a serial port) to a container
as it requires support at the hypervisor level both in
[Windows](https://docs.docker.com/docker-for-windows/faqs/#can-i-pass-through-a-usb-device-to-a-container)
and [macOS](https://docs.docker.com/docker-for-mac/faqs/#can-i-pass-through-a-usb-device-to-a-container)
as the host.

Therefore, `mix burn` cannot be operated from Docker image because there is no
right to access `/dev` to on host as a root user.

One way to burn Nerves firmware is just operating `fwup` on the host. `fwup` is
an utility for constructing/burning Nerves firmware. See https://github.com/fhunleth/fwup.

After installing `fwup` on the host according to [this step](https://github.com/fhunleth/fwup#installing),
please do following command on the host terminal (e.g., PowerShell **as
Administrator**, Terminal.app).

```bash
$ cd <your_nerves_project_dir>
$ fwup _build/${MIX_TARGET}_dev/nerves/images/<project_name>.fw
```

If you are using Linux as the host, you may be able to access microSD from the
Docker environment along with the `privileged` option.

You may need to use a different `/dev/` address for your purposes.

```bash
$ docker run -it -w /workspace -v ${PWD}:/workspace \\
  -v /dev/sdb:/dev/sdb --privileged docker-nerves
```

Please let us know if you have a cool solution!
([issue#1](https://github.com/NervesJP/docker-nerves/issues/1))

## Links

- Elixir Forum Topic: [Nerves development environment with Docker (and VS Code) - Nerves Forum / Chat / Discussions - Elixir Programming Language Forum](https://elixirforum.com/t/nerves-development-environment-with-docker-and-vs-code/35973)
- [Qiita article (in Japanese)](https://qiita.com/takasehideki/items/27005ba9c0d9eb693ea9)

## Contributors

- [@matsujirushi](https://github.com/matsujirushi) : original (awesome!) contributor
- [@takasehideki](https://github.com/takasehideki) : main maintainer
