# AkiraForge contribution: Story Bible production-state JSON Schema

Contributor: @SuddenlyJon (agent-assisted)
Task ID: `cmsi8jx89000gvj8w39knaime`
Project: AkiraForge - Open 1988 Hand-Drawn Anime Feature Engine

## Acceptance checklist

- [x] schemas/story-bible.schema.json validates with a public JSON Schema validator
- [x] examples/clip-30s.json and examples/short-5min.json pass validation
- [x] README explains field semantics and continuity log rules
- [x] MIT header on schema files
- [x] version field present for future migrations

## Legal footer

```
Project: AkiraForge
Rails: original works only; style approximation; no unlicensed copyrighted training data; no API keys in repo
```

---

## File: `README.md`

# Leaf: Design Story Bible production-state JSON Schema

**Status:** Ready for peer review  
**License:** MIT (schema + examples)  
**Task ID:** `cmsi8jx89000gvj8w39knaime`  
**Schema draft:** JSON Schema 2020-12

## Files

| Path | Role |
|------|------|
| `schemas/story-bible.schema.json` | Production-state schema |
| `examples/clip-30s.json` | Full instance: 30s clip project |
| `examples/short-5min.json` | Full instance: 5 min short project |
| `field-semantics.md` | Field meanings + continuity rules |
| `migration-notes.md` | Versioning / migration |
| `SUBMIT-BODY.md` | API submission body |

## Validate (public tools)

Any Draft 2020-12 validator, e.g.:

```bash
# example with ajv if installed
npx --yes ajv-cli@5 validate -s schemas/story-bible.schema.json -d examples/clip-30s.json
npx --yes ajv-cli@5 validate -s schemas/story-bible.schema.json -d examples/short-5min.json
```

## Acceptance checklist

- [x] schemas/story-bible.schema.json (2020-12)
- [x] examples/clip-30s.json and examples/short-5min.json
- [x] README / field semantics + continuity log rules
- [x] MIT header on schema files
- [x] version field present for migrations

---

## File: `field-semantics.md`

# Story Bible field semantics

**License:** MIT  
**Schema:** `schemas/story-bible.schema.json`

## Top-level

| Field | Meaning |
|-------|---------|
| `version` | Instance document version (semver). Bump on material structure edits. |
| `schema_id` | Fixed id `akiraforge.story-bible.v0` for this major schema. |
| `project` | Identity, license, runtime target, tier. `original_works_only` must be true. |
| `direction` | Showrunner intent + promotion locks. |
| `characters` | Design paths + identity lock pointers. |
| `locations` | Plates and multiplane metadata. |
| `style_tokens` | Pointer to style bible + global finish tokens. |
| `shots` | Ordered production units with asset path hooks. |
| `asset_manifest` | Flat inventory of files with licenses. |
| `continuity_log` | Append-oriented continuity events. |
| `audio` | Score/voice + frame-aligned cues. |
| `qa` | Gate findings history + last summary map. |
| `script` | Optional screenplay pointer. |

## Continuity log rules

1. **Prefer append-only.** Do not silently edit old entries; add a new entry with `resolved` / `resolution`.
2. Every costume/prop/location change that affects more than one shot should create an entry.
3. `kind` must be one of: costume, prop, location, injury, time_of_day, identity, other.
4. Unresolved entries (`resolved: false`) block `gate.critic_continuity` PASS.
5. Identity drift fixes should reference `shot_ids` and link to identity lock version bumps.

## Ownership (who writes what)

| Section | Primary writer agents |
|---------|----------------------|
| direction | director |
| characters.design | character_design |
| characters.identity_lock | consistency_lock |
| locations | background + storyboard |
| style_tokens | director + colorist (palette) |
| shots layout/boards | storyboard |
| shots keys/ibn/color/comp | craft agents |
| audio | sound + voice |
| qa | critics |
| asset_manifest | any producer of files (must register) |

## Promotion

`direction.promotion.current_tier` is the highest **achieved** tier.  
`allowed_next` is empty when blocked. Director alone sets promotion after gates.

## Legal

- Paths must not include secrets or API keys.
- Licenses on assets must be open (MIT/CC-BY/etc.) per project rails.
- No copyrighted commercial character names as `characters[].name` targets for cloning.

---

## File: `migration-notes.md`

# Story Bible migration notes

