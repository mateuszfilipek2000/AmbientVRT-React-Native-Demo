# AmbientVRT React Native demo

A small React Native + Storybook app (rendered on the web via
[`@storybook/react-native-web-vite`](https://storybook.js.org/docs/get-started/frameworks/react-native-web-vite))
guarded by **AmbientVRT**, a visual-regression testing tool. Every pull request
re-screenshots each story across the light/dark theme variants, diffs them
against committed baselines, and **fails the check on any unaccepted visual
change** — attaching an HTML report with a baseline / candidate / diff triptych.

> The AmbientVRT package itself is not open source yet. This repo is just a
> consumer demo: CI clones the private package repo with a read-only token to
> build the engine and the capture adapter.

## Stories

Five stories across three groups, each captured in `light` and `dark` (10
snapshots total):

- `Components/Button` — Primary, Secondary, Disabled
- `Components/Card` — Default
- `Foundations/Typography` — Greetings

## See it in action

Open the **Pull requests** and **Actions** tabs:

- On `main`, the **Visual gate** check is green — the re-captured stories match
  the committed baselines under [`.ambient/baselines/`](.ambient/baselines/).
- A demo PR tweaks a component. The gate goes **red**, and the run's
  **`ambient-report`** artifact contains `report.html` plus the
  `baseline` / `candidate` / `diff` PNGs. (GitHub doesn't render HTML artifacts
  inline — download the artifact, unzip, and open `report.html`.)

## How it works

| Piece | Role |
| --- | --- |
| `src/*.stories.tsx` | The stories that get screenshotted. |
| `.storybook/preview.tsx` | Declares the `theme` (and `locale`) globals the variants drive. |
| `.ambient/baselines/` | The blessed PNGs each capture is compared against. |
| `ambient.config.yaml` | Declares the RN adapter, local baseline storage, variants, and threshold. |
| `.github/workflows/visual-gate.yml` | Runs the gate on the default branch and every PR. |
| `.github/workflows/bless-baselines.yml` | One-shot: captures + commits the baselines (run once before the first PR). |

Because browser screenshots are only byte-reproducible inside a matched
environment, the baselines are captured **in CI** (the same x64 image the gate
runs in), not on a developer machine — that's what the "Bless baselines"
workflow is for.

## Setup

1. Push this repo to GitHub.
2. Add a repository secret **`AMBIENT_REPO_TOKEN`** — a fine-grained PAT with
   **Contents: Read-only** on the private AmbientVRT package repo.
3. Run the **Bless baselines** workflow once (Actions tab → Run workflow) to
   capture and commit the goldens.
4. Open PRs — the visual gate now has baselines to compare against.
