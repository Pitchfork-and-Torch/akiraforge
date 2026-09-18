# AkiraForge contribution: Prototype orchestrator with quality gate routing
Contributor: @SuddenlyJon (agent-assisted)
Task ID: `cmsi8jx89000hvj8w4k3ii7ex`
Project: AkiraForge - Open 1988 Hand-Drawn Anime Feature Engine

## Acceptance checklist

- [x] runnable package with README install + dry-run
- [x] quality gates encoded as explicit nodes/conditions
- [x] mock agent stubs for at least 4 roles
- [x] tests or scripted demo of gate fail and gate pass paths
- [x] MIT LICENSE + license headers; no secrets in repo

## Legal footer

```
License: MIT
Project: AkiraForge
Rails: original works only; style approximation; no unlicensed copyrighted training data; no API keys in repo
```

---

## File: `README.md`

# AkiraForge Orchestrator v0.1.0

**License:** MIT  
**Project:** AkiraForge - Open 1988 Hand-Drawn Anime Feature Engine  
**Task:** Prototype orchestrator with quality gate routing  
**Task ID:** `cmsi8jx89000hvj8w4k3ii7ex`

Pure-Python top-level studio orchestrator that routes mock craft agents and **rejects tier promotion** (clip -> short -> OVA -> feature) unless explicit quality-gate PASSes are present.

No paid API keys. No network. Stdlib only.

## Install + dry-run

```bash
cd 08-orchestrator
python -m akiraforge_orchestrator --scenario pass
python -m akiraforge_orchestrator --scenario fail_identity
python -m akiraforge_orchestrator --scenario fail_composite --json
```

Optional install:

```bash
pip install -e .
akiraforge-orchestrator --scenario pass
```

## Tests (gate pass + fail paths)

```bash
python tests/test_gates.py -v
# or
python -m unittest discover -s tests -v
```

Expected: all tests PASS. Fail scenarios assert `status=BLOCKED` and list missing gates.

## Quality gates (explicit nodes)

| Gate ID | Role | Clip promotion |
|---------|------|----------------|
| `gate.identity` | consistency_lock | required |
| `gate.critic_identity` | critic_identity | required |
| `gate.critic_style` | critic_style | required |
| `gate.composite` | compositor | required |
| `gate.script` | scriptwriter | short+ |
| `gate.boards` | storyboard | short+ |
| `gate.voice` / `gate.audio` | voice / sound | short+ |
| `gate.promote` | director | computed |

Promotion requirements live in `akiraforge_orchestrator/gates.py` (`PROMOTION_REQUIREMENTS`). Single-step only: you cannot jump none -> OVA without intermediate tiers.

## Mock agents (4+ roles)

Director, Scriptwriter, Consistency Lock, Storyboard, Compositor, Voice, Sound. Swap stubs for real tools without changing gate contracts.

## Rails

- Original works only; style approximation of 1988 theatrical techniques
- No unlicensed copyrighted training data
- No API keys in repo
- Progressive gates only; never claim "feature ready" from a single PASS

## Files

| Path | Role |
|------|------|
| `akiraforge_orchestrator/gates.py` | Explicit gate + tier rules |
| `akiraforge_orchestrator/agents.py` | Mock agent stubs |
| `akiraforge_orchestrator/graph.py` | Routing graph |
| `akiraforge_orchestrator/cli.py` | Dry-run CLI |
| `tests/test_gates.py` | Pass/fail path tests |
| `examples/brief-clip.json` | Sample brief |
| `LICENSE` | MIT |

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

## File: `pyproject.toml`

```toml
[project]
name = "akiraforge-orchestrator"
version = "0.1.0"
description = "AkiraForge top-level studio orchestrator with quality-gate routing (dry-run mock agents)"
readme = "README.md"
requires-python = ">=3.10"
license = { text = "MIT" }
authors = [{ name = "AkiraForge contributors" }]
dependencies = []

[project.optional-dependencies]
dev = []

[project.scripts]
akiraforge-orchestrator = "akiraforge_orchestrator.cli:main"

[build-system]
requires = ["setuptools>=61"]
build-backend = "setuptools.build_meta"

[tool.setuptools.packages.find]
include = ["akiraforge_orchestrator*"]
```

---

## File: `akiraforge_orchestrator/__init__.py`

