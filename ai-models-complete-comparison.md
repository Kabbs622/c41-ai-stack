# Complete AI Model Comparison — February 2026
*Every notable model across Image Gen, Video Gen, and LLM/Code. Cloud and local.*

---

## IMAGE GENERATION

| Model | Provider | Params | Open? | Run Locally? | VRAM Needed | Quality Tier | Speed | Cost | Best For | Key Strengths | Key Weaknesses |
|-------|----------|--------|-------|-------------|-------------|-------------|-------|------|----------|---------------|----------------|
| **Midjourney v6.1** | Midjourney | Unknown | Closed | No | N/A | S | Medium | $10-60/mo | Artistic, editorial, creative direction | Best artistic quality, incredible aesthetics | No API, Discord/web only, no fine-tuning |
| **DALL-E 3 / GPT-Image** | OpenAI | Unknown | Closed | No | N/A | A | Fast | ~$0.01-0.17/img | Marketing imagery, text-heavy graphics | Great prompt adherence, strong text rendering | Limited artistic range, safety filters aggressive |
| **Ideogram 3** | Ideogram | Unknown | Closed | No | N/A | A | Fast | $8-48/mo | Typography, logos, text-heavy designs | Best text rendering of any cloud model | Limited stylistic range outside text |
| **Adobe Firefly 3** | Adobe | Unknown | Closed | No | N/A | A | Fast | CC subscription | Commercially safe stock-style content | IP-safe training data, CC integration | Conservative outputs, less creative range |
| **Google Imagen 3/4** | Google | Unknown | Closed | No | N/A | A | Medium | Vertex AI pricing | Enterprise, Google ecosystem | High technical quality | Limited public access, Google lock-in |
| **Recraft V3** | Recraft | Unknown | Closed | No | N/A | A | Fast | $20-100/mo | Brand/logo/vector design | Vector generation, brand consistency | Niche use case |
| **Aurora (Grok)** | xAI | Unknown | Closed | No | N/A | B | Fast | X Premium | Social media quick content | X platform integration | Limited testing, platform locked |
| **Qwen-Image** | Alibaba | ~14B | Open | Yes | 16-24GB (Nunchaku) | S | 13s/img (Nunchaku) | Free | Composition, prompt adherence, text in images | Best prompt adherence of any open model, text rendering, image editing built in | Skin can look "plastic" without refinement pipeline |
| **Flux 2 Dev** | Black Forest Labs | 32B | Open (non-commercial) | Yes | BF16 >80GB; 4-bit: 24GB | S | Medium | Free | Multi-reference shots, brand consistency, editing | 10-image multi-reference, character/style consistency without fine-tuning, 4MP editing, Mistral-3 24B VLM encoder | Non-commercial license, huge at full precision, brand new ecosystem |
| **Flux 1 Pro** | Black Forest Labs | 12B | Closed (API) | Via API | N/A | S | Medium | $0.055/img | High-end commercial generation | Top-tier quality, API access | No local weights |
| **Flux 1 Dev** | Black Forest Labs | 12B | Open (non-commercial) | Yes | 24-40GB (FP16), 12GB (Q4) | A | Medium | Free | Development, LoRA training base | Massive LoRA ecosystem on Civitai, excellent quality | Non-commercial license |
| **Flux 1 Schnell** | Black Forest Labs | 12B | Open (Apache) | Yes | 16-24GB | B | Very Fast (4 steps) | Free | Rapid prototyping, concepting | 4-step generation, commercial license | Lower quality than Dev/Pro |
| **HiDream-I1 Full** | HiDream | 17B (sparse DiT) | Open (MIT) | Yes | FP16 ~40GB; optimized 11GB | A | Medium | Free | Commercial use (MIT license), benchmark performance | SOTA on GenEval/DPG-Bench, fully commercial, 4 text encoders incl. Llama 3.1 8B | Young ecosystem, fewer LoRAs |
| **Wan 2.2 14B (1-frame)** | Alibaba | 14B | Open | Yes | 16GB (GGUF Q5) | A | ~42s/img (4080) | Free | Photorealism refinement, img2img | Incredible photorealism for materials/lighting/skin at low denoise | Slower than dedicated image models |
| **SD 3.5 Large** | Stability AI | 8B | Open | Yes | 16-24GB | A | Medium | Free | High-quality local gen | Improved architecture over SDXL | Smaller community than SDXL |
| **SDXL** | Stability AI | 3.5B | Open | Yes | 8-12GB | B | Fast | Free | LoRA ecosystem, custom fine-tunes | Biggest LoRA/checkpoint ecosystem (Civitai), fast iteration | Architecture showing age |
| **SD 3.5 Medium** | Stability AI | 2.5B | Open | Yes | 8-12GB | B | Fast | Free | Balanced local gen on modest hardware | Good quality/VRAM balance | Not as detailed as Large |
| **Kolors** | Kuaishou | 5B | Open | Yes | 12-20GB | B | Medium | Free | Asian market content | Strong on Asian faces/aesthetics | Language/cultural bias in training |
| **PixArt-Sigma** | PixArt | 600M | Open | Yes | 4-8GB | B | Very Fast | Free | Fast prototyping on low-end hardware | Tiny, runs on anything | Lower detail |
| **Kandinsky 3** | Sber | 12B | Open | Yes | 16-24GB | B | Medium | Free | Artistic/creative | Unique artistic style | Russian-focused training data |
| **AuraFlow** | Fal.ai | 6.8B | Open | Yes | 16-24GB | B | Medium | Free | Research, flow-based experimentation | Novel architecture | Experimental, small community |
| **Stable Diffusion 1.5** | Stability AI | 860M | Open | Yes | 4-8GB | C | Very Fast | Free | Learning, legacy checkpoints | Thousands of legacy models/LoRAs | Outdated quality |
| **Z-Image Turbo** | Community | Small | Open | Yes | 4GB | C | Very Fast | Free | Ultra-fast concepting | Runs on nearly any GPU | Low quality ceiling |

