# Local AI Setup Research for a Film Agency (Feb 2026)

Deep research from 40+ sources: Reddit (r/LocalLLaMA, r/StableDiffusion), tech reviews, hardware guides, GitHub repos, product listings, real user benchmarks.

---

## The Big Picture

You want three things running locally: **LLM chat/code**, **image generation**, and **video generation**. Each has different bottlenecks. Here's the honest state of things:

- **LLMs locally:** Genuinely great. 70B models rival GPT-3.5. Open models improving monthly.
- **Image gen locally:** Commercial-quality output is achievable NOW with Flux and proper prompting.
- **Video gen locally:** Usable for drafts, social content, concepting. Not yet matching Runway/Kling for polished final deliverables, but the gap is closing fast (new models monthly).

---

## Part 1: Hardware

### The #1 Rule: VRAM is King

Every source agrees. VRAM determines what you can run. If a model doesn't fit, it either crashes or crawls.

| VRAM | LLMs | Image Gen | Video Gen |
|------|------|-----------|-----------|
| 8 GB | 7B quantized | SD 1.5, SDXL (tight) | Wan2.1 1.3B (480p), FramePack |
| 12 GB | 7B full, 13B quantized | SDXL, Flux NF4 | Wan2.1 1.3B comfortable |
| 16 GB | 13B full, 20B quantized | Flux FP8 | Basic video gen |
| 24 GB | 30B+, 70B Q4 (tight) | Flux FP16, everything | HunyuanVideo FP8, most models |
| 32 GB | 70B Q4 comfortable | Everything + headroom | Comfortable video gen |
| 96 GB | 70B FP16, 120B+ quantized | No limits | No limits |

Sources: SLYD GPU Guide, Hardware Corner, InsiderLLM VRAM guide

### Memory Bandwidth = Speed

VRAM size determines what fits. Bandwidth determines how fast it runs.

| Hardware | Bandwidth | 7B Q4 Speed | Price |
|----------|-----------|-------------|-------|
| Mac M4 Pro (24-64GB) | 273 GB/s | ~50 tok/s | $1,400-2,200 |
| Mac M4 Max (36-128GB) | 546 GB/s | ~83 tok/s | $2,000-4,700 |
| Mac M3 Ultra (96-512GB) | 800 GB/s | ~92 tok/s | $4,000-8,000 |
| RTX 3090 (24GB) | 936 GB/s | ~100 tok/s | $700-900 used |
| RTX 4090 (24GB) | 1,008 GB/s | ~130 tok/s | $1,400-1,800 |
| RTX 5090 (32GB) | 1,792 GB/s | ~180+ tok/s | $2,000-2,500 |
| RTX PRO 6000 Blackwell (96GB) | 1,800 GB/s | ~60-90 tok/s* | $7,990-9,000 |
| DGX Spark (128GB unified) | 273 GB/s | ~31 tok/s (120B) | $3,999 |

*PRO 6000 batch 1 speeds are bandwidth-limited similar to a 5090, but it shines on batched inference (see benchmarks below).

Sources: InsiderLLM Mac vs PC guide, SLYD, real Reddit benchmarks

---

## GPU Deep Dive: Every Serious Option

### RTX PRO 6000 Blackwell (96GB) - $7,990-9,000

**The single-card VRAM king for desktops.**

Real specs (verified from PNY listing + Reddit benchmarks):
- 96 GB GDDR7 ECC, 512-bit bus
- ~1,800 GB/s bandwidth
- 24,064 CUDA cores, 5th gen Tensor Cores
- 600W TDP (Max-Q variant: 300W, 12% fewer TFLOPs)
- PCIe 5.0 x16, dual-slot form factor

**Real-world benchmarks from a Reddit user with the Max-Q variant (300W):**

Training: 7.5x faster than a single RTX 3090 in model pretraining while pulling only 300W. "Very quiet, I could sleep during a training run."

Inference (batch 1, vLLM):
- gpt-oss-120B: ~147-182 tok/s
- Qwen3-Coder-30B-A3B (GPTQ 4bit): ~172-179 tok/s
- Mistral Small 3.2 24B (AWQ): ~86-96 tok/s
- Qwen3-32B (AWQ): ~61-69 tok/s

Batched inference (where it really shines):
- gpt-oss-120B batch 32: ~2,000-2,146 tok/s total throughput
- Qwen3-4B batch 32: ~2,400-2,666 tok/s
- Almost linear scaling with batch size

