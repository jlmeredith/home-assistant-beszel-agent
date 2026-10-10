# home-assistant-beszel-agent — Product Requirements

| Field | Value |
|---|---|
| Artifact ID | PRD-home-assistant-beszel-agent |
| Status | Draft |
| Owner | Jamie |
| Last Updated | 2026-10-10 |
| Plan | `docs/plans/home-assistant-beszel-agent-PLAN.md` |

## Overview

This repository packages Beszel, a lightweight server-monitoring stack, as Home Assistant apps. It provides a Hub app (the Beszel web UI, API and SQLite database) and a family of Agent apps that collect system, disk and GPU metrics and report them to a Hub, so a Home Assistant user can monitor the HA host and other machines without leaving the HA app store.

## Problem statement

Beszel is normally run with Docker Compose outside Home Assistant. Home Assistant OS users cannot run arbitrary containers, so they have no supported way to run a Hub or Agent, and the upstream project ships no Home Assistant apps. Without this repository, an HA user must run monitoring on a separate machine and lose the one-click install, Supervisor configuration, watchdog and app-level backups that HA apps get.

## Scope

- In scope:
  - A Beszel Hub app: web UI, REST API, `/api/health`, ingress, backed-up data.
  - Agent apps across the standard, S.M.A.R.T. and GPU (Intel, AMD, NVIDIA) variants.
  - Configuration via Supervisor options and `environment_vars`, plus a watchdog health endpoint.
  - Build, test and release automation for every app (multi-arch images, CI, auto upstream sync).
- Out of scope:
  - Changes to Beszel itself (tracked upstream at <https://github.com/henrygd/beszel>).
  - Home Assistant Core/Supervisor changes.
  - Hosting the Beszel Hub as a managed cloud service.

## Requirements

- R1 — Provide a standard Beszel Agent app installable from the HA app store on amd64 and aarch64.
- R2 — Provide S.M.A.R.T. and GPU agent variants (Intel amd64-only, AMD, NVIDIA) that bundle the tooling and required privileges/passthrough.
- R3 — Provide a Beszel Hub app with web UI, REST API, `/api/health`, ingress and cold backups.
- R4 — Read configuration (`hub_url`, `token`, `key`, `app_url`) from Supervisor options with `BESZEL_*` environment fallbacks for testing.
- R5 — Expose a watchdog health endpoint on port 45877 (agents) and `/api/health` on 8090 (hub).
- R6 — Build one multi-arch image per app and publish on a version change; automate syncing with upstream Beszel.
- R7 — Test every app statically, by build, by smoke test, and end-to-end (Hub + Agents), in CI and locally.
- R8 — Keep the repository's Portfolio documents (PRD, plan, WBS, changelog) filled and current.

## Acceptance criteria

- R1/R2/R3 — Each app has a `config.yaml`, `Dockerfile`, `run.sh` and `DOCS.md`; `scripts/test-apps.sh --static` passes and each image builds.
- R4 — `scripts/test-apps.sh --supervisor` shows the agent reading `hub_url`/`token`/`key` and the hub reading `app_url` from the mock Supervisor API, and a malformed `environment_vars` name is skipped with a warning.
- R5 — Agents reach port 45876 and stay up; the hub answers `/api/health` with `{"message":"API is healthy.","code":200,...}`.
- R6 — `publish.yml` produces one `linux/amd64,linux/arm64` manifest per app; the scheduled run only acts when `beszel_agent/config.yaml` moves.
- R7 — `scripts/test-apps.sh` exits non-zero on any failure; `scripts/test-e2e.sh` reports each agent `status=up` and stored real metrics.
- R8 — This PRD, the plan and the WBS contain no template placeholders and every section has content or an explicit open question.

## Constraints and dependencies

- Runs on Home Assistant OS/Supervised via the Supervisor; apps use `bashio` to read options.
- Base images: `ghcr.io/home-assistant/base` (Alpine) for most apps, `ghcr.io/home-assistant/base-debian` for the NVIDIA variant.
- Upstream Beszel release is pinned per app in `beszel_version` (currently 0.21.0); the Intel variant is amd64-only because `intel_gpu_top` is x86_64-only, and the NVIDIA variant needs glibc.
- Images are published to GHCR; GitHub Actions (`.github/workflows/validate.yml`, `publish.yml`) run the same scripts used locally.
- Key files: `README.md`, `docs/ARCHITECTURE.md`, `docs/TESTING.md`, `docs/RELEASE.md`, `scripts/test-apps.sh`, `scripts/test-e2e.sh`, each app's `config.yaml`/`Dockerfile`/`run.sh`.

## Open questions

- None open; the design (`docs/ARCHITECTURE.md`) and release process (`docs/RELEASE.md`) record the decisions taken.
