# AkiraForge contribution: Define multi-level QC critic metrics and agents
Contributor: @SuddenlyJon (agent-assisted)
Task ID: `cmsi8jx89000ivj8wik0o5rm8`
Project: AkiraForge - Open 1988 Hand-Drawn Anime Feature Engine

## Acceptance checklist

- [x] critics/spec.md for each critic type
- [x] metrics.md with formulas or decision rules
- [x] sample-quality-report.md template
- [x] human re-score instructions (blind check possible)
- [x] license headers present

## Legal footer

```
License: MIT (code stubs); CC-BY-4.0 (rubrics/docs)
Project: AkiraForge
Rails: original works only; style approximation; no unlicensed copyrighted training data; no API keys in repo
```

---

## File: `README.md`

# Leaf: Define multi-level QC critic metrics and agents

**Status:** Ready for peer review  
**License:** MIT (code stubs headers); CC-BY-4.0 (rubrics/docs)  
**Task ID:** `cmsi8jx89000ivj8wik0o5rm8`  
**Project:** AkiraForge - Open 1988 Hand-Drawn Anime Feature Engine

## Files

| File | Role |
|------|------|
| `critics/spec.md` | Spec for each critic type |
| `metrics.md` | Formulas and decision rules |
| `sample-quality-report.md` | Report template + filled sample |
| `human-rescore.md` | Blind human re-score instructions |
| `SUBMIT-BODY.md` | Single-file GrokForge API submission |

## Acceptance checklist

- [x] critics/spec.md for each critic type
- [x] metrics.md with formulas or decision rules
- [x] sample-quality-report.md template
- [x] human re-score instructions (blind check possible)
- [x] license headers present

## Footer

```
License: MIT (code stubs); CC-BY-4.0 (rubrics/docs)
Project: AkiraForge
Rails: original works only; style approximation; no unlicensed copyrighted training data; no API keys in repo
```

---

## File: `critics/spec.md`

# Multi-level QC / Critic agent specifications

**License:** CC-BY-4.0 (this spec); MIT for future code stubs implementing it  
**Project:** AkiraForge  
**Version:** 0.1.0  
**Date:** 2026-08-08

Critics are **fail-closed** agents. They do not render; they score and block promotion between gates: clip -> short -> OVA -> feature-path planning.

---

## Critic 0: LegalRailsCritic (hard gate)

**Purpose:** Block banned dual-use, unlicensed training data claims, title clones, secret leakage.

**Inputs**

- Contribution manifest, dataset card, file list, secret-scan result

**Outputs**

- `PASS` | `HARD_FAIL`
- Notes list

**Decision rule**

- Any FM-13 / FM-14 style violation -> HARD_FAIL
- Missing license headers on software/docs -> FAIL (repairable)

**Human override:** Founder only, with public note. Default: no override for copyrighted training.

---

## Critic 1: IdentityDriftCritic

**Purpose:** Detect character identity drift vs model sheets.

**Inputs**

- `character_id`, `costume_id`, reference pack, candidate frames/clip

**Checks**

- Embedding similarity (ID-EMB)
- Landmark ratio error (ID-LM)
- Optional silhouette / costume color

**Outputs**

```json
{
  "critic": "IdentityDriftCritic",
  "decision": "PASS",
  "scores": {"ID-EMB": 0.88, "ID-LM": 0.04},
  "failed_checks": [],
  "suggested_action": "none"
}
```

**Suggested actions on FAIL:** increase reference weight; regen; human redraw keys.

---

## Critic 2: StyleFidelityCritic

**Purpose:** Score against 1988 theatrical style bible (technique approximation, not IP clone).

**Inputs**

- Style bible MD/JSON axes, candidate frames

**Checks (v0)**

- Line weight stability (not hairline vector spam)
- Palette restraint vs bible color script
- Motion cadence: not over-smoothed VFI soup
- Camera grammar: multiplane vs random 3D orbit spam
- Grain/compositing notes if present

**Outputs**

- Per-axis 0-5 rubric scores + overall PASS if mean >= threshold and no axis < floor

**Suggested actions:** lineart control; reduce face-restore; enforce ones/twos timing.

---

## Critic 3: ContinuityCritic (costume / prop / location)

**Purpose:** Catch wardrobe, props, and sets that change illegally between shots.

**Inputs**

- Story Bible entities: costumes, props, locations
- Shot graph with declared IDs
- Candidate frames

**Checks**

- Costume palette delta vs declared `costume_id`
- Prop presence/absence vs shot list
- Location architecture cues vs background pack

**Outputs**

- List of continuity breaks with severity
- PASS only if zero high-severity breaks

---

## Critic 4: TemporalCoherenceCritic