```py
# SPDX-License-Identifier: MIT
"""AkiraForge orchestrator package (quality-gate routing)."""

__version__ = "0.1.0"
```

---

## File: `akiraforge_orchestrator/gates.py`

```py
# SPDX-License-Identifier: MIT
"""Explicit quality-gate nodes and tier promotion rules.

Gate IDs align with deliverables/03-agent-roster quality-gates.md.
Promotion is never automatic from a single agent PASS.
"""

from __future__ import annotations

from dataclasses import dataclass, field
from enum import Enum
from typing import Dict, Iterable, List, Optional, Set


class Tier(str, Enum):
    NONE = "none"
    CLIP_30S = "clip_30s"
    SHORT_5M = "short_5m"
    OVA_20M = "ova_20m"
    FEATURE = "feature"


# Ordered promotion ladder
TIER_ORDER: List[Tier] = [
    Tier.NONE,
    Tier.CLIP_30S,
    Tier.SHORT_5M,
    Tier.OVA_20M,
    Tier.FEATURE,
]


# Minimum required PASS gate IDs to promote from -> to
PROMOTION_REQUIREMENTS: Dict[tuple[Tier, Tier], Set[str]] = {
    (Tier.NONE, Tier.CLIP_30S): {
        "gate.identity",
        "gate.critic_style",
        "gate.composite",
        "gate.critic_identity",
    },
    (Tier.CLIP_30S, Tier.SHORT_5M): {
        "gate.identity",
        "gate.critic_style",
        "gate.composite",
        "gate.critic_identity",
        "gate.script",
        "gate.boards",
        "gate.voice",
        "gate.audio",
        "gate.critic_continuity",
        "gate.critic_av",
    },
    (Tier.SHORT_5M, Tier.OVA_20M): {
        "gate.identity",
        "gate.critic_style",
        "gate.composite",
        "gate.critic_identity",
        "gate.script",
        "gate.boards",
        "gate.keys",
        "gate.bg",
        "gate.fx",
        "gate.color",
        "gate.voice",
        "gate.audio",
        "gate.critic_continuity",
        "gate.critic_av",
        "gate.promote",
    },
    (Tier.OVA_20M, Tier.FEATURE): {
        "gate.identity",
        "gate.critic_style",
        "gate.composite",
        "gate.critic_identity",
        "gate.script",
        "gate.boards",
        "gate.keys",
        "gate.bg",
        "gate.fx",
        "gate.color",
        "gate.voice",
        "gate.audio",
        "gate.critic_continuity",
        "gate.critic_av",
        "gate.promote",
    },
}


@dataclass
class GateResult:
    gate_id: str
    passed: bool
    score: Optional[float] = None
    notes: str = ""
    owner: str = ""


@dataclass
class GateRegistry:
    """Collects explicit gate results for a production package."""

    results: Dict[str, GateResult] = field(default_factory=dict)

    def record(self, result: GateResult) -> None:
        self.results[result.gate_id] = result

    def passed_ids(self) -> Set[str]:
        return {gid for gid, r in self.results.items() if r.passed}

    def failed(self) -> List[GateResult]:
        return [r for r in self.results.values() if not r.passed]

    def missing_for_promotion(self, current: Tier, target: Tier) -> Set[str]:
        key = (current, target)
        if key not in PROMOTION_REQUIREMENTS:
            return set()
        required = PROMOTION_REQUIREMENTS[key]
        return required - self.passed_ids()

    def can_promote(self, current: Tier, target: Tier) -> bool:
        if current not in TIER_ORDER or target not in TIER_ORDER:
            return False
        if TIER_ORDER.index(target) != TIER_ORDER.index(current) + 1:
            # only allow single-step promotion
            return False
        return len(self.missing_for_promotion(current, target)) == 0


def next_tier(current: Tier) -> Optional[Tier]:
    i = TIER_ORDER.index(current)
    if i >= len(TIER_ORDER) - 1:
        return None
    return TIER_ORDER[i + 1]


def summarize_gates(registry: GateRegistry) -> Dict[str, object]:
    return {
        "passed": sorted(registry.passed_ids()),
        "failed": [
            {"gate_id": r.gate_id, "notes": r.notes, "score": r.score}
            for r in registry.failed()
        ],
        "count_pass": sum(1 for r in registry.results.values() if r.passed),
        "count_fail": sum(1 for r in registry.results.values() if not r.passed),
    }
```

