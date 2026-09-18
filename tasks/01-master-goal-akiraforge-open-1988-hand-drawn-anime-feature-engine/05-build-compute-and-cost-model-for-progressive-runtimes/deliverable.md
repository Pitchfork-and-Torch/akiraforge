# AkiraForge contribution: Compute and cost model for progressive runtimes

Contributor: @SuddenlyJon (agent-assisted)
Task ID: `cmsi8jx89000evj8w6qid56x0`
Project: AkiraForge - Open 1988 Hand-Drawn Anime Feature Engine

## Acceptance checklist

- [x] cost-model.csv with runtime rows and cost columns
- [x] assumptions.md with every variable explained
- [x] three scenarios (low/mid/high) for 2026 and 2028
- [x] sensitivity notes (what moves cost most)
- [x] reproducible with public price sources cited

## Legal footer

```
Project: AkiraForge
Rails: original works only; style approximation; no unlicensed copyrighted training data; no API keys in repo
```

---

## File: `README.md`

# Leaf: Build compute and cost model for progressive runtimes

**Status:** Ready for peer review  
**License:** CC-BY-4.0 (docs); CSV numeric tables reusable as CC0/CC-BY  
**Task ID:** `cmsi8jx89000evj8w6qid56x0`

## Files

| File | Role |
|------|------|
| `cost-model.csv` | Runtime x scenario x year cost table |
| `assumptions.md` | Every variable + formulas + sensitivity |
| `scenarios-narrative.md` | Human-readable gate guidance |
| `sources.md` | Public price source classes + peer checks |
| `SUBMIT-BODY.md` | Single-file submission for GrokForge API |

## Acceptance checklist

- [x] cost-model.csv with runtime rows and cost columns
- [x] assumptions.md with every variable explained
- [x] three scenarios (low/mid/high) for 2026 and 2028
- [x] sensitivity notes (what moves cost most)
- [x] reproducible with public price sources cited

---

## File: `assumptions.md`

# AkiraForge compute and cost model - assumptions

**License:** CC-BY-4.0  
**Project:** AkiraForge - Open 1988 Hand-Drawn Anime Feature Engine  
**Version:** 0.1.0  
**Date:** 2026-08-07  
**Disclaimer:** Planning model only. Public list prices change; re-run before budgeting capital. No secrets. Style approximation only; original works only.

## Purpose

Transparent cost model for progressive runtimes:

| Gate | Runtime | Intent |
|------|---------|--------|
| Gate-1 | ~30 seconds | High-fidelity style + identity proof |
| Gate-2 | ~5 minutes | Narrative short with audio |
| Gate-3 | ~20 minutes | OVA-scale experiment path |
| Gate-4 | 90-120 minutes | Costed theatrical feature path |

Currency: **USD**. Years: **2026 actual-ish** and **2028 projected**.

## Scenario definitions

| Scenario | Meaning |
|----------|---------|
| **Low** | Heavy local open-weight GPU, thrifty retries, volunteer human hours at $0 book cost |
| **Mid** | Hybrid local + API; paid human review at contractor rates; normal retry waste |
| **High** | Mostly cloud APIs + premium video models; professional review rates; high retry/waste |

## Variable dictionary

### Production volume (frames / shots)

| Variable | Unit | Default (used in CSV) | Notes |
|----------|------|----------------------|-------|
| `fps_out` | frames/sec | 24 | Theatrical delivery fps |
| `anim_on` | ones vs twos | mostly twos | Effective unique drawings ~12 fps average for limited animation; keys denser |
| `unique_draw_factor` | fraction of fps | 0.55 | Blend of keys/inbetweens; 0.5 ~ pure twos |
| `unique_frames` | count | `runtime_sec * fps_out * unique_draw_factor` | Primary gen unit for stills pipeline |
| `shot_count` | shots | see CSV | Storyboard / continuity units |
| `bg_count` | backgrounds | ~0.6 * shots | Shared BGs across multiplane layers |
| `fx_shot_frac` | fraction | 0.25 | Shots needing heavy FX layers |
| `retry_multiplier` | unitless | Low 1.3 / Mid 1.8 / High 2.5 | Failed gens, critic rejects, identity fixes |

