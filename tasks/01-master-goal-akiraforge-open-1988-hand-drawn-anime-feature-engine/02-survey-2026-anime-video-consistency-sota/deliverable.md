# AkiraForge contribution: Survey 2026 anime video + consistency SOTA
Contributor: @SuddenlyJon (agent-assisted)
Task ID: `cmsi8jx89000bvj8w9b525e5w`
Project: AkiraForge - Open 1988 Hand-Drawn Anime Feature Engine

## Acceptance checklist

- [x] sota-survey.md covering at least 8 tool families
- [x] integration-matrix.csv or JSON (tool x stage x fit)
- [x] gap-analysis section with top 5 unsolved problems for long-form
- [x] failure-mode catalog with examples described textually
- [x] sources cited; license headers present

## Legal footer

```
License: CC-BY-4.0 (docs); MIT (code samples)
Project: AkiraForge
Rails: original works only; style approximation; no unlicensed copyrighted training data; no API keys in repo
```

---

## File: `README.md`

# Leaf: Survey 2026 anime video + consistency SOTA

**Status:** Ready for peer review  
**License:** CC-BY-4.0 (report/docs); MIT (any code samples)  
**Task ID:** `cmsi8jx89000bvj8w9b525e5w`  
**Project:** AkiraForge - Open 1988 Hand-Drawn Anime Feature Engine

## Files

| File | Role |
|------|------|
| `sota-survey.md` | Full survey of 8+ tool families |
| `integration-matrix.csv` | tool x stage x fit matrix |
| `integration-matrix.json` | Same matrix as JSON |
| `gap-analysis.md` | Top 5 unsolved long-form problems |
| `failure-mode-catalog.md` | Textual failure modes (no pirated frames) |
| `sources.md` | Public citations + peer-check path |
| `SUBMIT-BODY.md` | Single-file GrokForge API submission |

## Acceptance checklist

- [x] sota-survey.md covering at least 8 tool families
- [x] integration-matrix.csv or JSON (tool x stage x fit)
- [x] gap-analysis section with top 5 unsolved problems for long-form
- [x] failure-mode catalog with examples described textually
- [x] sources cited; license headers present

## Footer

```
License: CC-BY-4.0 (docs); MIT (code samples)
Project: AkiraForge
Rails: original works only; style approximation; no unlicensed copyrighted training data; no API keys in repo
```

---

## File: `sota-survey.md`

# 2026 SOTA survey: anime-capable video generation and consistency tools

**License:** CC-BY-4.0  
**Project:** AkiraForge - Open 1988 Hand-Drawn Anime Feature Engine  
**Version:** 0.1.0  
**Date:** 2026-08-08  
**Disclaimer:** Planning and integration survey only. Product APIs, pricing, and model cards change. Prefer primary docs before budgeting. Style approximation of late-1980s theatrical hand-drawn technique; original characters and stories only. No unlicensed copyrighted training frames.

## Scope

This survey maps tool **families** usable by an open multi-agent studio orchestrator that targets:

1. Gate-1: 20-60s identity-stable clips  
2. Gate-2: 3-5 min narrative shorts  
3. Gate-3: 15-25 min OVA-scale experiments  
4. Gate-4: costed 90-120 min feature path  

Focus: anime-capable video, character consistency, layout control, open composability. Not a product endorsement ranking.

---

## Tool family 1: Commercial text/image-to-video foundation models

**Examples (class):** Runway Gen-class, Luma Dream Machine / Ray-class, Kling, Pika, Hailuo/MiniMax video, OpenAI Sora-class, Google Veo-class, xAI Imagine video-class endpoints.

**Strengths**

- Highest single-shot motion and lighting fidelity for short clips
- Fast iteration for concept boards and camera language tests
- Strong when given clear reference stills + motion prompts

**Failure modes**

- Identity drift across cuts without hard reference locking
- Style collapse toward "generic 3D anime filter" rather than 1988 cel language
- Temporal breaks at 4-10s boundaries when stitching clips
- ToS / training-data opacity for public-goods research reuse

**License / ops notes**

- Proprietary APIs; outputs often licensed for commercial use under vendor ToS
- Never store vendor API keys in the AkiraForge repo or GrokForge board
- Good for human-gated previews; weak as sole long-form backbone

**Integration point**

