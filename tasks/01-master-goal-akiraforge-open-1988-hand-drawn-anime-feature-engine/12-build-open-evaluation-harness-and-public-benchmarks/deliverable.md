# AkiraForge contribution: Build open evaluation harness and public benchmarks
Contributor: @SuddenlyJon (agent-assisted)
Task ID: `cmsi8jx89000lvj8ws8c4p9nb`
Project: AkiraForge - Open 1988 Hand-Drawn Anime Feature Engine

## Acceptance checklist

- [x] eval/ suite with runnable entrypoint
- [x] metrics documented with pass/fail thresholds (v0 provisional OK)
- [x] sample synthetic inputs included
- [x] human preference study template (markdown)
- [x] MIT LICENSE; no copyrighted benchmark frames

## Legal footer

```
License: MIT
Project: AkiraForge
Rails: original works only; style approximation; no unlicensed copyrighted training data; no API keys in repo
```

---

## File: `README.md`

# Open evaluation harness and public benchmarks v0

**License:** MIT  
**Project:** AkiraForge - Open 1988 Hand-Drawn Anime Feature Engine  
**Task:** Build open evaluation harness and public benchmarks  
**Task ID:** `cmsi8jx89000lvj8ws8c4p9nb`

Offline eval suite for Gate-1 anime studio packages: identity, temporal coherence, style fidelity vs style bible axes, optical-flow smoothness, narrative LLM-judge prompts, and a human preference study template.

## Run

```bash
cd 10-eval-harness
python -m eval
```

Expected: `overall_pass: True` on the synthetic fixture.

## Acceptance checklist

- [x] eval/ suite with runnable entrypoint
- [x] metrics documented with pass/fail thresholds (v0 provisional OK)
- [x] sample synthetic inputs included
- [x] human preference study template (markdown)
- [x] MIT LICENSE; no copyrighted benchmark frames

## Layout

| Path | Role |
|------|------|
| `eval/run_eval.py` | Entrypoint |
| `eval/metrics/*` | Metric implementations |
| `eval/thresholds.py` | Pass/fail gates |
| `sample_data/synthetic/clip_fixture.json` | Synthetic fixture |
| `templates/human-preference-study.md` | Human study template |
| `metrics.md` | Threshold docs |
| `LICENSE` | MIT |

## Rails

- No copyrighted benchmark frames
- Original IP / synthetic only
- No API keys in repo
- Progressive honesty: Gate-1 thresholds only

---

## File: `LICENSE`

MIT License

Copyright (c) 2026 AkiraForge contributors (@SuddenlyJon / Pitchfork-and-Torch)

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.

---

## File: `metrics.md`

# Evaluation metrics and pass/fail thresholds (v0)

**License:** MIT (this doc ships with harness code); formulas aligned with QC critics leaf  
**Project:** AkiraForge  
**Version:** 0.1.0  
**Note:** Provisional. Recalibrate on original synthetic fixtures only.

| Metric | Code | Gate-1 PASS | Notes |
|--------|------|-------------|-------|
| Identity | `identity_score` | mean >= 0.78 | Token Jaccard now; swap to ID-EMB later |
| Temporal drop | `temporal_coherence` | no drop > 0.15; mean >= 0.78 | TMP-FACE-DROP |
| Style axes | `style_fidelity` | mean >= 3.5 /5; no axis < 2 | ST-AX vs style bible |
| Optical flow | `optical_flow_smoothness` | spike rate <= 0.05 | cuts excluded; cut_flags length must match |
| Narrative | LLM judge prompt | mean >= 3.5; rails_ok | No auto-call in harness |

## How to run

```bash
cd 10-eval-harness
python -m eval
# or
python eval/run_eval.py
```

## Sample data

`sample_data/synthetic/clip_fixture.json` - original OC tokens, no copyrighted frames.

---

## File: `eval/__init__.py`

```py
# SPDX-License-Identifier: MIT
"""AkiraForge open evaluation harness v0."""

__version__ = "0.1.0"
```

---

## File: `eval/__main__.py`

```py
# SPDX-License-Identifier: MIT
from .run_eval import main

raise SystemExit(main())
```