**The reviewer's conclusion:** "Any model with 10-20B active parameters and up to 160B total parameters will be incredible on it." Best for processing large amounts of text, small-scale serving for 30-60 concurrent users. If you only care about batch-1 speed, 4x RTX 4090s at the same price would be faster.

Cost per GB: ~$93.75/GB (vs $25/GB for a used RTX 3090)

Video gen: DGX Spark user ran Wan2.2 720p@24fps on the same Blackwell architecture. Max GPU temp 91.8C, max TDP 101W, but "uncomfortably hot to touch" and noticeable coil whine. "Not really a 'on your desk machine'."

Source: Reddit r/LocalLLaMA benchmark posts

### RTX 5090 (32GB) - $1,999-2,500

**The new consumer king. The community's top pick for 2026.**

- 32 GB GDDR7, 512-bit bus
- 1,792 GB/s bandwidth
- 21,760 CUDA cores, 5th gen Tensor Cores
- 575W TDP
- Full SageAttention 2 support (basically doubles video gen speed)

A detailed r/StableDiffusion analysis concluded: "The RTX 5090 is 2.5-3x faster than a 3090 at computations, and the 32GB VRAM is very comfortable for Wan 2.2 video generation. With SageAttention 2, it basically doubles its already impressive video gen speed without visible quality loss."

The same poster analyzed the entire GPU market and concluded: 32GB will be the consumer VRAM ceiling for at least 2 years. No RTX 6090 expected until early/mid 2027. Dual 5080s (16GB each) are worse in every metric than a single 5090.

Z-Image (new SOTA realism model) speed on 5090: 1280x1920 image in ~95 seconds, 1680x1680 in ~110 seconds.

Source: Multiple r/StableDiffusion and r/LocalLLaMA posts

### RTX 4090 (24GB) - $1,400-1,800

**Still the most popular local AI GPU, but showing its age.**

Same 24GB VRAM ceiling as the 3090. Community consensus: if you're buying new, get a 5090 instead. The extra 8GB of VRAM matters more than anything else. 4090 only makes sense if you find a great used deal.

A dual 3090 builder found: "2x RTX 3090 GPUs offered nearly the same speed as 2x RTX 4090 when running AI models. For GPUs, it is much better to have 1 GPU with large VRAM than 2 GPUs."

Source: Ozeki dual 3090 build writeup, InsiderLLM

### RTX 3090 (24GB) - $700-900 used

**The value king. Still.** A 5-year-old card that remains competitive because VRAM is what matters. InsiderLLM calls it "the best deal in local AI hardware." At $25/GB, nothing touches it for price per VRAM.

Source: InsiderLLM GPU buying guide, multiple Reddit posts

### NVIDIA DGX Spark (128GB unified) - $3,999

**Brand new product worth knowing about.**

A tiny 1.13-liter, 240W box with 128GB unified LPDDR5X memory and a GB10 Grace Blackwell Superchip.

Real benchmarks from StorageReview:
- Llama 3.1 8B FP4: ~924 tok/s at 128 concurrency
- Qwen3 Coder 30B-A3B FP8: ~483 tok/s at batch 64

Real benchmarks from a Reddit user:
- GPT-OSS-120B: 31 tok/s, consumes 64GB (fits!)
- GLM-4.5-Air Q8: largest model that fits, ~12 tok/s, ~116GB VRAM
- 4090 is ~2.4x faster for same model at batch 1
- Wan2.2 video gen: works but "uncomfortably hot to touch, fan noise prevalent"

**The catch for your use case:** 273 GB/s memory bandwidth is SLOW. Same as a Mac M4 Pro. It's designed for batch inference and fine-tuning, not interactive single-user speed. And critically, it's ARM-based with limited CUDA support. Image/video gen ecosystem compatibility is uncertain.

**DGX Spark is NOT a good choice for a film agency.** It's designed for ML researchers and enterprise serving, not creative workloads.

Source: StorageReview teardown, Reddit user reviews

### Mac: Great for LLMs, Bad for Everything Else

**CRITICAL: The M4 Ultra DOES NOT EXIST.** Apple confirmed the M4 Max lacks UltraFusion. Current Mac Studio ships with M4 Max (128GB max) or the older M3 Ultra (up to 512GB). M5 Ultra expected mid-2026.

