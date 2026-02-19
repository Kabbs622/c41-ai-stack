# C41 Cinema — Local AI Stack

Research, planning, and architecture documents for building a fully local AI content production setup for C41 Cinema.

**Goal**: Zero cloud subscriptions. Everything on your hardware. One interface (OpenClaw on Discord).

---

## 🎯 Quick Reference for AI Agents

| Document | Purpose | Read This When... |
|----------|---------|-------------------|
| `c41-full-local-stack.md` | Complete architecture | You need the full picture |
| `c41-model-recommendations.md` | Curated model picks | Choosing which models to run |
| `openclaw-local-setup-plan.md` | Integration plan | Setting up OpenClaw skills |
| `hardware-research.md` | Hardware options | Buying/building the machine |
| `deep-practitioner-research.md` | Real-world usage | Validating approaches |

---

## Key Documents

### The Stack (Start Here)
- **[c41-full-local-stack.md](c41-full-local-stack.md)** — The final plan. Full local stack, zero cloud, managed through OpenClaw. Hardware options, software architecture, ROI pitch.

### Model Selection
- **[c41-model-recommendations.md](c41-model-recommendations.md)** — Focused recommendations by use case (LLM, code, photo, video, audio).
- **[ai-models-complete-comparison.md](ai-models-complete-comparison.md)** — Every notable AI model compared. Cloud and local. Image, video, LLM.
- **[local-ai-best-models-unconstrained.md](local-ai-best-models-unconstrained.md)** — Best models with no hardware constraints. Frontier MoE builds.
- **[local-ai-models-deep-dive.md](local-ai-models-deep-dive.md)** — 30+ source deep dive. Practitioner workflows, LoRA training, benchmarks.
- **[local-ai-models-first.md](local-ai-models-first.md)** — Models-first analysis with VRAM tables.

### Research
- **[deep-practitioner-research.md](deep-practitioner-research.md)** — Real practitioner quotes from Reddit/GitHub. What people actually use in production.
- **[hardware-research.md](hardware-research.md)** — GPU options, Mac vs PC, build recommendations.

### Planning
- **[openclaw-local-setup-plan.md](openclaw-local-setup-plan.md)** — How OpenClaw orchestrates the full stack.

---

## Architecture Overview

```
┌─────────────────────────────────┐     ┌─────────────────────────────────┐
│     MAC STUDIO                  │     │     NVIDIA WORKSTATION          │
│     M3 Ultra 512GB             │     │     RTX PRO 6000 96GB          │
│                                 │     │                                 │
│  ✦ OpenClaw (orchestration)     │     │  ✦ ComfyUI (all generation)    │
│  ✦ Ollama (LLMs, always on)    │     │    - Image gen                 │
│  ✦ Open WebUI (multi-user)     │     │    - Video gen                 │
│  ✦ TTS (Qwen3-TTS)             │     │    - Lip-sync                  │
│  ✦ Light compute               │     │    - Upscaling                 │
│                                 │     │    - LoRA training             │
└─────────────────────────────────┘     └─────────────────────────────────┘
         │                                         │
         └──────────── Local Network ──────────────┘
                         │
                    You (Discord)
```

### The Split
- **Mac Studio (512GB)**: LLM inference, orchestration, always-on services
- **NVIDIA Workstation (96GB)**: Generation (image/video/audio), training

---

## The Models

### Brain (LLMs)
| Model | VRAM | Speed | Use Case |
|-------|------|-------|----------|
| Qwen3-Coder-Next | 15GB | Fast | Daily driver, coding, writing |
| Qwen3-235B-A22B | 70GB | Medium | Hard problems, frontier reasoning |
| Kimi K2-0905 | ~500GB | Slower | Best creative writing |

### Eyes (Image Gen)
| Model | VRAM | Use Case |
|-------|------|----------|
| Flux 2 Dev | 24GB | Best prompt adherence, brand consistency |
| Z-Image | 16-24GB | Best photorealism, human bodies |
| Wan 2.2 14B | 16-48GB | Video generation, lip-sync |

### Motion (Video)
| Model | VRAM | Use Case |
|-------|------|----------|
| Wan 2.2 14B (i2v) | 24-48GB | Image-to-video, being used commercially |
| FramePack | 6GB+ | Long-form (1 min+), constant VRAM |
| HuMo | 68GB | Best lip-sync quality |

---

## Status

- [x] Model research (all categories)
- [x] Practitioner research (real-world usage)
- [x] Stack architecture
- [x] Hardware recommendations
- [ ] Pitch document for investor
- [ ] OpenClaw skills (ComfyUI integration)
- [ ] ComfyUI workflow presets

---

## Important Notes for AI Agents

1. **This is research/planning only** - No code to run here
2. **Dual-machine setup** - Mac + PC working together
3. **OpenClaw is the interface** - User talks on Discord, OpenClaw routes to tools
4. **ComfyUI for generation** - All image/video goes through ComfyUI workflows
5. **Local only** - Zero cloud dependencies in final architecture

---

## Questions for Kyle

1. What's the budget ceiling for hardware?
2. Is the Mac Studio M3 Ultra 512GB purchased yet?
3. Timeline for building out the PC workstation?
4. Priority: LLM first, or image/video generation first?
5. Should I start building OpenClaw skills for Ollama integration?

---

*Research and planning for C41 Cinema's local AI infrastructure.*
