# home-assistant-beszel-agent — Plan

| Field | Value |
|---|---|
| Artifact ID | PLAN-home-assistant-beszel-agent |
| Source PRD | `PRD-home-assistant-beszel-agent` (`docs/prd/home-assistant-beszel-agent-PRD.md`) |
| Status | Draft |
| Last Updated | 2026-10-10 |
| WBS | `docs/wbs/home-assistant-beszel-agent-WBS.md` |

## Goal

A Home Assistant user can install a Beszel Hub and one or more Beszel Agent apps from the app store, configure them with Supervisor options and watch them with HA's watchdog, on amd64 and aarch64. The repository keeps these apps in step with upstream Beszel and with each other, and every app is covered by the same test and release pipeline.

## Done means

- All six apps (Hub, standard Agent, S.M.A.R.T., Intel, AMD, NVIDIA) install and start on their supported architectures, and `scripts/test-apps.sh` passes.
- `scripts/test-e2e.sh` shows a real Hub and real Agents registering as `status=up` with stored metrics.
- One multi-arch image per app is published on a version change, and the repository's PRD, plan, WBS and changelog are filled in with no template text.

## Approach

The phases or steps in the order they will happen. Each phase becomes a group of rows in the WBS.

1. Phase 1 — Fork upstream and ship the standard agent app (Supervisor config, data mapping, watchdog).
2. Phase 2 — Add the S.M.A.R.T. variant with the privileges and device access it needs.
3. Phase 3 — Add the GPU variants (Intel, AMD, NVIDIA) with the right bases and tooling.
4. Phase 4 — Add the Beszel Hub app with web UI, `/api/health`, ingress and cold backups.
5. Phase 5 — Harden for homelab use: drop host privileges, verify downloads, stop auto-publishing.
6. Phase 6 — Build the test and release pipeline: `test-apps.sh`, `test-e2e.sh`, CI workflows and multi-arch publishing.
7. Phase 7 — Fill the Portfolio documents (PRD, plan, WBS, changelog).

## How it will be checked

What proves each phase works (tests, a demo, a measurement).

- Phases 1–4 — `scripts/test-apps.sh --static` (YAML, required files, versions, `bash -n`, shellcheck, hadolint) plus the build and smoke phases; agents must reach port 45876 and the hub must answer `/api/health`.
- Phase 4 — `scripts/test-apps.sh --supervisor` proves the Hub reads `app_url` and ingress config is valid.
- Phase 5 — the static phase passes with the known hadolint findings suppressed at source, and releases only ship verified digests.
- Phase 6 — `scripts/test-e2e.sh` with `--all-agents`, and the same scripts called from `.github/workflows/validate.yml` and `publish.yml`.
- Phase 7 — Portfolio's own scan of this folder warns or fails for no check.

## Decisions and open questions

| Decision | Choice | Why | Date |
|---|---|---|---|
| Which apps to ship | Hub plus five agent variants | Covers plain hosts, disk health and all supported GPU vendors | 2026-10-04 |
| GPU variant bases | Alpine for Intel/AMD, Debian for NVIDIA | NVIDIA toolkit injects glibc binaries that will not run on musl; `intel_gpu_top` is x86_64-only | 2026-10-04 |
| Versioning | One shared app version in `beszel_agent/config.yaml`; upstream Beszel pinned in `beszel_version` | Keeps every app in step and lets HA resolve the right arch | 2026-10-04 |
| Release automation | Scheduled sync checks upstream every 8 hours; single-app builds on demand | Keeps apps current without a maintainer step, and avoids empty rebuilds | 2026-10-04 |
| Homelab hardening | Drop host privileges, verify the download, stop auto-publishing | Reduce blast radius on a home network | 2026-10-04 |

Rejected options: a `-{arch}` image suffix (replaced by one multi-arch image per app, see `docs/RELEASE.md`); separate with/without S.M.A.R.T. images (upstream ships smartmontools in all non-scratch images, so GPU variants bundle it too); a 32-bit architecture (dropped).
