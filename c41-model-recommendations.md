# C41 Cinema: The Right Models for Your Goals
*Focused recommendations across LLM/Assistant, Code, Photo, Video, and Audio*

---

## 1. LLM / ASSISTANT (Daily Operations)

**What you need:** Writing scripts, client emails, proposals, brainstorming, research, general chat. Needs to be fast, smart, and always available.

### The Pick: Cloud + Local Hybrid

**Primary (Cloud):** You're already on Claude Max. Keep using it. Opus 4 for complex stuff, Sonnet 4 for daily. Gemini 2.5 Pro for anything needing massive context (1M+ tokens, like reading entire scripts or contracts at once).

**Local Backup / Unlimited Use:**

| Model | Why | VRAM | Speed |
|-------|-----|------|-------|
| **Qwen3-235B-A22B** | Frontier-class. Competes with Claude Sonnet/Gemini Pro. Best bang-for-VRAM because MoE (only 22B active). Your "unlimited Claude" for when you don't want to burn API credits. | 70GB (Q4) | 31.5 tok/s on 256GB VRAM setup |
| **Qwen3-32B** | The reliable daily driver. Matches old 72B quality at half the size. Fast, 128K context. For quick chat, writing, client comms when you don't need frontier reasoning. | 24GB (Q5) | Very fast on single GPU |
| **DeepSeek V3.1** | The absolute best open model period. 685B MoE. When you need GPT-4/Opus-level thinking locally. | 256GB+ (multi-GPU) | 25 tok/s on $17K rig |

**What NOT to bother with for this use case:**
- Llama 4 Scout/Maverick: Good but Qwen3 beats them at equivalent sizes
- GPT-4o API: You already have Claude Max, don't pay twice
- Smaller models (7B-14B): Not smart enough for client-facing writing

**Verdict:** Claude Max (cloud) + Qwen3-235B or Qwen3-32B (local). You're covered.

---

## 2. CODE (Website/App Building)

**What you need:** Building websites, web apps, automation scripts, CameraMatch-style projects. Long coding sessions, understanding large codebases, debugging.

### The Pick: Specialized Coding Model

| Model | Why | VRAM | Speed |
|-------|-----|------|-------|
| **Qwen3-Coder-Next** | THE coding model. Purpose-built. 80B total but only 3B active (MoE + DeltaNet hybrid). 256K NATIVE context means it can read your entire codebase at once. Competes with Claude Sonnet for coding. Uses fewer tool calls than Claude, reads whole files. | 15GB VRAM + 30GB RAM (CPU MoE offload) | 16-18 tok/s on RTX 5060 Ti 16GB |
| **Qwen3-235B-A22B** | When coding tasks need more reasoning power (architecture decisions, complex debugging) | 70GB (Q4) | 31.5 tok/s |

**Cloud comparison:**
- Claude Sonnet 4 with Claude Code: Still the gold standard for coding agents
- Cursor/Windsurf: Great IDEs but you're paying per-query
- **Local advantage:** Qwen3-Coder-Next at 256K context means you can throw your entire project at it without token limits or costs

**Why not these:**
- CodeLlama / StarCoder 2: Superseded. Qwen3-Coder-Next is strictly better
- DeepSeek-Coder: Good but Qwen3-Coder-Next has longer context and better architecture
- GPT-4.1: Great at coding but you're paying per token

**Verdict:** Qwen3-Coder-Next locally (fits on modest hardware!) + Claude Sonnet 4 (cloud, via your Max sub) for the hardest problems.

---

## 3. PHOTO / IMAGE GENERATION

**What you need:** Client-ready ad photos, product shots, social media imagery, brand-consistent visuals. Has to look professional, not "AI-generated."

### The Pick: Multi-Model Pipeline

This is where the magic is. No single model does it all. Pros chain 3-4 models:

**Step 1 — Composition & Text** (what goes where, text rendering)
| Model | Why | VRAM | Speed |
|-------|-----|------|-------|
| **Qwen-Image** | Best prompt adherence of any open model. Renders text perfectly. Does generation + editing in one model. The "director" of your image pipeline. | 16-24GB (with Nunchaku) | 13 sec/image |