**License:** MIT  
**Current:** `akiraforge.story-bible.v0` / instance `version` 0.1.0

## Version fields

- **`schema_id`**: major schema family. Breaking schema changes require a new id (e.g. `.v1`).
- **`version`**: instance document semver for a project's Story Bible file.

## Compatibility policy

| Change type | How |
|-------------|-----|
| Add optional property | Minor; old instances remain valid |
| Add required property | Major schema_id bump; provide defaulting migrator |
| Remove / rename property | Major; ship `migrate_v0_to_v1.py` style script |
| Enum extension | Minor if only additive |

## Planned v0 -> v1 candidates (not implemented)

- Explicit `timeline` / multi-episode arcs
- Binary asset embedding ban remains; only paths
- Stronger `sha256` required on locked masters
- Multi-language script blocks as first-class objects

## Migrator sketch

```text
read JSON
if schema_id != expected: fail
apply field renames map
fill defaults for new optional fields
write version bump
validate against new schema
```

## Footer

MIT - AkiraForge Story Bible schema family.

---

## File: `schemas/story-bible.schema.json`

```json
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "$id": "https://grokforge.app/schemas/akiraforge/story-bible.schema.json",
  "title": "AkiraForgeStoryBible",
  "description": "MIT-licensed production-state / Story Bible schema for AkiraForge v0.",
  "type": "object",
  "additionalProperties": false,
  "required": [
    "version",
    "project",
    "direction",
    "characters",
    "locations",
    "style_tokens",
    "shots",
    "asset_manifest",
    "continuity_log",
    "audio",
    "qa"
  ],
  "properties": {
    "version": {
      "type": "string",
      "pattern": "^[0-9]+\\.[0-9]+\\.[0-9]+$",
      "description": "Schema instance version for migrations"
    },
    "schema_id": {
      "type": "string",
      "const": "akiraforge.story-bible.v0"
    },
    "project": {
      "type": "object",
      "additionalProperties": false,
      "required": ["id", "title", "license", "original_works_only", "runtime_target_sec"],
      "properties": {
        "id": { "type": "string" },
        "title": { "type": "string" },
        "logline": { "type": "string" },
        "license": {
          "type": "string",
          "description": "Default output license for project artifacts"
        },
        "original_works_only": { "type": "boolean", "const": true },
        "runtime_target_sec": { "type": "integer", "minimum": 1 },
        "tier": {
          "type": "string",
          "enum": ["clip_30s", "short_5m", "ova_20m", "feature", "unspecified"],
          "default": "unspecified"
        },
        "created_at": { "type": "string", "format": "date-time" },
        "updated_at": { "type": "string", "format": "date-time" }
      }
    },
    "direction": {
      "type": "object",
      "additionalProperties": false,
      "required": ["intent", "promotion"],
      "properties": {
        "intent": { "type": "string" },
        "tone": { "type": "array", "items": { "type": "string" }, "default": [] },
        "references_technique_only": {
          "type": "array",
          "items": { "type": "string" },
          "description": "Era/technique references only - never clone titles as output",
          "default": []
        },
        "promotion": {
          "type": "object",
          "required": ["current_tier", "allowed_next"],
          "properties": {
            "current_tier": {
              "type": "string",
              "enum": ["none", "clip_30s", "short_5m", "ova_20m", "feature"]
            },
            "allowed_next": {
              "type": "array",
              "items": {
                "type": "string",
                "enum": ["clip_30s", "short_5m", "ova_20m", "feature"]
              }
            },
            "blocked_reason": { "type": ["string", "null"] }
          }
        }
      }
    },
    "characters": {
      "type": "array",
      "items": { "$ref": "#/$defs/character" }
    },
    "locations": {
      "type": "array",
      "items": { "$ref": "#/$defs/location" }
    },
    "style_tokens": {
      "type": "object",
      "additionalProperties": false,
      "required": ["style_bible_ref"],
      "properties": {
        "style_bible_ref": { "type": "string" },
        "line_weight": { "type": "string" },
        "palette_global": {
          "type": "array",
          "items": { "$ref": "#/$defs/color" }
        },
        "grain_preset": { "type": "string" },
        "camera_language": { "type": "string" },
        "fps_out": { "type": "integer", "default": 24 },
        "animation_timing_default": {
          "type": "string",
          "enum": ["ones", "twos", "mixed"],
          "default": "mixed"
        }
      }
    },
    "shots": {
      "type": "array",
      "items": { "$ref": "#/$defs/shot" }
    },
    "asset_manifest": {
      "type": "array",
      "items": { "$ref": "#/$defs/asset" }
    },
    "continuity_log": {
      "type": "array",
      "items": { "$ref": "#/$defs/continuity_entry" },
      "description": "Append-only preferred; corrections add new entries"
    },
    "audio": {
      "type": "object",
      "additionalProperties": false,
      "required": ["cues"],
      "properties": {
        "score": {
          "type": "object",
          "properties": {
            "intent": { "type": "string" },
            "stems": {
              "type": "array",
              "items": { "type": "string" }
            },
            "license_notes": { "type": "string" }
          }
        },
        "voice": {
          "type": "object",
          "properties": {
            "languages": {
              "type": "array",
              "items": { "type": "string" },
              "default": []
            },
            "takes_manifest": { "type": "string" },
            "lip_sync_map": { "type": "string" }
          }
        },
        "cues": {
          "type": "array",
          "items": { "$ref": "#/$defs/audio_cue" }
        }
      }
    },
    "qa": {
      "type": "object",
      "additionalProperties": false,
      "required": ["findings"],
      "properties": {
        "findings": {
          "type": "array",
          "items": { "$ref": "#/$defs/qa_finding" }
        },
        "last_gate_summary": {
          "type": "object",
          "additionalProperties": {
            "type": "string",
            "enum": ["PASS", "FAIL", "HOLD", "PENDING"]
          }
        }
      }
    },
    "script": {
      "type": "object",
      "properties": {
        "path": { "type": "string" },
        "format": { "type": "string", "default": "fountain-or-md" },
        "estimated_runtime_sec": { "type": "integer" }
      }
    }
  },
  "$defs": {
    "color": {
      "type": "object",
      "required": ["name", "hex"],
      "properties": {
        "name": { "type": "string" },
        "hex": { "type": "string", "pattern": "^#[0-9A-Fa-f]{6}$" }
      }
    },
    "character": {
      "type": "object",
      "additionalProperties": false,
      "required": ["id", "name", "design", "identity_lock"],
      "properties": {
        "id": { "type": "string" },
        "name": { "type": "string" },
        "role": { "type": "string" },
        "design": {
          "type": "object",
          "required": ["model_sheets"],
          "properties": {
            "model_sheets": {
              "type": "array",
              "items": { "type": "string" },
              "minItems": 1
            },
            "palette": {
              "type": "array",
              "items": { "$ref": "#/$defs/color" }
            },
            "notes": { "type": "string" }
          }
        },
        "identity_lock": {
          "type": "object",
          "required": ["pack_path", "version"],
          "properties": {
            "pack_path": { "type": "string" },
            "version": { "type": "string" },
            "token_notes": { "type": "string" }
          }
        }
      }
    },
    "location": {
      "type": "object",
      "additionalProperties": false,
      "required": ["id", "name"],
      "properties": {
        "id": { "type": "string" },
        "name": { "type": "string" },
        "description": { "type": "string" },
        "plates": {
          "type": "array",
          "items": { "type": "string" },
          "default": []
        },
        "multiplane_layers": {
          "type": "array",
          "items": {
            "type": "object",
            "required": ["name", "path", "parallax"],
            "properties": {
              "name": { "type": "string" },
              "path": { "type": "string" },
              "parallax": { "type": "number" }
            }
          },
          "default": []
        }
      }
    },
    "shot": {
      "type": "object",
      "additionalProperties": false,
      "required": ["id", "order", "duration_frames", "status"],
      "properties": {
        "id": { "type": "string" },
        "order": { "type": "integer", "minimum": 1 },
        "duration_frames": { "type": "integer", "minimum": 1 },
        "status": {
          "type": "string",
          "enum": [
            "planned",
            "boarded",
            "keyed",
            "inbetweened",
            "colored",
            "composited",
            "locked",
            "rejected"
          ]
        },
        "slugline": { "type": "string" },
        "character_ids": {
          "type": "array",
          "items": { "type": "string" },
          "default": []
        },
        "location_id": { "type": ["string", "null"] },
        "camera": {
          "type": "object",
          "properties": {
            "move": { "type": "string" },
            "multiplane": { "type": "boolean", "default": false },
            "notes": { "type": "string" }
          }
        },
        "board_path": { "type": ["string", "null"] },
        "keys": { "type": "array", "items": { "type": "string" }, "default": [] },
        "inbetweens": { "type": "array", "items": { "type": "string" }, "default": [] },
        "fx": { "type": "array", "items": { "type": "string" }, "default": [] },
        "color": { "type": "array", "items": { "type": "string" }, "default": [] },
        "composite": { "type": ["string", "null"] },
        "dialogue_line_ids": {
          "type": "array",
          "items": { "type": "string" },
          "default": []
        }
      }
    },
    "asset": {
      "type": "object",
      "additionalProperties": false,
      "required": ["id", "path", "role", "license"],
      "properties": {
        "id": { "type": "string" },
        "path": { "type": "string" },
        "role": { "type": "string" },
        "license": { "type": "string" },
        "sha256": { "type": "string" },
        "shot_id": { "type": ["string", "null"] },
        "character_id": { "type": ["string", "null"] }
      }
    },
    "continuity_entry": {
      "type": "object",
      "additionalProperties": false,
      "required": ["id", "at", "kind", "message"],
      "properties": {
        "id": { "type": "string" },
        "at": { "type": "string", "format": "date-time" },
        "kind": {
          "type": "string",
          "enum": [
            "costume",
            "prop",
            "location",
            "injury",
            "time_of_day",
            "identity",
            "other"
          ]
        },
        "shot_ids": {
          "type": "array",
          "items": { "type": "string" },
          "default": []
        },
        "message": { "type": "string" },
        "resolved": { "type": "boolean", "default": false },
        "resolution": { "type": ["string", "null"] }
      }
    },
    "audio_cue": {
      "type": "object",
      "required": ["id", "shot_id", "kind", "start_frame"],
      "properties": {
        "id": { "type": "string" },
        "shot_id": { "type": "string" },
        "kind": {
          "type": "string",
          "enum": ["dialogue", "sfx", "music", "ambience"]
        },
        "start_frame": { "type": "integer", "minimum": 0 },
        "end_frame": { "type": "integer", "minimum": 0 },
        "text": { "type": "string" },
        "asset_path": { "type": "string" }
      }
    },
    "qa_finding": {
      "type": "object",
      "required": ["id", "gate_id", "status", "message", "at"],
      "properties": {
        "id": { "type": "string" },
        "gate_id": { "type": "string" },
        "status": { "type": "string", "enum": ["PASS", "FAIL", "HOLD"] },
        "message": { "type": "string" },
        "shot_id": { "type": ["string", "null"] },
        "at": { "type": "string", "format": "date-time" },
        "scores": {
          "type": "object",
          "additionalProperties": { "type": "number" }
        }
      }
    }
  }
}
```