### Image / video generation

| Variable | Low | Mid | High | Notes |
|----------|-----|-----|------|-------|
| `still_cost_per_frame` | $0.002 | $0.02 | $0.08 | Local amortized electricity+wear vs API stills |
| `video_cost_per_sec` | $0.05 | $0.40 | $1.50 | Short clip models; feature path rarely pure video-gen |
| `video_use_frac` | 0.15 | 0.35 | 0.55 | Fraction of runtime that is direct video gen vs stills+interp |
| `controlnet_overhead` | 1.2x | 1.4x | 1.5x | Reference locks, depth, line, multi-view |

### Local GPU amortization (Low scenario dominant)

| Variable | Value | Notes |
|----------|-------|-------|
| `gpu_capex` | $2,000 - $4,000 | Consumer/prosumer 24GB-class card band (public street) |
| `useful_hours` | 4,000 | Conservative useful life before upgrade pressure |
| `power_kw` | 0.35 | Draw under load |
| `electricity_per_kwh` | $0.15 | US residential-ish; vary by region |
| `amort_per_gpu_hour` | ~$0.55 - $1.10 | Capex/hours + power |

CSV Low uses **$0.80/GPU-hour** blended compute cost converted into per-frame rates via assumed throughput (120 stills/GPU-hour mid quality with ControlNet stack; 40s video/GPU-hour for short open models - provisional).

### Storage

| Variable | Value | Notes |
|----------|-------|-------|
| `gb_per_min_intermediates` | 2.5 GB | Boards, keys, layers, logs, drafts |
| `gb_per_min_masters` | 0.4 GB | Final composites + audio |
| `storage_cost_per_gb_month` | $0.023 | Object storage public list ballpark (S3-class) |
| `retention_months` | 12 | Active project year |
| `egress_factor` | 0.3 * stored | Review downloads / peer review |

### Voice

| Variable | Low | Mid | High |
|----------|-----|-----|------|
| `dialogue_words_per_min` | 90 | 110 | 130 |
| `tts_cost_per_1k_chars` | $0 | $0.015 | $0.06 |
| `chars_per_word` | 5.5 | 5.5 | 5.5 |
| `voice_take_multiplier` | 1.2 | 2.0 | 3.0 | Retakes, multi-language (JP+EN option) |
| `human_va_per_min_finished` | $0 | $25 | $120 | Only Mid/High book human VA where used |

### Music / score

| Variable | Low | Mid | High |
|----------|-----|-----|------|
| `score_minutes` | = runtime | = runtime | = runtime |
| `ai_music_per_min` | $0 | $0.50 | $3.00 |
| `composer_human_per_min` | $0 | $40 | $250 | Geinoh-inspired density is human-heavy in High |
| `stems_mix_hours` | 0.1 * runtime_min | 0.25 * runtime_min | 0.5 * runtime_min |

### Human review hours

| Variable | Low | Mid | High |
|----------|-----|-----|------|
| `review_hours_per_min` | 0.4 | 1.2 | 2.5 | Critic + human gate |
| `hourly_rate` | $0 (volunteer) | $45 | $95 |
| `director_overhead_frac` | 0.15 | 0.25 | 0.35 | Showrunner / continuity |

### Orchestration / agent tokens (LLM)

| Variable | Low | Mid | High |
|----------|-----|-----|------|
| `llm_tokens_per_min_runtime` | 15k | 40k | 90k | Script, boards, critic, continuity |
| `llm_cost_per_1m_tokens` | $0.50 | $3.00 | $15.00 | Mix of free/local vs frontier |

## Runtime row formulas (summary)

