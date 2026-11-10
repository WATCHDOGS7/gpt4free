# G4F — Docker setup

> **Known source-build blocker (2026-11-10):** `docker compose build` currently
> fails in the old `seleniarm/node-chromium` base image during `apt-get install
> git`: `openssl-provider-legacy` tries to overwrite `legacy.so` owned by
> `libssl3t64`. This is not fixed by installing more Python packages on the host.
> Use the verified [source installation and test guide](git.md) to get a working
> local checkout. See the [setup audit](setup-audit.md) for evidence and limits.

The commands below describe the source-build workflow, but it remains blocked
until the base image is repaired. Do not interpret them as a verified Docker
quick start.

## Prerequisites

- [Docker Engine/Desktop](https://docs.docker.com/get-docker/) running locally.
- [Docker Compose v2](https://docs.docker.com/compose/install/), available as
  `docker compose` (the legacy `docker-compose` executable is not required).
- Git.

Check the installation:

```sh
docker info
docker compose version
```

## Build and run from source

```sh
git clone https://github.com/xtekky/gpt4free.git
cd gpt4free
docker compose build
docker compose up
```

Use your fork's URL if applicable. Compose pulls the Dockerfile's base image
itself; manually pulling `selenium/node-chrome` does not help because that is not
the image used by this Dockerfile.

Once the build/start succeeds, the configured endpoints are:

- GUI: <http://localhost:8080/chat/>
- API models smoke check: <http://localhost:1337/v1/models>
- OpenAI-compatible API base: `http://localhost:1337/v1`
- Browser desktop (noVNC): <http://localhost:7900/>

Do not expose these development services to untrusted networks. The Compose
file publishes ports on all host interfaces and the image has a default desktop
password.

Stop the services with:

```sh
docker compose down
```

The checkout is mounted at `/app`. Dependency changes require rebuilding with
`docker compose build`; Python source changes may require restarting the service.
The published-image commands in the README run a separately released image, not
the cloned source; that image was not validated in the source setup audit.

[Return to README](../README.md)
