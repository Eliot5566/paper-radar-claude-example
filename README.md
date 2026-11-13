# Paper Radar — Claude example

A one-minute starting point for a daily arXiv radar that runs on **Claude
(Haiku)** instead of Jev. Fork this repo, add your key, and every weekday
you get a page of the few new papers worth reading.

Built on [paper-radar](https://github.com/Eliot5566/JEV-Paper-Radar)
(`Eliot5566/JEV-Paper-Radar@v0.2.0` as a GitHub Action) and
[`paper-radar` on PyPI](https://pypi.org/project/paper-radar/).

[English Claude guide](https://github.com/Eliot5566/JEV-Paper-Radar/blob/main/docs/claude-quickstart.md) · [繁體中文教學](https://github.com/Eliot5566/JEV-Paper-Radar/blob/main/docs/claude-quickstart.zh-TW.md) · [Setup help](https://github.com/Eliot5566/JEV-Paper-Radar/issues/new?template=setup-help.yml)

The guide starts with a fictional offline demo requiring no API key. Live judging uses separately billed Anthropic API access, not a Claude chat subscription.

## Setup in 3 steps

1. **Use this repo.** Fork it (or "Use this template" if that button is
   on). Open Actions and enable workflows in the new fork. Edit `radar.toml` to say what you care about — the three
   interests in it now are just examples.
2. **Add your key.** Settings → Secrets and variables → Actions → New
   repository secret, name `ANTHROPIC_API_KEY`, value your Anthropic API
   key. Keys are never committed; the workflow only reads the secret.
3. **Enable Pages and run it.** Settings → Pages → Build and deployment →
   Source: **GitHub Actions**. Then Actions → Paper Radar → Run workflow.
   Enter `20` in the limit field for the first run. This limits papers, not dollar spending; retries and repeated runs can add charges.
   After that it runs weekdays at
   06:00 UTC (14:00 Taipei) and the page updates itself.

Your page lands at `https://<you>.github.io/paper-radar-claude-example/`.

> **Without the key the daily judging workflow fails** — that is expected.
> Add `ANTHROPIC_API_KEY` before running it. The separate configuration check
> and offline demo do not need a key.

## What you get

- A daily page: must-read / maybe / near-miss, with a per-interest
  probability for every paper, plus RSS.
- An audit trail in `data/`: every decision, probability and model
  version, committed by the workflow, so next day's run dedupes against
  it and you can check the tool against your own judgement later.
- Request usage in `data/usage.jsonl`: input, output and cache tokens,
  including repairs and failures. Missing usage or a required rate produces
  `cost_usd: null` (cost unknown), not a zero bill. Optional TL;DR generation
  is outside this accounting scope.

## Cost assumptions and historical measurements

The config pins Claude Haiku 5.5 and uses the ≤100K prompt rates checked on
2026-11-13: **$0.10 / million input tokens** and **$0.50 / million output
tokens**. Other models, prompt lengths, caching and provider discounts may
use different rates. Update the config when your applicable pricing changes.
[Official pricing](https://www.anthropic.com/claude/haiku).

The earlier 2026-11-11 experiment logged 50,125 input tokens and estimated
$0.0075 using its configured $0.15 / million input tokens. That was an
**input-only historical estimate**, not a provider invoice; it did not track
output or all retry charges. Version 0.2.0 adds the request audit so future
runs can be inspected without relabelling those historical estimates.
[Accounting fields and limits](https://github.com/Eliot5566/JEV-Paper-Radar/blob/v0.2.0/docs/usage-and-cost.md).

## About the numbers — read this before trusting them

The thresholds in `radar.toml` (`must_read = 0.65`, `maybe = 0.35`) come
from a **first-look calibration, not a user-validated one**: 49 papers
from one day, labelled by Muse (the assistant), not by the user who runs
the radar. On that set `paper-radar calibrate` reported Brier 0.040 and
ECE 0.081 — above the 0.05 bar the tool calls well calibrated — and the
suggested thresholds are 0.65 (precision ≥90%) and 0.35 (recall ≥90%).
Full method, label table and limitations:
[`docs/claude-calibration.md`](https://github.com/Eliot5566/JEV-Paper-Radar/blob/main/docs/claude-calibration.md).

Two things worth knowing:

- Claude's probabilities are **self-reported, not calibrated**. Jev's
  thresholds (0.80 / 0.50) do not transfer — do not copy them for Claude.
- The Jev thresholds were never validated against their user's own
  labels either, so this first-look is the first labelled measurement
  for either backend on the daily radar. Treat both backends the same
  way: label ~30 of your own papers (`paper-radar label …`) and re-fit
  with `paper-radar calibrate`.

## Check the setup without an API key

```bash
python -m pip install paper-radar==0.2.0
paper-radar check -c radar.toml
paper-radar demo --out demo
```

The CI workflow runs these checks and the fictional offline demo. It does
not fetch a live corpus or call a paid model. Keep independent human-labelled
holdout data separate from the examples before making accuracy claims.

## License

Code, workflows and configuration are MIT-licensed; see [LICENSE](LICENSE).
Cached paper titles, abstracts and other third-party content retain their
original source terms. The software license does not relicense that content.

## Links

- Main repo: <https://github.com/Eliot5566/JEV-Paper-Radar>
- Claude calibration write-up: <https://github.com/Eliot5566/JEV-Paper-Radar/blob/main/docs/claude-calibration.md>
- PyPI: <https://pypi.org/project/paper-radar/>