---

## File: `eval/run_eval.py`

```py
# SPDX-License-Identifier: MIT
"""Runnable entrypoint: python -m eval.run_eval

Offline. Uses sample_data/synthetic only. No copyrighted benchmark frames.
"""

from __future__ import annotations

import json
import sys
from pathlib import Path
from typing import Any, Dict

if __package__ in (None, ""):
    # allow `python eval/run_eval.py` from package root: the relative imports
    # below need a parent package, so register it before they run
    sys.path.insert(0, str(Path(__file__).resolve().parents[1]))
    __package__ = "eval"

from .metrics.identity import identity_score
from .metrics.temporal import temporal_coherence
from .metrics.style import style_fidelity
from .metrics.optical_flow import optical_flow_smoothness
from .metrics.narrative import narrative_judge_prompt
from .thresholds import THRESHOLDS

ROOT = Path(__file__).resolve().parents[1]
SAMPLE = ROOT / "sample_data" / "synthetic"


def load_json(name: str) -> Dict[str, Any]:
    return json.loads((SAMPLE / name).read_text(encoding="utf-8"))


def run() -> Dict[str, Any]:
    fixture = load_json("clip_fixture.json")
    id_res = identity_score(
        fixture["reference_tokens"],
        fixture["frame_tokens"],
        gate=THRESHOLDS["identity_gate"],
    )
    tmp_res = temporal_coherence(
        id_res["per_frame"],
        max_drop=THRESHOLDS["temporal_max_drop"],
        gate_mean=THRESHOLDS["temporal_mean_gate"],
    )
    st_res = style_fidelity(
        fixture["style_axes"],
        min_mean=THRESHOLDS["style_min_mean"],
        min_axis=THRESHOLDS["style_min_axis"],
    )
    of_res = optical_flow_smoothness(
        fixture["flow_magnitudes"],
        k=THRESHOLDS["optical_flow_k"],
        max_spike_rate=THRESHOLDS["optical_flow_max_spike_rate"],
        cut_flags=fixture.get("cut_flags"),
    )
    report = {
        "harness": "akiraforge-eval-v0",
        "version": "0.1.0",
        "license": "MIT",
        "fixture": "sample_data/synthetic/clip_fixture.json",
        "thresholds": THRESHOLDS,
        "metrics": {
            "identity": id_res,
            "temporal": tmp_res,
            "style": st_res,
            "optical_flow": of_res,
        },
        "narrative_judge_prompt_included": True,
        "overall_pass": all(
            [
                id_res["pass"],
                tmp_res["pass"],
                st_res["pass"],
                of_res["pass"],
            ]
        ),
        "rails": {
            "original_ip_only": True,
            "no_copyrighted_benchmark_frames": True,
            "no_api_keys": True,
        },
    }
    return report


def main(argv: list[str] | None = None) -> int:
    report = run()
    out = ROOT / "sample_data" / "synthetic" / "last_report.json"
    out.write_text(json.dumps(report, indent=2), encoding="utf-8")
    print("AkiraForge eval harness v0")
    print(f"  overall_pass: {report['overall_pass']}")
    for name, m in report["metrics"].items():
        print(f"  {name}: pass={m['pass']} score={m.get('score')}")
    print(f"  narrative prompt chars: {len(narrative_judge_prompt())}")
    print(f"  wrote {out}")
    # also print first 200 chars of judge prompt for visibility
    return 0 if report["overall_pass"] else 1


if __name__ == "__main__":
    raise SystemExit(main())
```

---

## File: `eval/thresholds.py`

```py
# SPDX-License-Identifier: MIT
"""v0 provisional pass/fail thresholds (recalibrate on original fixtures)."""

THRESHOLDS = {
    "identity_gate": 0.78,
    "temporal_max_drop": 0.15,
    "temporal_mean_gate": 0.78,
    "style_min_mean": 3.5,
    "style_min_axis": 2.0,
    "optical_flow_max_spike_rate": 0.05,
    "optical_flow_k": 4.0,
    "narrative_min_mean": 3.5,
}
```

---

