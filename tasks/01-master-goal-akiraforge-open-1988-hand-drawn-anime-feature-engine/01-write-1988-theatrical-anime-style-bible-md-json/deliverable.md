# AkiraForge contribution: Style Bible v0.1.0

Claimed leaf: Write 1988 theatrical anime style bible (MD+JSON)
Contributor: @SuddenlyJon (agent-assisted)
License: CC-BY-4.0

---

# Leaf deliverable: 1988 theatrical anime style bible

**Task:** Write 1988 theatrical anime style bible (MD+JSON)  
**License:** CC-BY-4.0 (docs + JSON knowledge base)  
**Project:** AkiraForge  
**Status:** Ready for peer review

## Files

| File | Role |
|------|------|
| `style-bible.md` | Full technique bible |
| `style-bible.json` | Machine-readable knowledge base |
| `sources.md` | Public citations + peer-check method |
| `README.md` | This index |

## Acceptance checklist

- [x] style-bible.md with required sections (fps/ones-twos, cels, camera, color, dialogue, mechanical density, FX, grain/timing)
- [x] style-bible.json structured knowledge base
- [x] sources.md with public citations / peer-check path
- [x] Legal disclaimer: style approximation, no copyrighted frame training
- [x] CC-BY-4.0 header
- [x] Peer reviewer can spot-check 5 technique claims against cited production vocabulary

## Footer

```
License: CC-BY-4.0 (docs/creative)
Project: AkiraForge
Rails: original works only; style approximation; no unlicensed copyrighted training data; no API keys in repo
```



---


# AkiraForge Style Bible: Late-1980s Theatrical Hand-Drawn Anime Techniques

**License:** CC-BY-4.0  
**Project:** AkiraForge  
**Version:** 0.1.0  
**Date:** 2026-08-06  
**Disclaimer:** Style approximation and technique analysis only. No training on copyrighted film frames. Do not clone named commercial titles or characters as deliverables.

```
Rails: original works only; style approximation; no unlicensed copyrighted training data; no API keys in repo
```

---

## 1. Purpose

This bible is the quality and technique reference for AkiraForge pipelines. It encodes production practices common to **late-1980s Japanese theatrical anime** (densest cyberpunk cinema era, multi-layer cel work, multiplane energy, mechanical background density). Agents and humans score outputs against these sections.

Companion machine file: `style-bible.json`.

---

## 2. Frame rates and animation on ones / twos

| Practice | Typical use | Notes for AI pipelines |
|----------|-------------|------------------------|
| 24 fps projection | Theatrical masters | Final package should target 24 fps containers |
| Animation on ones | Extreme action, impacts, camera shake, psychic bursts | New drawing every frame; expensive; reserve for peak moments |
| Animation on twos | Most character acting, walks, dialogue | Hold drawing for 2 frames (12 unique drawings/sec at 24 fps) |
| Mixed timing | Feature standard | Action peaks go to ones; holds and pauses stay on twos or longer |
| Limited animation vs full | Theatrical budget is "fuller" than TV | Prefer more keys and smarter spacing over endless inbetweens |

**Hand-drawn spacing principles (for key + inbetween agents):**
- Ease-in / ease-out on starts and stops (slow-in / slow-out)
- Arcs for limbs and head turns (avoid linear joint pops)
- Anticipation before major moves; follow-through and overlapping action after
- Hold frames are intentional, not freezes from model failure
- Smear frames and multiples only at high-velocity peaks

**QC rule:** A clip that is "always on ones" with no holds reads uncanny; a clip that never hits ones on impacts reads cheap.

---

## 3. Multi-layer cel practices

Classic theatrical stacks approximate:

1. **Background (BG)** - painted or detailed environment, often multiplane-ready
2. **Book / mid layers** - partial environments, vehicles, debris that move separately
3. **Character cels** - body, then face/hair overlays when needed
4. **FX cels** - smoke, energy, muzzle flash, speed lines, sparks
5. **Overlay / foreground** - rain, glass, HUD, debris near camera

**AI implications:**
- Generate and composite as separate layers when possible (characters not baked into BG forever)
- Preserve clean line art vs fill vs FX as discrete passes
- Shadows and contact shadows should sit between character and BG
- Avoid "single flat plate" generation for complex shots

**Line quality targets:**
- Confident black (or dark) contour with controlled variable weight
- Mechanical objects: denser, tighter line; organic faces: slightly softer but still graphic
- Avoid mushy soft-edge painterly collapse when the brief asks for cel

---

## 4. Camera moves and multiplane parallax

Late-80s theatrical language borrows live-action energy:

| Move | Feel | Implementation notes |
|------|------|----------------------|
| Multiplane truck-in | Depth; BG layers lag | Separate depth layers; different scroll speeds |
| Lateral pan with parallax | Chase / cityscape | Foreground faster than mid than far |
| Dutch / tilted frame | Unease, psychic stress | Use sparingly; pair with FX |
| Simulated handheld | Impact, chaos | Small rotational + positional jitter; not random shake every frame |
| Crash zoom | Shock reveal | Rapid scale change on ones with impact FX |
| Locked tripod | Dialogue, design showcase | Still frame; sell with acting and BG detail |

**QC rule:** Parallax must keep vanishing relationships consistent; drifting horizon is a fail.

---

## 5. Color scripts and limited-palette systems

Theatrical cyberpunk palettes of the era emphasize:

- Deep night blues, charcoal, cold concrete greys
- Neon accents (magenta, cyan, amber, toxic green) as **sparse** light sources
- Warm sodium / interior practicals for human spaces
- Skin tones that stay stable under neon spill (spill is tint, not full recolor of identity)
- High contrast in action; more restrained contrast in quiet character beats

**Working model for AkiraForge (not a claim of any studio's proprietary list):**
- Design a **scene color script** before mass generation
- Cap accent neon hues per sequence (often 2-4 accent families)
- Keep character local colors locked in the Story Bible identity tokens
- Night exteriors: prefer dark value structure over grey mush

**Color timing / finishing:**
- Slight filmic contrast curve
- Optional light grain (see section 8)
- Avoid modern HDR teal-orange defaults unless the brief asks for pastiche of later grading

---

## 6. Pre-scored dialogue workflow

Theatrical anime often locks performance and timing early:

1. Script with timing notes and breath marks
2. Voice recording (or AI voice placeholder) produces a timing bed
3. Animation is planned to the track (mouth charts + body acting)
4. Lip shapes follow classic anime mouth charts more than photoreal visemes
5. Subtitles / dub tracks are stems, not burned-in until delivery masters

**AI pipeline mapping:**
- Store dialogue lines, character, start/end times in Story Bible
- Mouth-shape sequence is a first-class asset (A/I/U/E/O + closed + special)
- Never invent long takes of talking without a timing sheet

---

## 7. Mechanical design density

Hallmarks of the era's prestige mecha / city / vehicle work:

- Readable silhouette first, then dense greebles
- Panel lines, rivets, vents, cables that respect form, not noise texture
- Wear: scuffs, heat staining, oil, not random dirt decals everywhere
- Motorbikes, military hardware, and city infrastructure should feel **engineered**
- Background city blocks: layered signage, cables, ducts, rooftop clutter, depth fog sparingly

**QC rule:** Density without silhouette = clutter fail. Silhouette without density on hero props = under-designed for this quality bar.

---

## 8. Effects animation principles

### 8.1 Speed lines
- Directional, composition-aware
- Stronger near subject velocity; thinner at edges
- Do not cover faces during critical emotional beats unless intentional

### 8.2 Explosions / debris
- Distinct phases: flash / fireball / smoke / debris arc / residual dust
- Debris has weight and arcs; not confetti
- Multiple FX layers over clean character hold when possible

### 8.3 Psychic / energy
- Graphic shapes, hard cores, soft outer glows used carefully
- Color script defines "power" color family early
- Distortion of BG (warping, chromatic fringing) is optional and rare

### 8.4 Impacts
- Impact frames (white flash, radial lines, squash) on ones
- Sound cue alignment when audio present

---

## 9. Film grain, finishing, and 1980s color timing feel

| Element | Guidance |
|---------|----------|
| Grain | Fine, even, optional; never crush detail |
| Soft bloom | Mild on neon; avoid modern heavy fog |
| Black levels | Deep but not crushed void on all values |
| Titles | Often bold graphic type; keep period-plausible if doing pastiche |
| Composite order | BG -> characters -> shadows -> FX -> overlays -> grade -> grain |

---

## 10. Character continuity locks (cross-ref)

This bible owns **style**. Identity persistence is owned by the Story Bible + consistency lock leaf. Minimum style-side rules:

- Line weight family stays consistent per character
- Costume local colors locked
- Eye highlight style consistent within a sequence
- Mechanical props attached to character follow same density language

---

## 11. Progressive quality gates (style scoring)

| Gate | Style must show |
|------|-----------------|
| 20-60s clip | Stable line language, correct timing mix, cel-like separation, palette control |
| 3-5 min short | Multiplane or camera language, FX on peaks, dialogue timing, identity held |
| OVA-scale | Sequence color scripts, denser BG, fewer style regressions |
| Feature path | Documented cost + critic PASS rates across acts |

**Suggested style fidelity score (v0, 0-5):**
1. Line / cel readability  
2. Timing (ones/twos judgment)  
3. Palette discipline  
4. Mechanical / BG density when required  
5. FX authenticity on peaks  
6. Finish (grain/grade restraint)

Average >= 3.5 to promote a clip; >= 4.0 preferred for public demo packages.

---

## 12. Anti-patterns (automatic style FAIL)

- 3D-look plastic skin with no graphic line
- Infinite handheld shake
- Neon rainbow noise without value structure
- Copyrighted character likenesses
- Single-layer video paste with no inspectable intermediates
- "Always morphing" faces sold as acting

---

## 13. How agents should use this file

1. Director sets sequence intent against sections 4-8  
2. Colorist commits a color script before mass gen  
3. Key animator respects ones/twos table  
4. Critics score against section 11  
5. Humans can override with notes in continuity log  

Machine-readable twin: `style-bible.json`.



---

# style-bible.json

```json

{
  "meta": {
    "id": "akiraforge-style-bible",
    "version": "0.1.0",
    "license": "CC-BY-4.0",
    "project": "AkiraForge",
    "title": "Late-1980s theatrical hand-drawn anime technique knowledge base",
    "disclaimer": "Style approximation and technique analysis only. No training on copyrighted film frames. Original works only as project deliverables.",
    "created": "2026-08-06",
    "quality_reference_era": "late-1980s Japanese theatrical anime (cyberpunk prestige cinema techniques)"
  },
  "frame_timing": {
    "projection_fps": 24,
    "default_character_acting": "on_twos",
    "peak_action": "on_ones",
    "principles": [
      "ease_in_out",
      "arcs",
      "anticipation",
      "follow_through",
      "overlapping_action",
      "intentional_holds"
    ],
    "qc_rules": [
      "always_on_ones_is_uncanny",
      "never_on_ones_at_impacts_reads_cheap"
    ]
  },
  "cel_layers": {
    "order_bottom_to_top": [
      "background",
      "mid_book_layers",
      "character_body",
      "character_face_hair_overlay",
      "shadows_contact",
      "fx",
      "foreground_overlay",
      "grade_grain"
    ],
    "line_quality": {
      "contour": "confident_dark_variable_weight",
      "mechanical": "tighter_denser",
      "organic_face": "graphic_not_mushy"
    }
  },
  "camera": {
    "moves": [
      {"id": "multiplane_truck_in", "feel": "depth"},
      {"id": "lateral_parallax_pan", "feel": "chase_city"},
      {"id": "dutch_tilt", "feel": "unease", "use": "sparse"},
      {"id": "simulated_handheld", "feel": "impact_chaos", "notes": "small jitter not random every frame"},
      {"id": "crash_zoom", "feel": "shock"},
      {"id": "locked_tripod", "feel": "dialogue_design"}
    ],
    "qc": ["consistent_horizon", "stable_vanishing_relations"]
  },
  "color": {
    "families": {
      "night_exteriors": ["deep_blue", "charcoal", "cold_concrete_grey"],
      "neon_accents": ["magenta", "cyan", "amber", "toxic_green"],
      "human_interiors": ["warm_sodium", "practical_warm"]
    },
    "rules": [
      "sparse_neon_accents",
      "stable_skin_under_spill",
      "sequence_color_script_before_mass_gen",
      "lock_character_local_colors",
      "avoid_default_modern_teal_orange"
    ],
    "accent_cap_per_sequence": {"min": 2, "max": 4}
  },
  "dialogue_workflow": {
    "order": [
      "script_with_timing",
      "voice_bed",
      "animation_to_track",
      "anime_mouth_charts",
      "subtitle_stems"
    ],
    "mouth_shapes": ["A", "I", "U", "E", "O", "closed", "special"]
  },
  "mechanical_density": {
    "priorities": ["silhouette_first", "engineered_greebles", "controlled_wear"],
    "subjects": ["vehicles", "city_infrastructure", "military_hardware", "motorbikes"],
    "qc": "density_without_silhouette_is_fail"
  },
  "fx": {
    "speed_lines": {"directional": true, "protect_faces_on_emotion_beats": true},
    "explosions": ["flash", "fireball", "smoke", "debris_arc", "residual_dust"],
    "energy": {"graphic_core": true, "soft_glow": "careful", "bg_warp": "rare"},
    "impacts": {"prefer_on_ones": true, "align_to_audio": true}
  },
  "finish": {
    "grain": "fine_optional",
    "bloom_on_neon": "mild",
    "black_levels": "deep_not_crushed",
    "composite_order": [
      "bg",
      "characters",
      "shadows",
      "fx",
      "overlays",
      "grade",
      "grain"
    ]
  },
  "gates": {
    "clip_20_60s": ["line_language", "timing_mix", "cel_separation", "palette"],
    "short_3_5m": ["camera_language", "fx_peaks", "dialogue_timing", "identity"],
    "ova_15_25m": ["sequence_color_scripts", "dense_bg", "low_style_regression"],
    "feature_path": ["cost_model", "critic_pass_rates"]
  },
  "style_score_axes_v0": [
    {"id": "line_cel", "weight": 1},
    {"id": "timing", "weight": 1},
    {"id": "palette", "weight": 1},
    {"id": "mechanical_bg_density", "weight": 1},
    {"id": "fx_peaks", "weight": 1},
    {"id": "finish", "weight": 1}
  ],
  "promote_thresholds_v0": {
    "clip_min_avg": 3.5,
    "public_demo_min_avg": 4.0,
    "scale": [1, 5]
  },
  "anti_patterns": [
    "plastic_3d_skin_no_line",
    "infinite_handheld_shake",
    "neon_rainbow_noise",
    "copyrighted_character_likeness",
    "single_layer_no_intermediates",
    "morphing_faces_as_acting"
  ],
  "agent_hooks": {
    "director": ["sections_camera_fx_color"],
    "colorist": ["color_script_first"],
    "key_animator": ["ones_twos_table"],
    "critics": ["gates_and_score_axes"]
  }
}


```

---


# Sources - AkiraForge Style Bible v0.1.0

**License:** CC-BY-4.0  
**Note:** Technique and production-history references. No copyrighted frames scraped or redistributed. Spot-check claims against cited public materials.

## Production literature and interviews (public)

1. **Anime production process overviews (studio pipeline literacy)**  
   - Industry explainers on key animation, inbetweens, timing sheets (x-sheets), and photography/compositing stages used across Japanese TV and theatrical pipelines.  
   - Useful for: ones/twos vocabulary, cel layer order, pre-score dialogue culture.

2. **Multiplane / multi-layer camera tradition**  
   - Public animation textbooks and museum/education materials describing multiplane camera history (Disney multiplane lineage and later digital multiplane equivalents in anime digital composite eras).  
   - Useful for: parallax layer speed differentials, truck-in depth.

3. **Limited vs full animation discourse**  
   - Academic and practitioner writing on limited animation (cost-driven holds, iconic poses) versus prestige theatrical budgets with higher key counts.  
   - Useful for: gate that "theatrical bar" is denser timing and BG investment, not infinite ones.

4. **Anime mouth charts and lip-sync practice**  
   - Public animation education resources documenting simplified vowel mouth shapes (A I U E O) common in Japanese commercial animation.  
   - Useful for: dialogue workflow section; anti-photoreal viseme default.

5. **Color script practice (feature animation)**  
   - Feature animation art books and public production blogs describing sequence color scripts before shot production.  
   - Useful for: palette discipline, neon-as-accent rule.

6. **Effects animation craft notes**  
   - Practitioner talks and breakdown essays on FX animation (smoke cycles, explosion phases, speed lines as graphic devices).  
   - Useful for: FX phase lists and impact-on-ones guidance.

7. **Late-1980s theatrical cyberpunk design language (historical context)**  
   - Public film history writing on late-80s Japanese theatrical anime production scale (large key teams, dense mechanical design, cityscape labor).  
   - Useful for: mechanical density and BG clutter expectations as a quality bar.  
   - **Not used as:** a license to copy protected characters, story beats, or frames.

## Method notes

- Claims are **technique-level** (how work was done), not shot-for-shot recreations.  
- Proprietary studio color lists (e.g. any specific film's exact paint codes) are **not** reproduced; AkiraForge uses original palette families.  
- Peer reviewers: pick any 5 technique claims in `style-bible.md` and verify vocabulary against public animation production glossaries (ones/twos, x-sheet, multiplane, color script, mouth chart).

## Suggested public glossaries for peer check

- Animation timing: "drawing on ones / twos"  
- Pipeline: key animation (genga), inbetweens (douga), background art, compositing  
- Camera: multiplane / multi-layer parallax  
- Performance: anticipation, follow-through, overlapping action (classic principles still taught in anime schools)

## Change log

- 2026-08-06: v0.1.0 initial AkiraForge contribution for GrokForge leaf "Write 1988 theatrical anime style bible (MD+JSON)".