- Optional **ShotRenderAdapter** behind a provider interface
- Only promote clips that pass Identity + Style critics
- Prefer export of stills/keys for open in-betweening rather than full-feature dependence

---

## Tool family 2: Open diffusion video stacks (local / self-host)

**Examples (class):** AnimateDiff / AnimateDiff-Evolved lineages, CogVideoX-class open weights, HunyuanVideo-class, Wan/open video checkpoints, SVD-XT descendants, ComfyUI video node graphs.

**Strengths**

- Full inspectability; node graphs are claimable modules
- Can pin seeds, ControlNets, and local LoRAs for character adapters
- Offline CI dry-runs possible with mock/tiny models

**Failure modes**

- VRAM walls for theatrical resolution and long clips
- Flicker, morphing backgrounds, hand/prop corruption
- Weak multi-shot continuity without external memory

**License notes**

- Mixed: Apache/MIT code wrappers + model-specific weights licenses
- Always record model card license in asset manifest

**Integration point**

- Default **local render path** for reproducible public demos
- ComfyUI workflow JSON as leaf artifacts

---

## Tool family 3: ComfyUI ecosystem + custom nodes

**Examples (class):** ComfyUI core, Impact Pack, Advanced ControlNet, IPAdapter Plus-class nodes, Video Helper Suite, anime upscale/restore nodes.

**Strengths**

- De facto open studio whiteboard: every stage is a graph
- Easy peer review (diff the workflow JSON)
- Bridges open weights and commercial adapters when needed

**Failure modes**

- Graph sprawl; unreproducible without pinned node versions
- "Works on my machine" without lockfiles
- Hidden paid custom nodes break public rebuild

**License notes**

- Prefer MIT/Apache nodes only in accepted leaves
- Pin `custom_nodes` git SHAs in rebuild.md

**Integration point**

- Primary **inspectable pipeline surface** for Key Animator / Inbetweener / Colorist stages

---

## Tool family 4: Identity and reference locking (IP-Adapter / ReferenceNet / InstantID-class)

**Examples (class):** IP-Adapter (plus face/style variants), ReferenceNet-style dual-UNet, InstantID / PuLID-class ID adapters, FaceID, character embedding caches.

**Strengths**

- Best open tools for keeping a face/outfit recognizable
- Composable with ControlNet pose/depth/lineart
- Supports multi-view model sheets as reference packs

**Failure modes**

- Over-locking freezes performance and mouth shapes
- Outfit/color drift while face holds
- Cross-shot lighting mismatch still reads as "different person"

**License notes**

- Research code often Apache-2.0; check weight licenses

**Integration point**

- **Character Consistency Lock** pipeline v0 core
- Story Bible `identity_tokens` + reference pack paths feed this stage

---

## Tool family 5: Spatial / motion control (ControlNet-class + pose + lineart)

**Examples (class):** ControlNet OpenPose, Depth, SoftEdge/Lineart, Canny; DWPose; AnimateAnyone / MimicMotion-class human motion transfer; camera trajectory controls.

**Strengths**

- Layout and acting can be storyboard-driven
- Lineart/depth preserve 1988 "drawn structure" better than pure T2V
- Enables multiplane-like parallax when layered

**Failure modes**

- Pose estimator fails on extreme foreshortening / mecha
- Lineart over-constrains and kills squash/stretch
- Motion transfer from live video can fight anime timing (ones/twos)

**License notes**

- ControlNet weights and pose estimators: mixed research licenses

**Integration point**

- Storyboard + Layout -> Key Animator handoff
- Prefer anime lineart/edge over photo-only pose for style fidelity

---

## Tool family 6: Character LoRAs / adapters / DreamBooth-class fine-tunes

**Examples (class):** LoRA / LoCon / LyCORIS on SDXL or anime bases; per-character adapters; style LoRAs trained on original model sheets.

**Strengths**

- Strongest open identity for a *single* original character pack
- Small files; versionable in git-lfs or release assets
- Can separate style LoRA vs character LoRA

**Failure modes**

- Overfit: character only works in training poses
- Style LoRA fights character LoRA (color bleed, eye shape wars)
- Illegal if trained on copyrighted frames (FORBIDDEN under AkiraForge rails)

**License notes**

- Training data must be original / licensed / synthetic only
- Publish training card: data sources, steps, base model, trigger words

