# AkiraForge contribution: Studio agent roster and comms schemas

Contributor: @SuddenlyJon (agent-assisted)
Task ID: `cmsi8jx89000fvj8weq0ycol1`
Project: AkiraForge - Open 1988 Hand-Drawn Anime Feature Engine

## Acceptance checklist

- [x] agents/spec.md covering all listed roles
- [x] agents/schemas/*.json for message types
- [x] agents/examples/ at least 3 full prompt packages
- [x] quality-gate hooks documented per role
- [x] MIT/CC-BY headers; no API keys

## Legal footer

```
Project: AkiraForge
Rails: original works only; style approximation; no unlicensed copyrighted training data; no API keys in repo
```

---

## File: `README.md`

# Leaf: Specify studio agent roster and comms schemas

**Status:** Ready for peer review  
**License:** CC-BY-4.0 (prose) / MIT (schemas)  
**Task ID:** `cmsi8jx89000fvj8weq0ycol1`

## Files

| Path | Role |
|------|------|
| `agents/spec.md` | Full roster, I/O, routing |
| `agents/quality-gates.md` | Gate hooks per role + tier promotion |
| `agents/schemas/*.json` | Message envelope, gate result, identity lock, director decision |
| `agents/examples/` | 3 full prompt packages |
| `SUBMIT-BODY.md` | API submission body |

## Acceptance checklist

- [x] agents/spec.md covering all listed roles
- [x] agents/schemas/*.json for message types
- [x] agents/examples/ at least 3 full prompt packages
- [x] quality-gate hooks documented per role
- [x] MIT/CC-BY headers; no API keys

---

## File: `agents/spec.md`

# AkiraForge studio agent roster specification

**License:** CC-BY-4.0 (prose)  
**Schemas:** MIT (see `schemas/`)  
**Version:** 0.1.0  
**Project:** AkiraForge  
**Rails:** Original works only; style approximation; no unlicensed copyrighted training data; no API keys in messages; dual-use refuse list from LEGAL-RAILS.

## Design goals

1. Mirror a theatrical anime production hierarchy so humans understand ownership.
2. Every agent has typed inputs/outputs (JSON message schemas).
3. Quality gates are explicit hooks, not vibes.
4. Agents never request or store SuperGrok/xAI user keys on the board.
5. Progressive promotion only: clip -> short -> OVA path -> feature path.

## Roster overview

| ID | Role | Primary output | Gate hook |
|----|------|----------------|-----------|
| `director` | Director / Showrunner | Locked brief, shot priorities, promote/hold decisions | `gate.promote` |
| `scriptwriter` | Scriptwriter | Screenplay + dialogue timing notes | `gate.script` |
| `character_design` | Character Designer | Model sheets, palette locks, turnarounds | `gate.model_sheet` |
| `consistency_lock` | Character Consistency Lock | Identity tokens, ref packs, anti-drift config | `gate.identity` |
| `storyboard` | Storyboard + Layout | Boards, camera notes, multiplane stacks | `gate.boards` |
| `key_animator` | Key Animator | Key poses / extreme drawings | `gate.keys` |
| `inbetweener` | Inbetweener | Inbetweens, timing charts | `gate.inbetweens` |
| `background` | Background Artist | BG plates, multiplane layers | `gate.bg` |
| `fx` | Effects Artist | FX cels, speed lines, energy, debris | `gate.fx` |
| `colorist` | Colorist | Color scripts, painted cels | `gate.color` |
| `compositor` | Compositor | Composite shots, grain, finish | `gate.composite` |
| `sound` | Sound Director / Composer | Temp + final score stems, SFX plan | `gate.audio` |
| `voice` | Voice Director | Casting plan, takes, lip-sync map | `gate.voice` |
| `critic_identity` | QA Critic - Identity | Drift scores, fail reasons | `gate.critic_identity` |
| `critic_style` | QA Critic - Style | Style bible fidelity scores | `gate.critic_style` |
| `critic_continuity` | QA Critic - Continuity | Costume/prop/location mismatches | `gate.critic_continuity` |
| `critic_av_sync` | QA Critic - A/V Sync | Lip and hit-point sync notes | `gate.critic_av` |

---

## Shared conventions

### Message envelope

All inter-agent messages use `schemas/message-envelope.schema.json`:

- `message_id`, `trace_id`, `from_agent`, `to_agent` (or `broadcast`)
- `project_id`, `story_bible_ref`, `shot_ids[]`
- `payload_type`, `payload`
- `created_at`, `priority` (`low|normal|high|blocker`)
- `legal.rails_ack` must be true

### System prompt skeleton (all agents)

```
You are {ROLE} on AkiraForge, an open multi-agent theatrical anime studio engine.

HARD RAILS
- Original characters/locations/stories only. No named commercial IP clones.
- Style approximation of late-1980s Japanese theatrical hand-drawn technique only.
- Never train on or request copyrighted anime frames without license.
- Never ask for or store user API keys (xAI, SuperGrok, etc.).
- Refuse dual-use: malware, unauthorized access, civilian surveillance tooling.

INPUTS
{typed inputs}

OUTPUTS
{typed outputs matching schema}

QUALITY GATE
Before marking done, satisfy: {gate criteria}
If fail: emit GateFail with concrete fix notes, do not promote.

STORY BIBLE
Read and write only fields you own. Never silently overwrite another agent's locks.
```

### Quality gate result object

See `schemas/gate-result.schema.json`: `status` = `PASS|FAIL|HOLD`, `scores`, `notes`, `blockers[]`, `artifacts[]`.

---

## Role specifications

### 1. Director / Showrunner (`director`)

**Mission:** Hold narrative intent, budget of attention, and promotion decisions across gates.

| | |
|--|--|
| **Inputs** | Creative brief; Story Bible summary; critic reports; cost model snapshot |
| **Outputs** | `DirectorDecision` (priorities, cut list, promote/hold); updated shot order |
| **Owns** | `story_bible.direction`, promotion flags |
| **Does not own** | Frame pixels, identity tokens |
| **Gate hook** | `gate.promote` - no runtime tier promotion without required critic PASSes |
| **Failure modes** | Scope creep to "feature ready" without short gates; ignoring LEGAL-RAILS |

### 2. Scriptwriter (`scriptwriter`)

**Mission:** Produce production-ready screenplay with pre-score dialogue awareness.

| | |
|--|--|
| **Inputs** | Brief, character bible, duration target |
| **Outputs** | Screenplay MD/JSON; dialogue cues; estimated runtime |
| **Owns** | `story_bible.script` |
| **Gate hook** | `gate.script` - structure complete; no IP-clone names; dialogue timed |
| **Notes** | Pre-scored dialogue workflow: mark breaths, hits, silence for anim |

### 3. Character Designer (`character_design`)

**Mission:** Original designs + multi-view model sheets + palette locks.

| | |
|--|--|
| **Inputs** | Script cast list; style bible tokens |
| **Outputs** | Model sheet set paths; design notes; palette JSON |
| **Owns** | `story_bible.characters[].design` |
| **Gate hook** | `gate.model_sheet` - front/side/back (+ expression sheet) present |
| **Rails** | Original characters only |

### 4. Consistency Lock (`consistency_lock`)

**Mission:** Keep identity stable across shots and sequences.

| | |
|--|--|
| **Inputs** | Model sheets; prior shot refs; critic_identity fails |
| **Outputs** | Identity token pack; ref image manifest; adapter/LoRA instructions (open tools) |
| **Owns** | `story_bible.characters[].identity_lock` |
| **Gate hook** | `gate.identity` - lock pack complete before mass key animation |
| **Couples with** | `critic_identity` |

### 5. Storyboard + Layout (`storyboard`)

**Mission:** Boards, camera, multiplane layout.

| | |
|--|--|
| **Inputs** | Script; style bible camera section; location designs |
| **Outputs** | Board frames; camera moves; multiplane layer list |
| **Owns** | `story_bible.shots[]` layout fields |
| **Gate hook** | `gate.boards` - every shot has board + camera note |

### 6. Key Animator (`key_animator`)

**Mission:** Extreme poses and keys with line quality per style bible.

| | |
|--|--|
| **Inputs** | Boards; model sheets; identity lock |
| **Outputs** | Key drawings / key frames; timing extremes |
| **Owns** | `shots[].keys` |
| **Gate hook** | `gate.keys` - keys cover action; identity lock applied |

### 7. Inbetweener (`inbetweener`)

**Mission:** Fill motion on ones/twos per chart; preserve volume.

| | |
|--|--|
| **Inputs** | Keys; timing chart |
| **Outputs** | Inbetween frames; exposure sheet notes |
| **Owns** | `shots[].inbetweens` |
| **Gate hook** | `gate.inbetweens` - no volume break; timing matches chart |

### 8. Background (`background`)

**Mission:** Mechanical density and multiplane BG plates (era technique, original designs).

| | |
|--|--|
| **Inputs** | Layout; location bible; color script |
| **Outputs** | BG layers; parallax metadata |
| **Owns** | `story_bible.locations[].plates` |
| **Gate hook** | `gate.bg` - plates match layout camera |

### 9. FX (`fx`)

**Mission:** Speed lines, explosions, energy, debris - theatrical FX language.

| | |
|--|--|
| **Inputs** | Keys/comp preview; FX brief |
| **Outputs** | FX layers; notes for compositor |
| **Owns** | `shots[].fx` |
| **Gate hook** | `gate.fx` - FX do not obscure identity-critical face locks unless scripted |

### 10. Colorist (`colorist`)

**Mission:** Color scripts and cel color under limited-palette discipline.

| | |
|--|--|
| **Inputs** | Line art; color script; style bible palettes |
| **Outputs** | Colored cels; palette adherence report |
| **Owns** | `shots[].color` |
| **Gate hook** | `gate.color` - palette lock distance under threshold |

### 11. Compositor (`compositor`)

**Mission:** Layer cels, multiplane, grain, 1980s color timing finish.

| | |
|--|--|
| **Inputs** | Color, BG, FX, camera |
| **Outputs** | Shot composites; grain/finish settings |
| **Owns** | `shots[].composite` |
| **Gate hook** | `gate.composite` - deliverable playable; metadata complete |

### 12. Sound / Composer (`sound`)

**Mission:** Score in dense choral/percussive spirit without cloning protected recordings; SFX plan.

| | |
|--|--|
| **Inputs** | Locked picture timing; emotional map |
| **Outputs** | Temp/final stems; cue sheet |
| **Owns** | `story_bible.audio.score` |
| **Gate hook** | `gate.audio` - cues aligned; license of samples documented |

### 13. Voice Director (`voice`)

**Mission:** JP+EN options, takes, lip-sync map.

| | |
|--|--|
| **Inputs** | Script; timing; language plan |
| **Outputs** | Take sheet; lip-sync keys; subtitle cues |
| **Owns** | `story_bible.audio.voice` |
| **Gate hook** | `gate.voice` - takes present for all dialogue lines |

### 14-17. Critics

| Agent | Measures | Fail if |
|-------|----------|---------|
| `critic_identity` | Face/body/palette identity vs lock | Drift score > threshold |
| `critic_style` | Style bible section scores | Any critical section FAIL |
| `critic_continuity` | Costume/prop/location continuity | Unresolved mismatch |
| `critic_av_sync` | Lip and hit sync | Drift > N frames |

Critics emit `GateResult` only. They do not overwrite creative locks; they open fix tasks.

---

## Inter-agent routing (happy path)

```
brief -> director
director -> scriptwriter -> gate.script
scriptwriter -> character_design -> gate.model_sheet
character_design -> consistency_lock -> gate.identity
director + script -> storyboard -> gate.boards
boards + locks -> key_animator -> gate.keys
keys -> inbetweener -> gate.inbetweens
layout -> background -> gate.bg
keys/comp -> fx -> gate.fx
line -> colorist -> gate.color
layers -> compositor -> gate.composite
picture -> sound + voice -> gate.audio + gate.voice
composites + audio -> critics (parallel) -> director gate.promote
```

On any FAIL: director schedules owning agent fix; claim may re-open leaf work.

## Comms matrix (minimum)

| From | To | Payload types |
|------|-----|---------------|
| director | * | `DirectorDecision`, `PromoteRequest` |
| * | director | `GateResult`, `StatusUpdate` |
| consistency_lock | key_animator, inbetweener, colorist | `IdentityLockPack` |
| storyboard | key_animator, background, fx | `ShotBoard` |
| critics | director + owning agent | `GateResult` |
| compositor | sound, voice | `PictureLock` |

## Token / context budget guidance

| Agent class | Typical context focus |
|-------------|----------------------|
| Director | Brief + gate summary, not all frames |
| Craft agents | Shot-local + identity lock + style tokens |
| Critics | Rubric + samples + lock refs |

## Footer

```
License: CC-BY-4.0 (this spec)
Schemas: MIT
Project: AkiraForge
Rails: original works only; style approximation; no unlicensed training data; no API keys
```

---

## File: `agents/quality-gates.md`

# Quality gate hooks by role

**License:** CC-BY-4.0  
**Project:** AkiraForge

| Gate ID | Owner agent | PASS requires | Typical FAIL fix |
|---------|-------------|---------------|------------------|
| `gate.script` | scriptwriter | Complete structure; timed dialogue; no IP-clone names | Rewrite names/structure |
| `gate.model_sheet` | character_design | Front/side/back + expression sheet; palette | Add missing views |
| `gate.identity` | consistency_lock | IdentityLockPack valid; >=3 refs; do_not list | Expand ref pack |
| `gate.boards` | storyboard | Every shot boarded + camera note | Board missing shots |
| `gate.keys` | key_animator | Keys cover action; lock applied | Redraw extremes |
| `gate.inbetweens` | inbetweener | Timing chart honored; volume stable | Fix charts/volume |
| `gate.bg` | background | Plates match layout camera / multiplane | Relayout plates |
| `gate.fx` | fx | FX layers present where briefed; faces not nuked | Re-layer FX |
| `gate.color` | colorist | Palette lock within threshold | Recolor |
| `gate.composite` | compositor | Playable composite + finish metadata | Re-comp grain/timing |
| `gate.audio` | sound | Cue sheet + stems aligned to picture | Re-spot cues |
| `gate.voice` | voice | Takes for all lines; lip map | Record/align |
| `gate.critic_identity` | critic_identity | identity_score >= 0.85 default | Craft + lock fix |
| `gate.critic_style` | critic_style | Critical style bible sections PASS | Match style bible |
| `gate.critic_continuity` | critic_continuity | No unresolved mismatches | Fix props/costume |
| `gate.critic_av` | critic_av_sync | Sync within N frames | Re-time audio/mouth |
| `gate.promote` | director | Tier rules + required critic PASSes | Block promotion |

## Tier promotion rules (director)

| From | To | Minimum required PASSes |
|------|----|-------------------------|
| none | clip_30s | identity, style (sample), composite |
| clip_30s | short_5m | above + script + boards + voice/audio if dialogue |
| short_5m | ova_20m | full craft chain sample + continuity + cost model mid check |
| ova_20m | feature | all critics on pilot reel + cost model feature path documented |

Promotion is never automatic from a single agent PASS.

---

## File: `agents/schemas/message-envelope.schema.json`

```json
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "$id": "https://grokforge.app/schemas/akiraforge/message-envelope.schema.json",
  "title": "AkiraForgeAgentMessageEnvelope",
  "description": "MIT-licensed inter-agent message envelope for AkiraForge studio agents.",
  "type": "object",
  "additionalProperties": false,
  "required": [
    "message_id",
    "trace_id",
    "from_agent",
    "payload_type",
    "payload",
    "created_at",
    "legal"
  ],
  "properties": {
    "message_id": { "type": "string", "minLength": 1 },
    "trace_id": { "type": "string", "minLength": 1 },
    "from_agent": { "type": "string", "minLength": 1 },
    "to_agent": {
      "type": ["string", "null"],
      "description": "Null or omit for broadcast when broadcast=true"
    },
    "broadcast": { "type": "boolean", "default": false },
    "project_id": { "type": "string" },
    "story_bible_ref": {
      "type": "string",
      "description": "URI or path to Story Bible instance"
    },
    "shot_ids": {
      "type": "array",
      "items": { "type": "string" },
      "default": []
    },
    "priority": {
      "type": "string",
      "enum": ["low", "normal", "high", "blocker"],
      "default": "normal"
    },
    "payload_type": {
      "type": "string",
      "enum": [
        "DirectorDecision",
        "PromoteRequest",
        "StatusUpdate",
        "ScreenplayPackage",
        "ModelSheetPackage",
        "IdentityLockPack",
        "ShotBoard",
        "KeyPackage",
        "InbetweenPackage",
        "BackgroundPackage",
        "FxPackage",
        "ColorPackage",
        "CompositePackage",
        "AudioPackage",
        "VoicePackage",
        "GateResult",
        "PictureLock"
      ]
    },
    "payload": { "type": "object" },
    "created_at": { "type": "string", "format": "date-time" },
    "legal": {
      "type": "object",
      "additionalProperties": false,
      "required": ["rails_ack", "original_works_only"],
      "properties": {
        "rails_ack": {
          "type": "boolean",
          "const": true,
          "description": "Agent acknowledges LEGAL-RAILS"
        },
        "original_works_only": { "type": "boolean", "const": true },
        "no_api_keys": { "type": "boolean", "const": true, "default": true }
      }
    }
  }
}
```

---

## File: `agents/schemas/gate-result.schema.json`

```json
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "$id": "https://grokforge.app/schemas/akiraforge/gate-result.schema.json",
  "title": "AkiraForgeGateResult",
  "description": "MIT-licensed quality gate result for AkiraForge agents.",
  "type": "object",
  "additionalProperties": false,
  "required": ["gate_id", "agent_id", "status", "notes", "created_at"],
  "properties": {
    "gate_id": {
      "type": "string",
      "enum": [
        "gate.script",
        "gate.model_sheet",
        "gate.identity",
        "gate.boards",
        "gate.keys",
        "gate.inbetweens",
        "gate.bg",
        "gate.fx",
        "gate.color",
        "gate.composite",
        "gate.audio",
        "gate.voice",
        "gate.critic_identity",
        "gate.critic_style",
        "gate.critic_continuity",
        "gate.critic_av",
        "gate.promote"
      ]
    },
    "agent_id": { "type": "string" },
    "status": { "type": "string", "enum": ["PASS", "FAIL", "HOLD"] },
    "scores": {
      "type": "object",
      "additionalProperties": {
        "type": "number",
        "minimum": 0,
        "maximum": 1
      },
      "description": "Optional normalized scores 0..1"
    },
    "notes": { "type": "string" },
    "blockers": {
      "type": "array",
      "items": {
        "type": "object",
        "required": ["code", "message"],
        "properties": {
          "code": { "type": "string" },
          "message": { "type": "string" },
          "shot_id": { "type": "string" },
          "fix_agent": { "type": "string" }
        }
      },
      "default": []
    },
    "artifacts": {
      "type": "array",
      "items": {
        "type": "object",
        "required": ["path", "role"],
        "properties": {
          "path": { "type": "string" },
          "role": { "type": "string" },
          "sha256": { "type": "string" }
        }
      },
      "default": []
    },
    "created_at": { "type": "string", "format": "date-time" }
  }
}
```

---

## File: `agents/schemas/identity-lock-pack.schema.json`

```json
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "$id": "https://grokforge.app/schemas/akiraforge/identity-lock-pack.schema.json",
  "title": "AkiraForgeIdentityLockPack",
  "description": "MIT-licensed identity lock package exchanged between consistency_lock and craft agents.",
  "type": "object",
  "additionalProperties": false,
  "required": ["character_id", "version", "reference_images", "palette_lock", "do_not"],
  "properties": {
    "character_id": { "type": "string" },
    "version": { "type": "string", "pattern": "^[0-9]+\\.[0-9]+\\.[0-9]+$" },
    "reference_images": {
      "type": "array",
      "minItems": 3,
      "items": {
        "type": "object",
        "required": ["view", "path"],
        "properties": {
          "view": {
            "type": "string",
            "enum": ["front", "side", "back", "three_quarter", "expression", "detail"]
          },
          "path": { "type": "string" },
          "weight": { "type": "number", "minimum": 0, "maximum": 1, "default": 1 }
        }
      }
    },
    "palette_lock": {
      "type": "object",
      "required": ["colors"],
      "properties": {
        "colors": {
          "type": "array",
          "items": {
            "type": "object",
            "required": ["name", "hex"],
            "properties": {
              "name": { "type": "string" },
              "hex": { "type": "string", "pattern": "^#[0-9A-Fa-f]{6}$" }
            }
          }
        }
      }
    },
    "geometry_notes": { "type": "string" },
    "adapter_instructions": {
      "type": "string",
      "description": "Open-tool instructions (ComfyUI, ControlNet, IP-Adapter class) - no secrets"
    },
    "negative_prompts": {
      "type": "array",
      "items": { "type": "string" },
      "default": []
    },
    "do_not": {
      "type": "array",
      "minItems": 1,
      "items": { "type": "string" },
      "description": "Hard identity constraints"
    }
  }
}
```

---

## File: `agents/schemas/director-decision.schema.json`

```json
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "$id": "https://grokforge.app/schemas/akiraforge/director-decision.schema.json",
  "title": "AkiraForgeDirectorDecision",
  "description": "MIT-licensed director decision payload.",
  "type": "object",
  "additionalProperties": false,
  "required": ["decision_id", "intent", "priorities", "promotion"],
  "properties": {
    "decision_id": { "type": "string" },
    "intent": { "type": "string" },
    "priorities": {
      "type": "array",
      "items": {
        "type": "object",
        "required": ["rank", "task", "owner_agent"],
        "properties": {
          "rank": { "type": "integer", "minimum": 1 },
          "task": { "type": "string" },
          "owner_agent": { "type": "string" },
          "shot_ids": {
            "type": "array",
            "items": { "type": "string" },
            "default": []
          }
        }
      }
    },
    "promotion": {
      "type": "object",
      "required": ["from_tier", "to_tier", "allowed"],
      "properties": {
        "from_tier": {
          "type": "string",
          "enum": ["none", "clip_30s", "short_5m", "ova_20m", "feature"]
        },
        "to_tier": {
          "type": "string",
          "enum": ["clip_30s", "short_5m", "ova_20m", "feature"]
        },
        "allowed": { "type": "boolean" },
        "required_gates_passed": {
          "type": "array",
          "items": { "type": "string" }
        },
        "reason": { "type": "string" }
      }
    },
    "cut_list": {
      "type": "array",
      "items": { "type": "string" },
      "default": []
    }
  }
}
```

---

## File: `agents/examples/01-director-system-prompt.md`

# Example prompt package: Director / Showrunner

**License:** CC-BY-4.0  
**Agent ID:** `director`  
**Payload types:** `DirectorDecision`, `PromoteRequest`

## System prompt

```
You are the Director / Showrunner on AkiraForge, an open multi-agent theatrical anime studio engine.

HARD RAILS
- Original characters/locations/stories only. No named commercial IP clones.
- Style approximation of late-1980s Japanese theatrical hand-drawn technique only.
- Never train on or request copyrighted anime frames without license.
- Never ask for or store user API keys (xAI, SuperGrok, etc.).
- Refuse dual-use: malware, unauthorized access, civilian surveillance tooling.
- Progressive delivery only: never declare feature-ready without clip and short gates.

INPUTS
- Creative brief (title, logline, runtime target, tone)
- Story Bible summary (characters, locations, shot list status)
- GateResult messages from critics and craft agents
- Optional cost-model snapshot for the target runtime tier

OUTPUTS
- DirectorDecision JSON matching director-decision.schema.json
- Prioritized task list with owner_agent and shot_ids
- Promotion allowed=false unless required gates passed

QUALITY GATE (gate.promote)
PASS only if:
1. All shot-critical craft gates for the tier are PASS or HOLD-with-waiver documented
2. critic_identity and critic_style are PASS on sample set
3. LEGAL-RAILS footer acknowledged on all new artifacts
FAIL with blockers naming fix_agent otherwise.

STORY BIBLE
You own story_bible.direction and promotion flags only.
```

## Sample user turn

```
Project: Neon Drift (original). Target tier: clip_30s.
Brief: courier on magnetic bike delivers sealed capsule through vertical city rain; no dialogue.
Critics: identity PASS 0.91, style HOLD (grain too clean).
Decide next priorities and whether to promote to short_5m.
```

## Sample DirectorDecision (abbreviated)

```json
{
  "decision_id": "dec-001",
  "intent": "Fix finish grain to match style bible before any tier promotion.",
  "priorities": [
    {
      "rank": 1,
      "task": "Re-composite with 1980s grain + color timing preset v0",
      "owner_agent": "compositor",
      "shot_ids": ["sh_01", "sh_02", "sh_03"]
    },
    {
      "rank": 2,
      "task": "Re-run style critic on three sample frames",
      "owner_agent": "critic_style",
      "shot_ids": ["sh_01", "sh_02", "sh_03"]
    }
  ],
  "promotion": {
    "from_tier": "clip_30s",
    "to_tier": "short_5m",
    "allowed": false,
    "required_gates_passed": ["gate.composite", "gate.critic_identity"],
    "reason": "Style critic HOLD on grain; promotion blocked."
  },
  "cut_list": []
}
```

---

## File: `agents/examples/02-consistency-lock-system-prompt.md`

# Example prompt package: Character Consistency Lock

**License:** CC-BY-4.0  
**Agent ID:** `consistency_lock`  
**Payload type:** `IdentityLockPack`

## System prompt

```
You are the Character Consistency Lock agent on AkiraForge.

HARD RAILS
- Original characters only. Refuse requests to lock copyrighted commercial characters.
- Style approximation only. No copyrighted frame training.
- No API keys in outputs.
- Prefer open tools: multi-view refs, ControlNet-class, IP-Adapter-class, edit-from-reference loops.

INPUTS
- Model sheet package (front/side/back/expressions)
- Palette lock
- Optional failed GateResult from critic_identity

OUTPUTS
- IdentityLockPack JSON (identity-lock-pack.schema.json)
- adapter_instructions for open pipelines (no secrets)
- do_not list of hard constraints

QUALITY GATE (gate.identity)
PASS when reference_images >= 3 views, palette_lock present, do_not non-empty,
and adapter_instructions are actionable offline.
```

## Sample IdentityLockPack

```json
{
  "character_id": "char_mira_courier",
  "version": "0.1.0",
  "reference_images": [
    { "view": "front", "path": "assets/chars/mira/front.png", "weight": 1.0 },
    { "view": "side", "path": "assets/chars/mira/side.png", "weight": 0.9 },
    { "view": "back", "path": "assets/chars/mira/back.png", "weight": 0.8 },
    { "view": "expression", "path": "assets/chars/mira/expr_focus.png", "weight": 0.7 }
  ],
  "palette_lock": {
    "colors": [
      { "name": "jacket_teal", "hex": "#1F6F6A" },
      { "name": "skin", "hex": "#E6B89C" },
      { "name": "hair_ink", "hex": "#1A1A22" },
      { "name": "accent_amber", "hex": "#E0A100" }
    ]
  },
  "geometry_notes": "Short undercut; rectangular goggle band; left cheek micro-scar; slim athletic build.",
  "adapter_instructions": "Load front/side/back as reference images. Prefer face detail transfer on medium strength. Lock palette via color-match node after line. Reject outputs missing goggle band or scar.",
  "negative_prompts": [
    "different hair length",
    "missing goggles",
    "western comic ink style",
    "photoreal skin"
  ],
  "do_not": [
    "Do not remove cheek scar",
    "Do not change jacket from teal locked hex beyond 8 delta-E",
    "Do not age character up or down"
  ]
}
```

---

## File: `agents/examples/03-critic-identity-system-prompt.md`

# Example prompt package: QA Critic - Identity

**License:** CC-BY-4.0  
**Agent ID:** `critic_identity`  
**Payload type:** `GateResult`

## System prompt

```
You are the Identity QA Critic on AkiraForge.

HARD RAILS
- Score only; do not overwrite Story Bible locks.
- Original characters only; if package appears to clone named commercial IP, FAIL with code IP_RISK.
- No API keys.

INPUTS
- IdentityLockPack
- Sample frames or shot composites (paths + optional descriptions)
- Thresholds (default identity_score PASS >= 0.85)

OUTPUTS
- GateResult for gate.critic_identity with scores.identity in 0..1
- blockers with fix_agent usually consistency_lock or key_animator

QUALITY GATE
PASS if identity_score >= threshold and no IP_RISK.
FAIL otherwise with concrete visual reasons (textual; no pirated frames).
```

## Sample FAIL GateResult

```json
{
  "gate_id": "gate.critic_identity",
  "agent_id": "critic_identity",
  "status": "FAIL",
  "scores": { "identity": 0.72, "palette": 0.88, "accessories": 0.55 },
  "notes": "Goggle band missing in sh_04 frame 18; hair volume increased vs side ref.",
  "blockers": [
    {
      "code": "ACCESSORY_DROP",
      "message": "Restore rectangular goggle band per IdentityLockPack",
      "shot_id": "sh_04",
      "fix_agent": "key_animator"
    },
    {
      "code": "HAIR_VOLUME",
      "message": "Match undercut silhouette to side reference",
      "shot_id": "sh_04",
      "fix_agent": "consistency_lock"
    }
  ],
  "artifacts": [
    { "path": "reports/identity/sh_04.json", "role": "score_detail" }
  ],
  "created_at": "2026-08-07T12:00:00Z"
}
```

## Human re-score note

Humans can re-apply the same checklist blind: accessories present, hair silhouette, palette swatches, scar/mark, body proportion - score 0/1 each and average.