**Step 2 — Photorealism** (make it look real)
| Model | Why | VRAM | Speed |
|-------|-----|------|-------|
| **Wan 2.2 14B** (1-frame, low denoise i2i) | Feed Qwen-Image output through this at 0.2-0.5 denoise. Materials, lighting, skin texture all dramatically improve. "Like magic" according to practitioners. | 16GB (GGUF Q5) | ~42 sec/image |

**Step 3 — Upscale to 4K**
| Model | Why | VRAM |
|-------|-----|------|
| **SEEDVR2** | SOTA upscaler. 2x-4x with intelligent detail hallucination. | 24-48GB |

**Step 4 — Detail Fixes**
| Model | Why | VRAM |
|-------|-----|------|
| **HiDream-E1.1** | "Remove the watch. Change background to a coffee shop." Natural language image editing. | 16-24GB |

### For Brand Consistency (Product Shoots, Recurring Characters)

| Model | Why | VRAM |
|-------|-----|------|
| **Flux 2 Dev** | Feed it up to 10 reference images. It maintains character/style consistency WITHOUT fine-tuning. Feed it 3 product shots + a brand guide = consistent new shots. The 32B model just dropped. | 24GB (4-bit quant) |

### For Speed (Rapid Concepting, Mood Boards)

| Model | Why |
|-------|-----|
| **Flux 1 Schnell** | 4-step generation. Seconds per image. Apache license (commercial OK). |
| **SDXL + fast LoRA** | Biggest LoRA ecosystem. Thousands of styles on Civitai. |
| **Nunchaku** | Slap this on any Flux/Qwen model for 10x speedup. |

### Cloud Alternatives (When You Need It)

| Service | When to Use | Cost |
|---------|-------------|------|
| **Midjourney v6.1** | When artistic quality matters most. Editorial, creative direction. Still the aesthetic king. | $10-60/mo |
| **Ideogram 3** | When you need perfect typography/text in images | $8-48/mo |
| **DALL-E 3 / GPT-Image** | Quick marketing assets, strong text rendering | Pay per image |

**The honest truth:** For C41 Cinema client work, the Qwen-Image > Wan 2.2 > SEEDVR2 pipeline produces output that's indistinguishable from the cloud options. The local pipeline wins on: unlimited generations, full control, client privacy, and the ability to train custom LoRAs for brand consistency.

**Verdict:** Qwen-Image > Wan 2.2 i2i > SEEDVR2 pipeline (primary). Flux 2 Dev for multi-reference brand work. Midjourney subscription as a creative backup.

---

## 4. VIDEO GENERATION

**What you need:** Social media ads, branded content, commercial clips. Realistic motion, consistent characters, professional quality. 5-30 second clips minimum.

### The Pick: Wan 2.2 Ecosystem

There's really only one answer for local video gen right now:

| Model | Role | VRAM | Speed |
|-------|------|------|-------|
| **Wan 2.2 14B** | Primary generator. i2v (image-to-video) is the money mode. The 1,766-upvote Reddit realism post proved it: subtle mouth movements, eye rolls, focus shifts, zero film grain. "Looks better than Runway." | 24-48GB (full quality), 8GB (LightX2V) | Minutes per clip on 4090 |
| **Wan 2.2 VACE** | Video editing: inpainting, outpainting, reference-based generation. Swiss Army knife. | Similar to base | Similar |
| **FramePack** | Long-form (1 min+). Constant VRAM regardless of length. Chain renders for longer content. | 6GB minimum | 1.5 sec/frame (4090 + TeaCache) |
| **SEEDVR2** | Upscale 720p/1080p generations to 4K | 48-96GB (this is the bottleneck) | Slow but worth it |

### The Practitioner Workflow (From That 1,766-Upvote Post)
1. Generate starting images at 60 steps, upscale to 4K with SEEDVR2
2. Wan 2.2 i2v at 1536x864, noise ratio 65-75%
3. 6-8 low-noise steps with speed-up LoRA (Self-Forcing/CausVid)
4. Sigma 0.9 for i2v
5. Verbose prompts: describe EVERYTHING, leave nothing to hallucinate
6. Post-process in Premiere Pro/After Effects
7. Add film grain and color grade in POST (not in generation)

