# AGENTS.md — pod-check-redis-init-layer

Standalone candy repo for the `check-redis-init-layer` candy — a companion
initializer that seeds redis with `bench:hello = world` after `redis-server` is
up. The entire candy lives in `charly.yml` at the repo root: the
`check-redis-init-layer:` entity with its `require`, `distro`, `service`, and
`plan:`. There is no source tree — the seed script is authored inline in the
plan.

Canonical files:

- `charly.yml` — the `check-redis-init-layer` candy entity (description,
  `require`, `distro`, `service`, `plan`).
- `.github/workflows/tag-on-merge.yml` — CalVer tag + `CHANGELOG/` on merge.
- `README.md` — user overview only; never agent guidance.

## Load these skills first (R0)

- `/charly-image:layer` — the owning authoring reference (`charly.yml` schema,
  `plan:` step verbs incl. `check:`, service declarations). Load before editing,
  building, or troubleshooting this candy.
- `/charly-infrastructure:redis` — the backing redis service this candy seeds.
- `/charly-check:check` — the disposable-deploy R10 bed surface.
- `/charly-internals:git-workflow` — before any git/PR action.

The candy carries no `skill:` entity of its own; the authoring reference
`/charly-image:layer` covers the surface. The gap is routed to the named
skill-authoring batch
[opencharly/opencharly#291](https://github.com/opencharly/opencharly/issues/291);
when that lands, add the owning skill here.

## Build / validate / test

- `charly box validate` at the repo root — the structural check: the manifest
  must parse and validate at the installed charly.
- The merge gate is the **org-wide** `charly/pr-validator` (required check
  `validate / validate`, defined in `opencharly/.github`); this repo has **no**
  per-repo candy gate. Its only workflow file is
  `.github/workflows/tag-on-merge.yml`.
- The live R10 witness is a composing check bed; the candy's own `check:` steps
  assert the seed script is installed and that live `redis-cli get bench:hello`
  returns `world`.

## Modify this repo

- Edit the `check-redis-init-layer:` candy entity in `charly.yml`. The seed
  script's wait loop and the key it sets are the product.
- Keep the `require: pod-redis` pin in step with the redis layer; the seed script
  depends on `redis-cli` shipping from that layer.

## Landing

- PR-only. Every change lands through a pull request; the org-required
  `charly/pr-validator` validates the diff and body and arms native auto-merge
  on PASS. Direct pushes to `main` are blocked.
- History lives in `CHANGELOG/` (written by `tag-on-merge` at merge time); the
  PR body IS the changelog.
- The authoritative rulebook is the umbrella `AGENTS.md` in
  `opencharly/opencharly` and `charly/AGENTS.md` in the charly repo. Do not
  restate its rules here.