**Integration point**

- Character Designer stage outputs: multi-view sheets + LoRA training recipe (original IP only)

---

## Tool family 7: Multi-agent anime / studio orchestration systems

**Examples (class):** LangGraph / CrewAI / AutoGen-style graphs; academic multi-agent animation pipelines; custom function-calling studio routers; storyboard-to-shot agent swarms.

**Strengths**

- Maps cleanly to AkiraForge roster (Director, Script, Board, Keys, Critics)
- Quality gates as explicit graph nodes
- Human-in-the-loop checkpoints between gates

**Failure modes**

- Agent chatter without asset contracts
- Silent gate bypass ("looks fine" without metrics)
- Cost blowups from retry loops

**License notes**

- Prefer MIT/Apache orchestrator code in accepted leaves

**Integration point**

- Top-level **Orchestrator + quality gate routing** leaf
- Agents communicate via Story Bible JSON + asset URIs, not free chat only

---

## Tool family 8: Upscale, restore, and temporal stabilization

**Examples (class):** Real-ESRGAN / anime-focused upscalers, CodeFormer/GFPGAN-class face restore (use carefully), RIFE / film interpolation, temporal-aware VFI, Deflicker tools, optical-flow smoothers.

**Strengths**

- Turns workable 720p keys into presentable finals
- Interpolation can fake higher frame density (with risk)
- Deflicker recovers some "cel stable" look

**Failure modes**

- Face restore plasticizes 1988 line weight
- Interpolation creates mushy motion (anti-theatrical)
- Upscale invents background texture not in boards

**License notes**

- Mostly open research weights; document versions

**Integration point**

- Compositor / finishing stage only after critic PASS on identity
- Style bible rule: prefer intentional limited animation over smooth VFI soup

---

## Tool family 9 (bonus): Audio, lip-sync, and editorial

**Examples (class):** Open TTS (Piper, Coqui-class), RVC for voice design (original VA only), Wav2Lip / MuseTalk-class lip sync, Resolve/OSS NLEs, forced-aligners for dialogue timing.

**Strengths**

- Completes Gate-2 narrative short requirements
- Lip-sync is a hard QC surface (easy fail)

**Failure modes**

- Lip-sync fights anime mouth charts (A/I/U/E/O)
- Music copyright if not original / CC

**Integration point**

- Sound Director + Voice Director agents after picture lock candidates

---

## Recommended integration matrix (summary)

See `integration-matrix.csv` / `.json` for full scoring. Narrative defaults:

| Stage | Primary open path | Optional commercial assist |
|-------|-------------------|----------------------------|
| Concept / stills | Local SDXL-anime + ControlNet | Image API stills |
| Character lock | IP-Adapter + LoRA + multi-view sheets | Reference image to video API |
| Motion keys | ComfyUI video + pose/lineart | Short T2V for camera tests |
| Inbetweens | Open video + RIFE sparingly | - |
| QC | Automated critics + human blind rescore | - |
| Audio | Open TTS + original score | Paid TTS if licensed |
| Orchestration | LangGraph/custom MIT graph | - |

## Progressive gate fit

| Gate | What SOTA can do today (honest) | What still needs lab work |
|------|----------------------------------|---------------------------|
| 20-60s clip | Achievable with lock + critics | Style fidelity vs 1988 bible |
| 3-5 min short | Achievable with heavy human gates | Outfit/prop continuity |
| 15-25 min OVA | Research path only | Memory + cost + identity |
| 90-120 min feature | Costed plan only, not "ready" | All of the above at scale |

## Cross-cutting recommendations for AkiraForge

1. **Open-first, API-optional:** public rebuilds must not require paid keys.
2. **Story Bible is memory:** every shot reads identity packs from schema, not chat.
3. **Critics before promotion:** clip -> short -> OVA is fail-closed.
4. **Original IP only:** no copyrighted title clones; no unlicensed frame training.
5. **Pin versions:** workflows without SHAs are not accept-grade.

---

## File: `integration-matrix.csv`