### Speed-Up Stack
| Tool | What It Does | Speedup |
|------|-------------|---------|
| **TeaCache** | Caches redundant computation | ~2x |
| **Self-Forcing / CausVid LoRAs** | Reduce denoising steps | ~2-3x |
| **CFG-Zero*** | Better guidance, same speed | Quality boost |
| **LightX2V** | Runs Wan on 8GB VRAM | Accessibility |
| **Nunchaku** | Being extended from image to video | TBD |

### The Commercial Unlock: Video LoRAs
Train character/product/style LoRAs with **musubi-tuner**:
- 16GB VRAM
- 250 images x 5 epochs in under 3 hours
- Use the same LoRA across image AND video generation
- **This is how you deliver consistent brand characters across a campaign**

### Cloud Alternatives

| Service | Quality | Best For | Cost |
|---------|---------|----------|------|
| **Runway Gen-4.5** | S-tier | Cinematic, director-level control, Act-Two performance capture | $12-76/mo (credits burn fast) |
| **Sora** | S-tier | Complex narratives, auto sound, character casting | ChatGPT Pro $200/mo |
| **Google Veo 2/3** | S-tier | Physics, realism (available inside Runway now) | Via Runway or Vertex AI |
| **Kling 2.0** | A-tier | Good quality, affordable | ~$0.10-0.30/s |
| **Pika 2.5** | A-tier | Quick social clips | $10-70/mo |

**Honest cloud vs local:** Sora and Runway Gen-4.5 still edge out Wan 2.2 on complex multi-scene narratives and some photorealistic human motion. But for single-scene commercial clips, product demos, and social content, Wan 2.2 with the right workflow is competitive or better. And you can generate unlimited content overnight.

**Verdict:** Wan 2.2 14B (primary) + FramePack (long-form) + SEEDVR2 (upscale). Runway Gen-4.5 subscription for the occasional hero shot that needs cloud-level polish.

---

## 5. AUDIO

**What you need:** Voiceovers for ads/content, sound effects, potentially music for social clips. Has to sound natural, not robotic.

### Text-to-Speech (Voiceover)

| Model | Type | Why | VRAM | Quality |
|-------|------|-----|------|---------|
| **FishAudio S1** (OpenAudio S1) | Open (CC-BY-NC-SA) | SOTA open TTS. 4B params. "True human-like" speech with emotion, pauses, intent. Voice cloning from short reference. Top of TTS Arena leaderboard. | 8-16GB | S-tier |
| **FishAudio S1-mini** | Open | Smaller version on HuggingFace. Good quality, lower requirements. | 4-8GB | A-tier |
| **F5-TTS** | Open | Flow-matching TTS. Very natural prosody. Fast inference. v1 released March 2025 with major quality improvements. | 4-8GB | A-tier |
| **Parler-TTS Large v1** | Open | 2.2B params, 45K hours training data. Control voice with text prompts ("a female speaker with a warm tone in a quiet room"). | 8-12GB | A-tier |
| **OpenVoice V2** | Open (MIT) | Instant voice cloning. Tone, emotion, rhythm, accent control. 6 languages native. Free commercial use. | 4-8GB | B-tier |
| **Bark** | Open | Suno's TTS. Generates speech + laughter + sighs + music + sound effects. Multilingual. Unpredictable but creative. | 8-12GB | B-tier |
| **Coqui XTTS v2** | Open | Battle-tested, 16 languages, fine-tunable. Great for production pipelines. | 4-8GB | B-tier |
| **Qwen3-TTS** | Open | Just open-sourced by Alibaba. Early but promising. | TBD | TBD |

### Voice Cloning (Clone a specific voice for branded content)

| Model | Why |
|-------|-----|
| **FishAudio S1** | Best quality cloning from short reference clips |
| **OpenVoice V2** | MIT license, commercial use, instant cloning |
| **RVC** (Retrieval-based Voice Conversion) | Train on 10 mins of voice data. Huge community. Used for covers/conversions. Runs on 4GB VRAM. |

### Music Generation