---

## File: `akiraforge_orchestrator/agents.py`

```py
# SPDX-License-Identifier: MIT
"""Mock studio agents for dry-run / CI (no paid API keys).

Real deployments swap these stubs for LLM or tool-backed agents while keeping
the same input/output contracts.
"""

from __future__ import annotations

from dataclasses import dataclass, field
from typing import Any, Dict, List, Protocol

from .gates import GateResult


class Agent(Protocol):
    role: str

    def run(self, state: Dict[str, Any]) -> Dict[str, Any]:
        ...


@dataclass
class MockDirector:
    role: str = "director"
    force_promote: bool = False

    def run(self, state: Dict[str, Any]) -> Dict[str, Any]:
        brief = state.get("brief", {})
        decision = {
            "action": "schedule_pipeline",
            "tier_goal": brief.get("tier_goal", "clip_30s"),
            "notes": "Director scheduled progressive pipeline (mock).",
            "force_promote": self.force_promote,
        }
        state.setdefault("artifacts", {})["director_decision"] = decision
        state.setdefault("log", []).append(f"[{self.role}] scheduled pipeline")
        return state


@dataclass
class MockScriptwriter:
    role: str = "scriptwriter"
    fail: bool = False

    def run(self, state: Dict[str, Any]) -> Dict[str, Any]:
        if self.fail:
            state.setdefault("gate_results", []).append(
                GateResult("gate.script", False, notes="incomplete structure", owner=self.role)
            )
            state.setdefault("log", []).append(f"[{self.role}] FAIL incomplete")
            return state
        state.setdefault("artifacts", {})["script"] = {
            "title": state.get("brief", {}).get("title", "Untitled"),
            "pages": 2,
            "dialogue_lines": 12,
            "timed": True,
        }
        state.setdefault("gate_results", []).append(
            GateResult("gate.script", True, score=1.0, notes="structure complete", owner=self.role)
        )
        state.setdefault("log", []).append(f"[{self.role}] script PASS")
        return state


@dataclass
class MockConsistencyLock:
    role: str = "consistency_lock"
    fail: bool = False
    identity_score: float = 0.91

    def run(self, state: Dict[str, Any]) -> Dict[str, Any]:
        pack = {
            "character_id": state.get("brief", {}).get("character_id", "ren7"),
            "refs": ["front", "profile", "three_quarter", "expression"],
            "do_not": ["named commercial IP"],
            "version": "0.1.0",
        }
        state.setdefault("artifacts", {})["identity_lock_pack"] = pack
        if self.fail or self.identity_score < 0.85:
            state.setdefault("gate_results", []).append(
                GateResult(
                    "gate.identity",
                    False,
                    score=self.identity_score,
                    notes="identity pack incomplete or score below threshold",
                    owner=self.role,
                )
            )
            state.setdefault("gate_results", []).append(
                GateResult(
                    "gate.critic_identity",
                    False,
                    score=self.identity_score,
                    notes="identity critic FAIL",
                    owner="critic_identity",
                )
            )
            state.setdefault("log", []).append(f"[{self.role}] identity FAIL score={self.identity_score}")
            return state
        state.setdefault("gate_results", []).append(
            GateResult("gate.identity", True, score=1.0, notes="pack valid", owner=self.role)
        )
        state.setdefault("gate_results", []).append(
            GateResult(
                "gate.critic_identity",
                True,
                score=self.identity_score,
                notes="identity critic PASS",
                owner="critic_identity",
            )
        )
        state.setdefault("log", []).append(f"[{self.role}] identity PASS score={self.identity_score}")
        return state


@dataclass
class MockStoryboard:
    role: str = "storyboard"
    fail: bool = False

    def run(self, state: Dict[str, Any]) -> Dict[str, Any]:
        if self.fail:
            state.setdefault("gate_results", []).append(
                GateResult("gate.boards", False, notes="missing shot boards", owner=self.role)
            )
            state.setdefault("log", []).append(f"[{self.role}] boards FAIL")
            return state
        state.setdefault("artifacts", {})["boards"] = {
            "shots": 8,
            "camera_notes": True,
        }
        state.setdefault("gate_results", []).append(
            GateResult("gate.boards", True, score=1.0, notes="all shots boarded", owner=self.role)
        )
        state.setdefault("log", []).append(f"[{self.role}] boards PASS")
        return state


@dataclass
class MockCompositor:
    role: str = "compositor"
    fail: bool = False

    def run(self, state: Dict[str, Any]) -> Dict[str, Any]:
        if self.fail:
            state.setdefault("gate_results", []).append(
                GateResult("gate.composite", False, notes="unplayable composite", owner=self.role)
            )
            state.setdefault("log", []).append(f"[{self.role}] composite FAIL")
            return state
        state.setdefault("artifacts", {})["composite"] = {
            "playable": True,
            "grain": "1988-style-mock",
            "duration_s": state.get("brief", {}).get("duration_s", 30),
        }
        state.setdefault("gate_results", []).append(
            GateResult("gate.composite", True, score=1.0, notes="playable composite", owner=self.role)
        )
        # style critic rides with composite for clip gate mock
        state.setdefault("gate_results", []).append(
            GateResult(
                "gate.critic_style",
                True,
                score=0.88,
                notes="style axes provisional PASS (mock)",
                owner="critic_style",
            )
        )
        state.setdefault("log", []).append(f"[{self.role}] composite+style PASS")
        return state


@dataclass
class MockVoice:
    role: str = "voice"
    fail: bool = False

    def run(self, state: Dict[str, Any]) -> Dict[str, Any]:
        if self.fail:
            state.setdefault("gate_results", []).append(
                GateResult("gate.voice", False, notes="missing takes", owner=self.role)
            )
            state.setdefault("log", []).append(f"[{self.role}] voice FAIL")
            return state
        state.setdefault("artifacts", {})["voice"] = {"takes": 12, "lip_map": True}
        state.setdefault("gate_results", []).append(
            GateResult("gate.voice", True, score=1.0, notes="takes complete", owner=self.role)
        )
        state.setdefault("log", []).append(f"[{self.role}] voice PASS")
        return state


@dataclass
class MockSound:
    role: str = "sound"
    fail: bool = False

    def run(self, state: Dict[str, Any]) -> Dict[str, Any]:
        if self.fail:
            state.setdefault("gate_results", []).append(
                GateResult("gate.audio", False, notes="cues misaligned", owner=self.role)
            )
            state.setdefault("log", []).append(f"[{self.role}] audio FAIL")
            return state
        state.setdefault("artifacts", {})["audio"] = {"cue_sheet": True, "stems": ["m&e", "dialogue"]}
        state.setdefault("gate_results", []).append(
            GateResult("gate.audio", True, score=1.0, notes="cues aligned", owner=self.role)
        )
        state.setdefault("gate_results", []).append(
            GateResult(
                "gate.critic_av",
                True,
                score=0.9,
                notes="AV sync mock PASS",
                owner="critic_av",
            )
        )
        state.setdefault("gate_results", []).append(
            GateResult(
                "gate.critic_continuity",
                True,
                score=0.92,
                notes="continuity mock PASS",
                owner="critic_continuity",
            )
        )
        state.setdefault("log", []).append(f"[{self.role}] audio+critics PASS")
        return state


def default_roster(
    *,
    fail_identity: bool = False,
    fail_composite: bool = False,
    fail_script: bool = False,
) -> List[Agent]:
    """Return at least 4 mock roles for acceptance criteria."""
    return [
        MockDirector(),
        MockScriptwriter(fail=fail_script),
        MockConsistencyLock(fail=fail_identity),
        MockStoryboard(),
        MockCompositor(fail=fail_composite),
        MockVoice(),
        MockSound(),
    ]
```