tool_family,stage_concept,stage_character_lock,stage_storyboard_layout,stage_key_animation,stage_inbetween,stage_background,stage_fx,stage_color,stage_composite,stage_audio,stage_qc,stage_orchestrate,fit_gate1_30s,fit_gate2_5m,fit_gate3_20m,fit_gate4_feature,openness,reproducibility,style_1988_fit,identity_fit,notes
commercial_t2v_i2v,3,2,2,4,3,2,3,2,3,1,1,1,4,2,1,1,1,2,2,2,Great short motion; weak long-form identity; proprietary
open_diffusion_video,3,3,3,4,4,3,3,3,3,1,2,2,4,3,2,1,4,4,3,3,Default public render path when VRAM allows
comfyui_ecosystem,5,5,5,5,5,5,5,5,5,3,4,4,5,4,3,2,5,5,4,4,Primary inspectable studio whiteboard
ip_adapter_referencenet_id,2,5,3,4,3,1,1,2,2,0,4,2,5,4,3,2,4,4,3,5,Core of consistency lock v0
controlnet_pose_lineart_depth,3,4,5,5,4,4,3,3,3,0,3,2,5,4,3,2,4,4,5,4,Preserves drawn structure / layout intent
character_lora_adapters,2,5,2,4,3,1,1,2,2,0,3,2,5,4,3,2,4,3,4,5,Original-IP training only; version training cards
multi_agent_orchestrators,4,3,4,3,3,3,3,3,3,3,5,5,4,4,3,2,5,4,3,3,Quality gates as first-class nodes
upscale_temporal_stabilize,1,1,1,2,3,3,2,3,5,0,3,1,4,3,2,2,4,4,2,2,Finishing only after identity PASS; avoid plastic restore
audio_lipsync_editorial,1,1,2,1,1,1,1,1,2,5,4,2,3,5,3,2,4,4,3,2,Required for Gate-2 narrative shorts

---

## File: `integration-matrix.json`

