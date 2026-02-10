# The Best Local AI Models for a Film/Content Agency
## No Hardware Constraints. Just the Best.

*Feb 10, 2026 | Sources: 40+ Reddit threads, HuggingFace, GitHub, Civitai, BFL blog, practitioner reports*

---

## THE SHORT VERSION

If money and hardware are no object, here's what you run:

**LLM**: DeepSeek V3.1 / Kimi K2 / Qwen3-235B (MoE giants) + Qwen3-Coder-Next (coding)
**Image**: Qwen-Image > Wan 2.2 i2i pipeline, with Flux 2 Dev for multi-reference editing
**Video**: Wan 2.2 14B (generation) + FramePack-P1 (long-form) + SEEDVR2 (upscaling)
**3D**: Hunyuan 3D 2.1 (product shots, asset gen)

Now here's why, and the stuff you can't Google.

---

## LLM: THE ABSOLUTE BEST YOU CAN RUN LOCALLY

### Tier 1: Frontier MoE Models (The Giants)

These are the models that compete with GPT-5, Claude Opus, Gemini 2.5 Pro. They're massive but run locally because MoE means most params are dormant.

**DeepSeek V3.1 "Terminus"** 
- The reigning king of open-weight general intelligence
- MoE architecture (685B total, ~37B active per token)
- On the 768GB multi-GPU build (8x 3090 + 2x 5090): 24.9 tok/s at Q2XXS, 100% GPU offload
- Workaround to run it: Multi-GPU with llama.cpp layer splitting. 256GB+ total VRAM recommended for decent quant. Or use KTransformers for CPU/GPU hybrid.
- The person running the $17K 10-GPU rig specifically built it for DeepSeek and Kimi K2

**Kimi K2** (Moonshot AI)
- Newest frontier MoE model. Competing directly with DeepSeek V3
- On the 10-GPU rig: 19.6 tok/s at TQ1 (87% GPU offload)
- Massive model, needs lots of memory, but quality is frontier-class

**MiniMax M2.1**
- The €9,000 dual-GH200 guy runs this as his daily driver for coding via Claude Code
- With FP8+INT4 AWQ quant on 192GB VRAM: ~78 tok/s (short context), 66 tok/s (medium)
- 163K context window running locally
- His verdict: "Better speeds than Claude Code with Sonnet, and the results vibe well"

**Qwen3-235B-A22B**
- 235B total, 22B active. 128K context.
- On the 10-GPU rig (256GB VRAM): 31.5 tok/s at Q6_K_XL
- That's FAST for a frontier model
- Competes with DeepSeek R1, o1, Gemini 2.5 Pro
- Cheapest-to-run frontier model because of efficient MoE routing
- One commenter: "I get 22 tok/s on Qwen3 235B Q4_K_XL fully in VRAM using six Mi50s. The entire rig cost me ~€1,600"

**GLM-4.5 / GLM-4.6 / GLM-5 (coming)**
- ChatGLM team. GLM-5 confirmed coming Feb 2026
- GLM-4.6 Q4_K_XL on the 10-GPU rig: 26.6 tok/s
- GLM-4.5-Air Q8: fits in 116GB VRAM on DGX Spark, runs at 12 tok/s
- These models are underrated. Great multilingual, strong reasoning.

### Tier 2: Specialist Models

**Qwen3-Coder-Next** (80B total, 3B active)
- THE coding model. Specifically designed for coding agents.
- Hybrid DeltaNet/MoE: 3 DeltaNet layers for every 1 attention layer. This means memory-efficient long-context without quadratic attention cost.
- 256K native context. Not extended, NATIVE.
- 512 experts, 10 active. Only 3B params compute per token.
- Competes with Claude Sonnet-class for coding tasks
- Workaround: CPU MoE offload lets it run on modest hardware. 15GB VRAM + 30GB RAM.
- For C41 Cinema: This is your web development, automation, script writing model.

**Qwen3-32B** (dense)
- When you need fast, reliable chat without MoE complexity
- Matches old 72B quality. 128K context.
- The "reliable workhorse" for daily use

### What Matters for a Content Agency LLM