| Model | Type | Why | VRAM |
|-------|------|-----|------|
| **MusicGen** (Meta/AudioCraft) | Open | Text-to-music + melodic conditioning. Up to 30s. Multiple sizes (300M to 3.3B). | 4-16GB |
| **Stable Audio Open 1.0** | Open | Text-to-audio, up to 47s stereo at 44.1kHz. DiT architecture. | 8-16GB |
| **Bark** | Open | Can generate music + speech + effects in one model | 8-12GB |

**Cloud alternatives:**
| Service | Why | Cost |
|---------|-----|------|
| **ElevenLabs** | Best commercial TTS/voice cloning. Used by studios worldwide. | $5-99/mo |
| **Suno v4** | Best AI music generation period. Full songs with vocals. | $10-30/mo |
| **Udio** | Competes with Suno for music gen | $10-30/mo |

### Sound Effects

| Model | Why |
|-------|-----|
| **AudioGen** (Meta/AudioCraft) | Text-to-sound effects. "Dog barking in a park with birds." |
| **Stable Audio Open** | Also does SFX, up to 47s |
| **LTX-2** | Audio-visual generation (audio paired with video, Jan 2026) |

**For C41 Cinema specifically:**
- **Voiceovers:** FishAudio S1 locally (SOTA quality, free) + ElevenLabs subscription (for when you need that extra 5% polish or instant turnaround)
- **Music for social content:** Suno subscription (cloud, nothing local competes yet for full songs with vocals)
- **SFX:** AudioGen/Stable Audio Open locally (free, good enough for background ambience and effects)
- **Voice cloning for branded content:** FishAudio S1 or OpenVoice V2 (clone the client's preferred voice talent, use across campaign)

**Verdict:** FishAudio S1 (local TTS/cloning) + ElevenLabs (cloud backup) + Suno (music) + AudioGen (SFX).

---

## THE COMPLETE C41 CINEMA STACK

### Local (What You Run on Your Machine)

| Category | Primary Model | Backup/Specialist | VRAM Budget |
|----------|--------------|-------------------|-------------|
| **LLM/Assistant** | Qwen3-235B-A22B | Qwen3-32B (fast daily) | 70GB |
| **Code** | Qwen3-Coder-Next | (falls back to cloud Claude) | 15GB + 30GB RAM |
| **Image Gen** | Qwen-Image > Wan 2.2 i2i > SEEDVR2 | Flux 2 Dev (multi-ref) | 24-48GB |
| **Video Gen** | Wan 2.2 14B + VACE | FramePack (long-form) | 24-48GB (96GB for upscale) |
| **Audio/TTS** | FishAudio S1 | F5-TTS, OpenVoice V2 | 8-16GB |
| **Music** | MusicGen / Stable Audio Open | (cloud Suno for full songs) | 8-16GB |
| **SFX** | AudioGen | Stable Audio Open | 8-16GB |

### Cloud (Subscriptions to Keep)

| Service | What For | Cost |
|---------|----------|------|
| **Claude Max** | Your AI partner (me), complex reasoning, coding | $100/mo |
| **Runway Gen-4.5** | Hero video shots, cinematic polish | $28-76/mo |
| **Midjourney** | Artistic direction, editorial imagery | $10-30/mo |
| **ElevenLabs** | Premium TTS when local isn't enough | $5-22/mo |
| **Suno** | Music with vocals for social content | $10/mo |
| **Total cloud** | | **~$150-240/mo** |

### Total VRAM Needed (All Local Models)

You don't run everything simultaneously. You swap models in and out. But the peak VRAM demands:

| Workload | Peak VRAM |
|----------|-----------|
| Frontier LLM (Qwen3-235B Q4) | 70GB |
| Coding (Qwen3-Coder-Next) | 15GB VRAM + 30GB RAM |
| Image pipeline (Qwen-Image + Wan i2i) | 24GB |
| Video gen (Wan 2.2 14B full quality) | 48GB |
| Video upscale (SEEDVR2 to 4K) | 48-96GB |
| TTS (FishAudio S1) | 16GB |
| **Comfortable minimum for everything** | **96GB total VRAM** |
| **Ideal for no compromises** | **192-256GB total VRAM** |

---

*This is your playbook. The pitch doc for your father should focus on: the local stack replaces $500-1K+/mo in cloud subscriptions, gives unlimited generation, full creative control, client data privacy, and the LoRA training capability is the competitive moat that cloud services can't match.*
