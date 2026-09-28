# agentteams-openclaw

The shared AgentTeams openclaw-gateway runtime for OpenCharly's AgentTeams images.

The `agentteams-openclaw` candy builds the openclaw gateway from a pinned upstream
source commit. It is built from source rather than installed from npm because the
Matrix channel extension is **not** in the published npm `openclaw` package and
the published `@openclaw/matrix` package is broken (its `openclaw.extensions`
entry references `./index.ts`, which the tarball does not ship). The source build
is the only complete, version-matched path — and it is what the upstream
manager/worker images do.

It is one build shared by two consumers (R3): the `agentteams-manager` and
`agentteams-worker` images both compose it.

## What it provides

| Property | Value |
|---|---|
| Layer / candy | `agentteams-openclaw` |
| Requires | `@github.com/opencharly/layer-nodejs` |
| Builds | `/opt/openclaw/` (the gateway + bundled extensions) |
| Binary | `/usr/local/bin/openclaw` → `/opt/openclaw/openclaw.mjs` |
| Arch packages | `git`, `python`, `make`, `gcc`, `gettext`, `jq` |
| Service / port | none (the gateway is started by the consuming image) |
| Environment | none |

## How to use it

Compose the layer in a box's `candy:` list:

```yaml
my-agentteams-image:
  candy:
    base: cachyos-base
    candy:
      - '@github.com/opencharly/layer-agentteams-openclaw:<tag>'
```

Then, inside the built image:

```bash
openclaw --help
```

## Layout

- `charly.yml` — the `agentteams-openclaw:` candy entity: the `layer-nodejs`
  require, the arch package section, and the `plan:` (cached source build +
  `check:` steps).
- `.github/workflows/deploy.yml` — the manifest gate (`charly box validate`).
- `.github/workflows/tag-on-merge.yml` — CalVer tag + `CHANGELOG/` on merge.
- `README.md` — this user overview.

## Related

- Family skill: `/charly-agentteams:agentteams`
- Consumers: the `agentteams-manager` and `agentteams-worker` boxes in `opencharly/layer-agentteams`
- Node runtime: `/charly-coder:nodejs`
- [`opencharly/charly`](https://github.com/opencharly/charly) — the charly CLI and image builder
- [`opencharly/opencharly`](https://github.com/opencharly/opencharly) — the umbrella