{
  "$schema_note": "AkiraForge integration matrix v0.1.0 - scores 0-5",
  "license": "CC-BY-4.0",
  "project": "AkiraForge - Open 1988 Hand-Drawn Anime Feature Engine",
  "score_legend": {
    "0": "Not applicable / harmful if forced",
    "1": "Poor fit",
    "2": "Weak assist only",
    "3": "Usable with heavy human gate",
    "4": "Strong recommended path",
    "5": "Primary recommended path for open studio"
  },
  "stages": [
    "concept",
    "character_lock",
    "storyboard_layout",
    "key_animation",
    "inbetween",
    "background",
    "fx",
    "color",
    "composite",
    "audio",
    "qc",
    "orchestrate"
  ],
  "tool_families": [
    {
      "id": "commercial_t2v_i2v",
      "name": "Commercial text/image-to-video foundation models",
      "scores": {
        "concept": 3,
        "character_lock": 2,
        "storyboard_layout": 2,
        "key_animation": 4,
        "inbetween": 3,
        "background": 2,
        "fx": 3,
        "color": 2,
        "composite": 3,
        "audio": 1,
        "qc": 1,
        "orchestrate": 1
      },
      "gate_fit": { "gate1_30s": 4, "gate2_5m": 2, "gate3_20m": 1, "gate4_feature": 1 },
      "openness": 1,
      "reproducibility": 2,
      "style_1988_fit": 2,
      "identity_fit": 2,
      "notes": "Great short motion; weak long-form identity; proprietary; never store API keys in repo"
    },
    {
      "id": "open_diffusion_video",
      "name": "Open diffusion video stacks",
      "scores": {
        "concept": 3,
        "character_lock": 3,
        "storyboard_layout": 3,
        "key_animation": 4,
        "inbetween": 4,
        "background": 3,
        "fx": 3,
        "color": 3,
        "composite": 3,
        "audio": 1,
        "qc": 2,
        "orchestrate": 2
      },
      "gate_fit": { "gate1_30s": 4, "gate2_5m": 3, "gate3_20m": 2, "gate4_feature": 1 },
      "openness": 4,
      "reproducibility": 4,
      "style_1988_fit": 3,
      "identity_fit": 3,
      "notes": "Default public render path when VRAM allows"
    },
    {
      "id": "comfyui_ecosystem",
      "name": "ComfyUI ecosystem + custom nodes",
      "scores": {
        "concept": 5,
        "character_lock": 5,
        "storyboard_layout": 5,
        "key_animation": 5,
        "inbetween": 5,
        "background": 5,
        "fx": 5,
        "color": 5,
        "composite": 5,
        "audio": 3,
        "qc": 4,
        "orchestrate": 4
      },
      "gate_fit": { "gate1_30s": 5, "gate2_5m": 4, "gate3_20m": 3, "gate4_feature": 2 },
      "openness": 5,
      "reproducibility": 5,
      "style_1988_fit": 4,
      "identity_fit": 4,
      "notes": "Primary inspectable studio whiteboard; pin node SHAs"
    },
    {
      "id": "ip_adapter_referencenet_id",
      "name": "IP-Adapter / ReferenceNet / InstantID-class identity lock",
      "scores": {
        "concept": 2,
        "character_lock": 5,
        "storyboard_layout": 3,
        "key_animation": 4,
        "inbetween": 3,
        "background": 1,
        "fx": 1,
        "color": 2,
        "composite": 2,
        "audio": 0,
        "qc": 4,
        "orchestrate": 2
      },
      "gate_fit": { "gate1_30s": 5, "gate2_5m": 4, "gate3_20m": 3, "gate4_feature": 2 },
      "openness": 4,
      "reproducibility": 4,
      "style_1988_fit": 3,
      "identity_fit": 5,
      "notes": "Core of consistency lock v0"
    },
    {
      "id": "controlnet_pose_lineart_depth",
      "name": "ControlNet-class pose / lineart / depth",
      "scores": {
        "concept": 3,
        "character_lock": 4,
        "storyboard_layout": 5,
        "key_animation": 5,
        "inbetween": 4,
        "background": 4,
        "fx": 3,
        "color": 3,
        "composite": 3,
        "audio": 0,
        "qc": 3,
        "orchestrate": 2
      },
      "gate_fit": { "gate1_30s": 5, "gate2_5m": 4, "gate3_20m": 3, "gate4_feature": 2 },
      "openness": 4,
      "reproducibility": 4,
      "style_1988_fit": 5,
      "identity_fit": 4,
      "notes": "Preserves drawn structure and multiplane layout intent"
    },
    {
      "id": "character_lora_adapters",
      "name": "Character LoRAs / adapters (original IP only)",
      "scores": {
        "concept": 2,
        "character_lock": 5,
        "storyboard_layout": 2,
        "key_animation": 4,
        "inbetween": 3,
        "background": 1,
        "fx": 1,
        "color": 2,
        "composite": 2,
        "audio": 0,
        "qc": 3,
        "orchestrate": 2
      },
      "gate_fit": { "gate1_30s": 5, "gate2_5m": 4, "gate3_20m": 3, "gate4_feature": 2 },
      "openness": 4,
      "reproducibility": 3,
      "style_1988_fit": 4,
      "identity_fit": 5,
      "notes": "FORBIDDEN to train on unlicensed copyrighted frames"
    },
    {
      "id": "multi_agent_orchestrators",
      "name": "Multi-agent studio orchestrators",
      "scores": {
        "concept": 4,
        "character_lock": 3,
        "storyboard_layout": 4,
        "key_animation": 3,
        "inbetween": 3,
        "background": 3,
        "fx": 3,
        "color": 3,
        "composite": 3,
        "audio": 3,
        "qc": 5,
        "orchestrate": 5
      },
      "gate_fit": { "gate1_30s": 4, "gate2_5m": 4, "gate3_20m": 3, "gate4_feature": 2 },
      "openness": 5,
      "reproducibility": 4,
      "style_1988_fit": 3,
      "identity_fit": 3,
      "notes": "Quality gates as explicit nodes; fail-closed promotion"
    },
    {
      "id": "upscale_temporal_stabilize",
      "name": "Upscale / restore / temporal stabilize",
      "scores": {
        "concept": 1,
        "character_lock": 1,
        "storyboard_layout": 1,
        "key_animation": 2,
        "inbetween": 3,
        "background": 3,
        "fx": 2,
        "color": 3,
        "composite": 5,
        "audio": 0,
        "qc": 3,
        "orchestrate": 1
      },
      "gate_fit": { "gate1_30s": 4, "gate2_5m": 3, "gate3_20m": 2, "gate4_feature": 2 },
      "openness": 4,
      "reproducibility": 4,
      "style_1988_fit": 2,
      "identity_fit": 2,
      "notes": "Finishing only after identity PASS; avoid plastic face restore"
    },
    {
      "id": "audio_lipsync_editorial",
      "name": "Audio, lip-sync, editorial",
      "scores": {
        "concept": 1,
        "character_lock": 1,
        "storyboard_layout": 2,
        "key_animation": 1,
        "inbetween": 1,
        "background": 1,
        "fx": 1,
        "color": 1,
        "composite": 2,
        "audio": 5,
        "qc": 4,
        "orchestrate": 2
      },
      "gate_fit": { "gate1_30s": 3, "gate2_5m": 5, "gate3_20m": 3, "gate4_feature": 2 },
      "openness": 4,
      "reproducibility": 4,
      "style_1988_fit": 3,
      "identity_fit": 2,
      "notes": "Required for Gate-2; original music and VO only"
    }
  ]
}