Mac's unified memory is unmatched for running huge LLMs (70B-405B) on a single quiet device. But:

- **Flux on Mac takes 20 MINUTES per image** vs under 1 minute on RTX 4090. That's not a typo.
- Video gen tools are mostly CUDA-only
- Training, fine-tuning, LoRA: mostly CUDA-only
- ComfyUI works via MPS backend but significantly slower

**For a film agency needing all three workloads, Mac is not the answer.** NVIDIA is the only serious option.

Community Mac users' main use: always-on quiet LLM server at 40-80W. That's it.

Source: InsiderLLM "Mac vs PC for Local AI" (extremely detailed comparison)

---

## Part 2: Software Stack

### LLM Inference

**Ollama** for daily use. One command install, dead simple. GPU acceleration automatic.
**llama.cpp** for max speed. 10-20% faster than Ollama when tuned. r/LocalLLaMA increasingly recommends going direct.
**vLLM** for serving multiple users or batched workloads. The PRO 6000 shines here.
**MLX** on Mac. Apple's framework, 20-50% faster than llama.cpp on Apple Silicon.

**Best models (Feb 2026):**
- 7-8B: Llama 3.1 8B, Qwen 2.5 7B, DeepSeek R1 Distill 8B
- 14B: Qwen 2.5 14B, DeepSeek R1 Distill 14B
- 32B: Qwen 2.5 32B, DeepSeek R1 Distill 32B (the "sweet spot" for quality/size)
- 70B: Llama 3.3 70B, Qwen 2.5 72B
- Coding: Qwen3 Coder (multiple sizes), DeepSeek Coder
- 120B+: GPT-OSS-120B, Mistral Large 123B

Source: awesome-local-ai GitHub, InsiderLLM model guides

### Image Generation

**ComfyUI** is the standard. Node-based, supports every model, 15% faster than alternatives, best VRAM management. Desktop installer available.

**Best models:**

**Flux 2** (from Black Forest Labs, Stable Diffusion creators):
- Insane prompt adherence. Follows extremely complex multi-paragraph prompts with camera settings, lighting, composition
- Renders readable text in images, draws hands correctly
- Full FP16 needs 24GB; NF4 quantized runs on 8-12GB
- RTX 4090: under 10 seconds/image FP16
- RTX 5090: ~95 seconds for 1280x1920 with Z-Image base
- Real user review: "Absolutely brilliant instruction follower. Realism doesn't yet match expectations for compute required."

**Z-Image base** (new contender, mentioned repeatedly on r/StableDiffusion):
- "Really, really realistic images, really easily"
- Best model for realism among Klein 9B, Chroma, SDXL
- Can natively generate at very high resolution (1920px+)
- BF16 recommended for 16GB+ VRAM

**LoRA training** for brand consistency is doable on 24GB GPUs. Train on your agency's style, apply to every generation. This is a major competitive advantage for a film agency.

**ComfyUI professional workflow tools:** There are now FLUX 2 JSON prompt generators with camera presets (Sony A7IV, Hasselblad X2D, Kodak Ektachrome), 60+ professional style presets (fashion editorial, commercial, cinematic, etc.), and lighting presets. Basically a professional photography studio in software.

Source: InsiderLLM Flux guide, r/StableDiffusion Z-Image and Flux 2 reviews, ComfyUI TBG Takeaways plugin

### Video Generation

**The frontier. Evolving monthly.**

**Wan 2.1 / 2.2** (Alibaba) - Current SOTA open video model
- 14B and 1.3B versions
- Text-to-video, image-to-video, video editing, video-to-audio
- 1.3B runs on 8GB VRAM (generates 5s 480p on RTX 4090 in ~4 min)
- 14B model: needs 16GB+ (with offloading to RAM), 24GB+ preferred
- Integrated into ComfyUI and Diffusers
- VACE extension for all-in-one video editing
- Wan 2.2 is out with improvements

**Detailed user guide from someone running Wan2.1 14B on an RTX 3070 (8GB!):**
- Uses --lowvram flag + 128GB system RAM as "Shared GPU Memory"
- RTX 3090 (24GB): ~6 seconds/iteration for 81 frames 480x480
- RTX 3070 (8GB): ~20-30 seconds/iteration with RAM offloading
- Can generate 81 frame (5 sec @ 16fps) video at 480x480, upscale 2x + interpolate 2x, in ~10-15 minutes
- "Always create your videos at low resolution then upscale. Same quality, 10x faster."
- I2V (image-to-video) workflow recommended: generate image with Flux/SDXL, then animate with Wan