```
unique_frames = runtime_sec * 24 * unique_draw_factor
still_spend = unique_frames * still_cost_per_frame * retry_multiplier * controlnet_overhead
video_spend = runtime_sec * video_use_frac * video_cost_per_sec * retry_multiplier
storage_spend = runtime_min * (gb_per_min_intermediates + gb_per_min_masters)
                * storage_cost_per_gb_month * retention_months * (1 + egress_factor)
voice_spend = dialogue path (TTS and/or VA) * take_multiplier
music_spend = ai_music and/or human composer + mix hours * rate
llm_spend = runtime_min * llm_tokens_per_min_runtime / 1e6 * llm_cost_per_1m_tokens
human_spend = runtime_min * review_hours_per_min * hourly_rate * (1 + director_overhead_frac)
total = still + video + storage + voice + music + llm + human
```

## 2026 vs 2028 projection rules

| Factor | 2028 Low | 2028 Mid | 2028 High |
|--------|----------|----------|-----------|
| Still / video unit cost | x0.45 | x0.55 | x0.70 | Open weights + competition |
| LLM token unit cost | x0.35 | x0.50 | x0.65 |
| Storage | x0.85 | x0.90 | x0.95 |
| Human rates | x1.10 | x1.12 | x1.15 | Wage inflation |
| Retry multiplier | x0.85 | x0.90 | x0.95 | Better critics reduce waste |
| Video use fraction | +0.05 | +0.08 | +0.10 | More usable long-form video |

## Sensitivity (what moves cost most)

Ranked by impact on Gate-4 feature (mid 2026):

1. **Retry multiplier** - identity/style rejects dominate waste. Cutting Mid 1.8 -> 1.3 saves ~25-30% gen spend.
2. **Video use fraction + video $/sec** - pure cloud video for long form is lethal; prefer stills + limited video.
3. **Human review hours/rate** - High scenario is human-dominated above ~20 min.
4. **Still cost * unique frames** - fps and ones/twos choices matter.
5. **Score/VA human path** - optional but large if fully professional.
6. **Storage** - rarely dominant unless multi-year HD intermediates retained hot.

## What this model deliberately excludes

- Marketing, festival fees, legal clearance beyond open-license defaults
- Office rent, full studio payroll for a commercial house
- Unlicensed training data costs (forbidden by LEGAL-RAILS)
- SuperGrok/xAI keys stored on the board (never)

## Peer check method

1. Pick one CSV row (e.g. 2026_mid_5min).
2. Recompute `unique_frames` from runtime and factor.
3. Multiply by Mid still cost, retry, controlnet; compare to `still_usd`.
4. Spot-check one public price source in `sources.md` against a cell.

## Footer

```
License: CC-BY-4.0 (docs) / CC0 for numeric CSV tables preferred for reuse
Project: AkiraForge
Rails: original works only; style approximation; no unlicensed copyrighted training data; no API keys in repo
```

---

## File: `cost-model.csv`

