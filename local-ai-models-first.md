# Local AI: Models First, Hardware Second

*Research compiled Feb 10, 2026*

---

## The Three Workloads

For C41 Cinema, you need three things running locally:
1. **LLM** (chat, code, writing, client comms)
2. **Image Gen** (ad creative, social media visuals, product shots)
3. **Video Gen** (social clips, concepting, draft edits)

Let's pick the best model for each, figure out what it actually needs to run well, then build around that.

---

## 1. LLM: Chat & Code

### The Current Landscape

The local LLM space has a clear hierarchy right now:

**Tier 1: Frontier-class (need 48GB+ VRAM)**
- **Qwen3-235B-A22B** (MoE, 235B total / 22B active) - Competes with DeepSeek-R1, o1, Gemini 2.5 Pro. Best open model period. At Q4 quant: ~70GB. At Q3: ~55GB.
- **Qwen3-Coder-Next** (MoE, 80B total / 3B active, 512 experts) - Purpose-built coding agent. 256K context. Only 3B active params but performs like models 10-20x bigger. At Q3_K_M: ~36GB on disk, needs ~15GB VRAM + 30GB RAM with partial offload.

**Tier 2: Sweet Spot (24-32GB VRAM)**
- **Qwen3-32B** (dense, 32B) - Outstanding general model. Matches Qwen2.5-72B. At Q4_K_M: ~20GB. Fits comfortably on a single 24GB card.
- **Qwen3-30B-A3B** (MoE, 30B total / 3B active) - Outperforms QwQ-32B with 10x fewer active params. At Q4: ~18GB. Screaming fast because only 3B params are active per token.

**Tier 3: Efficient (12-16GB VRAM)**
- **Qwen3-14B** (dense) - Matches Qwen2.5-32B. At Q4: ~9GB.
- **GLM-4.7-Flash** - Impressive on limited hardware.

### What Kyle Actually Needs

For a content agency doing client work + code, you want:
- **Primary workhorse**: Qwen3-32B dense (Q5 or Q6 quant) for best quality chat/writing
- **Coding agent**: Qwen3-Coder-Next for dev work (the hybrid DeltaNet architecture is specifically designed for long coding sessions)
- **Quick tasks**: Qwen3-30B-A3B for fast responses where you need speed over depth

### LLM VRAM Budget
- Qwen3-32B at Q5_K_M: ~24GB VRAM (fits a single 24GB card perfectly)
- Qwen3-32B at Q6_K: ~28GB (needs 32GB card)
- Qwen3-Coder-Next at Q4_K_M: split GPU/CPU, ~15GB VRAM + 30GB system RAM
- Qwen3-235B-A22B at Q4: ~70GB (need 96GB card, or dual-GPU)

**Verdict**: 24GB minimum, 32GB comfortable, 96GB if you want frontier-class

---

## 2. Image Generation

### The Current Landscape

**Tier 1: Best Quality**
- **HiDream-I1 Full** (17B params, sparse DiT) - SOTA on every benchmark. Beats Flux.1-dev, DALL-E 3, Midjourney v5/v6 on HPS v2.1 (human preference score). Best prompt following of any open model. MIT license = fully commercial. CUDA required, Flash Attention required. Uses Llama-3.1-8B as text encoder (!). At FP16: ~40GB. With optimization: runs on 11GB (per Draw Things team).
- **Flux.1 Dev** (12B) - Still the community workhorse. Massive LoRA ecosystem. Correct hands, readable text, great photorealism. At FP16: ~24GB. Quantized (Q4/Q8 GGUF): 8-12GB.

**Tier 2: Fast / Practical**
- **Flux.1 Schnell** (12B, distilled) - 4-step generation, much faster. Same architecture as Dev.
- **HiDream-I1 Fast** (17B, distilled) - Quick version of HiDream.
- **Chroma1-HD** (Apache 2.0, base model) - New open foundation model. Great for fine-tuning.

**Tier 3: Editing**
- **HiDream-E1.1** - Instruction-based image editing (like "remove this object", "change the background"). Only open-source model of its kind.
- **Flux Kontext** - Image editing, but not fully open.

### What Kyle Actually Needs