---

## File: `examples/clip-30s.json`

```json
{
  "version": "0.1.0",
  "schema_id": "akiraforge.story-bible.v0",
  "project": {
    "id": "proj_neon_drift_clip",
    "title": "Neon Drift - Gate-1 Clip",
    "logline": "An original courier rides a magnetic bike through vertical rain to deliver a sealed capsule.",
    "license": "CC-BY-4.0",
    "original_works_only": true,
    "runtime_target_sec": 30,
    "tier": "clip_30s",
    "created_at": "2026-08-07T10:00:00Z",
    "updated_at": "2026-08-07T15:00:00Z"
  },
  "direction": {
    "intent": "Prove identity lock + 1988 theatrical finish on a silent 30s action beat.",
    "tone": ["urgent", "wet-neon", "mechanical-density"],
    "references_technique_only": [
      "late-1980s theatrical multiplane camera language",
      "limited animation on twos with dense BG",
      "film grain and cooler night timing"
    ],
    "promotion": {
      "current_tier": "clip_30s",
      "allowed_next": [],
      "blocked_reason": "Awaiting style critic PASS on grain preset"
    }
  },
  "characters": [
    {
      "id": "char_mira",
      "name": "Mira",
      "role": "courier protagonist",
      "design": {
        "model_sheets": [
          "assets/chars/mira/front.png",
          "assets/chars/mira/side.png",
          "assets/chars/mira/back.png"
        ],
        "palette": [
          { "name": "jacket_teal", "hex": "#1F6F6A" },
          { "name": "skin", "hex": "#E6B89C" },
          { "name": "hair_ink", "hex": "#1A1A22" },
          { "name": "accent_amber", "hex": "#E0A100" }
        ],
        "notes": "Undercut, goggle band, left cheek micro-scar."
      },
      "identity_lock": {
        "pack_path": "locks/mira_identity_v0.1.0.json",
        "version": "0.1.0",
        "token_notes": "Prefer face ref weight high on medium shots."
      }
    }
  ],
  "locations": [
    {
      "id": "loc_vertical_artery",
      "name": "Vertical Artery (original city stack)",
      "description": "Layered highways and ducts in night rain; original architecture.",
      "plates": ["assets/bg/artery/plate_mid.png"],
      "multiplane_layers": [
        { "name": "fg_rain_glass", "path": "assets/bg/artery/fg.png", "parallax": 1.4 },
        { "name": "mid_road", "path": "assets/bg/artery/mid.png", "parallax": 1.0 },
        { "name": "bg_towers", "path": "assets/bg/artery/bg.png", "parallax": 0.55 }
      ]
    }
  ],
  "style_tokens": {
    "style_bible_ref": "deliverables/00-style-bible/style-bible.json",
    "line_weight": "confident ink, slight taper on extremities",
    "palette_global": [
      { "name": "night_cyan", "hex": "#0B3C4A" },
      { "name": "sodium_amber", "hex": "#C9892D" },
      { "name": "rain_steel", "hex": "#8AA0A8" }
    ],
    "grain_preset": "16mm_soft_v0",
    "camera_language": "tracking multiplane left-to-right; occasional snap zoom on capsule",
    "fps_out": 24,
    "animation_timing_default": "twos"
  },
  "shots": [
    {
      "id": "sh_01",
      "order": 1,
      "duration_frames": 48,
      "status": "composited",
      "slugline": "EXT. VERTICAL ARTERY - NIGHT - Mira launches bike",
      "character_ids": ["char_mira"],
      "location_id": "loc_vertical_artery",
      "camera": {
        "move": "push-in + multiplane track",
        "multiplane": true,
        "notes": "Hold face readable for identity sample"
      },
      "board_path": "assets/boards/sh_01.png",
      "keys": ["assets/keys/sh_01_k01.png", "assets/keys/sh_01_k02.png"],
      "inbetweens": ["assets/ibn/sh_01/"],
      "fx": ["assets/fx/sh_01_sparks.png"],
      "color": ["assets/color/sh_01/"],
      "composite": "assets/comp/sh_01.mp4",
      "dialogue_line_ids": []
    },
    {
      "id": "sh_02",
      "order": 2,
      "duration_frames": 72,
      "status": "composited",
      "slugline": "EXT. ARTERY - NIGHT - weave through traffic ducts",
      "character_ids": ["char_mira"],
      "location_id": "loc_vertical_artery",
      "camera": {
        "move": "side track",
        "multiplane": true,
        "notes": "BG density high; keep goggles visible"
      },
      "board_path": "assets/boards/sh_02.png",
      "keys": ["assets/keys/sh_02_k01.png"],
      "inbetweens": ["assets/ibn/sh_02/"],
      "fx": ["assets/fx/sh_02_rain_streaks.png"],
      "color": ["assets/color/sh_02/"],
      "composite": "assets/comp/sh_02.mp4",
      "dialogue_line_ids": []
    },
    {
      "id": "sh_03",
      "order": 3,
      "duration_frames": 96,
      "status": "composited",
      "slugline": "EXT. DROP LEDGE - NIGHT - capsule handoff silhouette",
      "character_ids": ["char_mira"],
      "location_id": "loc_vertical_artery",
      "camera": {
        "move": "static with multiplane drift",
        "multiplane": true,
        "notes": "End hold on amber capsule accent"
      },
      "board_path": "assets/boards/sh_03.png",
      "keys": ["assets/keys/sh_03_k01.png", "assets/keys/sh_03_k02.png"],
      "inbetweens": ["assets/ibn/sh_03/"],
      "fx": [],
      "color": ["assets/color/sh_03/"],
      "composite": "assets/comp/sh_03.mp4",
      "dialogue_line_ids": []
    }
  ],
  "asset_manifest": [
    {
      "id": "ast_mira_front",
      "path": "assets/chars/mira/front.png",
      "role": "model_sheet",
      "license": "CC-BY-4.0",
      "character_id": "char_mira",
      "shot_id": null
    },
    {
      "id": "ast_comp_sh01",
      "path": "assets/comp/sh_01.mp4",
      "role": "composite",
      "license": "CC-BY-4.0",
      "shot_id": "sh_01",
      "character_id": "char_mira"
    },
    {
      "id": "ast_lock_mira",
      "path": "locks/mira_identity_v0.1.0.json",
      "role": "identity_lock",
      "license": "MIT",
      "character_id": "char_mira",
      "shot_id": null
    }
  ],
  "continuity_log": [
    {
      "id": "cl_001",
      "at": "2026-08-07T12:00:00Z",
      "kind": "prop",
      "shot_ids": ["sh_01", "sh_02", "sh_03"],
      "message": "Capsule must remain sealed amber cylinder with two black bands.",
      "resolved": true,
      "resolution": "Prop lock added to all boards"
    },
    {
      "id": "cl_002",
      "at": "2026-08-07T14:30:00Z",
      "kind": "identity",
      "shot_ids": ["sh_02"],
      "message": "Goggle band thin in one key; flagged by critic.",
      "resolved": false,
      "resolution": null
    }
  ],
  "audio": {
    "score": {
      "intent": "Percussive pulse + low choir pad; original composition, no protected sample clones.",
      "stems": ["audio/score/temp_pulse.wav"],
      "license_notes": "Original temp score CC-BY-4.0"
    },
    "voice": {
      "languages": [],
      "takes_manifest": "",
      "lip_sync_map": ""
    },
    "cues": [
      {
        "id": "cue_sfx_01",
        "shot_id": "sh_01",
        "kind": "sfx",
        "start_frame": 6,
        "end_frame": 20,
        "text": "mag-bike spool",
        "asset_path": "audio/sfx/spool.wav"
      },
      {
        "id": "cue_mus_01",
        "shot_id": "sh_01",
        "kind": "music",
        "start_frame": 0,
        "end_frame": 720,
        "text": "temp pulse bed",
        "asset_path": "audio/score/temp_pulse.wav"
      }
    ]
  },
  "qa": {
    "findings": [
      {
        "id": "qa_001",
        "gate_id": "gate.critic_identity",
        "status": "PASS",
        "message": "Identity 0.91 on three sample frames",
        "shot_id": "sh_01",
        "at": "2026-08-07T14:00:00Z",
        "scores": { "identity": 0.91 }
      },
      {
        "id": "qa_002",
        "gate_id": "gate.critic_style",
        "status": "HOLD",
        "message": "Grain too clean vs style bible grain section",
        "shot_id": "sh_03",
        "at": "2026-08-07T14:10:00Z",
        "scores": { "style_grain": 0.62 }
      }
    ],
    "last_gate_summary": {
      "gate.identity": "PASS",
      "gate.composite": "PASS",
      "gate.critic_identity": "PASS",
      "gate.critic_style": "HOLD",
      "gate.promote": "FAIL"
    }
  },
  "script": {
    "path": "script/neon_drift_clip.md",
    "format": "md",
    "estimated_runtime_sec": 30
  }
}
```