## File: `eval/metrics/__init__.py`

```py
# SPDX-License-Identifier: MIT
from .identity import identity_score
from .temporal import temporal_coherence
from .style import style_fidelity
from .optical_flow import optical_flow_smoothness
from .narrative import narrative_judge_prompt

__all__ = [
    "identity_score",
    "temporal_coherence",
    "style_fidelity",
    "optical_flow_smoothness",
    "narrative_judge_prompt",
]
```

---

## File: `eval/metrics/identity.py`

```py
# SPDX-License-Identifier: MIT
"""Character identity scores (v0 heuristic on synthetic fixtures)."""

from __future__ import annotations

from typing import Any, Dict, List, Sequence


def identity_score(
    reference_tokens: Sequence[str],
    frame_tokens_list: Sequence[Sequence[str]],
    *,
    gate: float = 0.78,
) -> Dict[str, Any]:
    """Mean Jaccard overlap of identity tokens vs reference set.

    Production: replace with embedding cosine (ID-EMB) from QC metrics leaf.
    """
    ref = set(t.lower() for t in reference_tokens)
    if not ref or not frame_tokens_list:
        return {"score": 0.0, "pass": False, "gate": gate, "per_frame": []}
    per = []
    for toks in frame_tokens_list:
        fset = set(t.lower() for t in toks)
        inter = len(ref & fset)
        union = len(ref | fset) or 1
        per.append(inter / union)
    mean = sum(per) / len(per)
    return {
        "metric": "ID-TOKEN-JACCARD",
        "score": round(mean, 4),
        "min": round(min(per), 4),
        "pass": mean >= gate,
        "gate": gate,
        "per_frame": [round(x, 4) for x in per],
    }
```

---

## File: `eval/metrics/temporal.py`

```py
# SPDX-License-Identifier: MIT
"""Temporal coherence metrics."""

from __future__ import annotations

from typing import Any, Dict, List, Sequence


def temporal_coherence(
    frame_scores: Sequence[float],
    *,
    max_drop: float = 0.15,
    gate_mean: float = 0.78,
) -> Dict[str, Any]:
    """Detect identity/style score drops across consecutive frames."""
    if not frame_scores:
        return {"score": 0.0, "pass": False, "drops": []}
    drops = []
    for i in range(1, len(frame_scores)):
        drop = frame_scores[i - 1] - frame_scores[i]
        if drop > max_drop:
            drops.append({"from": i - 1, "to": i, "drop": round(drop, 4)})
    mean = sum(frame_scores) / len(frame_scores)
    return {
        "metric": "TMP-FACE-DROP",
        "score": round(mean, 4),
        "pass": mean >= gate_mean and len(drops) == 0,
        "max_drop_allowed": max_drop,
        "drops": drops,
        "gate_mean": gate_mean,
    }
```

---

## File: `eval/metrics/style.py`

```py
# SPDX-License-Identifier: MIT
"""Style fidelity vs style bible axes (v0)."""

from __future__ import annotations

from typing import Any, Dict, Mapping


DEFAULT_AXES = (
    "line_quality",
    "palette_restraint",
    "limited_animation_timing",
    "camera_multiplane",
    "fx_vocabulary",
    "composite_grain",
)


def style_fidelity(
    axis_scores: Mapping[str, float],
    *,
    min_mean: float = 3.5,
    min_axis: float = 2.0,
    scale_max: float = 5.0,
) -> Dict[str, Any]:
    """ST-AX rubric: axes 0-5. PASS if mean >= 3.5 and no axis < 2."""
    scores = []
    missing = []
    for ax in DEFAULT_AXES:
        if ax not in axis_scores:
            missing.append(ax)
            continue
        scores.append(float(axis_scores[ax]))
    if not scores:
        return {"score": 0.0, "pass": False, "missing": list(DEFAULT_AXES)}
    mean = sum(scores) / len(scores)
    worst = min(scores)
    return {
        "metric": "ST-AX",
        "score": round(mean, 4),
        "score_normalized": round(mean / scale_max, 4),
        "worst_axis": worst,
        "pass": mean >= min_mean and worst >= min_axis and not missing,
        "min_mean": min_mean,
        "min_axis": min_axis,
        "missing": missing,
        "axes": {k: float(axis_scores[k]) for k in DEFAULT_AXES if k in axis_scores},
    }
```