---

## File: `gap-analysis.md`

# Gap analysis: top unsolved problems for long-form open anime studio

**License:** CC-BY-4.0  
**Project:** AkiraForge  
**Version:** 0.1.0  
**Date:** 2026-08-08

## Method

Gaps are ranked by impact on progressive gates (30s -> 5m -> 20m -> 105m) and by whether open tooling can close them without proprietary lock-in.

---

## Top 5 unsolved problems

### 1. Multi-shot character identity under costume, lighting, and aging changes

**Symptom:** Face holds in shot A; shot B has different eye ratio, hair mass, or costume color while "still the same name."

**Why unsolved:** Most ID adapters optimize for still or short clip similarity, not Story-Bible-level continuity across acts.

**AkiraForge implication:** Identity critic + multi-view sheets + per-costume reference packs must be mandatory promotion gates.

**Research direction:** Temporal identity memory tokens keyed by `character_id + costume_id + age_band`; fail closed on outfit delta.

### 2. Temporal coherence across edit boundaries (not just inside one generated clip)

**Symptom:** Each 4-8s generation looks fine; the cut sequence feels like different shows.

**Why unsolved:** Commercial and open video models optimize intra-clip optical flow, not inter-clip editorial continuity.

**AkiraForge implication:** Shot graph with shared seed banks, color scripts, and end-frame -> start-frame handoff rules.

**Research direction:** Explicit bridge frames and layout locks at every cut.

### 3. 1988 theatrical style fidelity (cel, multiplane, limited animation) vs modern "smooth anime filter"

**Symptom:** Outputs look like 2020s 3D-ish anime web shorts, not late-80s theatrical hand-drawn craft.

**Why unsolved:** Training distributions skew modern; limited animation and mechanical density are underrepresented as controllable axes.

**AkiraForge implication:** Style bible JSON must be machine-scored (line weight, palette, camera grammar), not prompt poetry only.

**Research direction:** Style classifiers trained only on original synthetic + licensed study stills (never unlicensed copyrighted frames).

### 4. Prop / location / VFX continuity at OVA and feature length

**Symptom:** Hero mecha panel lines morph; background architecture drifts; effect vocabulary resets each scene.

**Why unsolved:** Character face tools get research attention; props and sets do not.

**AkiraForge implication:** Continuity critic for costume/prop/location IDs from Story Bible.

**Research direction:** Asset bible with canonical orthographics for every recurring prop.

### 5. Cost-transparent long-form under open licenses without secret API dependence

**Symptom:** Impressive demos require paid APIs or 8x H100 clusters; public rebuild fails.

**Why unsolved:** Economics of retries + human QC dominate; open models still expensive at theatrical resolution.

**AkiraForge implication:** Cost model leaf + fail-closed retry budgets in orchestrator.

**Research direction:** Hierarchical generation (layout -> keys -> thrifty inbetweens) with explicit token/$ caps per gate.

---

## Secondary gaps (watch list)

| Gap | Gate first hit | Severity |
|-----|----------------|----------|
| Lip-sync vs anime mouth charts | Gate-2 | High |
| Hand / finger collapse in acting | Gate-1 | Medium |
| Audio stem rights / original score | Gate-2 | High (legal) |
| Human preference study logistics | All | Medium |
| Orchestrator agent thrash (cost) | Gate-2+ | High |

## What is NOT claimed unsolved (honest wins)

- Single beautiful 5-15s anime-like clip: largely solved commercially and increasingly open
- Pose-conditioned short action: usable with ControlNet-class stacks
- Multi-agent task routing: software-solved; quality is the hard part
- Publishing MIT/CC packs with peer review: GrokForge process works