**Purpose:** Intra-clip morphs and illegal teleport motion.

**Inputs**

- Frame sequence or video

**Checks**

- Optical-flow magnitude spikes without cut
- Face-crop temporal cosine drops
- Flicker energy

**Outputs**

- PASS/FAIL + timestamps of worst segments

---

## Critic 5: AudioVisualSyncCritic

**Purpose:** Lip-sync and dialogue intelligibility heuristics for Gate-2+.

**Inputs**

- Picture lock candidate, dialogue wav, transcript, mouth chart (optional)

**Checks**

- Forced-aligner onset vs mouth open events (tolerance ms)
- Music ducking under dialogue (simple RMS rule)
- No copyrighted audio stems declared without license field

**Outputs**

- PASS/FAIL + worst lines

---

## Critic 6: NarrativeCoherenceCritic (LLM-judge assist)

**Purpose:** Soft check that shot order and action match script/board intent.

**Inputs**

- Script excerpt, board notes, shot list, captions or frames descriptions

**Checks**

- Required beats present
- Character goals not contradicted
- No missing establishing info for new locations

**Outputs**

- Advisory score 0-5; hard FAIL only if critical beat missing (configurable)

**Note:** LLM-judge is assistive; human director can override with note.

---

## Critic levels and routing

| Level | Critics required | Gate |
|-------|------------------|------|
| L0 | LegalRails | Always |
| L1 | Identity + Style + Temporal | Gate-1 clip |
| L2 | L1 + Continuity + AVSync + Narrative advisory | Gate-2 short |
| L3 | L2 + stricter thresholds + human panel | Gate-3 OVA path |

```
Render candidates
      |
      v
 LegalRailsCritic --HARD_FAIL--> reject
      |
      v
 Identity + Style + Temporal --FAIL--> retry budget
      |
      v
 Continuity + AVSync (if audio) --FAIL--> fix assets
      |
      v
 Narrative advisory --> human director
      |
      v
  PROMOTE to next bin
```

## Agent interface (comms sketch)

Aligns with agent roster leaf:

```json
{
  "type": "critic_request",
  "critic_id": "IdentityDriftCritic",
  "artifact_uri": "assets/shots/s01_003/",
  "story_bible_ref": "bible@0.1.0",
  "gate": "gate1"
}
```

```json
{
  "type": "critic_result",
  "critic_id": "IdentityDriftCritic",
  "decision": "FAIL",
  "scores": {},
  "report_uri": "reports/s01_003_identity.md"
}
```

## Stub implementation note (MIT)

Future code may live under `critics/` as pure functions:

- `score_identity(frames, pack) -> CriticResult`
- `score_style(frames, bible) -> CriticResult`

No secrets; offline fixtures only.

## Footer

```
License: CC-BY-4.0 (spec); MIT (future code stubs)
Project: AkiraForge
Rails: original works only; style approximation; no unlicensed copyrighted training data; no API keys in repo
```

---

## File: `metrics.md`

# QC metrics: formulas and decision rules

**License:** CC-BY-4.0  
**Project:** AkiraForge  
**Version:** 0.1.0  
**Date:** 2026-08-08  
**Note:** v0 provisional thresholds. Recalibrate on original synthetic fixtures.

---

## Shared conventions

- Scores normalized where possible to `[0, 1]` with higher = better, unless noted.
- `PASS` requires all mandatory metrics for that critic.
- Record tool versions in every report.

---

## M1 Identity embedding similarity (ID-EMB)

```
score = mean_i cosine(embed(sheet_face), embed(frame_i_face))
```

| Gate | PASS |
|------|------|
| Gate-1 | score >= 0.78 |
| Gate-2 | score >= 0.82 |
| Gate-3 | score >= 0.85 |

## M2 Landmark geometry error (ID-LM)

```
ratios = [iod/face_w, nose_h/face_h, jaw_w/face_w]
err = mean(abs(r_shot - r_sheet)/r_sheet)
score = max(0, 1 - err/0.2)
```

PASS Gate-1 if `err <= 0.08`; Gate-2+ if `err <= 0.06`.

## M3 Style axis rubric (ST-AX)

Human or classifier scores each axis 0-5:

1. Line quality  
2. Palette restraint  
3. Limited-animation timing  
4. Camera / multiplane grammar  
5. FX vocabulary fit  
6. Composite / grain discipline  

```
mean_axis = average(axes)
```

PASS if `mean_axis >= 3.5` and no axis `< 2`.

## M4 Optical-flow spike rate (TMP-OF)

```
spikes = count(flow_mag[t] > k * median(flow_mag) for non-cut t)
rate = spikes / num_frames
```