**FramePack** (from ControlNet/Fooocus creator) - Game changer
- 1-minute (60 second) 30fps video on just 6GB VRAM with 13B model
- Next-frame prediction, see results progressively
- RTX 4090: ~2.5 sec/frame (unoptimized), ~1.5 sec/frame (TeaCache)
- One-click Windows installer
- Linux supported

**HunyuanVideo** (Tencent)
- v1.5 released Nov 2025
- FP8 weights available
- Avatar animation, custom video gen
- Large ComfyUI community

**Open-Sora 2.0** (11B model)
- On par with HunyuanVideo on benchmarks
- Fully open source including training code
- Trained for $200K

**LightX2V** - Acceleration framework for Wan2.1/2.2 that can run on RTX 4060 (8GB VRAM)

**The professional use case reality:**
- Local video gen is excellent for: concepting, storyboards, draft iterations, social media content, client previews
- Still behind cloud for: final polished TV commercials, hero shots requiring pixel-perfect consistency
- The workflow that works: Generate concept images with Flux -> Animate with Wan/FramePack -> Edit/composite traditionally -> Use cloud services (Runway, Kling) only for final hero shots if needed
- FramePack makes iterating on video concepts accessible on basically any modern GPU

Source: Wan2.1 GitHub, FramePack GitHub, HunyuanVideo GitHub, Open-Sora GitHub, detailed r/StableDiffusion Wan guide

---

## Part 3: Recommended Builds

### Build 1: "No Compromise" (~$10,500)

For running everything: 120B+ LLMs, full-quality image gen, 14B video gen.

| Component | Selection | Price |
|-----------|-----------|-------|
| GPU | RTX PRO 6000 Blackwell 96GB | ~$7,990 |
| CPU | AMD Ryzen 7 9800X3D | ~$460 |
| Motherboard | ASUS ProArt X870E-CREATOR WIFI | ~$507 |
| RAM | 96GB DDR5-6000 (2x48GB) | ~$1,050 |
| Storage | 2TB NVMe PCIe 4.0 | ~$143 |
| Case | Fractal Meshify 3 XL | ~$220 |
| PSU | 850W 80+ Gold | ~$110 |
| **Total** | | **~$10,480** |

What it runs: GPT-OSS-120B at 150+ tok/s. Flux at FP16 in seconds. Wan2.1 14B without compromise. Any future model up to 96GB.

Real user says: "Very quiet" (Max-Q variant at 300W). "Could sleep during training run with computer in same room."

BUT: If you only care about single-user interactive speed, the same $8K on 4x RTX 4090s would be faster at batch-1 inference. The PRO 6000 wins on VRAM capacity and batched workloads.

Source: Hardware Corner build, Reddit PRO 6000 benchmark

### Build 2: "Sweet Spot" (~$3,000-3,500)

Best bang for buck. Handles 90% of workloads.

| Component | Selection | Price |
|-----------|-----------|-------|
| GPU | RTX 5090 32GB | ~$2,000-2,500 |
| CPU | AMD Ryzen 5 9600X | ~$196 |
| Motherboard | MSI B650 Gaming Plus WiFi | ~$170 |
| RAM | 32GB DDR5-6000 | ~$329 |
| Storage | 2TB NVMe | ~$103 |
| Case + PSU | Mid tower + 1000W 80+ Gold | ~$210 |
| **Total** | | **~$3,000-3,500** |

What it runs: 70B LLMs quantized. Flux at FP16 fast. Wan2.1 14B video gen. Z-Image realism at high res. SageAttention 2 doubles video gen speed.

Community consensus: Best consumer GPU available. 32GB will be the ceiling for at least 2 years. Nothing better coming until 2027.

Note: 575W TDP. Need good cooling and a 1000W+ PSU.

Source: Multiple r/StableDiffusion analyses

### Build 3: "Prove It First" (~$1,700-1,850)

Start here if you want to learn the tools before going big.