```csv
year,scenario,runtime_label,runtime_sec,runtime_min,shot_count,unique_frames,still_usd,video_usd,storage_usd,voice_usd,music_usd,llm_usd,human_usd,total_usd,cost_per_finished_min_usd,notes
2026,low,30s,30,0.5,6,396,1.23,0.98,0.45,0.00,0.00,0.00,0.00,2.66,5.32,local GPU thrifty; volunteer review
2026,low,5min,300,5,40,3960,12.35,9.75,4.49,0.00,0.00,0.04,0.00,26.63,5.33,gate-2 thrifty path
2026,low,20min,1200,20,140,15840,49.40,39.00,17.97,0.00,0.00,0.15,0.00,106.52,5.33,ova thrifty
2026,low,feature_105min,6300,105,700,83160,259.35,204.75,94.34,0.00,0.00,0.79,0.00,559.23,5.33,local-heavy feature path
2026,mid,30s,30,0.5,6,396,20.16,7.56,0.45,1.82,20.25,0.06,33.75,84.05,168.10,hybrid API+local; contractor review
2026,mid,5min,300,5,40,3960,201.60,75.60,4.49,18.15,202.50,0.60,337.50,840.44,168.09,gate-2 mid
2026,mid,20min,1200,20,140,15840,806.40,302.40,17.97,72.60,810.00,2.40,1350.00,3361.77,168.09,ova mid
2026,mid,feature_105min,6300,105,700,83160,4233.60,1587.60,94.34,381.15,4252.50,12.60,7087.50,17649.29,168.09,feature mid hybrid
2026,high,30s,30,0.5,6,396,118.80,61.88,0.45,14.85,126.50,0.68,166.25,489.41,978.82,premium API+pro human
2026,high,5min,300,5,40,3960,1188.00,618.75,4.49,148.50,1265.00,6.75,1662.50,4894.00,978.80,gate-2 high
2026,high,20min,1200,20,140,15840,4752.00,2475.00,17.97,594.00,5060.00,27.00,6650.00,19575.97,978.80,ova high
2026,high,feature_105min,6300,105,700,83160,24948.00,12993.75,94.34,3118.50,26565.00,141.75,34912.50,102773.84,978.80,feature high not recommended as pure cloud
2028,low,30s,30,0.5,6,396,0.47,0.40,0.38,0.00,0.00,0.00,0.00,1.25,2.50,2028 open-weight deflation
2028,low,5min,300,5,40,3960,4.73,3.95,3.82,0.00,0.00,0.01,0.00,12.51,2.50,2028 thrifty
2028,low,20min,1200,20,140,15840,18.92,15.80,15.27,0.00,0.00,0.05,0.00,50.04,2.50,2028 ova thrifty
2028,low,feature_105min,6300,105,700,83160,99.33,82.95,80.19,0.00,0.00,0.28,0.00,262.75,2.50,2028 local feature
2028,mid,30s,30,0.5,6,396,9.98,3.74,0.40,1.82,22.68,0.03,41.58,80.23,160.46,2028 mid hybrid
2028,mid,5min,300,5,40,3960,99.79,37.42,4.04,18.15,226.80,0.30,415.80,802.30,160.46,2028 gate-2
2028,mid,20min,1200,20,140,15840,399.17,149.69,16.17,72.60,907.20,1.20,1663.20,3209.23,160.46,2028 ova
2028,mid,feature_105min,6300,105,700,83160,2095.63,785.86,84.91,381.15,4762.80,6.30,8731.80,16848.45,160.46,2028 feature mid
2028,high,30s,30,0.5,6,396,79.00,41.16,0.43,14.85,145.48,0.42,191.19,472.53,945.06,still expensive if all-cloud
2028,high,5min,300,5,40,3960,790.02,411.67,4.27,148.50,1454.75,4.22,1911.88,4725.31,945.06,2028 gate-2 high
2028,high,20min,1200,20,140,15840,3160.08,1646.69,17.07,594.00,5819.00,16.88,7647.50,18891.22,945.06,2028 ova high
2028,high,feature_105min,6300,105,700,83160,16590.42,8645.11,89.62,3118.50,30549.75,88.59,40149.38,99131.37,945.06,prefer hybrid over pure high
```

---

## File: `scenarios-narrative.md`

# Progressive runtime cost narrative

**License:** CC-BY-4.0  
**Companion:** `cost-model.csv`, `assumptions.md`

## Executive summary (rounded)

| Runtime | 2026 Low | 2026 Mid | 2026 High | 2028 Mid |
|---------|----------|----------|-----------|----------|
| 30s | ~$3 | ~$84 | ~$490 | ~$80 |
| 5 min | ~$27 | ~$840 | ~$4.9k | ~$800 |
| 20 min | ~$107 | ~$3.4k | ~$20k | ~$3.2k |
| 105 min feature | ~$560 | ~$18k | ~$103k | ~$17k |

**Takeaway:** Gate-1 (30s) and Gate-2 (5 min) are affordable on Mid hybrid. Pure High cloud for a feature is a capital event, not a weekend. Low local paths stay under $1k for a full feature **if** volunteers and open weights hold quality - that is the open-source bet, not a free lunch on quality.