You're not doing PhD-level reasoning. You need:
1. **Fast, high-quality writing** (scripts, copy, client emails) > Qwen3-235B or Qwen3-32B
2. **Code** (websites, tools, automation) > Qwen3-Coder-Next
3. **Long context** (reading scripts, documents, contracts) > Any of the MoE models at 128K+

The frontier MoE models (DeepSeek V3.1, Kimi K2, Qwen3-235B) are genuinely comparable to the cloud APIs you pay for. The difference is you own it.

---

## IMAGE GENERATION: THE ABSOLUTE BEST LOCAL

### Tier 1: The Pipeline (How Professionals Actually Work)

Nobody uses one model. The professional workflow is a chain:

**Step 1: Composition & Prompt Adherence**

**Qwen-Image** (Aug 2025, Alibaba/Qwen team)
- Text-to-image AND image editing in one model
- Best prompt adherence of any open model (based on community consensus)
- Best text rendering (English AND Chinese, renders mathematical formulas accurately)
- Diffusers-based, 50 steps default
- Can do: generation, style transfer, object insertion/removal, text editing in images, human pose manipulation
- Weakness: Skin can look "plastic" on its own
- Speed with Nunchaku acceleration: 13 seconds per image on RTX 4080 Mobile
- **This is the model that changed the game in mid-2025**

**Step 2: Photorealism Refinement**

**Wan 2.2 14B** (low-noise img2img, 1 frame)
- Feed Qwen-Image output through Wan 2.2 at 0.2-0.5 denoise
- "Dropping Wan 2.2 low noise at the end is like magic" (12 upvotes, practitioner consensus)
- Produces the most photorealistic output of any local pipeline
- Materials, lighting, skin texture all dramatically improved
- On RTX 4080 16GB (GGUF Q5): ~42 sec per fullHD image

**Step 3: Upscale**

**SEEDVR2**
- Video/image upscaler. State of the art for local upscaling.
- 2x-4x upscale with detail hallucination
- Used by the Wan 2.2 realism creator (1,764 upvote post) for 4K output

**Step 4: Detail Fix**

**HiDream-E1.1** (instruction-based image editing)
- "Remove this watch. Change the background to a coffee shop. Fix the hands."
- Only open-source model of its kind
- For C41 Cinema: Fix specific details in generated images with natural language

### Tier 2: Standalone Models (When You Need One Model)

**FLUX.2 [dev]** (32B, Black Forest Labs, just released)
- 32 billion parameter rectified flow transformer
- MASSIVE upgrade from Flux.1: multi-reference support (up to 10 images), character/style consistency without fine-tuning, image editing built in
- Uses Mistral-3 24B VLM as text encoder (!)
- Can generate AND edit in one model
- Editing at up to 4MP (4 megapixels)
- Open-weight, non-commercial license
- FP8 optimized version for consumer GPUs (collaboration with NVIDIA + ComfyUI)
- BF16 full: exceeds 80GB VRAM. 4-bit quantized: runs on RTX 4090/5090.
- "State of the art in open text-to-image generation, single-reference editing and multi-reference editing"
- **For C41 Cinema: The multi-reference capability is huge.** Feed it 3 product shots + a brand guide and it generates consistent new shots.

**HiDream-I1 Full** (17B, sparse DiT)
- SOTA on all academic benchmarks (GenEval, DPG-Bench, HPSv2.1)
- MIT license (fully commercial, unlike Flux 2 Dev)
- 4 text encoders including Llama 3.1 8B
- Draw Things engine runs it optimized on 11GB VRAM
- Full FP16: ~40GB
- Best for: Benchmark-winning quality when you need numbers to back it up

**Qwen Image Edit**
- Companion to Qwen-Image for instruction-based editing
- Nunchaku accelerated version available for speed

### Tier 3: Speed Demons (Rapid Iteration)

**Z-Image Turbo** - Works on 4GB VRAM. Great for rapid concepting.
**Flux.1 Schnell** - 4-step generation. Seconds per image.
**SDXL + DMD2 LoRA** - 4-8 steps, amazing realism for its speed. Seconds per image.
**Nunchaku** on anything - 10x speedup on Flux/Qwen models. Turns minutes into seconds.

### The LoRA Ecosystem (Critical for Commercial Work)