## Recommended AkiraForge research order

1. Identity persistence techniques paper + lock pipeline v0 (this batch + next leaves)
2. QC critics with human re-score (this batch)
3. Orchestrator quality gates (next software leaf)
4. 30-60s style demo package (public proof)
5. Eval harness v0 (closes the loop)

## Footer

```
License: CC-BY-4.0
Project: AkiraForge
Rails: original works only; style approximation; no unlicensed copyrighted training data; no API keys in repo
```

---

## File: `failure-mode-catalog.md`

# Failure-mode catalog (textual; no pirated frames)

**License:** CC-BY-4.0  
**Project:** AkiraForge  
**Version:** 0.1.0  
**Date:** 2026-08-08

Describe failures so critics and humans can label them without reference to copyrighted screenshots.

---

## FM-01 Identity drift (face)

**Description:** Same character_id, different facial proportions across shots (eye spacing, jaw, hairline).

**Typical trigger:** New seed per shot; weak reference weight; costume change coupled with re-prompt.

**Detect:** Embedding distance vs model sheet; human A/B same-person question.

**Mitigation:** Hard reference pack; IP-Adapter/ID lock; reject on identity critic FAIL.

## FM-02 Identity drift (body / costume)

**Description:** Face holds; outfit colors, logos, silhouette change.

**Typical trigger:** Prompt mentions action more than wardrobe; no costume_id conditioning.

**Detect:** Color histogram + silhouette IoU vs costume sheet.

**Mitigation:** Per-costume reference images; continuity critic.

## FM-03 Temporal break (intra-clip)

**Description:** Mid-clip morph, teleporting limbs, background melt.

**Typical trigger:** Long single generation beyond model comfort; conflicting motion prompts.

**Detect:** Optical-flow spikes; frame-to-frame SSIM collapse.

**Mitigation:** Shorter clips; motion control; regenerate segment.

## FM-04 Temporal break (inter-clip / edit)

**Description:** Each clip OK; sequence feels like recast show.

**Typical trigger:** No end-frame handoff; inconsistent color script.

**Detect:** Human continuity pass; cross-shot identity metrics.

**Mitigation:** Bridge frames; shared LUT/color script; shot graph memory.

## FM-05 Style collapse (modern filter)

**Description:** Smooth 3D-ish shading, thin vector lines, TikTok pacing; not 1988 theatrical cel language.

**Typical trigger:** Default commercial video model style; heavy face restore; over-interpolation.

**Detect:** Style classifier vs style bible features; human style rubric.

**Mitigation:** Lineart control; limited animation timing; ban plastic restore on keys.

## FM-06 Style collapse (over-limited / slideshow)

**Description:** Almost no motion; holds feel unfinished rather than intentional anime timing.

**Typical trigger:** Too aggressive limited-animation prompt without keys.

**Detect:** Motion energy below floor for shot type (action vs dialogue).

**Mitigation:** Shot-type budgets in style bible; director review.

## FM-07 Hand / prop corruption

**Description:** Extra fingers, melting mechanical detail, floating props.

**Typical trigger:** Complex mecha or handheld objects without reference.

**Detect:** Human QC; optional keypoint sanity checks.

**Mitigation:** Prop orthographics; freeze prop layers; redraw keys.

## FM-08 Background architecture drift

**Description:** Recurring city/block geometry changes between angles.

**Typical trigger:** Pure generative BG each shot.

**Detect:** Layout comparison to master background pack.

**Mitigation:** Multiplane BG pack; camera moves over painted layers.

## FM-09 FX vocabulary reset

**Description:** Explosions, sparks, speed lines change language each scene.

**Typical trigger:** No FX bible; random model defaults.

**Detect:** FX sheet comparison; director notes.

**Mitigation:** Effects Artist pack with approved FX stills (original).

## FM-10 Lip-sync mismatch

**Description:** Mouth flaps ignore phonemes; dialogue unreadable.

**Typical trigger:** Photo lip-sync models on anime mouths.

**Detect:** Forced-aligner vs mouth chart; human intelligibility.

**Mitigation:** Anime mouth chart pipeline; retime to dialogue.

## FM-11 Over-lock / mannequin performance

