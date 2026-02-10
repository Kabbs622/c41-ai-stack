# C41 Cinema: Hardware Research
*No budget limit. No money wasted. Every dollar justified.*

---

## WHAT WE ACTUALLY NEED (VRAM Map)

Before picking hardware, here's exactly what VRAM each model in our stack uses. This prevents overbuying.

### Models We'll Run (From Our Stack)

| Model | Task | VRAM Used | Notes |
|-------|------|-----------|-------|
| **Qwen3-Coder-Next Q4_K_XL** | Daily LLM (chat, code, writing) | 15GB GPU + 30GB RAM | CPU MoE offload. Real numbers from practitioners. |
| **Qwen3-Coder-Next Q4_K_M** | Same, slightly smaller | 12-15GB GPU + 30GB RAM | User on 3060 12GB gets 24 tok/s gen, 254 tok/s prefill |
| **Qwen3-235B Q3_K_M** | Frontier reasoning | 105GB | Need multi-GPU. German R9700 build: 24.5 tok/s gen |
| **Qwen3-32B Q5** | Fast daily tasks | 24GB | Fits on single 24GB card |
| **Qwen-Image + Nunchaku** | Image composition | 16-24GB | 13 sec/image |
| **Wan 2.2 14B GGUF Q6_K** | Video gen (i2v) | 16-24GB | The Jeffu client work post: single 4090, 1856x1040 |
| **Wan 2.2 14B** (full precision) | Video gen (best quality) | 32-48GB | For hero shots |
| **SEEDVR2** | 4K upscaling | 24-48GB | Biggest VRAM hog for single operations |
| **Flux 2 Dev 4-bit** | Multi-reference images | 24GB | Brand consistency |
| **HuMo 17B** | Lip-sync dialogue | ~48-68GB | New, exact requirements TBD |
| **Qwen3-TTS** | Voice generation | 4-8GB | Runs alongside other models |
| **MMAudio** | Video-to-audio | 8-16GB | Ambient sound for video |

### Key Insight: You Don't Run Everything At Once

You swap between workloads:
- **Workload A (daily)**: Qwen3-Coder-Next (15GB) + Qwen3-TTS (4GB) = ~19GB active
- **Workload B (image gen)**: Qwen-Image (24GB) + Wan 2.2 i2i (16GB) = ~40GB peak
- **Workload C (video gen)**: Wan 2.2 i2v (24-48GB) + MMAudio (16GB) = ~48-64GB peak
- **Workload D (talking head)**: HuMo (48-68GB) + TTS (4GB) = ~52-72GB peak
- **Workload E (frontier LLM)**: Qwen3-235B (105GB) = needs multi-GPU
- **Workload F (upscaling)**: SEEDVR2 (48GB) = dedicated pass

**Minimum useful VRAM: 48GB** (handles workloads A-C with quantized models)
**Comfortable: 96GB** (handles everything except frontier LLM without swapping)
**No compromises: 128GB+** (frontier LLM + gen simultaneously)

---

## GPU OPTIONS (Real Pricing, Feb 2026)

### NVIDIA

| GPU | VRAM | New Price | Used Price | VRAM/$ (new) | Best For |
|-----|------|-----------|-----------|-------------|----------|
| **RTX 3090** | 24GB | Discontinued | $600-900 | $33-40/GB | Budget multi-GPU builds. Proven ecosystem. |
| **RTX 4090** | 24GB | ~$1,800 | $1,400-1,600 | $75/GB | Single-card powerhouse. Best CUDA performance per card. |
| **RTX 5090** | 32GB | $2,000 MSRP | $1,300-2,100 (scalped) | $63/GB | Newest gen. Faster than 4090 for gen tasks. Hard to find at MSRP. |
| **RTX Pro 6000 Blackwell** | 96GB | ~$8,000-10,000 | N/A (new product) | $83-104/GB | Single card = 96GB. No multi-GPU hassle. Pro driver. |

**NVIDIA Pros**: Best software ecosystem. ComfyUI, CUDA, Nunchaku, TorchCompile, SageAttention all work perfectly. Every tutorial assumes NVIDIA.
**NVIDIA Cons**: Expensive per GB of VRAM. Consumer cards limited to 24-32GB per card.

### AMD