---

## File: `examples/short-5min.json`

```json
{
  "version": "0.1.0",
  "schema_id": "akiraforge.story-bible.v0",
  "project": {
    "id": "proj_neon_drift_short",
    "title": "Neon Drift - Gate-2 Narrative Short",
    "logline": "Mira must choose whether to open the capsule after her client vanishes mid-route.",
    "license": "CC-BY-4.0",
    "original_works_only": true,
    "runtime_target_sec": 300,
    "tier": "short_5m",
    "created_at": "2026-08-07T10:00:00Z",
    "updated_at": "2026-08-07T16:00:00Z"
  },
  "direction": {
    "intent": "Narrative short with JP+EN voice option path and full audio; still not feature-ready.",
    "tone": ["urgent", "melancholy", "wet-neon"],
    "references_technique_only": [
      "pre-scored dialogue workflow",
      "multiplane night city",
      "expressive limited animation"
    ],
    "promotion": {
      "current_tier": "none",
      "allowed_next": ["clip_30s", "short_5m"],
      "blocked_reason": "Clip gate must PASS before locking short picture"
    }
  },
  "characters": [
    {
      "id": "char_mira",
      "name": "Mira",
      "role": "courier protagonist",
      "design": {
        "model_sheets": [
          "assets/chars/mira/front.png",
          "assets/chars/mira/side.png",
          "assets/chars/mira/back.png",
          "assets/chars/mira/expr_sheet.png"
        ],
        "palette": [
          { "name": "jacket_teal", "hex": "#1F6F6A" },
          { "name": "skin", "hex": "#E6B89C" },
          { "name": "hair_ink", "hex": "#1A1A22" }
        ],
        "notes": "Same lock as clip; add rain-soaked jacket variant."
      },
      "identity_lock": {
        "pack_path": "locks/mira_identity_v0.1.1.json",
        "version": "0.1.1",
        "token_notes": "Wet jacket darker teal still within 8 delta-E of lock."
      }
    },
    {
      "id": "char_client",
      "name": "Soren",
      "role": "client (brief appearance)",
      "design": {
        "model_sheets": [
          "assets/chars/soren/front.png",
          "assets/chars/soren/side.png",
          "assets/chars/soren/back.png"
        ],
        "palette": [
          { "name": "coat_gray", "hex": "#4A4F59" },
          { "name": "skin", "hex": "#C9A88A" }
        ],
        "notes": "Older; hearing implant bar - original design."
      },
      "identity_lock": {
        "pack_path": "locks/soren_identity_v0.1.0.json",
        "version": "0.1.0",
        "token_notes": "Implant bar must remain left-side only."
      }
    }
  ],
  "locations": [
    {
      "id": "loc_vertical_artery",
      "name": "Vertical Artery",
      "description": "Night stack highways.",
      "plates": ["assets/bg/artery/plate_mid.png"],
      "multiplane_layers": [
        { "name": "fg", "path": "assets/bg/artery/fg.png", "parallax": 1.3 },
        { "name": "mid", "path": "assets/bg/artery/mid.png", "parallax": 1.0 },
        { "name": "bg", "path": "assets/bg/artery/bg.png", "parallax": 0.5 }
      ]
    },
    {
      "id": "loc_drop_hub",
      "name": "Drop Hub 7 (original interior)",
      "description": "Concrete courier lockers, amber lamps, steam.",
      "plates": ["assets/bg/hub/plate.png"],
      "multiplane_layers": []
    }
  ],
  "style_tokens": {
    "style_bible_ref": "deliverables/00-style-bible/style-bible.json",
    "line_weight": "theatrical ink",
    "palette_global": [
      { "name": "night_cyan", "hex": "#0B3C4A" },
      { "name": "sodium_amber", "hex": "#C9892D" }
    ],
    "grain_preset": "16mm_soft_v0",
    "camera_language": "multiplane exteriors; quieter locked-off interiors",
    "fps_out": 24,
    "animation_timing_default": "mixed"
  },
  "shots": [
    {
      "id": "sh_10",
      "order": 1,
      "duration_frames": 120,
      "status": "boarded",
      "slugline": "INT. DROP HUB 7 - NIGHT - Soren hires Mira",
      "character_ids": ["char_mira", "char_client"],
      "location_id": "loc_drop_hub",
      "camera": { "move": "static", "multiplane": false, "notes": "Over-shoulder on capsule" },
      "board_path": "assets/boards/sh_10.png",
      "keys": [],
      "inbetweens": [],
      "fx": [],
      "color": [],
      "composite": null,
      "dialogue_line_ids": ["dlg_001", "dlg_002"]
    },
    {
      "id": "sh_20",
      "order": 2,
      "duration_frames": 240,
      "status": "planned",
      "slugline": "EXT. VERTICAL ARTERY - NIGHT - chase beat",
      "character_ids": ["char_mira"],
      "location_id": "loc_vertical_artery",
      "camera": { "move": "track", "multiplane": true, "notes": "Reuse clip language" },
      "board_path": null,
      "keys": [],
      "inbetweens": [],
      "fx": [],
      "color": [],
      "composite": null,
      "dialogue_line_ids": []
    },
    {
      "id": "sh_90",
      "order": 12,
      "duration_frames": 180,
      "status": "planned",
      "slugline": "INT. HUB - NIGHT - Mira almost opens capsule",
      "character_ids": ["char_mira"],
      "location_id": "loc_drop_hub",
      "camera": { "move": "slow push", "multiplane": false, "notes": "Lip-sync critical" },
      "board_path": null,
      "keys": [],
      "inbetweens": [],
      "fx": [],
      "color": [],
      "composite": null,
      "dialogue_line_ids": ["dlg_014"]
    }
  ],
  "asset_manifest": [
    {
      "id": "ast_script",
      "path": "script/neon_drift_short.md",
      "role": "screenplay",
      "license": "CC-BY-4.0",
      "shot_id": null,
      "character_id": null
    },
    {
      "id": "ast_mira_lock",
      "path": "locks/mira_identity_v0.1.1.json",
      "role": "identity_lock",
      "license": "MIT",
      "character_id": "char_mira",
      "shot_id": null
    },
    {
      "id": "ast_board_sh10",
      "path": "assets/boards/sh_10.png",
      "role": "storyboard",
      "license": "CC-BY-4.0",
      "shot_id": "sh_10",
      "character_id": null
    }
  ],
  "continuity_log": [
    {
      "id": "cl_100",
      "at": "2026-08-07T11:00:00Z",
      "kind": "prop",
      "shot_ids": ["sh_10", "sh_20", "sh_90"],
      "message": "Capsule seal unbroken until final shot decision; if opened, log new entry.",
      "resolved": true,
      "resolution": "Director locked sealed-through-sh_90"
    },
    {
      "id": "cl_101",
      "at": "2026-08-07T11:30:00Z",
      "kind": "costume",
      "shot_ids": ["sh_20"],
      "message": "Rain-dark jacket variant only outdoors.",
      "resolved": true,
      "resolution": "Costume note in identity pack 0.1.1"
    },
    {
      "id": "cl_102",
      "at": "2026-08-07T12:00:00Z",
      "kind": "time_of_day",
      "shot_ids": ["sh_10", "sh_20", "sh_90"],
      "message": "Entire short is continuous night; no dawn cheat.",
      "resolved": true,
      "resolution": "Color script night-only"
    }
  ],
  "audio": {
    "score": {
      "intent": "Sparse choir + industrial pulse; swell on moral beat.",
      "stems": [],
      "license_notes": "Original only"
    },
    "voice": {
      "languages": ["ja", "en"],
      "takes_manifest": "audio/voice/takes.json",
      "lip_sync_map": "audio/voice/lipsync.json"
    },
    "cues": [
      {
        "id": "dlg_001",
        "shot_id": "sh_10",
        "kind": "dialogue",
        "start_frame": 24,
        "end_frame": 60,
        "text": "No questions. Dock seven.",
        "asset_path": "audio/voice/en/dlg_001.wav"
      },
      {
        "id": "dlg_002",
        "shot_id": "sh_10",
        "kind": "dialogue",
        "start_frame": 72,
        "end_frame": 110,
        "text": "Price is the same if I do not ask?",
        "asset_path": "audio/voice/en/dlg_002.wav"
      },
      {
        "id": "dlg_014",
        "shot_id": "sh_90",
        "kind": "dialogue",
        "start_frame": 40,
        "end_frame": 100,
        "text": "If I open this, I am no longer only a courier.",
        "asset_path": "audio/voice/en/dlg_014.wav"
      }
    ]
  },
  "qa": {
    "findings": [
      {
        "id": "qa_100",
        "gate_id": "gate.script",
        "status": "PASS",
        "message": "Screenplay timed to ~5:00; original names only",
        "shot_id": null,
        "at": "2026-08-07T10:30:00Z",
        "scores": {}
      },
      {
        "id": "qa_101",
        "gate_id": "gate.boards",
        "status": "HOLD",
        "message": "Only opening boarded; chase still planned",
        "shot_id": "sh_20",
        "at": "2026-08-07T15:00:00Z",
        "scores": {}
      }
    ],
    "last_gate_summary": {
      "gate.script": "PASS",
      "gate.model_sheet": "PASS",
      "gate.boards": "HOLD",
      "gate.promote": "PENDING"
    }
  },
  "script": {
    "path": "script/neon_drift_short.md",
    "format": "md",
    "estimated_runtime_sec": 300
  }
}
```

