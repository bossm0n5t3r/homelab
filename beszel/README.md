# Beszel

Lightweight system and container monitoring with a Hub and local Agent.

## Initial setup

Create the local environment file:

    cp .env.example .env

Set `BESZEL_APP_URL` in `.env` to the URL used to access the Hub. Keep the default when accessing it locally at
`http://localhost:1021`, or use the public HTTPS URL when serving it through a reverse proxy.

Start the Hub first:

    docker compose up -d beszel

Create the administrator account, then select **Add System** in the Hub. Copy the generated token and public key into
`.env`:

    BESZEL_AGENT_TOKEN=<token>
    BESZEL_AGENT_KEY="<public key>"

Start both the Hub and Agent:

    docker compose up -d

Use the following value for **Host / IP** when adding the local system:

    /beszel_socket/beszel.sock

## Access

The Hub is available on the server at:

    http://localhost:1021

The port is bound to `127.0.0.1` only. Use an authenticated HTTPS reverse proxy or an SSH tunnel for remote access.

## Commands

Start:

    docker compose up -d

Stop:

    docker compose down

Logs:

    docker compose logs -f

Update:

    docker compose pull
    docker compose up -d

## Storage

Runtime data is stored in bind-mounted directories:

- `./data/hub` for Hub data
- `./data/agent` for Agent state
- `./data/socket` for the local Hub-Agent Unix socket

`docker compose down` preserves these directories.

## Host integration

The Agent uses the host network namespace to collect host network-interface statistics.

It listens on a Unix socket instead of exposing the default Agent TCP port.

The Docker socket is mounted read-only for container monitoring.

Access to the Docker API remains security-sensitive even when the socket bind mount is read-only, so only run the
trusted official image.

## References

- [Install the Beszel Hub](https://beszel.dev/guide/hub-installation)
- [Install the Beszel Agent](https://beszel.dev/guide/agent-installation)
- [Beszel security](https://beszel.dev/guide/security)