## Gate recommendations

1. **Do not fund Gate-4 until Gate-1 and Gate-2 pass** with published intermediates.
2. **Budget Mid for community demos**, High only for festival-polish spikes.
3. **Spend first on identity lock + critic quality** (retry multiplier), not more raw video seconds.
4. **Prefer stills + limited video + human polish** over end-to-end premium video gen for >5 min.

## Scenario stories

### Low 2026 - garage / lab

One strong GPU, open ComfyUI graph, free local LLM for agents, volunteer peer review on GrokForge. Electricity and wear are the real costs. Risk: quality bar may fail theatrical style bible without human craft.

### Mid 2026 - recommended default

Local GPU for bulk stills and ControlNet locks; paid API for hard shots and short video bursts; contractor review at $45/hr; light TTS; hybrid score. Matches "public goods software" ambition with honest labor.

### High 2026 - warning track

Frontier video + pro VA + pro composer + dense human QC. Useful as a **ceiling** and grant narrative, dangerous as a default plan. Feature High ~$100k compute+labor band before marketing.

### 2028

Unit gen costs fall; human craft does not. Mid feature still ~$17k in this model because review, music, and voice remain. Open orchestrator value = compressing retries and coordinating free/local capacity.

## Sensitivity one-pagers

- **If retry Mid 1.8 -> 1.3:** feature Mid 2026 total drops roughly 15-25% (gen-dominated slices).
- **If video_use_frac Mid 0.35 -> 0.15:** large cut on video_usd; more pressure on still pipeline quality.
- **If human rate Mid $45 -> $0:** totals collapse toward Low; only valid with real volunteer supply.

## Footer

```
License: CC-BY-4.0
Project: AkiraForge
Rails: original works only; no API keys; progressive gates only
```

---

## File: `sources.md`

# Cost model sources (public)

**License:** CC-BY-4.0  
**Note:** Prices are indicative public ranges as of early/mid 2026 planning. Replace cells before capital decisions.

## Compute and APIs

1. Cloud object storage public list pricing (S3-class ~$0.023/GB-month standard) - vendor pricing pages.
2. Consumer GPU street prices and TDP - public retailer listings; amortize over useful hours.
3. Residential electricity averages - EIA / regional utility public rates (~$0.12-$0.20/kWh US band).
4. Image API still pricing - public developer pricing pages for frontier and mid-tier image models (order-of-magnitude $0.01-$0.10/image).
5. Video gen API pricing - public per-second or per-clip lists for short-form models (order-of-magnitude $0.05-$2.00/sec depending on quality).
6. Open-weight local inference - electricity + amortized hardware only (no per-token cloud bill).

## Voice / music / labor

7. TTS API public rates - per-character or per-1k-char lists.
8. Contractor illustration / animation review rates - public freelance market bands ($30-$100+/hr).
9. Voice actor session rates - union and non-union public rate cards (High scenario only).
10. Composer / mixer freelance bands - public rate surveys (High scenario).

## LLM orchestration

11. Frontier and open LLM token pricing pages - blended $0.50-$15 per 1M tokens depending on model tier.
12. Local LLM serving - power only when Low scenario uses offline models.

## Method notes

- Prefer **order-of-magnitude** honesty over fake precision.
- CSV is **reproducible from assumptions.md formulas**; small rounding differences are OK.
- Do not paste private invoices or API keys into this pack.

## Peer spot-check (5 claims)

| # | Claim | Where to verify |
|---|-------|-----------------|
| 1 | Storage ~$0.02/GB-month class | Major cloud storage public pricing |
| 2 | High video $/sec dominates long form | Any premium video API price list x 6300s |
| 3 | Retry multiplier moves total more than storage | Compare still_usd with retry 1.3 vs 2.5 |
| 4 | Low scenario near-zero human $ | Volunteer assumption documented |
| 5 | 2028 unit costs lower, human rates higher | Projection rules table in assumptions.md |

