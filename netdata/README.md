# Netdata

Real-time system and container monitoring for the home server.

## Access

Open:

    http://<server-ip>:1021

Netdata does not require authentication for its local dashboard by default.

Allow TCP port `1021` only from a trusted LAN or VPN, and do not expose it directly to the internet.

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

Configuration, metrics, and cache data are stored in Docker named volumes:

- `netdataconfig` at `/etc/netdata`
- `netdatalib` at `/var/lib/netdata`
- `netdatacache` at `/var/cache/netdata`

`docker compose down` preserves these volumes.

Use `docker compose down --volumes` only when the stored configuration and metrics should also be deleted.

## Host integration

The service uses host PID and network namespaces, host filesystem mounts, the Docker socket, `SYS_PTRACE`, `SYS_ADMIN`,
and an unconfined AppArmor profile.

These settings follow Netdata's full host-monitoring setup for Docker on Ubuntu, but give the container extensive
visibility into the host.

Only run the trusted official image.

Port `1021` is below `1024`, so the `NET_BIND_SERVICE` capability is required.

The `/run/dbus` mount enables systemd unit monitoring, and the Docker socket mount enables container discovery.

Access to the Docker socket is security-sensitive even when the bind mount is marked read-only.

## References

- [Install Netdata with Docker](https://learn.netdata.cloud/docs/netdata-agent/installation/docker)
- [Securing Netdata Agents](https://learn.netdata.cloud/docs/netdata-agent/configuration/securing-agents/)