---

## File: `akiraforge_orchestrator/graph.py`

```py
# SPDX-License-Identifier: MIT
"""Function-calling style production graph with explicit quality-gate nodes.

No external orchestration framework required. Pure stdlib so CI runs offline
without paid keys. Nodes:

  brief -> director -> craft_agents -> collect_gates -> promote_check -> done

Promotion from clip->short->OVA->feature is rejected unless required gate
PASSes are present.
"""

from __future__ import annotations

import json
from typing import Any, Dict, List, Optional

from .agents import Agent, default_roster
from .gates import (
    GateRegistry,
    GateResult,
    TIER_ORDER,
    Tier,
    next_tier,
    summarize_gates,
)


def _coerce_tier(value: str) -> Tier:
    try:
        return Tier(value)
    except ValueError:
        return Tier.NONE


def _promotion_target(current: Tier, goal: Tier) -> Optional[Tier]:
    """Single-step promotion toward goal (never multi-hop)."""
    if current == goal:
        return current
    nxt = next_tier(current)
    if nxt is None:
        return None
    # If goal is further ahead, still only evaluate one step
    if TIER_ORDER.index(goal) <= TIER_ORDER.index(current):
        return current
    return nxt


def run_pipeline(
    brief: Dict[str, Any],
    *,
    agents: Optional[List[Agent]] = None,
    current_tier: str = "none",
    scenario: str = "pass",
) -> Dict[str, Any]:
    """Execute a dry-run production graph.

    scenario:
      - pass: all mock gates PASS (clip promotion eligible)
      - fail_identity: identity gates FAIL (promotion blocked)
      - fail_composite: composite FAIL (promotion blocked)
      - fail_script: script FAIL (blocks short promotion)
    """
    fail_identity = scenario == "fail_identity"
    fail_composite = scenario == "fail_composite"
    fail_script = scenario == "fail_script"
    roster = agents if agents is not None else default_roster(
        fail_identity=fail_identity,
        fail_composite=fail_composite,
        fail_script=fail_script,
    )

    state: Dict[str, Any] = {
        "brief": brief,
        "artifacts": {},
        "gate_results": [],
        "log": [f"[graph] start scenario={scenario}"],
        "current_tier": current_tier,
        "target_tier": brief.get("tier_goal", "clip_30s"),
    }

    for agent in roster:
        state = agent.run(state)

    registry = GateRegistry()
    for gr in state.get("gate_results", []):
        if isinstance(gr, GateResult):
            registry.record(gr)

    cur = _coerce_tier(state["current_tier"])
    goal = _coerce_tier(state["target_tier"])
    eval_target = _promotion_target(cur, goal)

    if eval_target is None or eval_target == cur:
        can = False
        missing = set()
        notes = "already at goal or no next tier"
        if eval_target == cur and cur == goal:
            # treat remaining at goal after successful craft as no-op promote
            can = True
            notes = "already at goal tier"
    else:
        missing = registry.missing_for_promotion(cur, eval_target)
        can = registry.can_promote(cur, eval_target)
        notes = "promotion allowed" if can else f"blocked; missing={sorted(missing)}"

    promote_result = GateResult(
        "gate.promote",
        can,
        notes=notes,
        owner="director",
    )
    registry.record(promote_result)

    state["promotion"] = {
        "from": cur.value,
        "to": eval_target.value if eval_target else None,
        "allowed": can,
        "missing_gates": sorted(missing),
    }
    state["gates_summary"] = summarize_gates(registry)
    state["log"].append(
        f"[graph] promote {cur.value}->{eval_target.value if eval_target else None} allowed={can}"
    )
    # If already at goal, report PROMOTED for pass scenarios; else use can
    if eval_target == cur and cur == goal:
        state["status"] = "PROMOTED"
    else:
        state["status"] = "PROMOTED" if can else "BLOCKED"
    state["gate_results"] = [
        {
            "gate_id": r.gate_id,
            "passed": r.passed,
            "score": r.score,
            "notes": r.notes,
            "owner": r.owner,
        }
        for r in registry.results.values()
    ]
    return state


def dumps_result(result: Dict[str, Any], indent: int = 2) -> str:
    return json.dumps(result, indent=indent, default=str)
```