**Description:** Identity perfect but acting dead; micro-expressions gone.

**Typical trigger:** Excessive ID weight; same reference every frame.

**Detect:** Human performance score; motion variance floor.

**Mitigation:** Dual-path: identity hold + performance ControlNet.

## FM-12 Upscale hallucination

**Description:** New background glyphs, jewelry, or scars invented at 4K.

**Typical trigger:** Aggressive ESRGAN on soft frames.

**Detect:** Diff vs pre-upscale at downsampled res.

**Mitigation:** Light upscale; denoise before upscale; reject novel high-freq detail on faces.

## FM-13 Illegal data path (process failure)

**Description:** Contributor trains on unlicensed copyrighted anime frames or ships title clones.

**Typical trigger:** "for quality" rationalization.

**Detect:** Legal rails checklist; dataset cards; human audit.

**Mitigation:** HARD FAIL; reject contribution; no exceptions.

## FM-14 Secret leakage (process failure)

**Description:** API keys or personal tokens in workflows or logs.

**Typical trigger:** Exporting ComfyUI env or shell history into pack.

**Detect:** Secret scan before accept.

**Mitigation:** HARD FAIL; rotate keys; scrub.

## Severity matrix

| ID | Gate-1 | Gate-2 | Gate-3+ | Auto-block? |
|----|--------|--------|---------|-------------|
| FM-01 | High | High | Critical | Yes |
| FM-02 | High | High | Critical | Yes |
| FM-03 | High | High | High | Yes |
| FM-04 | Med | High | Critical | Human+auto |
| FM-05 | High | High | High | Yes (style) |
| FM-06 | Med | Med | Med | Human |
| FM-07 | Med | High | High | Human |
| FM-08 | Low | High | Critical | Human+auto |
| FM-09 | Low | Med | High | Human |
| FM-10 | Low | High | High | Yes (if audio) |
| FM-11 | Med | Med | Med | Human |
| FM-12 | Med | Med | Med | Auto warn |
| FM-13 | Critical | Critical | Critical | HARD FAIL |
| FM-14 | Critical | Critical | Critical | HARD FAIL |

## Footer

```
License: CC-BY-4.0
Project: AkiraForge
Rails: original works only; style approximation; no unlicensed copyrighted training data; no API keys in repo
```

---

## File: `sources.md`

# Sources and peer-check path

**License:** CC-BY-4.0  
**Project:** AkiraForge  
**Version:** 0.1.0  
**Date:** 2026-08-08

## How to peer-check this survey

1. Pick 5 tool-family claims (e.g. "ComfyUI is inspectable", "ID adapters help face lock").
2. Open primary public docs or model cards for that family (not random social reposts).
3. Confirm strengths/failure modes are directionally correct as of your check date.
4. Note product renames: commercial video models rebrand often; score the *class*, not a transient SKU.
5. Reject any suggested training on unlicensed copyrighted frames.

## Source classes (public)

| Class | Examples of primary surfaces | Use |
|-------|------------------------------|-----|
| Model cards / GitHub READMEs | ComfyUI, ControlNet, IP-Adapter, AnimateDiff, open video repos | Openness, install, license |
| Vendor docs / ToS | Commercial video API docs | Capability claims, output rights, key handling |
| Research papers / arXiv | Identity adapters, temporal video, lip-sync | Failure modes, metrics |
| Community workflow knowledge | ComfyUI workflow sharing (careful with licenses) | Integration patterns |
| AkiraForge prior leaves | Style bible, legal rails, cost model, agent roster, Story Bible | Project-specific fit |

## Explicit non-sources

- Pirated frame dumps or "train on [famous film]" guides
- Leaked API keys or private Discord weight drops of unclear license
- Marketing posts without technical method

## Citation discipline for future edits

When updating SOTA rows:

- Add `checked_on` ISO date per family
- Link primary doc URL
- Keep textual failure examples (no copyrighted stills)

## Related AkiraForge artifacts

- Style bible: technique axes for FM-05 scoring
- Legal rails: FM-13 enforcement
- Cost model: FM economics / Gate-4 honesty
- Agent roster: which critic owns which FM

## Footer

```
License: CC-BY-4.0
Project: AkiraForge
Rails: original works only; style approximation; no unlicensed copyrighted training data; no API keys in repo
```
