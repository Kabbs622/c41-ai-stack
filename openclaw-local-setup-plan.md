# OpenClaw as Your Unified AI Setup — The Plan

## What OpenClaw Can Do Today

### LLM / Assistant / Code
**Fully supported.** OpenClaw natively integrates with Ollama. You install Ollama, pull models, set `OLLAMA_API_KEY`, and OpenClaw auto-discovers them. You can set local models as your primary or fallback.

Example: Set Qwen3-Coder-Next as your local model, keep Claude Max as cloud fallback:
```json5
{
  agents: {
    defaults: {
      model: {
        primary: "anthropic/claude-sonnet-4",  // cloud primary
        fallbacks: ["ollama/qwen3-coder-next"]  // local fallback
      }
    }
  }
}
```
Or flip it: local primary, cloud fallback for hard problems.

### TTS / Voice
**Supported via OpenAI-compatible API.** OpenClaw's TTS system supports OpenAI, ElevenLabs, and Edge TTS. Since Qwen3-TTS exposes an OpenAI-compatible FastAPI endpoint, you can point OpenClaw's TTS at your local Qwen3-TTS server:
```json5
{
  messages: {
    tts: {
      auto: "always",
      provider: "openai",
      openai: {
        apiKey: "not-needed",
        baseUrl: "http://localhost:8880/v1",
        model: "qwen3-tts",
        voice: "Vivian"
      }
    }
  }
}
```
97ms latency, voice cloning, 10 languages, all local. Or use IndexTTS2 for emotion control.

### Image Analysis (Vision)
**Supported.** OpenClaw has an `image` tool that uses a configured image model. You can point it at a local vision model via Ollama (like Qwen2.5-VL).

### Web Search, Browser, Exec, Files
**All working today.** These are core OpenClaw tools you already use.

## What Needs to Be Built (Skills)

### Image Generation
OpenClaw doesn't have a native image generation tool, but it has **Skills** — custom tool definitions that extend what I can do. Here's what we'd build:

**ComfyUI Skill**: A skill that lets me trigger ComfyUI workflows via its API.
- ComfyUI runs as a server (localhost:8188)
- The skill would let me send prompts and receive generated images
- I could trigger specific workflows (Qwen-Image pipeline, Flux 2 Dev multi-reference, etc.)
- Results come back as images I can send to Discord

This is a custom skill with a SKILL.md + a small script. I can build this.

### Video Generation
Same approach:
**Wan 2.2 Skill**: Trigger Wan 2.2 video generation via ComfyUI's API.
- ComfyUI handles Wan 2.2 natively (it's how everyone runs it)
- I send the workflow JSON (like Jeffu's workflow we found), get video back
- Could include presets: "hero clip", "product demo", "social short"

### Music / SFX Generation
**AudioGen/MusicGen Skill**: Trigger audio generation via a local API.
- MusicGen runs as a simple Python FastAPI server
- Skill lets me generate background music or SFX from text prompts

## The Full Local Stack

### Software Layer

| Component | What It Does | How OpenClaw Talks to It |
|-----------|-------------|------------------------|
| **Ollama** | Runs LLMs (Qwen3-Coder-Next, Qwen3-235B, etc.) | Native integration (built-in) |
| **ComfyUI** | Runs image gen (Qwen-Image, Flux, SDXL) + video gen (Wan 2.2, FramePack) | Custom skill via REST API |
| **Qwen3-TTS server** | Text-to-speech, voice cloning | OpenAI-compatible TTS config (built-in) |
| **MusicGen server** | Music/SFX generation | Custom skill via REST API |
| **OpenClaw** | The brain — routes requests, manages everything | You talk to me on Discord |

### How It Works Day-to-Day

You message me on Discord:
- "Write a proposal for Client X" → I use Ollama (Qwen3 or Claude fallback)
- "Build me a landing page for this" → I use Ollama for code (Qwen3-Coder-Next)
- "Generate a product shot for this campaign" → I trigger ComfyUI (Qwen-Image pipeline)
- "Make a 5-second video of this product" → I trigger ComfyUI (Wan 2.2 workflow)
- "Record a voiceover for this script" → I use local Qwen3-TTS
- "Generate background music for this video" → I trigger MusicGen

All through one chat. No switching apps.

### Model Swapping
Ollama handles model loading/unloading automatically. ComfyUI loads models on demand. The bottleneck is VRAM — you can only run one big model at a time on a single GPU. With enough VRAM (96GB+), you can keep an LLM loaded while running image/video gen.

## Hardware Requirements

Based on everything we've researched, here's what you need to run this full stack:

### Minimum Viable (Single GPU)
- **RTX 4090 24GB** or **RTX 5090 32GB**
- 64GB system RAM
- Fast NVMe SSD (models are big)
- Can run: Qwen3-Coder-Next (with CPU MoE offload), Wan 2.2 (GGUF Q5), Qwen-Image (with Nunchaku), TTS
- Limitation: One big task at a time, model swapping between LLM and gen

### Comfortable (Dual GPU or 48GB+)
- **2x RTX 3090 (48GB total)** or **RTX 6000 Ada 48GB** or **modded 4090 48GB**
- 128GB system RAM
- Can run: LLM + image gen simultaneously, video gen at good quality, TTS always on
- Still swap for frontier LLMs (Qwen3-235B needs more)

### No Compromises (Multi-GPU)
- **4x RTX 3090 (96GB)** or **2x RTX 5090 (64GB)** or similar
- 256GB+ system RAM
- Can run: Frontier LLMs (Qwen3-235B Q4), full-quality Wan 2.2, SEEDVR2 upscaling, TTS, all with room to breathe

### The Dream ($17K build from research)
- **8x RTX 3090 + 2x RTX 5090 (256GB+ VRAM)**
- Threadripper Pro, 512GB DDR4
- Runs literally everything including DeepSeek V3.1 at 25 tok/s

## What I Can Build Right Now

1. **Install Ollama** on your machine and configure OpenClaw to use it
2. **Build a ComfyUI skill** that lets me trigger image/video generation
3. **Set up Qwen3-TTS** as local TTS provider
4. **Build a MusicGen skill** for audio generation

The Mac mini M1 16GB is too small for most of this. This setup needs a proper GPU machine. But we can plan the software side now and deploy when the hardware arrives.

## Next Steps

1. Decide on hardware budget/timeline (pitch to your father?)
2. I build the ComfyUI and audio skills (software side, ready to deploy)
3. Set up Ollama config in OpenClaw
4. When hardware arrives: install everything, test the full pipeline