### Image Gen Tier Summary

**S-Tier:** Midjourney v6.1, Qwen-Image (w/ pipeline), Flux 2 Dev, Flux 1 Pro
**A-Tier:** DALL-E 3/GPT-Image, Ideogram 3, Adobe Firefly 3, Imagen 3/4, HiDream-I1, Flux 1 Dev, Wan 2.2 (1-frame), SD 3.5 Large
**B-Tier:** Recraft V3, Flux 1 Schnell, SDXL, SD 3.5 Medium, Kolors, PixArt, Kandinsky, AuraFlow
**C-Tier:** Aurora, SD 1.5, Z-Image Turbo

### Pro Image Workflow (How Practitioners Actually Work)
1. **Qwen-Image** (composition + prompt adherence) > **Wan 2.2 i2i** (photorealism at 0.2-0.5 denoise) > **SEEDVR2** (4K upscale) > **HiDream-E1.1** (detail fixes via natural language)
2. **Flux 2 Dev** for multi-reference brand consistency (feed 10 product/brand images)
3. **SDXL** for rapid LoRA iteration (biggest ecosystem)
4. **Nunchaku** acceleration: 10x speedup on Flux/Qwen models

---

## VIDEO GENERATION

| Model | Provider | Params | Open? | Run Locally? | VRAM Needed | Quality Tier | Max Length | Cost | Best For | Key Strengths | Key Weaknesses |
|-------|----------|--------|-------|-------------|-------------|-------------|-----------|------|----------|---------------|----------------|
| **Sora** | OpenAI | Unknown | Closed | No | N/A | S | ~20s | ChatGPT Pro ($200/mo) | Cinematic narratives, complex scenes | Best multi-scene coherence, auto sound/music, character casting | Expensive, limited access, no fine-tuning |
| **Runway Gen-4.5** | Runway | Unknown | Closed | No | N/A | S | 10-40s | $12-76/mo (credits: ~25s Gen-4.5 = 625 credits) | Professional film/TV, cinematic | Director-level control, Act-Two performance capture, Veo 3.1 integration | Credit system adds up fast for production |
| **Runway Gen-4 / Gen-3 Alpha** | Runway | Unknown | Closed | No | N/A | A | 10s | Included in plans | Quick commercial clips | Proven, reliable, fast turbo mode | Superseded by Gen-4.5 |
| **Google Veo 2/3** | Google | Unknown | Closed | No | N/A | S | ~20s | Vertex AI / available in Runway | Photorealism, physics | Strong physics simulation, Google scale | Limited direct access, mostly via partners |
| **Kling 2.0** | Kuaishou | Unknown | Closed | No | N/A | A | 5-10s | ~$0.10-0.30/s | Commercial clips, Asian market | Good quality/cost ratio, long-form | Geographic restrictions, some quality inconsistency |
| **Pika 2.0/2.5** | Pika Labs | Unknown | Closed | No | N/A | A | 5-10s | $10-70/mo | Social media content, quick turnaround | User-friendly, fast, good for short clips | Limited fine control, shorter outputs |
| **Luma Dream Machine** | Luma AI | Unknown | Closed | No | N/A | A | 5s | $30/mo+ | Marketing videos, product demos | Good quality/cost balance | Short max length |
| **Luma Ray2** | Luma AI | Unknown | Closed | No | N/A | A | 5-10s | $100/mo+ | Advanced marketing, physics-heavy | Improved physics simulation | New, premium pricing |
| **Hailuo / MiniMax Video** | MiniMax/ByteDance | Unknown | Closed | No | N/A | A | 5-10s | Regional pricing | Chinese market content | Strong quality, large user base in China | Geographic/language barriers |
| **Vidu 2.0** | Shengshu | Unknown | Closed | No | N/A | A | 5-10s | Regional pricing | Asian productions | Chinese video gen leader | Limited global access |
| **ByteDance Seedance** | ByteDance | Unknown | Closed | No | N/A | A | 5s | TBD | Short-form, TikTok-style | TikTok integration potential | Limited availability |
| **Haiper 2.0** | Haiper | Unknown | Closed | No | N/A | B | 5s | $30/mo+ | Quick content creation | AI-native platform | Newer, less proven |
| **Genmo Mochi (cloud)** | Genmo | Unknown | Closed | No | N/A | B | 5s | $10-50/mo | Animation, creative | Creative animation focus | Specialized |
| **Wan 2.2 14B** | Alibaba | 14B | Open | Yes | 24-48GB (full), 8GB (LightX2V) | S | 5-10s (chain for longer) | Free | Hero clips, ads, social, realism | **Undisputed local SOTA.** 1,766-upvote realism post: "looks better than Runway." VACE for editing. LoRA support. | Needs good GPU for quality settings, upscaling is VRAM-hungry |
| **Wan 2.1** | Alibaba | 14B | Open | Yes | 24-48GB | A | 5-10s | Free | Stable production use | Mature, well-documented, VACE ecosystem | Superseded by 2.2 |
| **FramePack** | lllyasviel | 13B | Open | Yes | 6GB minimum | A | 1 min+ (constant VRAM) | Free | Long-form video, low VRAM | Next-frame prediction = constant VRAM regardless of length. 1 min on 6GB. | Quality degrades after ~25-30s per render, chain renders needed |
| **HunyuanVideo** | Tencent | 13B | Open | Yes | 29-48GB | A | 5-10s | Free | Anime-style, high quality local | Large model, good quality, FP8 available, multi-GPU via xDiT | Very high VRAM, slower than Wan |
| **LTX-2** | Lightricks | ~2B | Open | Yes | 16-32GB | A | 5-10s | Free | Audio-visual generation | Audio + video in one model (Jan 2026) | Very new, limited testing |
| **CogVideoX-5B** | Tsinghua/ZhipuAI | 5B | Open | Yes | 16-24GB | B | 5s | Free | Research, moderate hardware | Good quality for size | Limited length, older architecture |
| **Mochi 1** | Genmo | 10B | Open | Yes | 32-48GB | B | 5s | Free | Quality local gen | High quality open model | Very resource intensive, short |
| **Allegro** | RhymesAI | 6B | Open | Yes | 16-32GB | B | 5s | Free | Balanced local gen | Good size/quality balance | Newer, less tested |
| **LTX Video** | Lightricks | 500M | Open | Yes | 8-12GB | B | 3-5s | Free | Low-VRAM local video | Lightweight, fast | Shorter, lower quality ceiling |
| **AnimateDiff** | Community | Varies | Open | Yes | 8-16GB | C | 2-3s | Free | SD-based animation loops | Built on SD ecosystem | SD quality limitations, short |
| **Open-Sora** | Colossal-AI | 1.1B | Open | Yes | 16-32GB | C | 5s | Free | Research, learning | Sora-inspired open architecture | Quality well below commercial |
| **Pyramid Flow** | ByteDance | ~2B | Open | Yes | 16-32GB | C | 3-5s | Free | Research | Novel autoregressive approach | Experimental |