| Component | Selection | Price |
|-----------|-----------|-------|
| GPU | RTX 3090 24GB (used) | ~$750-900 |
| CPU | AMD Ryzen 5 9600X | ~$196 |
| Motherboard | MSI B650 Gaming Plus WiFi | ~$170 |
| RAM | 32GB DDR5-6000 | ~$329 |
| Storage | 2TB NVMe | ~$103 |
| Case + PSU | Mid tower + 850W | ~$150 |
| **Total** | | **~$1,700-1,850** |

What it runs: 32B LLMs great, Flux FP16 in 12-15 sec/image, FramePack video gen, Wan2.1 14B (with RAM offloading if you have 64GB+ system RAM).

The value king. InsiderLLM: "A used RTX 3090 at ~$750 runs Flux at full FP16 in 12-15 seconds per image."

Source: InsiderLLM GPU buying guide

### Build 4: "The Brazilian Model" (Honorable Mention)

A r/LocalLLaMA user in Brazil built a business deploying local AI to enterprises using RTX 3090/4090 builds (~$5K setup). He charges $2-5K/month per client, nets ~$10K/month, bought a motorcycle and MacBook. His model: deploy local AI + dashboard showing ROI, cancel their cloud AI subscriptions, charge monthly maintenance.

Relevant to your agency: this is a proven business model for selling local AI services.

Source: Reddit r/LocalLLaMA

---

## Part 4: Critical Findings

### The Dual GPU Question

From the dual 3090 builder: "It is much better to have 1 GPU with large VRAM than 2 GPUs." Reasons:
- Single GPU uses less power
- Easier to cool
- Easier motherboard/case selection  
- PCIe lane limitations (only 1 GPU runs at full x16 on consumer boards)
- Most models can't be split across GPUs efficiently

