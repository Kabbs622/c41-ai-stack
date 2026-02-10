# Local AI Deep Dive: Models First, Hardware Second

*Exhaustive research compiled Feb 10, 2026*
*Sources: Reddit threads (r/StableDiffusion, r/LocalLLaMA), HuggingFace model cards, GitHub repos/discussions, Civitai, practitioner reports, actual benchmark data*

---

## What's Actually Happening Right Now (Not Marketing Hype)

The local AI landscape shifted dramatically in mid-2025. Three things happened simultaneously:
1. **Qwen3 dropped and dominated LLMs** - MoE architecture made frontier models runnable on consumer GPUs
2. **Wan 2.1/2.2 became the video gen standard** - and surprised everyone by also being amazing at still images
3. **A new "multi-model pipeline" workflow emerged** - people chain models (e.g., Qwen Image for composition, then Wan for realism refinement)

This is NOT a market where you pick one model. Professional users run 3-5 models in sequence.

---

## PILLAR 1: LLM (Chat/Code/Writing)

### The Models That Matter

**Qwen3-Coder-Next** (80B total, 3B active, 512 experts)
- THE coding model right now. Hybrid DeltaNet/MoE architecture specifically designed for long coding sessions
- 256K native context (not extended, native)
- Competes with Claude Sonnet 4 / Gemini Flash level according to real users
- On RTX 5060 Ti 16GB (Q3_K_M): 16-18 tok/s generation, 118-225 tok/s prefill
- Needs ~15GB VRAM + 30GB system RAM with CPU MoE offload
- At Q4_K_M: ~36GB on disk
- **Key insight from practitioners**: Uses far fewer tool calls than Claude, reads whole files at once. Feels faster than the tok/s numbers suggest.

**Qwen3-32B** (dense)
- Best all-around local model. Matches old Qwen2.5-72B quality at half the size.
- At Q5_K_M: ~24GB. Fits perfectly on a single 24GB card.
- At Q6_K: ~28GB. Needs 32GB card.
- 128K context.
- For general chat, writing, client comms - this is the one.

**Qwen3-235B-A22B** (MoE, 235B total, 22B active)
- Frontier-class. Competes with DeepSeek-R1, o1, o3-mini, Grok-3, Gemini 2.5 Pro.
- At Q4: ~70GB VRAM. At Q3: ~55GB.
- Need 96GB card for comfortable use, or dual consumer cards with split.

**Qwen3-30B-A3B** (MoE, 30B total, 3B active)
- Speed demon. Outperforms QwQ-32B with 10x fewer active params.
- At Q4: ~18GB. Lightning fast because only 3B params active.
- Good for quick tasks where speed matters more than depth.

**GLM-4.7-Flash-REAP** (23B total, 3B active, MoE)
- Sleeper hit. On RTX 5060 Ti 16GB with CPU MoE offload:
  - 16K context: 965 tok/s prefill, 26 tok/s gen
  - 64K context: 671 tok/s prefill, 8.8 tok/s gen  
  - 200K context (!): 325 tok/s prefill, 7.7 tok/s gen
- That 200K context on a 16GB card is insane.

**DGX Spark context** (from 2-month owner review):
- GPT-OSS-120B (big model): 31-35 tok/s, uses ~64GB VRAM
- GPT-OSS-20B: 53 tok/s on Spark vs 123 tok/s on 4090 (Spark is 2.4x slower)
- Biggest model that fits: GLM-4.5-Air Q8 at ~116GB, runs at ~12 tok/s
- Max usable VRAM: 119.7GB (of 128GB unified)
- Owner verdict: "Much more honestly perceived as a compact CUDA node or Blackwell dev-kit. NOT a supercomputer."

### LLM Deep Insight
The MoE revolution means VRAM ≠ active compute. Qwen3-Coder-Next has 80B params but only activates 3B per token. The key metric is: **how much VRAM do you need to LOAD the model** (all params, even inactive) vs **how fast it runs** (only active params). This means big VRAM + moderate bandwidth = good for MoE. The DGX Spark's 128GB / 273 GB/s is actually a reasonable fit for MoE models despite slow bandwidth.

### LLM VRAM Requirements (Realistic)