For commercial photo/video agency work:
- **Hero model**: HiDream-I1 Full for highest quality client-facing images
- **Daily driver**: Flux.1 Dev for fast iteration with LoRAs (the LoRA ecosystem is way bigger)
- **Quick drafts**: Flux.1 Schnell for rapid concepting
- **Editing**: HiDream-E1.1 for instruction-based edits

### Image Gen VRAM Budget
- HiDream-I1 Full (optimized): ~11-16GB usable, ~24GB comfortable, ~40GB for full FP16
- Flux.1 Dev (FP16): ~24GB. Quantized Q8: ~12GB.
- Running both back-to-back with model swapping: 24GB minimum

**Verdict**: 24GB gets you Flux at full quality + HiDream optimized. 48GB+ lets you run HiDream at full precision without compromise.

---

## 3. Video Generation

### The Current Landscape

This is the fastest-moving space. The hierarchy:

**Tier 1: Best Quality**
- **Wan 2.1 14B** (and Wan 2.2) - Current SOTA open video model. Alibaba's flagship. Text-to-video, image-to-video, VACE (editing). Incredible material/lighting understanding. Community using it for text-to-IMAGE at 4K resolution with stunning results. VRAM: ~18-24GB for 480p, 40GB+ for 720p. On RTX 4090: 5-sec 480p clip in ~4 min.
- **Wan 2.1 VACE** - All-in-one video creation and editing. Same model, unified interface for inpainting, outpainting, reference-based gen.

**Tier 2: Practical / Efficient**
- **FramePack** (13B, based on Wan architecture) - Game changer for accessibility. Generates 1-minute 30fps video on 6GB VRAM. Next-frame prediction = constant memory regardless of video length. On RTX 4090: 2.5 sec/frame (unoptimized), 1.5 sec/frame with TeaCache. New FramePack-P1 has anti-drifting for long videos.
- **Wan 2.1 1.3B** - Lightweight version. Only 8.19GB VRAM. 5-sec 480p on 4090 in ~4 min. Quality comparable to some closed-source models.

**Tier 3: Specialized**
- **HunyuanVideo** - Good quality but higher VRAM (~29GB for full gen).
- **CogVideoX** - Decent but being eclipsed by Wan.
- **LightX2V** - Optimization framework that makes Wan run on RTX 4060 (8GB).

### What Kyle Actually Needs

For a video agency:
- **Hero model**: Wan 2.1/2.2 14B for best quality output (client-facing concepts, social media drafts)
- **Long-form**: FramePack for generating longer clips (1-min+) without VRAM explosion
- **Quick previews**: Wan 2.1 1.3B for rapid iteration
- **Editing**: Wan VACE for video editing workflows

### Video Gen VRAM Budget
- FramePack 13B: 6GB minimum, runs on anything
- Wan 2.1 1.3B: 8GB minimum
- Wan 2.1 14B at 480p: ~18-24GB
- Wan 2.1 14B at 720p: ~40GB+
- Wan 2.2 14B at 720p (from DGX Spark benchmark): runs in 128GB unified, hits thermal limits

**Verdict**: 24GB gets you good 480p video gen. 48GB+ for 720p without compromise. 96GB if you want to gen at higher res or run the biggest upcoming models.

---

## The VRAM Math

Here's where it all comes together. What does each workload actually need?

| Workload | Minimum | Comfortable | No Compromise |
|----------|---------|-------------|---------------|
| LLM (Qwen3-32B) | 24GB | 32GB | 96GB (for 235B) |
| Image Gen (HiDream/Flux) | 12GB | 24GB | 48GB |
| Video Gen (Wan 14B) | 18GB | 24GB | 48GB+ |
| **All three (not simultaneous)** | **24GB** | **32GB** | **96GB** |

Key insight: **You're not running all three at once.** You load one model, use it, unload it, load the next. So the VRAM requirement is the MAX of what you need, not the sum.

---

## Hardware Recommendations (Models-First)