The PRO 6000 benchmark user confirmed: "Cannot be used with tensor parallelism with Ampere" (i.e., can't pair a PRO 6000 with a 3090).

If you go multi-GPU, use identical cards.

Source: Ozeki build writeup, Reddit PRO 6000 benchmarks

### Electricity Costs

A user with dual 3090s at $0.65/kWh peak rate: "A few hours of tinkering burns ~2kW, meaning this rig could easily cost $5/day."

By comparison:
- Mac Studio M4 Max: ~$50/year
- PC + RTX 4090: ~$340/year
- PC + dual 3090s: ~$500+/year
- RTX PRO 6000 Max-Q (300W): comparable to a single 4090

Source: Reddit r/LocalLLaMA

### The "32GB is the Ceiling" Problem

Consumer GPUs will max at 32GB for the foreseeable future. NVIDIA won't put more VRAM in gaming cards because it would cannibalize their datacenter business. The gap between 32GB (consumer) and 96GB (workstation) is where the real decisions happen.

For running 70B+ models without quantization quality loss, you need to jump to the PRO 6000 tier ($8K+) or go Mac (which kills your image/video gen).

Source: r/StableDiffusion 5090 analysis, r/LocalLLaMA hardware discussion

### Software Ecosystem: CUDA Wins Everything

Tools that work on NVIDIA CUDA: everything.
Tools that work on Mac Metal/MPS: LLM inference, some image gen (slow), almost no video gen.
Tools that work on AMD ROCm: improving but still painful.

"Every AI tool works with CUDA. Every tutorial assumes CUDA. Every optimization is CUDA-first." There is no alternative for a multi-workload setup.

Source: InsiderLLM, multiple Reddit posts

---

## Part 5: Honest Limitations

1. **Video gen is not "push button, get commercial."** It's a tool in the pipeline. Generate at low res, upscale, interpolate, composite, edit. Professional output requires the same creative skill as traditional production, just faster iteration.

2. **Electricity costs add up.** A 575W GPU running 8 hours/day at $0.12/kWh = ~$200/year just for the GPU. Factor this in.

3. **Models improve monthly.** Whatever you buy today will run even better models in 6 months. The hardware investment is future-proof; the software stack will keep getting better.

4. **96GB is overkill for most daily work.** The sweet spot for most people is 24-32GB. The PRO 6000 is for people who need 70B+ at full quality OR want to serve multiple users OR want maximum headroom for future models.

5. **Used 3090s have no warranty.** Stress test for 24 hours before trusting it. Check for mining damage.

---

## My Recommendation

For C41 Cinema specifically:

**Start with Build 2 (RTX 5090, ~$3,000-3,500).** It handles LLMs, Flux image gen at full quality, Wan/FramePack video gen, and any current model. Learn ComfyUI, learn prompting, develop your workflows. This is where 90% of the creative work happens.

**If/when you outgrow 32GB** (wanting to run 120B+ models, serve multiple team members, or run massive video models at full precision), upgrade to the RTX PRO 6000 build. At that point you'll know exactly what you need and why.

**If budget is truly unconstrained,** go straight to Build 1. The PRO 6000 eliminates VRAM as a constraint. But be honest: the creative output quality difference between a $3K 5090 build and a $10.5K PRO 6000 build is small. The PRO 6000 gives you headroom and convenience, not fundamentally better creative output.

**Do NOT buy a Mac for this use case.** Do NOT buy a DGX Spark for this use case. Both are wrong tools for multi-workload creative work.

---

## Sources (40+)

### Guide Sites
- [SLYD: Best GPUs for AI Inference 2026](https://slyd.com/guides/inference-gpu-guide)
- [Hardware Corner: PC Builds for Local LLMs](https://hardware-corner.net/pc-builds-for-local-llms/)
- [Fluence: 7 Best GPU for LLM 2026](https://fluence.network/blog/best-gpu-for-llm/)
- [InsiderLLM: Mac vs PC for Local AI](https://insiderllm.com/guides/mac-vs-pc-local-ai/)
- [InsiderLLM: GPU Buying Guide](https://insiderllm.com/guides/gpu-buying-guide-local-ai/)
- [InsiderLLM: Flux Locally](https://insiderllm.com/guides/flux-locally-complete-guide/)
- [InsiderLLM: Stable Diffusion Locally](https://insiderllm.com/guides/stable-diffusion-locally-getting-started/)
- [InsiderLLM: Running LLMs on Mac M-Series](https://insiderllm.com/guides/running-llms-mac-m-series/)
- [GPU Bottleneck Calculator: Best GPUs for AI 2026](https://gpubottleneckcalculator.com/blog/best-gpus-for-ai-and-deep-learning/)
- [InferenceBrief: AI Hardware for Local LLMs 2026](https://inferencebrief.ai/answers/what-ai-hardware-should-i-use-for-running-local-llms-in-2026-202601)

### GitHub Repos
- [awesome-local-ai](https://github.com/msb-msb/awesome-local-ai)
- [Wan2.1](https://github.com/Wan-Video/Wan2.1)
- [HunyuanVideo](https://github.com/Tencent-Hunyuan/HunyuanVideo)
- [FramePack](https://github.com/lllyasviel/FramePack)
- [Open-Sora](https://github.com/hpcaitech/Open-Sora)

### Hardware Reviews
- [StorageReview: NVIDIA DGX Spark Review](https://storagereview.com/review/nvidia-dgx-spark-review-the-ai-appliance-bringing-datacenter-capabilities-to-desktops)
- [PNY RTX PRO 6000 Blackwell listing](https://centralcomputer.com)

### Reddit (Real User Experiences)
- r/LocalLLaMA: RTX PRO 6000 Max-Q benchmark with vLLM (training 7.5x faster than 3090, detailed inference benchmarks)
- r/LocalLLaMA: RTX 4090 vs 5090 vs PRO 6000 throughput benchmark (neuralrack)
- r/LocalLLaMA: DGX Spark hands-on review (GPT-OSS-120B at 31 tok/s, Wan2.2 video gen)
- r/LocalLLaMA: "Sanity check: Kimi K2.5 on 2x RTX PRO 6000" build plan
- r/LocalLLaMA: Large model inference options analysis (3090 stacking vs EPYC server vs PRO 6000 vs Mac Ultra)
- r/LocalLLaMA: Brazilian entrepreneur local AI business model ($10K/month)
- r/LocalLLaMA: Dual RTX 3090 AI server build writeup (Ozeki)
- r/LocalLLaMA: Electricity costs and ROI analysis
- r/StableDiffusion: Wan2.1 14B on RTX 3070 (8GB) detailed guide with memory management
- r/StableDiffusion: "Why I'm buying a 5090" complete market analysis
- r/StableDiffusion: Flux 2 review (prompt adherence "absolutely insane", realism "disappointing for compute")
- r/StableDiffusion: Z-Image base workflow guide (SOTA realism)
- r/StableDiffusion: ComfyUI FLUX 2 JSON prompt generator with professional presets