PASS if `rate <= 0.05` (Gate-1), `<= 0.03` (Gate-2+).  
`k` default 4.0.

## M5 Face temporal drop (TMP-FACE)

```
drop = max over t of (cos_t - cos_{t+1}) on face crops within clip
```

FAIL if any `drop > 0.15` without cut flag.

## M6 Costume color delta (CNT-COL)

CIEDE2000 mean delta E for primary garments vs costume sheet.

PASS if `deltaE <= 12` unless shot tagged `costume_change_beat`.

## M7 Prop continuity (CNT-PROP)

```
breaks = missing_required_props + unexpected_hero_props
```

PASS if `breaks == 0` for high-severity props; warnings allowed for background extras.

## M8 Lip-sync offset (AV-LIP)

```
offset_ms = mean(abs(mouth_open_onset - phoneme_onset))
```

PASS Gate-2 if `offset_ms <= 80` on sampled lines; FAIL if `> 150` on any hero line.

## M9 Narrative beat coverage (NAR-BEAT)

```
coverage = matched_required_beats / required_beats
```

Advisory PASS if `coverage >= 0.9`; hard FAIL if critical beat missing when `strict=true`.

## M10 Legal / license completeness (LEG-OK)

Binary checklist:

- [ ] Original IP declaration  
- [ ] License headers present  
- [ ] Dataset card OK (no unlicensed copyrighted frames)  
- [ ] Secret scan clean  
- [ ] No dual-use ban triggers  

PASS only if all checked.

---

## Aggregate gate policies

### Gate-1 (20-60s clip)

Mandatory: M10, M1, M2, M3, M4, M5  
Optional: M6  

### Gate-2 (3-5 min short)

Mandatory: Gate-1 set + M6 + M7 + M8 + human spot check  
Advisory: M9  

### Gate-3 (OVA path)

Mandatory: Gate-2 with tighter thresholds (use Gate-3 column where listed) + multi-human panel on sample reels  

### Gate-4 (feature path)

Not a render gate; requires cost model + Gate-3 evidence. Critics still apply per-shot if experiments continue.

---

## Scoring report JSON schema (informal)

```json
{
  "gate": "gate1",
  "artifact_id": "clip_demo_001",
  "metrics": {
    "M1_ID_EMB": 0.86,
    "M2_ID_LM_err": 0.05,
    "M3_ST_AX_mean": 3.8,
    "M4_TMP_OF_rate": 0.02,
    "M5_TMP_FACE": "pass",
    "M10_LEG_OK": true
  },
  "decision": "PASS",
  "critics": ["LegalRailsCritic", "IdentityDriftCritic", "StyleFidelityCritic", "TemporalCoherenceCritic"]
}
```

## Footer

```
License: CC-BY-4.0
Project: AkiraForge
Rails: original works only; style approximation; no unlicensed copyrighted training data; no API keys in repo
```

---

## File: `sample-quality-report.md`

# Sample quality report template (+ filled example)

**License:** CC-BY-4.0  
**Project:** AkiraForge  
**Version:** 0.1.0

---

## Template (copy per artifact)

```markdown
# Quality report: <artifact_id>

- Project: AkiraForge
- Gate: gate1 | gate2 | gate3
- Artifact URI: ...
- Story Bible ref: ...
- Character IDs: ...
- Costume IDs: ...
- Generated at: ISO-8601
- Tools/versions: ...

## LegalRailsCritic
- Decision: PASS | HARD_FAIL
- Checklist: original IP / licenses / dataset / secrets / dual-use
- Notes:

## IdentityDriftCritic
- Decision:
- ID-EMB:
- ID-LM err:
- Notes:

## StyleFidelityCritic
- Decision:
- Axis scores (0-5): line / palette / timing / camera / fx / composite
- Mean:
- Notes:

## TemporalCoherenceCritic
- Decision:
- OF spike rate:
- Face temporal drops:
- Worst timestamps:

## ContinuityCritic (if applicable)
- Decision:
- Costume deltaE:
- Prop breaks:
- Location notes:

## AudioVisualSyncCritic (if applicable)
- Decision:
- Lip offset ms:
- Audio license OK:

## NarrativeCoherenceCritic (advisory)
- Score 0-5:
- Missing beats:

## Aggregate decision
- PROMOTE | RETRY | REJECT
- Retry budget remaining:
- Human reviewer:
- Signature / date:
```

---

## Filled sample (synthetic original demo)

# Quality report: clip_demo_neoalley_001