| Model | Quant | VRAM (GPU-only) | VRAM + RAM (split) | Speed |
|-------|-------|-----------------|---------------------|-------|
| Qwen3-32B | Q5_K_M | 24GB | N/A | 30-50 tok/s on 4090 |
| Qwen3-32B | Q6_K | 28GB | N/A | 25-40 tok/s on 4090 |
| Qwen3-Coder-Next | Q3_K_M | 36GB full / 15GB partial | 15GB + 30GB RAM | 16-18 tok/s on 5060Ti |
| Qwen3-Coder-Next | Q4_K_M | ~45GB full | 20GB + 35GB RAM | est. 14-16 tok/s |
| Qwen3-235B-A22B | Q4 | ~70GB | Split possible | 20-30 tok/s on PRO 6000 |
| Qwen3-235B-A22B | Q3 | ~55GB | Split possible | 25-35 tok/s on PRO 6000 |
| GLM-4.7-Flash | Q5_K_XL | ~12GB + RAM | 7GB + 29GB RAM | 14.4 tok/s on 5060Ti |

---

## PILLAR 2: Image Generation

### The Real Hierarchy (From Practitioners, Not Benchmarks)

**Tier 0: The New Pipeline Workflow**
The community has moved BEYOND single-model generation. The cutting edge workflow (from the highest-voted recent thread, 107 upvotes):

> "For image gen I use **Qwen** to start because the prompt adherence is awesome, then transfer img2img using **Wan 2.2** for final."

This two-step pipeline is becoming standard:
1. Generate with Qwen Image (best prompt following) or HiDream (best benchmarks)
2. Refine with Wan 2.2 low-noise i2v (1 frame) for photorealism
3. Optionally upscale with SEEDVR2 or Tiled Diffusion

**Qwen-Image** (NEW - released Aug 2025)
- From the Qwen team. Text-to-image AND image editing in one model.
- **Best text rendering of any open model** (Chinese AND English). Can render pi to 30+ digits accurately.
- "Complex text rendering and precise image editing" - not just generation
- Uses diffusers pipeline, 50 steps default, true_cfg_scale=4.0
- Community calls it "amazing prompt adherence" but skin can look "plastic"
- Nunchaku quantized version: 13 seconds per image on RTX 4080 Mobile (8 steps!)
- **This is the model to watch** - it does generation + editing

**HiDream-I1 Full** (17B, sparse DiT)
- SOTA on all benchmarks (GenEval 0.83, DPG-Bench 85.89, HPSv2.1 33.82)
- Beats Flux, DALL-E 3, Midjourney v5/v6 on human preference
- MIT license, fully commercial
- Needs 4 text encoders including Llama 3.1 8B (!)
- On 16GB VRAM: possible with GGUF quants (Q2 fits at 15.4GB GPU-only, Q3_K_S with tiled VAE)
- Full FP16: ~40GB
- Optimized (Draw Things engine): runs on 11GB VRAM, even on 2080 Ti
- **Crucial detail**: The Q2K quant quality is "not good" per users. You want Q4+ for professional work = 24GB+
- FULL version follows prompts better than DEV/FAST but significantly slower
- Uses FLUX VAE (bf16) - add --bf16-vae flag or waste VRAM on conversion

**Flux.1 Dev** (12B)
- Still the workhorse. MASSIVE LoRA ecosystem on Civitai
- At FP16: ~24GB. GGUF Q8: ~12GB. Q4: ~8GB.
- Community is split: some say "still king for NSFW and flexibility"
- Others: "Flux is agony for NSFW" and "super generic compositions"
- **Real workflow**: Use Flux for base composition, then inpaint faces/hands, then upscale
- Nunchaku acceleration: "divided gen speeds by 10" - seriously, 10x faster

**Wan 2.1/2.2 14B as IMAGE generator** (the surprise)
- People discovered Wan makes INCREDIBLE still images when you set frames=1
- On RTX 4080 16GB (GGUF Q5_K_S): ~42 seconds per fullHD (1920x1080) image
- Even Q3_K_S quality "still great"
- Materials and lighting understanding far beyond Flux
- One creator used Wan t2i to produce 4K images that "fooled my friends" (1,764 upvote post)
- **The workflow**: Generate at base res > upscale to 4K with SEEDVR2 > fine-tune if needed

**Chroma1** (Apache 2.0 base model)
- New foundation model. 105,000 H100 hours of training.
- Deliberately NOT aesthetically tuned - raw base for fine-tuning
- Good for tabletop/fantasy work. Community says "terrible for NSFW right now, needs a year to cook"
- Base (512x512) and HD (1024x1024) variants
- **For C41 Cinema**: Not directly useful yet. Watch for fine-tunes.

**SDXL** (still alive!)
- "Plenty of people still using SDXL. New stuff's quality increase is somewhere between 'sidegrade' and 'straight up worse'" (35 upvotes)
- "SDXL, the LoRA support is still unmatched" (27 upvotes)
- Key insight: SDXL with good LoRAs > Flux/HiDream base models for specific styles
- BigLust + DMD2 LoRA = 4-8 step amazing realism (speed king)
- PonyRealism, TAME Pony for realistic styles
- Runs FAST on modest hardware. A 1024 image in seconds vs minutes for Flux.