**For Flux 1.x**: Massive Civitai ecosystem. Thousands of LoRAs. Celebrity faces, styles, concepts, lighting.
**For Wan 2.1/2.2**: Growing fast. Character LoRAs, style LoRAs. Training confirmed on 16GB VRAM with musubi-tuner.
**For SDXL**: Still the biggest ecosystem. PonyRealism, BigLust, TAME for photorealism.
**For Flux 2**: Just launched, ecosystem building.
**For HiDream**: Very early. Not many LoRAs yet.

**LoRA Training for C41 Cinema**:
- Train character LoRAs for consistent brand mascots/models
- Train style LoRAs for your signature C41 look
- Train product LoRAs for client product consistency
- Wan video LoRA training: 16GB VRAM, musubi-tuner, 3 hours for 250 images x 5 epochs
- Flux LoRA training with Draw Things: possible with 16GB system RAM on Mac

---

## VIDEO GENERATION: THE ABSOLUTE BEST LOCAL

### Tier 1: Quality King

**Wan 2.2 14B** (Alibaba, latest version)
- UNDISPUTED best open-source video model
- HunyuanVideo team themselves acknowledge it: "outperforms previous state-of-the-art including Runway Gen-3, Luma 1.6"
- The 1,764-upvote realism post: Zero film grain, subtle mouth movement, eye rolls, brow movement, focus shifts. All 100% local.
- Creator's workflow details:
  - Starting images: 60 steps, upscaled to 4K with SEEDVR2
  - Generated at 1536x864 resolution
  - High noise (65-75%), 6-8 steps low noise with speed-up LoRA
  - "Verbose prompts describing everything, leaving nothing to hallucinate"
  - Self-Forcing / CausVid LoRAs for faster generation
  - Took 2 weekends
  - Pain point: "RTX 6000 with 96GB would probably solve [batch upscaling artifacts]"

**Wan 2.2 VACE** (Video Any-edit Creation Engine)
- All-in-one video editing model built on Wan 2.1
- Inpainting, outpainting, reference-based generation in one model
- The Swiss Army knife for video

**Wan ecosystem**: Video-As-Prompt, LightX2V, Phantom (multi-subject), UniAnimate-DiT (human animation), MagicTryOn (virtual try-on), AniCrafter (3D avatar animation)

### Tier 2: Long-Form & Accessibility

**FramePack** (13B, from ControlNet creator lllyasviel)
- Next-frame prediction: constant VRAM regardless of video length
- 1-minute 30fps video on 6GB VRAM
- RTX 4090: 1.5 sec/frame with TeaCache (1 minute video ~45 min)
- **FramePack-P1** (in development): Planned Anti-Drifting + History Discretization for truly consistent long video
- **FramePack-F1** (released May 2025): Available now
- Practical limit: 25-30 seconds per render before distortion. Chain renders for longer.

### Tier 3: Competitors

**HunyuanVideo** (Tencent, 13B+)
- Was state of the art before Wan. Still very good.
- 720p generation, FP8 weights available
- Multi-GPU parallel inference via xDiT
- ~29GB VRAM for full generation
- Good for: Anime-style video (some users prefer it over Wan for certain styles)

**Hunyuan 3.0** (mentioned by users as "almost none can run locally")
- Significantly better quality than Hunyuan 1.0/2.0
- Very heavy memory requirements
- Not fully documented yet

### Video Speed-Up Techniques (Running Bigger Models Faster)

**TeaCache**: ~2x speedup across Wan, Flux, HunyuanVideo, HiDream. Free.
**Self-Forcing / CausVid LoRAs**: Speed-up LoRAs that reduce denoising steps. Used by the Wan realism creator.
**LightX2V**: Framework making Wan 2.1/2.2 run on RTX 4060 8GB VRAM.
**CFG-Zero***: Enhances Wan from the perspective of classifier-free guidance. Better quality, same speed.
**Nunchaku**: 10x on image models, being extended to video.

### Cloud vs Local Quality Comparison (Honest Assessment)

Where local **wins**:
- Unlimited generation (no credits, no rate limits)
- Custom LoRAs (character consistency, brand style)
- Full control over every parameter
- Privacy (client work stays on your machine)
- Speed for iteration (generate hundreds of variations overnight)

