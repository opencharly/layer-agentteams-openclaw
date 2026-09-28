# AGENTS.md — layer-agentteams-openclaw

Standalone candy repo for the `agentteams-openclaw` layer. The whole candy lives
in `charly.yml` at the repo root: a `layer-nodejs` require, an Arch package
section, and an ordered `plan:` that builds the openclaw gateway from a pinned
upstream source commit (with a build cache) and asserts the gateway and its
Matrix extension are present. There is no source tree and no service of its own.

**No dedicated owning skill exists for this candy.** The openclaw gateway surface
is documented by the family stack skill (below); the npm-based `/charly-openclaw:*`
skills describe the published gateway, not this source build. If a future change
adds a `skill:` entity, project it as `/charly-agentteams:<name>` here.

Canonical files:

- `charly.yml` — the `agentteams-openclaw:` candy entity (require, packages, plan).
- `.github/workflows/tag-on-merge.yml` — CalVer tag + `CHANGELOG/` on merge.
- `README.md` — user overview only; never agent guidance.

## Load these skills first (R0)

- `/charly-agentteams:agentteams` — the family stack skill: how the manager and
  worker images that consume this candy are composed and deployed. Load before
  editing or troubleshooting the candy.
- `/charly-internals:root-cause-analyzer` — the build has several
  environment-sensitive steps (the JS pnpm re-exec, npm git-dep gating, the
  Matrix crypto native addon). Load before changing the build commands.
- `/charly-image:layer` — the candy authoring reference (`charly.yml` schema,
  `plan:` step verbs incl. `run:`/`check:`, `cache:`, distro package sections).
  Load before editing any entity field or plan step.

## Build / validate / test

- `charly box validate` at the repo root — the structural check: the manifest
  must parse and validate at the installed charly.
  Keep the `version:` schema stamp within the installed charly's supported range.
- The merge gate is the **org-wide** `charly/pr-validator` (required check
  `validate / validate`, defined in `opencharly/.github`); this repo has no
  per-repo candy gate.
- There is no live bed: the candy is a build, so the evidence is its `plan:`
  `check:` steps — `/usr/local/bin/openclaw` is a symlink and
  `/opt/openclaw/extensions/matrix` is a directory.

## Modify this repo

- There is no embedded `skill:` entity to mirror here; a build change is
  described by the candy `description:` and proven by the `plan:` `check:`
  steps. If a dedicated skill is added later, add it as a `skill:` entity in the
  same change.
- The pinned upstream commit and the pnpm/npm environment flags are the
  production contract; change them only with a build that passes the plan.
- Keep `version:` at the schema stamp the pinned CI charly supports.

## Landing

- PR-only. Every change lands through a pull request; the org-required
  `charly/pr-validator` validates the diff and body and arms native auto-merge
  on PASS. Direct pushes to `main` are blocked.
- History lives in `CHANGELOG/` (written by `tag-on-merge` at merge time); the
  PR body IS the changelog.
- The authoritative rulebook is the umbrella `AGENTS.md` in
  `opencharly/opencharly` and `charly/AGENTS.md` in the charly repo. Do not
  restate its rules here.
