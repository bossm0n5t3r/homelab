# Netdata

Real-time system and container monitoring for the home server.

## Access

Open the dashboard at:

    http://<server-ip>:PORT

The local dashboard does not require authentication by default.

Allow TCP port `PORT` only from a trusted LAN or VPN, and do not expose it directly to the internet.

## Commands

Start:

    docker compose up -d

Stop:

    docker compose down

Logs:

    docker compose logs -f

Update to the latest stable image:

    docker compose pull
    docker compose up -d

## Storage

Netdata stores its configuration, metrics, and cache in Docker named volumes:

- `netdataconfig` at `/etc/netdata`
- `netdatalib` at `/var/lib/netdata`
- `netdatacache` at `/var/cache/netdata`

`docker compose down` preserves these volumes.

Use `docker compose down --volumes` only when the stored configuration and metrics should also be deleted.

## Host integration

Full host monitoring requires:

- Host PID and network namespaces
- Read-only access to the host filesystem, process information, system information, logs, and D-Bus
- The Docker socket bind-mounted read-only for container discovery
- `SYS_PTRACE` and `SYS_ADMIN` capabilities
- An unconfined AppArmor profile
- `NET_BIND_SERVICE` when `PORT` is set below `1024`

These settings are based on Netdata's host-monitoring setup for Docker on Ubuntu and give the container extensive
visibility into the host. Access to the Docker socket remains security-sensitive even when the bind mount is read-only.
Only run the trusted official image.

### Mount propagation

The host root is mounted without recursive slave propagation (`rslave`) for compatibility with this host. As a result,
filesystems mounted or unmounted on the host after the container starts may not be reflected inside the container.
Restart Netdata after changing host mounts:

    docker compose restart netdata

## References

- [Install Netdata with Docker](https://learn.netdata.cloud/docs/netdata-agent/installation/docker)
- [Securing Netdata Agents](https://learn.netdata.cloud/docs/netdata-agent/configuration/securing-agents/)
