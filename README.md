# pod-check-redis-init-layer

The `check-redis-init-layer` candy of the OpenCharly candy library, as a
standalone repo (the candy de-submodule cutover, kind-prefixed naming). It is a
companion initializer for the redis layer.

## What it provides

`bench-redis-init` — a supervisord service that pre-populates redis with
`bench:hello = world` after `redis-server` is up. It exists so a harness recipe's
`redis-cli get bench:hello` probe returns the expected value.

| Property | Value |
|---|---|
| Service | `bench-redis-init` (priority 30, `restart: always`) |
| Requires | `pod-redis` |
| Package | `iproute`, `procps-ng` (fedora) |
| Seed script | `/usr/local/bin/bench-redis-init.sh` |

The script waits (bounded, up to 60s) for `redis-cli ping` to return `PONG`, sets
`bench:hello world`, then `exec sleep infinity` so supervisord does not
restart-loop it.

## How to use it

Compose the candy into a test image that needs the seeded key:

```yaml
my-redis-bench:
  candy:
    base: cachyos.cachyos
    candy:
      - '@github.com/opencharly/pod-check-redis-init-layer:<tag>'
```

## Layout

- `charly.yml` — the `check-redis-init-layer` candy entity (description,
  `require`, `distro`, `service`, plan).
- `.github/workflows/tag-on-merge.yml` — CalVer tag + `CHANGELOG/` on merge.
- `README.md` — this user overview.

## Related

- Owning procedure: `/charly-image:layer` — candy authoring reference.
- `/charly-infrastructure:redis` — the backing redis service.
- [`opencharly/charly`](https://github.com/opencharly/charly) — the charly CLI and image builder
- [`opencharly/opencharly`](https://github.com/opencharly/opencharly) — the umbrella