### Video Gen Tier Summary

**S-Tier:** Sora, Runway Gen-4.5, Google Veo 2/3, **Wan 2.2 14B** (local king)
**A-Tier:** Runway Gen-4/Gen-3, Kling 2.0, Pika 2.5, Luma Ray2, Hailuo, Vidu 2.0, Seedance, Wan 2.1, FramePack, HunyuanVideo, LTX-2
**B-Tier:** Haiper 2.0, Genmo Mochi, CogVideoX, Mochi 1, Allegro, LTX Video
**C-Tier:** AnimateDiff, Open-Sora, Pyramid Flow

### Pro Video Workflow (Practitioner-Tested)
From the 1,766-upvote Wan 2.2 realism post:
1. **Starting images**: Generate at 60 steps, upscale to 4K with SEEDVR2
2. **Video gen**: Wan 2.2 i2v at 1536x864, high noise (65-75%), 6-8 low-noise steps with speed-up LoRA
3. **Sigma curves**: 0.9 for i2v
4. **Prompting**: "Verbose prompts describing everything, leaving nothing to hallucinate"
5. **Speed-ups**: TeaCache (2x), Self-Forcing/CausVid LoRAs, CFG-Zero*
6. **Upscale**: SEEDVR2 to 4K (this is where you need 48-96GB VRAM)
7. **Post**: HandBrake > Premiere Pro > After Effects, add grain/grade in post

