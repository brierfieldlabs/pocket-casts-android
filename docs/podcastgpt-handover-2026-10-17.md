# PodcastGPT development handover — 17 October 2026

## Authoritative state

This file records verified GitHub state at handover. **Resume against GitHub**, not conversation summaries or old CT140 directories. This is Brierfield Labs' personal Android fork of Pocket Casts; do not submit personal customisations to upstream without explicit approval.

- Fork: https://github.com/brierfieldlabs/pocket-casts-android
- Stable **source** baseline: `add/podcastgpt-customisations` at `347602d288afb7933579cb95f1fb6b8f55a66f0e`.
- Immutable source milestone: tag **`v1.0.0`**, release https://github.com/brierfieldlabs/pocket-casts-android/releases/tag/v1.0.0 . The `podcastgpt.version` file at this tag is `1.0.0`. **No APK is attached to that milestone**.
- Latest downloadable personal test APK remains **`podcastgpt-android-v0.2.1-rc.1`**: https://github.com/brierfieldlabs/pocket-casts-android/releases/tag/podcastgpt-android-v0.2.1-rc.1 . Do not rename/relabel this binary as 1.0.0.
- The fork's `main` is its default branch, used by scheduled workflows; the application customisations live on the separate branch above.
- The next, **unmerged** startup-fix PR is **#4**, https://github.com/brierfieldlabs/pocket-casts-android/pull/4 , head `42cd5898ce9ef81117fbf3dc4c4360c67c4055e6`, branch `fix/podcastgpt-startup-debug-overhead`, targeting `add/podcastgpt-customisations`. Its version file currently says **`1.0.1`** (the earlier `1.0.1-rc.1` intermediate commit was superseded). **There is no 1.0.1 release/APK yet.**
- Official Pocket Casts contribution **PR #6188** (hide caught-up podcasts): https://github.com/Automattic/pocket-casts-android/pull/6188 , still open; do not modify it with personal fork features.

## Implemented on the v1.0.0 source baseline

- Android launcher name **PodcastGPT** with separate `.debug` application ID and debug certificate; coexists with official Pocket Casts.
- **Hide caught-up podcasts** switch: hides subscriptions with no unarchived, unplayed/in-progress episodes, with reactive reappearance and folder filtering. Source originated from upstream PR #6188.
- Sort the Podcasts screen by total unfinished episode count, including folder totals; reverse order works with every existing sort choice. Stored manual positions remain unchanged. New fork-only sort preferences do not alter Pocket Casts' synced sort enum.
- Badges show the real unfinished episode count rather than capping it at 99.
- Edge-to-edge artwork grid; existing list mode remains available.
- Scrolling changes: targeted badge updates rather than whole-grid refreshes, reduced change animations, reuse of badge composition, and pool-aware Compose disposal. **User still reported jerkiness**; do not claim it is fixed.
- Removed automatic Smart folders popup and folder upsell without deleting existing folders.
- Removed advertising fetch integration and the ad UI from Podcasts and Player; keep unrelated legacy database/compatibility types.
- New subscriptions default to **oldest episodes first**; preserve per-podcast sort choices for existing subscriptions.
- About displays the PodcastGPT version separately from the underlying Pocket Casts version/build.

Previous focused source checks and v0.2.1 APK were successful, but earlier CT140 manual build/test logs must not be represented as authoritative CI. Phone acceptance of the **v1.0.0 or v1.0.1 APK has not happened**, because neither APK has been released.

## Current problem: prolonged white screen at cold launch

User reports a lengthy blank white screen when starting the app. This is **not yet resolved or benchmarked**.

Open PR #4 is a deliberately narrow proposal:
- App `debugProd` variant only: set `isDebuggable = false` in `app/build.gradle.kts` to avoid debug-only overhead, including `StrictMode.detectAll()` at application start.
- Keep `.debug` package ID, production-server selection, signing key, playback setup, and migrations unchanged.
- Set `podcastgpt.version` to `1.0.1`; preserve v1.0.0 tag.
- This is a candidate improvement, **not evidence that the white screen is fixed**.