### Image Gen Deep Insights

**LoRA Training VRAM (critical for commercial work)**:
- Wan 2.1 T2I LoRA training on 16GB VRAM (RTX 4080): confirmed working
  - Using musubi-tuner, fp8_base, gradient_checkpointing
  - Rank 32: 8 sec/step, 250 images x 5 epochs in <3 hours
  - Rank 64: uses more RAM (~30GB system), larger LoRAs (300MB)
  - **This means you can train custom LoRAs for C41 Cinema clients on an RTX 5090**
  
- Flux LoRA training: ~16GB minimum, 24GB comfortable
- HiDream LoRA training: requires Flash Attention, CUDA 12.4+. Not well documented yet.
- Draw Things (macOS app) can train Flux LoRAs with 16GB system RAM (!!)

**Speed Comparison (real world, single image)**:
| Model | Hardware | Resolution | Time |
|-------|----------|-----------|------|
| Wan 2.2 Q6 | 12GB VRAM + 32GB RAM | 480x832 | ~3 min |
| Wan 2.2 Q6 + Lightning | 12GB VRAM | 1280x720 | ~10 min |
| Wan 2.1 Q5_K_S (1 frame) | RTX 4080 16GB | 1920x1080 | ~42 sec |
| HiDream Full Q3_K_S | RTX 4600Ti 16GB | 1024x1024 | slow (minutes) |
| Qwen Image (Nunchaku r128) | RTX 4080 Mobile | ~1328x1328 | 13 sec (8 steps!) |
| SDXL BigLust + DMD2 | RTX 4090 | 1024x1024 | ~5 sec |
| Flux Dev | RTX 4090 | 1024x1024 | ~25 sec |
| Flux Dev (Nunchaku) | RTX 4080 | 1024x1024 | ~3-5 sec |

**The "Plastic Skin" Problem**:
- Both Qwen Image and HiDream produce slightly plastic-looking skin
- Community fix: img2img through Wan 2.2 at low denoise (0.2-0.5) for realism
- Or: Add "Illustrious Realism Slider" LoRA + "Dramatic Lighting Slider" LoRA
- Or: Use Rescale CFG node to reduce plastic look
- "Qwen itself usually doesn't [look real]. You get that flux plastic look. But dropping Wan 2.2 low noise at the end is like magic." (12 upvotes)

### Image Gen VRAM Requirements (Realistic)

| Model | Quant/Mode | VRAM Needed | Quality Level |
|-------|-----------|-------------|---------------|
| SDXL + LoRAs | FP16 | 8-10GB | Good with right LoRAs |
| Flux Dev | GGUF Q8 | 12GB | Good |
| Flux Dev | FP16 | 24GB | Excellent |
| Flux Dev + Nunchaku | FP16 | 12-16GB | Excellent + fast |
| Qwen Image | Q4_K_M | ~12GB | Great prompt adherence |
| HiDream I1 Full | GGUF Q4+ | 16-20GB | SOTA benchmarks |
| HiDream I1 Full | FP16 | ~40GB | Maximum quality |
| Wan 2.2 (1 frame t2i) | GGUF Q5 | 16GB | Amazing photorealism |
| Wan 2.2 (1 frame t2i) | BF16 | 24-32GB | Best local photorealism |

---

## PILLAR 3: Video Generation

### The Real State of Local Video Gen (Practitioner Reports)

**Wan 2.1/2.2 14B** - The King, No Contest
- 1,764 upvote post showing Wan 2.2 realism that "fooled all my friends"
- Creator's detailed workflow:
  - Starting images: LOTS of steps (up to 60), upscaled to 4K with SEEDVR2
  - Generated at 1536x864 resolution
  - High noise heavy (65-75% on high noise), 6-8 steps low noise with speed-up LoRA
  - "Verbose prompts with most things described, leaving nothing to hallucinate"
  - Character consistency via LoRAs + prompting only
  - Whole project: ~2 weekends
  - **Pain point**: "Using a card like RTX 6000 with 96GB would probably solve [the SEEDVR2 flickering from batch joins]"
- Wan 2.1 VACE: All-in-one editing. Inpainting, outpainting, reference-based gen.
- Community ecosystem is MASSIVE: Video-As-Prompt, LightX2V, FramePack, UniAnimate, Phantom, CFG-Zero, TeaCache...