---

## File: `akiraforge_orchestrator/cli.py`

```py
# SPDX-License-Identifier: MIT
"""CLI: python -m akiraforge_orchestrator.cli --scenario pass|fail_identity|fail_composite"""

from __future__ import annotations

import argparse
import json
import sys

from .graph import dumps_result, run_pipeline


def main(argv: list[str] | None = None) -> int:
    p = argparse.ArgumentParser(description="AkiraForge orchestrator dry-run")
    p.add_argument(
        "--scenario",
        choices=["pass", "fail_identity", "fail_composite", "fail_script"],
        default="pass",
        help="Mock agent failure scenario",
    )
    p.add_argument("--current-tier", default="none", help="Starting tier")
    p.add_argument("--tier-goal", default="clip_30s", help="Brief tier goal")
    p.add_argument("--title", default="Neon Rail Relay (original)", help="Brief title")
    p.add_argument("--json", action="store_true", help="Print full JSON result")
    args = p.parse_args(argv)

    brief = {
        "title": args.title,
        "character_id": "ren7",
        "tier_goal": args.tier_goal,
        "duration_s": 30,
        "original_ip_only": True,
    }
    result = run_pipeline(brief, current_tier=args.current_tier, scenario=args.scenario)

    if args.json:
        print(dumps_result(result))
    else:
        promo = result["promotion"]
        print("AkiraForge orchestrator dry-run")
        print(f"  scenario: {args.scenario}")
        print(f"  status:   {result['status']}")
        print(f"  promote:  {promo['from']} -> {promo['to']} allowed={promo['allowed']}")
        if promo["missing_gates"]:
            print(f"  missing:  {', '.join(promo['missing_gates'])}")
        gs = result["gates_summary"]
        print(f"  gates:    pass={gs['count_pass']} fail={gs['count_fail']}")
        for line in result["log"]:
            print(f"  log: {line}")
    return 0 if result["status"] in ("PROMOTED", "BLOCKED") else 1


if __name__ == "__main__":
    raise SystemExit(main())
```