---

## File: `eval/metrics/optical_flow.py`

```py
# SPDX-License-Identifier: MIT
"""Optical-flow smoothness notes (v0 synthetic magnitudes)."""

from __future__ import annotations

from statistics import median
from typing import Any, Dict, Sequence


def optical_flow_smoothness(
    flow_magnitudes: Sequence[float],
    *,
    k: float = 4.0,
    max_spike_rate: float = 0.05,
    cut_flags: Sequence[bool] | None = None,
) -> Dict[str, Any]:
    """TMP-OF: spike rate where mag > k * median on non-cut frames.

    Median is taken over non-cut frames only so shot-boundary spikes do not
    inflate the baseline and hide real non-cut jitter.

    When cut_flags is provided it must be the same length as flow_magnitudes;
    a shorter flag list previously treated trailing frames as non-cuts and
    could hide (or invent) spikes around the truncated boundary.
    """
    if not flow_magnitudes:
        return {"score": 0.0, "pass": False, "spike_rate": 1.0}
    mags = list(flow_magnitudes)
    if cut_flags is not None and len(cut_flags) != len(mags):
        return {
            "score": 0.0,
            "pass": False,
            "spike_rate": 1.0,
            "error": "cut_flags_length_mismatch",
            "frames_counted": 0,
            "cut_flags_len": len(cut_flags),
            "flow_len": len(mags),
        }

    def _is_cut(i: int) -> bool:
        return cut_flags is not None and bool(cut_flags[i])

    noncut = [mag for i, mag in enumerate(mags) if not _is_cut(i)]
    if not noncut:
        return {"score": 0.0, "pass": False, "spike_rate": 1.0, "frames_counted": 0}
    med = median(noncut) or 1.0
    spikes = 0
    counted = 0
    for i, mag in enumerate(mags):
        if _is_cut(i):
            continue
        counted += 1
        if mag > k * med:
            spikes += 1
    rate = spikes / max(counted, 1)
    # score: 1 - rate (higher better)
    score = max(0.0, 1.0 - rate)
    return {
        "metric": "TMP-OF",
        "score": round(score, 4),
        "spike_rate": round(rate, 4),
        "median_mag": round(med, 4),
        "k": k,
        "pass": rate <= max_spike_rate,
        "max_spike_rate": max_spike_rate,
        "spikes": spikes,
        "frames_counted": counted,
    }
```

---

## File: `eval/metrics/narrative.py`

```py
# SPDX-License-Identifier: MIT
"""Narrative coherence LLM-judge prompts (no model calls in harness)."""

from __future__ import annotations

NARRATIVE_JUDGE_PROMPT = """You are an AkiraForge narrative coherence judge.

TASK
Score the provided original short clip package (script + boards + shot list)
for narrative coherence. Original IP only. Style approximation of 1988
theatrical hand-drawn anime technique is in-scope; title clones are out.

INPUTS
- logline
- beat list (ordered)
- shot list with durations
- optional dialogue transcript

RUBRIC (0-5 each)
1. Goal clarity: does the protagonist have a readable want?
2. Beat coverage: are setup / turn / button present for clip length?
3. Causal chain: does each beat follow from the previous?
4. Visual storytelling: would boards alone communicate the story?
5. Scope honesty: is runtime claim consistent with shot count?

OUTPUT JSON
{
  "axes": {"goal_clarity": 0-5, "beat_coverage": 0-5, "causal_chain": 0-5,
           "visual_storytelling": 0-5, "scope_honesty": 0-5},
  "mean": number,
  "pass": true/false,
  "notes": "short free text",
  "rails_ok": true/false
}

PASS if mean >= 3.5, no axis < 2, and rails_ok is true.
Refuse if the package clones a named commercial title/character.
"""


def narrative_judge_prompt() -> str:
    return NARRATIVE_JUDGE_PROMPT
```

---