Where cloud **still wins** (as of Feb 2026):
- Runway Gen-3 Alpha Turbo, Kling 2.0, Veo 2: still slightly better for photorealistic humans in motion
- Sora: still better for complex multi-scene narratives
- BUT: The gap is closing fast. The Wan 2.2 realism post literally has comments saying "this looks better than Runway"

---

## BONUS MODELS YOU SHOULD KNOW

**Hunyuan 3D 2.1** - Generate 3D models from text/image. Product shots, asset gen. ~29GB VRAM.
**Qwen3-TTS** (just open-sourced) - Text-to-speech. Local voice generation.
**JoyCaption** - Auto-caption images for LoRA training. Florence2 + JoyTag combo.
**Superlinear Attention** (new research) - 10M token context on single GPU. 1M context at 109 tok/s decode. This will change LLM context windows.

---

## THE DEFINITIVE MODEL STACK FOR C41 CINEMA

### Daily Operations
- **Client comms/writing**: Qwen3-235B-A22B or Qwen3-32B
- **Coding/automation**: Qwen3-Coder-Next (256K context)
- **Quick image concepts**: Qwen-Image + Nunchaku (13 sec/image)
- **Polished images**: Qwen-Image > Wan 2.2 i2i > SEEDVR2 upscale
- **Product/brand shots**: Flux 2 Dev (multi-reference, 10 images, consistency)
- **Image editing**: HiDream-E1.1 or Qwen Image Edit

### Video Production
- **Hero clips** (social, ads): Wan 2.2 14B i2v at 1536x864+
- **Long-form** (1 min+): FramePack
- **Character consistency**: Wan 2.2 + custom character LoRAs
- **Upscaling**: SEEDVR2 to 2K/4K
- **Speed**: TeaCache + Self-Forcing LoRAs

### Brand Consistency Pipeline
1. Train client-specific LoRAs (character, product, style)
2. Use LoRAs across both image and video generation
3. Multi-reference Flux 2 Dev for brand guide adherence
4. Consistent look across all deliverables

### Quality Assurance
- Generate at high step count (40-60 steps for hero images)
- Use verbose prompts describing everything
- Generate many variations, cherry-pick the best
- Post-process: HandBrake > Premiere Pro > After Effects
- Add film grain, color grade in post (not in generation)

---

## HOW PEOPLE ACTUALLY RUN THESE MONSTERS

The multi-GPU community is real. Here are actual working setups:

**The $17K Monster** (from Reddit, supporting a graphic designer):
- 8x RTX 3090 + 2x RTX 5090 = 256GB+ total VRAM
- Threadripper Pro 3995WX, 512GB DDR4
- Runs DeepSeek V3.1 at 25 tok/s, Wan 2.2 720p, Qwen3-235B at 31.5 tok/s
- All in a Thermaltake Core W200 dual-system case
- "The objective was to make a system for running extra large MoE models that is also capable of lengthy video generation and rapid image gen"
- Key insight: Under actual multi-GPU inference load, each 3090 only pulls ~150W (not 350W), total system ~1,700W

**The €9K Dual-GH200** (from Reddit, coder):
- 2x GH200 96GB = 192GB VRAM
- Runs MiniMax M2.1 at 78 tok/s, 163K context
- No NVLink (PCIe5 only), but TP2 (tensor parallel) works great
- PP2 (pipeline parallel) faceplanted despite guides recommending it

**The €1,600 Budget Beast** (from Reddit comment):
- 6x AMD Mi50 GPUs
- Runs Qwen3-235B Q4_K_XL fully in VRAM at 22 tok/s
- 1/10th the cost of the monster build

**Key Workarounds for Big Models**:
- **llama.cpp layer splitting**: Split model across multiple GPUs regardless of brand/size mix
- **KTransformers**: CPU/GPU hybrid inference for models that don't fit in VRAM
- **CPU MoE offload** (--cpu-moe in llama.cpp): Keep expert weights in RAM, only active experts hit GPU
- **GGUF quantization**: Q4_K_M or Q3_K_M makes 200B+ models fit in 64-96GB
- **FP8 quantization**: For diffusion models, minimal quality loss, halves memory
- **Tiled VAE**: Decode large images in tiles when VRAM is tight
- **Model swapping**: Don't run everything simultaneously. Load, use, unload, load next.