**Wan 2.2 5B** (lightweight)
- On 4GB VRAM GTX 1050 Ti + 16GB RAM: 29 min for 97 frames at 1024x576
  - "1 out of 3 attempts is usable"
  - Using Z-Image Turbo AIO for starting images (~2 min per image)
- On 12GB VRAM: ~3 min for 480x832 (81 frames) with Lightning LoRA
- Verdict: Usable but inconsistent quality

**FramePack** (13B, next-frame prediction)
- The democratizer. 1-minute 30fps video on 6GB VRAM.
- RTX 4090: 2.5 sec/frame (unoptimized), 1.5 sec/frame (TeaCache)
- RTX 3070 Ti laptop: 4-8x slower (~10-20 sec/frame)
- RTX 3060 laptop: Similar range
- **FramePack-F1**: Newer variant (May 2025)
- **FramePack-P1**: Coming next with "Planned Anti-Drifting" and "History Discretization" - specifically addresses long video drift
- Practical workflow from user (3070 8GB):
  - ~4 min per 1 second of video
  - Max 25-30 seconds per render (longer = distortion)
  - Post-process with HandBrake for codec conversion
  - Frame interpolation with After Effects Beta generative fill
  - Export at 4K 30fps (NOT 60fps - causes issues)
  - **Never send raw to CapCut, never upscale in CapCut, never alter FPS**

**FramePack vs Wan Direct**:
- FramePack = constant memory, great for length, lower VRAM needs
- Wan Direct = better quality per frame, but VRAM scales with frame count
- For commercial work: use Wan Direct for short hero clips, FramePack for longer sequences

