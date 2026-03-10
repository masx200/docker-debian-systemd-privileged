# Docker Debian Systemd Privileged

A Docker setup for running a privileged Debian container with systemd as PID 1.
This configuration enables full systemd functionality inside the container with
IPv6 networking support.

## Features

- **Full systemd support** - Runs systemd as PID 1 inside the container
- **Privileged mode** - Required for systemd cgroup management
- **IPv6 networking** - Custom bridge network with dual-stack addressing
- **Kernel module access** - Mounted `/lib/modules` from host
- **Custom DNS entries** - Pre-configured hosts for ProtonVPN and DoH server

## Prerequisites

- Docker Engine 20.10+
- Docker Compose v2.0+
- Linux host (recommended for full systemd support)

## Quick Start

```bash
# Build the Docker image
./build.sh

# Start the container
./up.sh

# Enter the running container
./exec.sh

# Stop the container
./down.sh
```

## Manual Commands

### Build the image

```bash
docker build -f dockerfile -t docker.cnb.cool/masx200/docker_mirror/docker-debian-systemd-privileged:latest .
```

### Start the container

```bash
docker compose -f docker-compose.yml up -d
```

### Stop the container

```bash
docker compose -f docker-compose.yml down
```

### Enter the container

```bash
docker exec -it docker-debian-systemd-privileged bash
```

## Container Configuration

| Setting      | Value        | Description                            |
| ------------ | ------------ | -------------------------------------- |
| `privileged` | `true`       | Required for systemd to manage cgroups |
| `cgroup`     | `host`       | Uses host's cgroup namespace           |
| `restart`    | `always`     | Auto-restart on failure/reboot         |
| `command`    | `/sbin/init` | Starts systemd as PID 1                |

### Network Configuration

The container uses a custom bridge network with IPv4 and IPv6 support:

| Protocol | Subnet                | Container IP        |
| -------- | --------------------- | ------------------- |
| IPv4     | `10.20.15.0/24`       | `10.20.15.2`        |
| IPv6     | `fd12:dead:beef::/64` | `fd12:dead:beef::2` |

### Volume Mounts

| Host Path          | Container Path   | Purpose                                |
| ------------------ | ---------------- | -------------------------------------- |
| `/sys/fs/cgroup`   | `/sys/fs/cgroup` | Required for systemd cgroup management |
| `/usr/lib/modules` | `/lib/modules`   | Kernel module access                   |

### Tmpfs Mounts

| Path        | Purpose                   |
| ----------- | ------------------------- |
| `/run`      | Systemd runtime directory |
| `/run/lock` | Lock files                |
| `/tmp`      | Temporary files           |

### Extra Hosts

Custom DNS entries configured for:

- `repo.protonvpn.com` - ProtonVPN repository
- `deno-dns-over-https-server.g18uibxgnb.de5.net` - DNS-over-HTTPS server

## File Structure

```
.
├── dockerfile           # Docker image definition
├── docker-compose.yml   # Container orchestration config
├── build.sh             # Build script
├── up.sh                # Start container script
├── down.sh              # Stop container script
├── exec.sh              # Enter container script
├── export.sh            # Export container to tarball
├── import.sh            # Import container from tarball
└── readme.md            # This file
```

## Image Export/Import

### Export container

```bash
./export.sh
# Creates: docker-debian-systemd-privileged.tgz
```

### Import container

```bash
./import.sh
# Imports from: docker-debian-systemd-privileged.tgz
```

## Base Image

Built on
`docker.cnb.cool/masx200/docker_mirror/debian-systemd-post:2026-03-03-01-15-50`

This is a Debian-based image with systemd pre-configured for containerized
environments.

## Security Considerations

> **Warning**: This container runs in privileged mode with full access to the
> host system. Only use this setup in trusted environments.

- The container has full access to host devices
- Cgroup modifications can affect the host
- Kernel modules can be loaded from the host

## License

MIT License