### Where Local Beats Cloud (Honest)
- Unlimited generation (no credits/rate limits)
- Custom LoRAs (character, product, brand style consistency)
- Full parameter control
- Client privacy (nothing leaves your machine)
- Overnight batch generation (hundreds of variations)

### Where Cloud Still Wins (Honest)
- Slightly better photorealistic humans in complex motion (Runway, Kling, Veo)
- Multi-scene narrative coherence (Sora)
- Zero setup, instant access
- **But**: Gap is closing fast. Wan 2.2 realism post comments: "this looks better than Runway"

---

## LLM / CODE

| Model | Provider | Params | Architecture | Open? | Run Locally? | VRAM Needed | Quality Tier | Context | Speed (local) | Cost | Best For |
|-------|----------|--------|-------------|-------|-------------|-------------|-------------|---------|--------------|------|----------|
| **Claude Opus 4** | Anthropic | Unknown | Dense | Closed | No | N/A | S | 200K | N/A | ~$15/75 per 1M tok | Complex reasoning, long analysis, nuanced writing |
| **Claude Sonnet 4** | Anthropic | Unknown | Dense | Closed | No | N/A | A | 200K | N/A | ~$3/15 per 1M tok | Balanced performance/cost, coding |
| **GPT-4o / GPT-4.1** | OpenAI | Unknown | Dense/MoE | Closed | No | N/A | A | 128K-1M | N/A | $2.50-10/1M tok | General purpose, multimodal, coding |
| **Gemini 2.5 Pro** | Google | Unknown | MoE | Closed | No | N/A | S | 1M+ | N/A | Competitive | Massive context, multimodal, integrated |
| **Gemini 2.5 Flash** | Google | Unknown | MoE | Closed | No | N/A | A | 1M+ | N/A | Low cost | Real-time apps, cost-sensitive |
| **Grok 3/4** | xAI | Unknown | Unknown | Closed | No | N/A | A | 128K+ | N/A | X Premium | Real-time info, social analysis |
| **Command R+** | Cohere | Unknown | Dense | Closed | No | N/A | A | 128K | N/A | Enterprise | Enterprise RAG, document processing |
| **Mistral Large 3** | Mistral | Unknown | Dense | Closed | No | N/A | A | 128K | N/A | Competitive | European alternative, privacy-conscious |
| **DeepSeek V3.1** | DeepSeek | 685B (37B active) | MoE | Open | Yes | 256GB+ (multi-GPU) | S | 128K | 25 tok/s (10-GPU $17K rig) | Free | Frontier general intelligence |
| **DeepSeek R1** | DeepSeek | 685B (37B active) | MoE | Open | Yes | 256GB+ (multi-GPU) | S | 128K | Similar to V3.1 | Free | Complex reasoning, math, science |
| **Kimi K2** | Moonshot AI | Large MoE | MoE | Open | Yes | 256GB+ (multi-GPU) | S | 128K+ | 20 tok/s (10-GPU rig) | Free | Frontier, competes with DeepSeek V3 |
| **MiniMax M2.1** | MiniMax | Large MoE | MoE | Open | Yes | 192GB (dual-GH200) | A | 163K | 78 tok/s (FP8+INT4, 192GB) | Free | Coding (daily driver for Claude Code replacement), long context |
| **Qwen3-235B-A22B** | Alibaba | 235B (22B active) | MoE | Open | Yes | 70GB (Q4), 55GB (Q3) | S | 128K | 31.5 tok/s (Q6, 256GB VRAM) | Free | Cheapest-to-run frontier model, multilingual |
| **Qwen3-Coder-Next** | Alibaba | 80B (3B active) | Hybrid MoE + DeltaNet | Open | Yes | 15GB VRAM + 30GB RAM (CPU MoE) | A | 256K native | 16-18 tok/s (Q3, 5060 Ti 16GB) | Free | Coding agents, long coding sessions |
| **Qwen3-32B** | Alibaba | 32B | Dense | Open | Yes | 24GB (Q5), 28GB (Q6) | A | 128K | Fast | Free | Daily chat, writing, client comms |
| **Qwen3-30B-A3B** | Alibaba | 30B (3B active) | MoE | Open | Yes | 18GB (Q4) | B | 128K | Very Fast | Free | Speed-critical tasks, quick queries |
| **Llama 4 Scout** | Meta | 109B (17B active) | MoE | Open | Yes | ~65GB (Q4) | A | 10M (claimed) | Medium | Free | Multimodal, natively multilingual |
| **Llama 4 Maverick** | Meta | 400B (17B active) | MoE | Open | Yes | ~200GB (Q4) | A | 1M | Slow (needs multi-GPU) | Free | Large-scale reasoning |
| **Phi-4** | Microsoft | 14B | Dense | Open | Yes | 16-20GB | A | 16K | Fast | Free | Efficient reasoning on modest hardware |
| **Gemma 3** | Google | 2B-27B | Dense | Open | Yes | 4-32GB | B | 8K-128K | Fast | Free | Mobile/edge, small footprint |
| **Mistral Small/Nemo** | Mistral | 7B-12B | Dense | Open | Yes | 8-16GB | B | 32K-128K | Very Fast | Free | Privacy-focused local, European |
| **GLM-4.5/4.6** | Zhipu AI | 9B-Large | MoE | Open | Yes | 12-116GB | B | 128K | 12-27 tok/s | Free | Multilingual, underrated |
| **Command R** | Cohere | 35B | Dense | Open | Yes | 32-64GB | B | 128K | Medium | Free | RAG, document processing |
| **Yi Lightning** | 01.AI | 34B | Dense | Open | Yes | 32-64GB | B | 32K | Fast | Free | Asian market applications |
| **InternLM 3** | Shanghai AI Lab | 20B | Dense | Open | Yes | 24-48GB | B | 32K | Medium | Free | Research |
| **CodeLlama** | Meta | 7B-70B | Dense | Open | Yes | 8-80GB | B | 16K-100K | Varies | Free | Pure coding (legacy, superseded by Qwen3-Coder) |
| **StarCoder 2** | BigCode | 3B-15B | Dense | Open | Yes | 8-32GB | B | 16K | Fast | Free | Code completion, fill-in-middle |

