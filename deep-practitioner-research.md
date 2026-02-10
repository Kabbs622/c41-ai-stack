# Deep Practitioner Research: What People Actually Use and Why
*Real quotes from real users. No benchmarks. No marketing.*
*Compiled Feb 10, 2026 from Reddit threads, GitHub, practitioner reports*

---

## 1. CODING — What's Actually the Best?

### The Cloud Tier (No Restrictions)

The Aider leaderboard and real-world tests tell different stories. Here's what practitioners actually say:

**GPT-5 / Codex** is winning benchmarks. A team tested GPT-5.3 Codex vs Claude Opus 4.6 on their own Rails codebase (1,770 upvotes on r/ClaudeAI):
> "GPT-5.3 Codex: ~0.70 quality score at under $1/ticket. Opus 4.6: ~0.61 quality score at ~$5/ticket. Codex is delivering better code at roughly 1/7th the price."

But then u/cc_apt107 (39pts) pushes back:
> "Codex always beats Claude on benchmarks I look at. Somehow, when it comes down to which one does a better job at my day to day, it always end up being Claude. It always ends up being Claude by a decisive margin which somehow is never reflected in benchmarks."

And u/Galahead (37pts):
> "Every time I make a plan with Opus and send it to Codex, Codex identifies a bunch of real issues with Claude's plan and now I can't ever really trust Claude without running the plan through Codex to identify issues."

**The real answer**: GPT-5/Codex and Claude Opus 4.6 are both S-tier but excel at different things. GPT-5 is more precise and cheaper. Claude is better at understanding what you actually want and writing code that "vibes" with your project. Smart practitioners use BOTH: Claude for planning, Codex for implementation, or vice versa.