### Option A: "Get Started Now" - RTX 5090 ($2,000)
- **32GB GDDR7**, 1,792 GB/s bandwidth
- Runs: Qwen3-32B at Q5-Q6 beautifully, Flux at full FP16, Wan 14B at 480p, FramePack easily
- Can't do: Qwen3-235B (too big), Wan 14B at 720p+ (borderline), HiDream I1 at full FP16
- **Best for**: Learning the tools, producing real work immediately, 90% of what you need

### Option B: "No Compromise" - RTX PRO 6000 Blackwell ($8,000-9,000)
- **96GB GDDR7 ECC**, 1,800 GB/s bandwidth, 600W TDP
- Runs: EVERYTHING. Qwen3-235B at Q3-Q4. HiDream at full precision. Wan 14B at 720p+. Future models that don't exist yet.
- Can't do: Basically nothing is too big for this card
- **Best for**: Future-proofing, running frontier models, zero compromises

### Option C: "The Hybrid" - RTX 5090 + DGX Spark ($6,000)
- 5090 (32GB) for image/video gen (needs CUDA speed)
- DGX Spark (128GB unified) for LLMs (needs memory, speed less critical)
- Runs: Big LLMs on Spark at ~30 tok/s, fast image/video on 5090
- **Catch**: Spark is 2.4x slower than 4090 for pure inference. Only 273 GB/s bandwidth. ARM-based so some software headaches.

### Option D: "Go Big" - RTX PRO 6000 Blackwell + RTX 5090 ($11,000)
- PRO 6000 as primary for everything big
- 5090 as secondary for parallel workloads or serving
- Runs: Literally anything local AI can throw at it for the next 3+ years

---

## My Recommendation for Kyle

**Start with Option A (RTX 5090 build, ~$3,500 total system).**

Why:
1. Covers 90% of your actual use cases TODAY
2. You learn ComfyUI, FramePack, Ollama, the whole workflow
3. Models are improving faster than hardware - by the time you need more VRAM, there'll be better options
4. $3.5K is nothing to test with before asking for a bigger investment
5. If you decide you need more, the 5090 becomes your secondary card

Then if the workflow proves out and you're hitting VRAM walls, upgrade to a PRO 6000 build. That's the pitch to Dad: "I've already proven this works with a $3.5K test build, here's what I produced, now I need $10K to remove all limitations."

---

## Speed Benchmarks (Real World)

From actual user reports:

**LLM (Qwen3-Coder-Next Q3_K_M on RTX 5060 Ti 16GB)**
- Prefill: 118-225 tok/s
- Generation: 16-18 tok/s
- Verdict: Very usable for coding agent work

**LLM (GPT-OSS-120B on DGX Spark 128GB)**
- Generation: 31-35 tok/s
- VRAM used: ~64GB
- Verdict: Solid for big model inference

**Image Gen (HiDream on DGX Spark vs RTX 4090)**
- Z Image Turbo: Spark 6s hot / 4090 2.6s hot
- Spark is ~2.3x slower but can run models 4090 can't (Flux 2 Q8 OOMs on 4090)

**Video Gen (Wan 2.2 720p@24fps on DGX Spark)**
- Runs but hits 91.8C, 195W from wall
- Makes the device uncomfortably hot

**Video Gen (FramePack on RTX 4090)**
- 2.5 sec/frame unoptimized
- 1.5 sec/frame with TeaCache
- 1-minute video (1800 frames) = ~45-75 minutes
- 3070 Ti laptop: 4-8x slower

---

## Models to Watch (Coming Soon)

These are on the horizon and will influence hardware needs:
- **Wan 3.x** - Alibaba iterating fast, expect better quality and efficiency
- **FramePack-P1** - Next gen with planned anti-drifting (already showing results)
- **Flux 2** - BF16 full model exceeds 80GB VRAM (!)
- **Qwen3-Coder-Next successors** - The hybrid DeltaNet/MoE architecture is a breakthrough, expect more
- **Video upscalers** - Generate at 480p, upscale to 1080p+ (workflow already working in community)

The trend is clear: **models are getting bigger AND more efficient simultaneously.** MoE means you need lots of total VRAM but only activate a fraction. Video models are getting FramePack-style optimizations that slash requirements. More VRAM is always better, but 24-32GB will remain the practical sweet spot for the next 2-3 years.