| GPU | VRAM | New Price | Used Price | VRAM/$ (new) | Best For |
|-----|------|-----------|-----------|-------------|----------|
| **Radeon AI PRO R9700** | 32GB | $1,300-1,400 | N/A (too new) | $41-44/GB | Best VRAM/$ for new cards. 128GB with 4 cards for $5,200. |
| **MI100** | 32GB | Discontinued | ~$400-600 | $13-19/GB | Cheapest VRAM per dollar. Data center card. |
| **MI250** | 128GB | Used ~$2,000-3,000 | varies | $16-23/GB | Massive single-card VRAM. Data center. |

**AMD Pros**: Way more VRAM per dollar. R9700 is 32GB for $1,300 vs NVIDIA's 24GB for $1,800 (4090). 4x R9700 = 128GB for $5,200.
**AMD Cons**: ROCm software support is worse. ComfyUI works but with more setup friction. Some optimizations (Nunchaku, SageAttention, TorchCompile) may not work or work slower. Video gen specifically may have issues.

### The NVIDIA vs AMD Decision

**For your use case (content generation agency):** NVIDIA is the safer choice. Every ComfyUI workflow, every Wan 2.2 optimization, every Nunchaku speedup, every tutorial — they're all built for CUDA. Going AMD saves money on VRAM but costs time in compatibility issues, especially for image/video gen.

The R9700 is incredible for LLM inference (see benchmarks: Qwen3-Coder-Next at 39 tok/s on 4x R9700). But for ComfyUI + diffusion models, NVIDIA's ecosystem advantage is real.

**Exception**: If you went hybrid — AMD cards for LLM inference + NVIDIA cards for generation — you'd get the best of both. But that adds complexity.

---

## BUILD OPTIONS (Ranked by Value)

### Build 1: "The Workhorse" — $6,000-8,000
*Best value. No wasted capacity. Handles 90% of workloads.*

| Component | Choice | Price |
|-----------|--------|-------|
| GPU 1 | RTX 5090 32GB | ~$2,000 |
| GPU 2 | RTX 3090 24GB (used) | ~$700 |
| CPU | AMD Ryzen 9 9950X or Threadripper 7960X | $500-1,200 |
| RAM | 128GB DDR5 | ~$300 |
| Motherboard | X870 or TRX50 (dual x16 slots) | $300-600 |
| Storage | 4TB NVMe Gen4 | ~$250 |
| PSU | 1600W 80+ Gold | ~$300 |
| Case + cooling | Mid-tower with good airflow | ~$300 |
| **Total** | | **$4,650-5,650** |

**56GB total VRAM. What it runs:**
- Qwen3-Coder-Next on GPU2 (15GB), image/video gen on GPU1 (32GB) — simultaneously
- Wan 2.2 full quality on GPU1 (32GB)
- HuMo quantized across both GPUs
- TTS always on in background
- Qwen3-235B: needs to swap out gen models, use both GPUs + RAM offload

**What it doesn't do well:** Frontier 235B LLM + gen simultaneously. 4K upscaling with SEEDVR2 needs swapping.

### Build 2: "The Producer" — $8,000-11,000
*The sweet spot. Nothing wasted, nothing compromised for your actual workloads.*

| Component | Choice | Price |
|-----------|--------|-------|
| GPU 1 | RTX 5090 32GB | ~$2,000 |
| GPU 2 | RTX 5090 32GB | ~$2,000 |
| GPU 3 | RTX 3090 24GB (used) | ~$700 |
| CPU | Threadripper 7960X or 7970X | $1,200-2,000 |
| RAM | 256GB DDR5 | ~$600 |
| Motherboard | TRX50 (4x PCIe x16) | ~$800 |
| Storage | 4TB NVMe Gen4 | ~$250 |
| PSU | 2000W 80+ Platinum | ~$400 |
| Case | Enthoo Pro 2 or similar tower | ~$200 |
| Cooling | AIO + case fans | ~$200 |
| **Total** | | **$8,350-9,150** |

**88GB total VRAM. What it runs:**
- LLM on GPU3 + gen on GPU1/2 simultaneously
- Qwen3-235B Q3 across GPU1+2 (64GB) while TTS runs on GPU3
- Wan 2.2 full precision on single 5090
- HuMo across GPU1+2
- SEEDVR2 4K upscaling on single 5090
- Everything in our stack without swapping except the biggest frontier models