## File: `sample_data/synthetic/clip_fixture.json`

```json
{
  "id": "ren7_rail_run_synthetic_v0",
  "license": "CC0-1.0",
  "note": "Synthetic fixture only. Original OC tokens. No copyrighted frames.",
  "reference_tokens": [
    "ren7",
    "spiky_dark_hair",
    "amber_eye_glint",
    "steel_blue_jacket",
    "amber_stripe",
    "courier_silhouette"
  ],
  "frame_tokens": [
    ["ren7", "spiky_dark_hair", "amber_eye_glint", "steel_blue_jacket", "amber_stripe", "courier_silhouette"],
    ["ren7", "spiky_dark_hair", "amber_eye_glint", "steel_blue_jacket", "amber_stripe", "courier_silhouette"],
    ["ren7", "spiky_dark_hair", "amber_eye_glint", "steel_blue_jacket", "amber_stripe", "courier_silhouette"],
    ["ren7", "spiky_dark_hair", "amber_eye_glint", "steel_blue_jacket", "amber_stripe", "courier_silhouette"],
    ["ren7", "spiky_dark_hair", "amber_eye_glint", "steel_blue_jacket", "amber_stripe", "courier_silhouette"],
    ["ren7", "spiky_dark_hair", "amber_eye_glint", "steel_blue_jacket", "amber_stripe", "courier_silhouette"],
    ["ren7", "spiky_dark_hair", "amber_eye_glint", "steel_blue_jacket", "amber_stripe", "courier_silhouette"],
    ["ren7", "spiky_dark_hair", "amber_eye_glint", "steel_blue_jacket", "amber_stripe", "courier_silhouette"]
  ],
  "style_axes": {
    "line_quality": 4.0,
    "palette_restraint": 4.2,
    "limited_animation_timing": 3.8,
    "camera_multiplane": 4.1,
    "fx_vocabulary": 3.6,
    "composite_grain": 3.5
  },
  "flow_magnitudes": [1.0, 1.1, 1.05, 1.2, 1.15, 1.3, 1.25, 1.1, 1.05, 4.8, 1.2, 1.1],
  "cut_flags": [false, false, false, false, false, false, false, false, false, true, false, false],
  "narrative": {
    "logline": "Original courier Ren-7 races a neon rail packet across Neo-Kowloon before curfew.",
    "beats": ["setup: receive packet", "turn: rail blockade", "button: vault the gap"]
  }
}
```

---

## File: `templates/human-preference-study.md`

# Human preference study template (AkiraForge Gate-1)

**License:** CC-BY-4.0  
**Project:** AkiraForge  
**Version:** 0.1.0  
**Purpose:** Blind A/B preference between two original clip packages.

## Rules

1. **Original IP only.** Discard any sample that clones a named commercial title/character.
2. **Blind.** Raters do not see model names, seed, or author.
3. **Short sessions.** Max 12 pairs per rater to limit fatigue.
4. **No unlicensed frames.** Synthetic or artist-contributed only.

## Rater instructions

You will see **Pair N: Clip A vs Clip B** (order randomized).

For each pair, pick the winner on each axis (or "tie"):

| Axis | Question |
|------|----------|
| Identity | Which keeps the same character more consistently? |
| Style | Which better matches 1988 theatrical hand-drawn technique (line, palette, timing, multiplane)? |
| Temporal | Which has fewer pops / morphs / jitter? |
| Narrative | Which tells a clearer micro-story? |
| Overall | Which would you rather show as a public Gate-1 demo? |

Optional free text (1-2 sentences): why?

## Score sheet (copy per rater)

```
Rater ID: ________  Date: ________  Session: ________
Pair __ : Identity A/B/T  Style A/B/T  Temporal A/B/T  Narrative A/B/T  Overall A/B/T
Notes:
```

## Aggregation

- Per axis win-rate for system X = wins / (wins+losses) ignoring ties
- Overall preference = overall wins / pairs
- Report n raters, n pairs, confidence interval if n >= 30 pairs

## Consent / ethics

Educational research on open synthetic media. No biometric collection. No personal data beyond optional anonymous rater ID.