**DGX Spark Video Gen** (from actual owner):
- Wan 2.2 720p@24fps: Runs but hits 91.8°C, 195W from wall
- "Uncomfortably hot to touch, fan grinding sound, coil whine"
- "Not an 'on your desk machine' - makes more sense in a server rack"
- Hot start benchmarks vs RTX 4090:
  - Z Image Turbo: 6.0s vs 2.6s (2.3x slower)
  - Qwen Image Edit (4 steps): 18.0s vs 8.5s (2.1x slower)
  - Qwen Image Edit (20 steps): 172.0s vs 57.8s (3x slower)
  - Flux 2 GGUF Q8: 265s vs OOM (Spark wins by fitting in memory)
  - Hunyuan3D 2.1: 185s vs OOM (same - Spark can run what 4090 can't)

### Video LoRA Training (Critical for C41 Cinema)

From the detailed Wan LoRA training guide:
- **16GB VRAM confirmed working** (RTX 4080) with:
  - fp8_base quantization during training
  - gradient_checkpointing enabled
  - adamw8bit optimizer
  - bf16 mixed precision
  - Rank 32: 8 sec/step, produces 300MB LoRAs
  - Rank 64: more RAM hungry, marginal quality improvement
  - 250 images x 5 epochs = 1,250 steps in <3 hours
- Training data: image-text pairs with Florence2 captioning + JoyTag tags
- **For video LoRAs**: Same framework (musubi-tuner) supports video data
- Character LoRAs for consistent characters across video clips

### Video Gen VRAM Requirements (Realistic)

| Model | Resolution | Frames | VRAM Needed | Time (4090) |
|-------|-----------|--------|-------------|-------------|
| Wan 2.2 5B | 480x832 | 81 | 8-12GB | ~3 min |
| Wan 2.2 14B | 480p | 81 | 18-24GB | ~4 min |
| Wan 2.2 14B | 720p | 81 | 24-40GB | ~10-15 min |
| Wan 2.2 14B | 1536x864 | varies | 32-48GB | varies |
| Wan 2.2 14B (upscale to 4K) | 4K | N/A | 48-96GB (batching) | varies |
| FramePack 13B | 640x480 | 1800 (1 min) | 6GB min | ~75 min |
| FramePack 13B | 720p | 1800 | 12-16GB | ~45 min |

---

## PILLAR 4: The Models You Didn't Know About

**Qwen-Image** (Aug 2025) - Text-to-image + editing. Best text rendering. Pipeline starter.
**Qwen Image Edit** - Instruction-based editing companion to Qwen-Image.
**Z-Image Turbo** - Incredibly fast image gen. Works on 4GB VRAM. Great for rapid concepting.
**HiDream-E1.1** - Only open-source instruction-based image editing model (like "remove this, change that").
**LightX2V** - Framework that makes Wan 2.1/2.2 run on RTX 4060 8GB (!).
**Nunchaku** - NVIDIA acceleration library. 10x speedup on Flux/Qwen Image. Game changer for iteration speed.
**SEEDVR2** - Video upscaler. Key part of the 480p-generate > 4K-upscale pipeline.
**TeaCache** - ~2x speedup across Flux, Wan, Hunyuan, HiDream. Free performance.

---

## THE ACTUAL WORKFLOW FOR C41 CINEMA

Based on what the best practitioners are actually doing:

### Still Image Production (ads, social, product shots)
1. **Concept**: Qwen Image for initial composition (best prompt adherence, 13 sec/image with Nunchaku)
2. **Refine**: img2img through Wan 2.2 at 0.2-0.5 denoise for photorealism
3. **Detail**: Inpaint faces/hands with Flux Dev or HiDream
4. **Upscale**: SEEDVR2 or Tiled Diffusion to 4K
5. **LoRA**: Train custom LoRAs for client brand consistency

### Video Production (social clips, concepts, drafts)
1. **Hero image**: Generate starting frame at high quality (60 steps, Wan t2i)
2. **Upscale**: Starting image to 4K with SEEDVR2
3. **Animate**: Wan 2.2 i2v at 1536x864 or 720p
4. **Extend**: FramePack for longer sequences (1 min+)
5. **Upscale video**: SEEDVR2 to 2K/4K (batch processing)
6. **Post**: HandBrake > Premiere Pro frame interpolation > Export 4K@30fps
7. **LoRA**: Character LoRAs for consistency, style LoRAs for brand look

### LLM Support (scripts, copy, code)
- Qwen3-32B for client comms, copy, brainstorming
- Qwen3-Coder-Next for web/app development
- Both run alongside creative workloads (just swap models)

---

## HARDWARE RECOMMENDATION (Models-First)

### What the models actually need:

**The bottleneck is VIDEO, not images or LLMs.**

For professional Wan 2.2 work at 720p+, you want 24-48GB VRAM.
For 4K upscaling without batch-join artifacts, you want 48-96GB.
For LoRA training (images + video), 16GB minimum, 24GB comfortable.

### Tier 1: RTX 5090 Build (~$3,500 total)
- 32GB GDDR7, 1,792 GB/s
- **Can do**: Everything at 480-720p. All image gen models. LoRA training. LLMs up to Qwen3-32B full quality. Qwen3-Coder-Next with split. FramePack easily.
- **Pain points**: Wan 14B at 720p is tight. No 4K video upscaling in one batch. Can't run Qwen3-235B.
- **Covers**: ~85% of the workflow described above

### Tier 2: RTX PRO 6000 Blackwell Build (~$12,000 total)
- 96GB GDDR7 ECC, 1,800 GB/s, 600W
- **Can do**: EVERYTHING. Wan at 1536x864+. 4K video upscale in one batch (no flickering). Qwen3-235B. HiDream Full at FP16. Future models.
- **Pain points**: 600W TDP, needs serious cooling and PSU. Single card (no NVLink).
- **Covers**: 100% of the workflow, future-proofed for 3+ years

### Tier 3: RTX 5090 NOW, PRO 6000 LATER (~$3,500 then ~$12,000)
- Start producing NOW on 5090
- Learn the tools, build workflows, train LoRAs, prove the concept
- When you hit the 32GB wall (and you will, for video upscaling), pitch the PRO 6000
- 5090 becomes secondary render node or gets sold

### Why NOT DGX Spark for C41 Cinema:
- 2-3x slower than RTX 4090 for the workloads that matter (image/video gen)
- 91°C and "uncomfortably hot" during video gen
- ARM architecture = dependency hell for half the tools
- $3,999 for worse gen performance than a $2,000 RTX 5090
- Only advantage: 128GB memory for huge LLMs. But that's not the bottleneck for a content agency.

---

## WHAT'S COMING (Watch List)

- **Flux 2** (full BF16 model exceeds 80GB VRAM) - will need PRO 6000 class cards
- **FramePack-P1** - Anti-drifting for truly long-form video. In development now.
- **Wan 3.x** - Alibaba shipping updates fast
- **Qwen-Image refinements** - Community fine-tunes already appearing (Jib Mix Qwen)
- **Chroma mature fine-tunes** - Apache 2.0 base model needs time to cook
- **LightX2V optimizations** - Making Wan run on 8GB is getting better
- **Nunchaku wider support** - Currently NVIDIA only, expanding to more models
- **Video LoRA tools** - musubi-tuner getting better rapidly
- **Hunyuan 3D 2.1** - 3D generation for product shots. Needs ~29GB VRAM.

The trend: Models getting smarter about memory (MoE, quantization, tiling, next-frame-prediction). But also getting BIGGER at full precision. The gap between "runs on 8GB" and "runs at full quality" is widening. More VRAM = more flexibility to try things without spending hours on optimization workarounds.
