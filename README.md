# Setting up the Jaunty Server

The goal of this document is to describe the hardware and software configuration of the Jaunty server for future reference.

**Revision**: 01.2025

# Hardware

Jaunty is hosted on a [DigitalOcean](https://digitalocean.com) Droplet &mdash; one of their cloud-hosted virtual private servers. In the interest of accommodating every player as much as possible, the server is hosted in DO's NYC3 region. The current hardware is as follows:

**CPU**: DO Regular, 8 vCPU cores
**RAM**: 16GB
**Disk (SSD)**: 155GB

# Software (base)

## Operating System

At the very base level, Jaunty runs on `Ubuntu 24.10` and version `6.11.0-13-generic` of the Linux kernel. Remote access is done via SSH, which has been configured to disallow root login and the use of password authentication. A separate user account has been created and added to the sudo group; the server process runs under this.

## Shell

Jaunty uses `fish` as a shell, trading portability for a generally better syntax over other shells.

<details>
<summary>config.fish</summary>

```fish
set -gx EDITOR vim
```
</details>

## Text editing

Editing configurations, scripts, etc is all done using the stock version of `vim` that comes with 24.10.

<details>
<summary>.vimrc</summary>

```vim
autocmd FileType yaml setlocal ts=2 sts=2 sw=2 expandtab
```
</details>

## Backups

Jaunty's choice of backup tool is `rclone`; currently `v1.60.1-DEV` is installed. Backups of the server are taken nightly and synced to a [Backblaze](https://www.backblaze.com) B2 bucket. Files within the bucket are retained for 3 days before being purged. See [#docker](#docker) for the script.

## Process management

Historically, a lot of tutorials would suggest using `screen` or `tmux` to keep long-lived processes (e.g. Minecraft servers) running while disconnected. This always felt very janky to me; I never liked doing it. Nowadays, I would argue the most common method would be to run the server in a container.

`docker` is Jaunty's chosen method of maintaining the server process. It does not come pre-installed with Ubuntu, but it can be installed by configuring `apt` to use Docker's sources[^1]. We also make a point to install the `compose` and `buildx` plugins as well. We're running version `27.4.1` of the engine and client.

# Software (Minecraft)

## Server

As per mentioned, the server is ran with Docker. The [itzg/minecraft-server](https://docker-minecraft-server.readthedocs.io) image has become the defacto standard in recent years due to its extensive configuration and excellent support. Rather than the vanilla server, we use [Paper](https://papermc.io) &mdash; a fork of Spigot, which is in turn a fork of Bukkit. Paper is pretty opinionated in its decisions, but the performance boost it brings to the table is substantial. Beyond Paper, there's also a few plugins installed:

* [Chunky](https://modrinth.com/plugin/chunky) &mdash; automatically generates chunks in a radius
* [Simple Voice Chat](https://modrinth.com/plugin/simple-voice-chat) &mdash; implements proximity-based voice chat into the game
* [AxGraves](https://modrinth.com/plugin/axgraves) &mdash; places a "grave" in the world upon player death


[^1]: https://docs.docker.com/engine/install/ubuntu