- Project: AkiraForge
- Gate: gate1
- Artifact URI: `assets/demos/clip_demo_neoalley_001/`
- Story Bible ref: `storybible@0.1.0-example`
- Character IDs: `char_rin_okuda` (original)
- Costume IDs: `rin_courier_jacket`
- Generated at: 2026-08-08T12:00:00Z
- Tools/versions: ComfyUI pinned SHA example; IP-Adapter class; open embed model recorded in metrics JSON

## LegalRailsCritic
- Decision: **PASS**
- Checklist: original IP yes / CC-BY docs + MIT scripts / synthetic sheets only / secret scan clean / no dual-use
- Notes: Character is original courier protagonist; no title clone.

## IdentityDriftCritic
- Decision: **PASS**
- ID-EMB: 0.87
- ID-LM err: 0.045
- Notes: Profile shots slightly weaker; still above Gate-1 floor.

## StyleFidelityCritic
- Decision: **PASS**
- Axis scores: line 4 / palette 4 / timing 3 / camera 4 / fx 3 / composite 4
- Mean: 3.67
- Notes: Timing slightly smooth; recommend less VFI on next take.

## TemporalCoherenceCritic
- Decision: **PASS**
- OF spike rate: 0.02
- Face temporal drops: none > 0.15
- Worst timestamps: t=4.2s minor background flicker (warn)

## ContinuityCritic
- Decision: N/A (single continuous clip, one costume)

## AudioVisualSyncCritic
- Decision: N/A (silent Gate-1 picture proof)

## NarrativeCoherenceCritic
- Score: 4
- Missing beats: none for "cross neon alley, look back" micro-beat

## Aggregate decision
- **PROMOTE** to Gate-1 accepted bin
- Retry budget remaining: 2
- Human reviewer: @SuddenlyJon (sample)
- Signature / date: 2026-08-08

---

## Footer

```
License: CC-BY-4.0
Project: AkiraForge
Rails: original works only; style approximation; no unlicensed copyrighted training data; no API keys in repo
```

---

## File: `human-rescore.md`

# Human re-score instructions (blind-check capable)

**License:** CC-BY-4.0  
**Project:** AkiraForge  
**Version:** 0.1.0  
**Date:** 2026-08-08

Humans remain the final authority on audience trust. Automated critics reduce load; they do not replace spot checks.

---

## Goals

1. Re-score identity and style without seeing auto scores first (blind).
2. Catch failures auto metrics miss (acting, prop logic, "uncanny anime").
3. Produce comparable rubrics across reviewers.

---

## Materials prep (operator)

1. Export **reference pack** images for characters under review.
2. Export **randomized frame stills** or silent clips (strip filenames that leak scores).
3. Include **distractor** stills from other original cast members (not copyrighted IP).
4. Prepare form with only: stimulus id, questions, free notes.
5. Hide auto report until after human submit.

## Blind protocol

1. Reviewer does not see M1-M9 numbers beforehand.
2. Reviewer may see style bible excerpts (technique vocabulary), not auto style score.
3. Timebox: ~60-90s per clip stimulus; ~20s per still pair.
4. After human submit, compare to auto decision; log disagreements.

---

## Task A: Same-person check (identity)

For each pair (sheet vs frame):

- Q1: Same character? `yes / no / unsure`
- Q2: Confidence 1-5
- Q3: If no/unsure, what drifted? `face / hair / body / costume / other`

**PASS guidance (Gate-1):** >= 90% yes on true pairs; <= 10% yes on distractors.

## Task B: Style bible fit

Rate 0-5 using style bible axes (line, palette, timing, camera, fx, composite).

- Optional comment: "modern filter", "too smooth", "good multiplane", etc.

**PASS guidance:** mean >= 3.5; no axis < 2.

## Task C: Continuity spot (Gate-2+)

Show 3-6 ordered stills from different shots.

- Mark any illegal costume/prop/location change.
- Severity: low / high

**PASS guidance:** zero high severity.

## Task D: AV sync (if audio)

Watch with sound once.

- Any line badly un-synced? list timestamps
- Dialogue intelligible? yes/no

---

## Disagreement resolution

| Case | Action |
|------|--------|
| Human FAIL, auto PASS | Block promotion; fix auto threshold or model |
| Human PASS, auto FAIL | Allow human override with note; file metric bug |
| Two humans disagree | Third reviewer or director break; record |

## Ethics / legal for reviewers

- Do not use copyrighted comparison screenshots from commercial anime titles.
- Do not request or paste API keys.
- Original / synthetic stimuli only.

## Minimal re-score sheet (CSV columns)

```text
reviewer_id,stimulus_id,task,same_person,confidence,style_mean,high_severity_breaks,notes,blind,timestamp
```

## Footer

```
License: CC-BY-4.0
Project: AkiraForge
Rails: original works only; style approximation; no unlicensed copyrighted training data; no API keys in repo
```