GitHub's latest reported PR #4 head is `42cd5898...`. The unprivileged checkout `/home/workbench/worktrees/podcastgpt-startup` was observed at `7254b8d1d`, **behind the PR head**; fetch and verify before making further changes. PR #4's lightweight GitHub Validation checks passed. Its inherited `claude-review` job failed because the Claude GitHub app is not installed in the fork, not due to the proposed code. **Do not treat these checks as a full Android build or on-phone acceptance.**

### Immediate next action

1. Fetch the current remote state and PR #4. Read the repository's current `AGENTS.md`, README, accepted decisions, and PRs; follow the Brierfield Labs Coding House Rules.
2. Verify the exact PR head `42cd5898...` (or its successor) with the appropriate Android build/checks on the **designated CI runner**. CT140 is a workbench, not CI. Don't substitute another machine or create needless test infrastructure.
3. Confirm the APK still uses `au.com.shiftyjelly.pocketcasts.debug` and the existing signing certificate so it updates the installed personal fork without data loss.
4. Test cold versus warm launches on a **real Android phone**, including playback, sync, and normal settings. Compare the white-screen delay with the existing v0.2.1 APK. If debug overhead is not the main bottleneck, inspect startup traces before adding more fixes.
5. Only after checks and approval, merge PR #4, then publish a properly versioned **1.0.1** APK and verify uploaded digest/signature. Keep the immutable v1.0.0 milestone and older APKs available.
6. Separately, investigate remaining podcast scrolling jerkiness **using phone evidence**. Avoid another broad UI rewrite without identifying the bottleneck.

## Upstream synchronisation

The fork's GitHub Action `.github/workflows/podcastgpt-upstream-review.yml` is active on `main`:
https://github.com/brierfieldlabs/pocket-casts-android/actions/workflows/podcastgpt-upstream-review.yml

- Schedule: **Monday 08:23 UTC**; manual `workflow_dispatch` available.
- Checks Automattic upstream `main` and, if necessary, prepares a reviewable **merge proposal PR** targeting `add/podcastgpt-customisations`.
- Merge conflicts produce a failed run with conflict details; no automatic merge, APK release, or phone update.
- Initial manual run https://github.com/brierfieldlabs/pocket-casts-android/actions/runs/38980871011 completed successfully and found the branch already current at that time. PR-creation/conflict behaviour has not yet been exercised in a live upstream update.
- Workflow introduced/merged through the fork's PR #2, https://github.com/brierfieldlabs/pocket-casts-android/pull/2 .
- Check/fix downstream app compatibility and test before accepting any proposed upstream merge.

## Environment and rules

- GitHub is authoritative: commit and push completed work; keep PR discussions and the next action discoverable there. The fork currently has GitHub Issues disabled, so PRs and this handover are the appropriate records.
- **Do not develop as root.** The legacy checkout `/srv/workspaces/pocket-casts-android` is root-owned; avoid root Git/gh and avoid adding a global `safe.directory` exception. Use a workbench-owned checkout/worktree, such as `/home/workbench/worktrees/podcastgpt-startup`, after fetching latest remote state.
- **CT140 is a Git workbench, not CI.** Follow https://github.com/brierfieldlabs/infrastructure/blob/main/procedures/coding-house-rules.md and repository `AGENTS.md`. Do not use personal laptops as CI runners.
- Keep changes small, compatible with upstream, and proportional. Do not introduce extra services, broad security restrictions, or test infrastructure without evidence.
- Keep upstream PR #6188 separate from PodcastGPT personal customisations; ask before changing production behaviour, publishing a final release, or expanding scope.

**One next step:** verify and test fork PR **#4** on its current head for the white launch screen. No release or phone update should be claimed until that work succeeds.