### LLM Tier Summary

**S-Tier:** Claude Opus 4, Gemini 2.5 Pro, DeepSeek V3.1, DeepSeek R1, Kimi K2, Qwen3-235B
**A-Tier:** Claude Sonnet 4, GPT-4o/4.1, Gemini 2.5 Flash, Grok 3/4, Command R+, Mistral Large, MiniMax M2.1, Qwen3-Coder-Next, Qwen3-32B, Llama 4 Scout/Maverick, Phi-4
**B-Tier:** Gemma 3, Mistral Small/Nemo, GLM-4.5/4.6, Command R, Yi Lightning, InternLM 3, Qwen3-30B-A3B, CodeLlama, StarCoder 2

### Key LLM Insight
The frontier open MoE models (DeepSeek V3.1, Kimi K2, Qwen3-235B) genuinely compete with cloud APIs. The difference: you own them, no rate limits, no throttling, complete privacy. The tradeoff: you need serious multi-GPU hardware to run them at usable speeds.

---

## BONUS / UTILITY MODELS

| Model | Category | What It Does | VRAM | Why It Matters |
|-------|----------|-------------|------|----------------|
| **SEEDVR2** | Upscaling | 2x-4x video/image upscale with detail hallucination | 24-48GB | The standard upscaler in pro local pipelines |
| **HiDream-E1.1** | Image Editing | Natural language instruction-based editing | 16-24GB | "Remove the watch. Change background to coffee shop." |
| **Qwen Image Edit** | Image Editing | Instruction-based editing (Nunchaku accelerated) | 16-24GB | Companion to Qwen-Image |
| **Hunyuan 3D 2.1** | 3D Generation | Text/image to 3D model | ~29GB | Product shots, asset generation |
| **Qwen3-TTS** | Text-to-Speech | Local voice generation | Small | Just open-sourced |
| **JoyCaption** | Auto-Captioning | Caption images for LoRA training | 8GB | Critical for training data prep |
| **Nunchaku** | Acceleration | 10x speedup on Flux/Qwen image models | N/A | Turns minutes into seconds |
| **TeaCache** | Acceleration | ~2x speedup on Wan/Flux/HunyuanVideo | N/A | Free speed, minimal quality loss |
| **LightX2V** | Optimization | Run Wan 2.1/2.2 on 8GB VRAM | 8GB | Makes video gen accessible on low-end |
| **musubi-tuner** | LoRA Training | Train Wan video LoRAs | 16GB | 250 images x 5 epochs in <3 hours |

---

## THE COMMERCIAL UNLOCK: LoRA TRAINING

This is what separates "playing with AI" from "running an AI content agency":

| Ecosystem | Training Tool | VRAM Needed | Time | Use Case |
|-----------|--------------|-------------|------|----------|
| **Wan 2.1/2.2** | musubi-tuner | 16GB | ~3 hrs (250 imgs x 5 epochs) | Client character/product LoRAs for video |
| **Flux 1.x** | kohya-ss, ai-toolkit | 16-24GB | 1-4 hrs | Brand style LoRAs, consistent characters |
| **SDXL** | kohya-ss | 8-12GB | 1-2 hrs | Fastest iteration, biggest community |
| **Flux 2 Dev** | TBD (just launched) | TBD | TBD | Multi-reference may reduce need for LoRAs |

Train client-specific LoRAs (character, product, style) and use them across BOTH image and video generation for consistent brand deliverables.

---

*Report compiled Feb 10, 2026. Models and capabilities change rapidly.*
*Sources: 40+ Reddit threads, HuggingFace model cards, GitHub repos, Civitai, BFL blog, Runway pricing page, OpenAI, practitioner reports, benchmark data.*
