# Paper Radar — Claude example

A one-minute starting point for a daily arXiv radar that runs on **Claude
(Haiku)** instead of Jev. Fork this repo, add your key, and every weekday
morning you get a page of the few new papers worth reading.

Built on [paper-radar](https://github.com/Eliot5566/JEV-Paper-Radar)
(`Eliot5566/JEV-Paper-Radar@v0.1.3` as a GitHub Action) and
[`paper-radar` on PyPI](https://pypi.org/project/paper-radar/).

## Setup in 3 steps — about a minute

1. **Use this repo.** Fork it (or "Use this template" if that button is
   on), and edit `radar.toml` to say what you care about — the three
   interests in it now are just examples.
2. **Add your key.** Settings → Secrets and variables → Actions → New
   repository secret, name `ANTHROPIC_API_KEY`, value your Anthropic API
   key. Keys are never committed; the workflow only reads the secret.
3. **Enable Pages and run it.** Settings → Pages → Build and deployment →
   Source: **GitHub Actions**. Then Actions → Paper Radar → Run workflow.
   The first run takes a few minutes; after that it runs weekdays at
   02:00 UTC (10:00 Taipei) and the page updates itself.

Your page lands at `https://<you>.github.io/paper-radar-claude-example/`.

> **Without the key the workflow fails** — that is expected. Nothing runs
> until you add `ANTHROPIC_API_KEY`; the Action never prints the key in
> the log.

## What you get

- A daily page: must-read / maybe / near-miss, with a per-interest
  probability for every paper, plus RSS.
- An audit trail in `data/`: every decision, probability and model
  version, committed by the workflow, so next day's run dedupes against
  it and you can check the tool against your own judgement later.
- Cost, measured not guessed: on 2026-11-11 Claude Haiku judged 50
  papers for **50,125 input tokens, $0.0075** — about **$0.00015 per
  paper judged** (input tokens only, at Haiku's $0.15 per million input
  tokens). That day's listing for these five categories was 478 papers,
  so a full run that day would have been roughly **$0.07**. It scales
  with papers judged: a wider watchlist costs more. Note the tool counts
  input tokens only — Anthropic also bills the short JSON replies
  (output tokens), which the tool does not track, so the real bill is
  somewhat higher than the input-only figure.

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

## Links

- Main repo: <https://github.com/Eliot5566/JEV-Paper-Radar>
- Claude calibration write-up: <https://github.com/Eliot5566/JEV-Paper-Radar/blob/main/docs/claude-calibration.md>
- PyPI: <https://pypi.org/project/paper-radar/>
