# home-assistant-beszel-agent — Work Breakdown

| Field | Value |
|---|---|
| Artifact ID | WBS-home-assistant-beszel-agent |
| Source PRD | `PRD-home-assistant-beszel-agent` (`docs/prd/home-assistant-beszel-agent-PRD.md`) |
| Source Plan | `docs/plans/home-assistant-beszel-agent-PLAN.md` |
| Status | Active |
| Last Updated | 2026-10-10 |

Status words the Portfolio board understands: **Not started**, **In progress**, **Blocked**, **Done**, **Deferred**.
Put `Jamie` in Owner for anything only a person can do: it shows up under "Needs you" and is never handed to an AI tool.

| WBS ID | Work package | Done means | Owner | Depends on | Status |
|---|---|---|---|---|---|
| W-1 | Phase 1: fork upstream and ship the standard agent app | Agent app installs and starts; `config.yaml`, `Dockerfile`, `run.sh` and data mapping exist | Jamie | | Done (2c025e4, 87d286a) |
| W-2 | Phase 2: add the S.M.A.R.T. agent variant | S.M.A.R.T. variant reads disks and passes `test-apps.sh --static` | Jamie | W-1 | Done (c842912, 27798c9) |
| W-3 | Phase 3: add the Intel, AMD and NVIDIA GPU variants | Each GPU variant builds on its base and passes its smoke test | Jamie | W-2 | Done (671b4d6) |
| W-4 | Phase 4: add the Beszel Hub app with ingress | Hub serves the UI, `/api/health` and ingress; `--supervisor` passes | Jamie | W-1 | Done (67774c9, c80001f, 5306f6b) |
| W-5 | Phase 5: harden for homelab use | Host privileges dropped, download verified, auto-publishing stopped | Jamie | W-4 | Done (3bdf0a4) |
| W-6 | Phase 6: build the test and release pipeline | `test-apps.sh` and `test-e2e.sh` pass; CI publishes one multi-arch image per app | Jamie | W-3, W-4 | Done (86759fb, 3c2d372, 671b4d6, 88e37cb) |
| W-7 | Phase 7: fill the Portfolio documents | PRD, plan, WBS and changelog have no template text or marker | Jamie | W-6 | Done (2b1b3e0, fix:check.template) |
| W-8 | Fix: Fill the template docs | No template text or marker is left, and every section has content or an explicit open question | Portfolio worker | | Done (checks not run here (docs-only fix, hp800 worker); Portfolio's own scan is the acceptance check) |
| W-9 | Review: Fill the template docs | The filled PRD, plan and WBS are accepted by the owner | Jamie | W-8 | Not started |