---

## File: `akiraforge_orchestrator/__main__.py`

```py
# SPDX-License-Identifier: MIT
from .cli import main

raise SystemExit(main())
```

---

## File: `tests/test_gates.py`

```py
# SPDX-License-Identifier: MIT
"""Scripted demo of gate fail and gate pass paths (stdlib unittest)."""

from __future__ import annotations

import sys
import unittest
from pathlib import Path

# allow running without install: python tests/test_gates.py
ROOT = Path(__file__).resolve().parents[1]
if str(ROOT) not in sys.path:
    sys.path.insert(0, str(ROOT))

from akiraforge_orchestrator.graph import run_pipeline


class TestPromotionGates(unittest.TestCase):
    def test_pass_path_promotes_clip(self):
        r = run_pipeline(
            {"title": "Test", "tier_goal": "clip_30s", "character_id": "ren7"},
            current_tier="none",
            scenario="pass",
        )
        self.assertEqual(r["status"], "PROMOTED")
        self.assertTrue(r["promotion"]["allowed"])
        self.assertEqual(r["promotion"]["to"], "clip_30s")
        self.assertEqual(r["promotion"]["missing_gates"], [])

    def test_fail_identity_blocks(self):
        r = run_pipeline(
            {"title": "Test", "tier_goal": "clip_30s", "character_id": "ren7"},
            current_tier="none",
            scenario="fail_identity",
        )
        self.assertEqual(r["status"], "BLOCKED")
        self.assertFalse(r["promotion"]["allowed"])
        missing = set(r["promotion"]["missing_gates"])
        self.assertTrue("gate.identity" in missing or "gate.critic_identity" in missing)

    def test_fail_composite_blocks(self):
        r = run_pipeline(
            {"title": "Test", "tier_goal": "clip_30s", "character_id": "ren7"},
            current_tier="none",
            scenario="fail_composite",
        )
        self.assertEqual(r["status"], "BLOCKED")
        self.assertIn("gate.composite", r["promotion"]["missing_gates"])

    def test_short_promotion_needs_extra_gates(self):
        # pass scenario fills voice/audio/script; promote clip->short
        r = run_pipeline(
            {"title": "Test", "tier_goal": "short_5m", "character_id": "ren7"},
            current_tier="clip_30s",
            scenario="pass",
        )
        self.assertEqual(r["status"], "PROMOTED")
        self.assertEqual(r["promotion"]["to"], "short_5m")

    def test_fail_script_blocks_short_promotion(self):
        r = run_pipeline(
            {"title": "Test", "tier_goal": "short_5m", "character_id": "ren7"},
            current_tier="clip_30s",
            scenario="fail_script",
        )
        self.assertEqual(r["status"], "BLOCKED")
        self.assertIn("gate.script", r["promotion"]["missing_gates"])


if __name__ == "__main__":
    unittest.main()
```

---

## File: `examples/brief-clip.json`

```json
{
  "title": "Neon Rail Relay (original)",
  "character_id": "ren7",
  "tier_goal": "clip_30s",
  "duration_s": 30,
  "original_ip_only": true,
  "notes": "Gate-1 proof brief. Style approximation of 1988 theatrical hand-drawn anime technique only."
}
```