### Build 3: "The Studio" — $12,000-16,000
*For running frontier models + generation simultaneously. The $17K Reddit build, refined.*

| Component | Choice | Price |
|-----------|--------|-------|
| GPUs | 4x RTX 3090 (used) + 2x RTX 5090 | $2,800 + $4,000 |
| CPU | Threadripper PRO 7975WX | ~$2,500 |
| RAM | 512GB DDR5 | ~$1,200 |
| Motherboard | WRX90 (7 PCIe slots) | ~$900 |
| Storage | 8TB NVMe | ~$400 |
| PSUs | 1600W + 1300W | ~$500 |
| Case | Enthoo Pro 2 Server or W200 | ~$200 |
| Cooling + risers | AIO + fans + PCIe risers | ~$400 |
| **Total** | | **$12,900-14,900** |

**160GB total VRAM. Runs literally everything.**

### Build 4: "Clean and Simple" — $8,000-10,000
*Single pro card. Zero multi-GPU hassle.*

| Component | Choice | Price |
|-----------|--------|-------|
| GPU | RTX Pro 6000 Blackwell 96GB | ~$8,000 |
| CPU | AMD Ryzen 9 9950X | ~$500 |
| RAM | 128GB DDR5 | ~$300 |
| Motherboard | X870 | ~$300 |
| Storage | 4TB NVMe | ~$250 |
| PSU | 1000W | ~$150 |
| Case + cooling | Standard | ~$200 |
| **Total** | | **$9,700** |

**96GB in a single card. What it runs:**
- Everything except DeepSeek V3.1 (needs 256GB+)
- Qwen3-235B Q4 with room to spare
- No multi-GPU driver issues, no PCIe lane splitting, no PSU headaches
- Clean, quiet, simple

**Trade-off:** More expensive per GB than multi-3090 builds. Can't upgrade piecemeal.

---

## MY RECOMMENDATION

**Build 2 ("The Producer") at ~$9,000** is the sweet spot for you.

Here's why:
- 88GB VRAM covers every workload in our stack without waste
- 3 GPUs means LLM stays loaded while you generate (no swapping for daily work)
- Two 5090s give you the fastest gen speed available (matters for iteration)
- The 3090 as a dedicated LLM card is perfect for Qwen3-Coder-Next (only needs 15GB VRAM)
- 256GB RAM enables CPU MoE offload for frontier models when needed
- Threadripper gives PCIe lanes for all 3 cards at full bandwidth
- Room to add a 4th GPU later if needed

**Build 4 ("Clean and Simple")** is worth considering if you value simplicity over raw capacity. One card, no multi-GPU headaches, 96GB covers almost everything. But it's $9,700 for just the GPU.

**What I'd avoid:**
- The $17K 10-GPU monster: 60% of that VRAM would sit idle for your workloads. You're not running DeepSeek V3.1 every day.
- Pure AMD builds: The VRAM-per-dollar is tempting but ComfyUI compatibility risk isn't worth it for a production agency
- Single 4090/5090: 24-32GB is tight. You'd be swapping models constantly.

---

## WHAT'S ACTUALLY USED VS IDLE (Justification)

For Build 2 (88GB):

| Your Daily Reality | VRAM Used | Idle |
|-------------------|-----------|------|
| Morning: Writing, emails, coding (Qwen3-Coder-Next + TTS) | 19GB | 69GB |
| Client work: Image generation (Qwen-Image pipeline) | 40GB | 48GB |
| Client work: Video generation (Wan 2.2 i2v) | 48GB | 40GB |
| Client work: Talking head (HuMo + TTS) | 56GB | 32GB |
| Heavy reasoning (Qwen3-235B) | 70-80GB | 8-18GB |
| 4K upscaling (SEEDVR2) | 48GB | 40GB |

Average utilization across a workday: **~50-65%**. That's healthy. You want headroom for larger models as they come out, bigger context windows, and running two things at once.

100% utilization = you're bottlenecked. 30% = you overspent. 50-65% = right-sized with growth room.

---

*Pricing as of Feb 2026. GPU prices fluctuate. Used 3090 prices sourced from eBay. R9700 from Newegg. 5090 at MSRP (street prices higher due to demand).*
