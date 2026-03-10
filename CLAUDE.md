# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with
code in this repository.

## Project Overview

This is a Docker setup for running a privileged Debian container with systemd as
PID 1. The container uses a custom base image from a Chinese mirror
(`docker.cnb.cool`) and includes IPv6 networking support.

## Common Commands

### Build the Docker image

```bash
./build.sh
# Or directly:
docker build -f dockerfile -t docker-debian-systemd-privileged .
```

### Start the container

```bash
./up.sh
# Or directly:
docker compose -f docker-compose.yml up -d
```

### Stop the container

```bash
./down.sh
# Or directly:
docker compose -f docker-compose.yml down
```

### Enter the running container

```bash
docker exec -it docker-debian-systemd-privileged bash
```

## Architecture

### Container Configuration (docker-compose.yml)

- **Privileged mode**: Required for systemd to function properly inside the
  container
- **Cgroup mode**: Set to `host` for systemd cgroup management
- **Tmpfs mounts**: `/run`, `/run/lock`, `/tmp` for systemd operation
- **Volume mounts**:
  - `/sys/fs/cgroup:/sys/fs/cgroup` - Required for systemd
  - `/usr/lib/modules:/lib/modules` - Kernel module access
- **IPv6 network**: Custom bridge network with dual-stack (IPv4/IPv6) addressing
  - IPv4: 172.20.15.0/24 (container: 172.20.15.2)
  - IPv6: fd12:dead:beef::/64 (container: fd12:dead:beef::2)

### Base Image

The container is based on
`docker.cnb.cool/masx200/docker_mirror/debian-systemd-post:2026-03-03-01-15-50`
which appears to be a Chinese mirror of a systemd-enabled Debian image.

### Extra Hosts

Custom DNS entries are configured for:

- `repo.protonvpn.com` - ProtonVPN repository access
- `deno-dns-over-https-server.g18uibxgnb.de5.net` - DoH server

## File Structure

```
.
├── dockerfile           - Single-line FROM directive using custom base image
├── docker-compose.yml   - Container orchestration with IPv6 networking
├── build.sh            - Build script for Docker image
├── up.sh               - Start container script
└── down.sh             - Stop container script
```