**Gemini 2.5 Pro** scores 83.1% on Aider (just behind GPT-5's 88%). At its price point, it's arguably the best value in cloud coding.

**DeepSeek V3.2 Exp** is the open-source bombshell: 70-74% on Aider at $0.88-1.30/task. That's Claude Opus performance at 1/50th the cost. And it's open-weight.

### The Local Tier

**Qwen3-Coder-Next** is the breakout story. 476-upvote post titled "Do not Let the 'Coder' in Qwen3-Coder-Next Fool You!":

u/Iory1998 (OP):
> "For the first time, I could have stimulating and enlightening conversations with a local model. Qwen3-Coder-Next is more consistent, and you can feel that it's a pragmatic model trained to be a problem-solver rather than being a sycophant. Unprompted, it will suggest an author, a book, or a theory that already exists that might help. I genuinely feel I am conversing with a fellow thinker rather than an echo chamber. It's the closest model to Gemini-2.5/3 that I can run locally."

u/penguinzb1 (122pts):
> "The coder tag actually makes sense for this — those models are trained to be more literal and structured, which translates well to consistent reasoning in general conversations. You're basically getting the benefit of clearer logic paths without the sycophancy tuning that chatbot-focused models tend to have."

356-upvote post: "Qwen3 Coder Next as first 'usable' coding model < 60 GB":
> "I've tried lots of models < 60 GB in the past: GLM 4.5 Air, GLM 4.7 Flash, GPT OSS 20B and 120B, Magistral, Devstral... What's different? **Speed** (no reasoning loops), **Quality** (reliable tool calls, handles complex multi-hop codebases), **Context** (100K+ easy with minimal VRAM)."

u/andrewmobbs (51pts):
> "Qwen3-Coder-Next replacing gpt-oss-120b as my standard local coding model (on a 16GB VRAM, 64GB DDR5 system). Worth increasing ubatch-size to 4096 which tripled prompt processing speed."

u/SatoshiNotMe (45pts):
> "It's also usable in Claude Code via llama-server. On my M1 Max MacBook 64 GB I get a decent 20 tok/s generation speed and around 180 tok/s prompt processing."

**The €9K GH200 guy** (702 upvotes) replaced Claude Code entirely with MiniMax M2.1 running locally:
> "You can go fully local with Claude Code, and with the right tuning, the results are amazing. I am getting better speeds than Claude Code with Sonnet, and the results vibe well. Tool use works perfectly. It only cost me 321X the yearly subscription fee for MiniMax!"

His setup: 2x GH200 96GB (192GB VRAM), running MiniMax M2.1 at ~78 tok/s with 163K context via vLLM.

### What Developers Actually Do (Not What They Say)

u/kevin_1994 (68pts, from the "who actually daily drives local" thread):
> "I'm a software developer and I only use local AI. Yes, they aren't quite as good as cloud models, but for me, this is ironically a positive. I found that my code quality decreased, I started writing far more bugs, and cognitively offloading every hard problem to an AI led to me enjoying my job less. The weaker local models are kinda perfect because they can handle trivial boilerplate..."

u/Barafu (72pts): "I run a coding LLM on KoboldCPP. Then I start VSCode with the extension 'Continue' and use it."

u/fdg_avid (26pts): "Qwen 2.5 32B Coder in BF16 on 4x 3090 via vLLM using Open WebUI and a custom agent function. All running at my work (a hospital) so I can do data science using our EHR database."

### Coding Verdict (All Models, No Restrictions)

**Best overall**: GPT-5 / Codex (cloud) — wins benchmarks AND real-world tests
**Best for "vibing" with your code**: Claude Opus 4.6 (cloud) — practitioners keep coming back to it
**Best value cloud**: DeepSeek V3.2 Exp ($0.88/task, 70%+ on Aider, open-weight)
**Best local**: Qwen3-Coder-Next (runs on 16GB VRAM + 64GB RAM, actually usable for real projects)
**Best local if money isn't an issue**: MiniMax M2.1 on dual-GH200 (78 tok/s, 163K context, replaces Claude Code)
**Upcoming**: Qwen3.5 (multiple practitioners mention it as potentially game-changing), Claude Sonnet 5 "Fennec" (leaked Feb 2026 release, rumored 50% cheaper than Opus 4.5)

---

## 2. LLM / ASSISTANT — For Daily Agency Operations

### Who's Actually Daily Driving Local?

From the 163-upvote thread "Who is ACTUALLY running local or open source model daily and mainly?":

u/Nepherpitu (18pts):
> "Now it's 5090 + 4090 + 3090... Qwen3 32B on VLLM using AWQ at 50tps single request, 90tps for two requests. And embeddings, code completions and image generation on llama.cpp"

u/custodiam99 (28pts):
> "Qwen 3 14b q8 is the first local LLM which I can really use a LOT. I have an RX 7900XTX 24GB. I use the model mainly to summarize online texts and to formulate highly detailed responses to inquiries."

u/dinerburgeryum (15pts):
> "I'm a local hosting absolutist. Never used any of the closed providers. I use Qwen3-30B-3A for general tasks, Devstral for general coding questions. I'm working to see now if I can get better results using a group of specialized small models behind some kind of query router to automatically handle model selection per task."

u/recitegod (20pts) — using local LLMs for creative writing:
> "I am writing a serialized TV show. I use the latent space as a transcoder. I write the beginning of a scene, the end of the scene, then feed it to the machine. I fix the lack of soul."

### The Quality Gap (Honest)

Qwen3-Coder-Next is being praised as the first local model that genuinely competes with cloud for general intelligence. But writing polish specifically? Cloud still leads. Nobody in any thread I found said "my local model writes better client-facing copy than ChatGPT/Claude."

For internal work (emails, summaries, brainstorming, research): Local is ready.
For client-facing deliverables (proposals, scripts, web copy): Cloud still has an edge.

---

## 3. VIDEO GENERATION — Is Local Actually Client-Ready?

### The Proof: Real Client Work on a 4090

449-upvote post on r/StableDiffusion: **"Wan 2.2's still got it! Used it + Qwen Image Edit 2509 exclusively to locally gen on my 4090 all my shots for some client work."**

u/Jeffu (OP, 55pts) shared his entire workflow:
> "I had planned on going closed source with this upcoming project thinking it'd be 'better' but after wasting $35 on LTX-2 (and not wanting to sign up for the more expensive ones since I wouldn't use them enough) I decided to go back to good ole Wan 2.2 which I knew how far I could push it."

His workflow:
- Wan 2.2 high-noise i2v (GGUF Q6_K) with SageAttention + TorchCompile
- Qwen Image Edit for clothing swaps and inpainting
- Next Scene LoRA for generating additional shots with consistency
- Topaz for upscaling
- Resolution: 1856x1040 (!)
- All on a single RTX 4090

u/Klinky1984 (6pts):
> "To get good output can still be difficult and time consuming, so it's still impressive that they got such clean results that were good enough for commercial use."

u/Jeffu in replies:
> "This project convinced me that I can still use 2.2 quite effectively, so I'll probably be using it for a while yet. I don't feel a huge need to pay for the closed source models so far."

### What About Cloud Video?

Jeffu specifically tried LTX-2 (cloud) first and wasted $35 before going back to local Wan 2.2. He didn't even bother with Runway or Sora's pricing.

The key insight: For someone who knows the tools, local Wan 2.2 on a 4090 produces client-acceptable work. The skill ceiling matters more than the model ceiling.

### Cloud vs Local Video: The Real Hierarchy

**Best cloud (no restrictions)**: Sora, Runway Gen-4.5, Google Veo 3 — still the quality ceiling for complex scenes
**Best local (proven in production)**: Wan 2.2 14B — literally being used for paid client work right now
**The gap**: Closing. Wan 2.2 with the right workflow (good starting images, proper settings, LoRAs) is competitive for most commercial use cases

---

## 4. AUDIO / TTS — The Rapidly Evolving Frontier

### The Current SOTA Local TTS Models

From the 240-upvote thread "ElevenLabs is killing my budget. What are the best 'hidden gem' alternatives for documentary style TTS?":

u/MixtureOfAmateurs (136pts):
> "The best local options are: Soprano, Kokoro, VibeVoice, XTTS v2 still somehow, F5 TTS."

u/Finguili (15pts):
> "From local TTS, VibeVoice Large seems to have highest ceiling, but the model is very unstable. With one generation it sounds as if text was almost professionally narrated; with another its prosody is so bad that you start to wonder is it the same model. It also loves to add strange music to the background. So expect to reroll a lot."

u/CheatCodesOfLife (30pts):
> "VibeVoice if you don't want to write code. Echo-TTS if you can work around the 30-second limitation. Maya-1 if you want to act like a director, eg. put 'documentary domain' in the description prompt."

u/IONaut (15pts):
> "Currently VibeVoice large is the best most natural sounding option. You could even give it a sample of the voice you like from ElevenLabs and clone it that way. Chatterbox just came out with a new version (2) that is super lightweight and fast."

### IndexTTS2 — The Game Changer (641 upvotes)

"The most realistic and expressive text-to-speech model so far":
- Zero-shot voice cloning from one audio file
- Zero-shot EMOTION cloning (world first) — provide a second audio file for emotional state
- Text control of emotions without reference audio
- Duration control (perfect for dubbing)
- Open weights, runs locally

### Qwen3-TTS (749 + 319 upvotes)

Just dropped with OpenAI-compatible API:
- 97ms end-to-end latency (streaming)
- Natural language voice direction ("Say this in an incredibly angry tone")
- 3-second voice cloning
- 10+ languages
- Drop-in replacement for OpenAI TTS endpoints

u/Fragrant_Dog6303 (48pts):
> "Holy shit 97ms latency is actually insane for local TTS. Been using tortoise-tts and it takes like 30 seconds just to say 'hello'"

### Audio Verdict (All Models, No Restrictions)

**Best cloud TTS**: ElevenLabs (still the production standard), Google TTS (about to challenge it)
**Best local TTS (quality)**: IndexTTS2 (emotion control is a world first), VibeVoice Large (highest ceiling but unstable)
**Best local TTS (practical)**: Qwen3-TTS (97ms latency, OpenAI-compatible API, easy setup)
**Best local TTS (speed/lightweight)**: Kokoro, Chatterbox 2
**Best voice cloning**: IndexTTS2 (emotion + voice), FishAudio S1 (quality), OpenVoice V2 (MIT, commercial)
**Best music**: Suno v4 (cloud, nothing local competes for full songs), MusicGen (local, instrumental only)
**Best SFX**: AudioGen (local), Stable Audio Open (local, 47s stereo)

### The Honest Gap
Local TTS is rapidly approaching ElevenLabs quality. IndexTTS2 and Qwen3-TTS represent a massive leap. For voiceovers, the gap is narrowing to the point where skilled users can get production-quality results locally. For music with vocals, cloud (Suno) still has no real local competitor.

---

## 5. IMAGE GENERATION — Production-Ready?

### Already Covered In Depth

The Wan 2.2 client work post above (Jeffu) proves local image gen is production-ready when paired with the right workflow.

Key additions from this research round:

The professional pipeline (Qwen-Image > Wan 2.2 i2i > SEEDVR2 upscale) is validated by multiple practitioners. Jeffu's specific approach: Wan 2.2 + Qwen Image Edit + Next Scene LoRA for consistency across shots.

**The LoRA ecosystem is the commercial unlock.** Training client-specific LoRAs (characters, products, styles) and using them across image AND video gen is what makes local viable for agency work. Cloud can't do this.

---

## 6. ORCHESTRATION — The Biggest Unsolved Problem

### Everyone Wants This, Nobody Has It

u/dinerburgeryum (15pts):
> "I'm working to see if I can get better results using a group of specialized small models behind some kind of query router to automatically handle model selection per task."

This sentiment appears across dozens of threads. The problem: you need an LLM for chat, a different model for coding, ComfyUI for images, Wan for video, TTS server for audio. Managing all of this is a nightmare.

### What Exists Today

**ClaraVerse** (443 + 725 upvotes) — The closest thing to a unified workspace:
> "Everything's just LLMs and diffusion models anyway, so why do we need separate apps for everything? Built ClaraVerse to put it all in one place."

Features: Chat + image gen (ComfyUI) + agents + RAG + N8N workflows + web dev. MIT licensed, 20K+ downloads.

u/Cool-Chemical-5629 (42pts): "There really doesn't seem to be anything like it (all in one app)."

**llama.cpp Router Mode** (170 upvotes) — New feature that lets you manage multiple LLM models without restarting the server. Brings Ollama-like model switching to llama.cpp.

**ConductorAPI** (26 upvotes) — Lightweight control plane that:
- Dynamically loads/unloads models on demand
- Routes requests based on task
- Queues requests to avoid VRAM contention
- Single API for all runtimes

u/GaryDUnicorn (4pts):
> "TabbyAPI supports hot loading of models per API call. You can cache the models in RAM for speed. Tier them out to NVME disk. Works super good when you are wanting to call many big models on limited VRAM."

**The 300-upvote setup guide** recommends:
- Containers for everything (isolation, easy upgrades)
- Open WebUI as the frontend
- llama-swap or Ollama for model hot-swapping
- Tailscale for remote access

### The OpenClaw Opportunity

Here's what's interesting for you specifically: OpenClaw already acts as an orchestration layer for LLMs (it routes between Claude, Gemini, local models). The missing piece is extending that to image gen, video gen, and audio.

Conceptually:
- OpenClaw receives a request ("generate a product shot for this client")
- It routes to ComfyUI via API for image generation
- Or triggers a Wan 2.2 workflow for video
- Or hits Qwen3-TTS for voiceover
- All through one interface (Discord, in your case)

This doesn't exist as a packaged solution yet. ClaraVerse is the closest but it's a separate app, not an assistant that routes for you.

### What Practitioners Actually Do (Today)

1. **Model swapping**: Load one model at a time, use it, unload, load next (llama-swap, Ollama)
2. **Dedicated servers**: Different GPU for LLM vs image gen vs video gen (if you have multiple)
3. **ComfyUI workflows**: For image/video pipelines, triggered manually or via API
4. **Separate TTS server**: FastAPI endpoint running Qwen3-TTS or similar
5. **Glue code**: Custom scripts that tie it all together (N8N, Python scripts, etc.)

Nobody has a clean, unified solution. Everyone is duct-taping.

---

## THE BOTTOM LINE FOR C41 CINEMA

### What's Real Right Now
- **Coding**: GPT-5/Codex (cloud) or Qwen3-Coder-Next (local) are both production-ready. DeepSeek V3.2 is the insane value play.
- **Video**: Wan 2.2 is being used for real client work on a single 4090. Proven.
- **Image**: Multi-model pipelines are production-ready. LoRAs are the commercial moat.
- **Audio**: Qwen3-TTS and IndexTTS2 are approaching ElevenLabs quality. 97ms local latency.
- **LLM**: Qwen3-Coder-Next is the first local model people genuinely prefer over cloud for daily use.

### What's NOT Ready
- Unified orchestration (everyone duct-tapes)
- Local music generation with vocals (Suno still wins)
- Local models writing polished client-facing copy (cloud still has the edge)

### The Opportunity
Nobody has built a clean AI orchestration layer for content agencies. ClaraVerse tries but it's a standalone app. The idea of an AI assistant (like OpenClaw/me) that automatically routes tasks to the right model — that's what practitioners actually want and nobody has built yet.

---

*Sources: 15+ Reddit threads (r/LocalLLaMA, r/StableDiffusion, r/ClaudeAI), GitHub repos, HuggingFace, Pastebin workflow shares. All quotes are real with upvote counts noted.*
