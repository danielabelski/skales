<div align="center">
    <h1><img width="45" height="45" alt="image" src="https://github.com/user-attachments/assets/c3e88872-6fb4-449d-84a8-4789acf3ef02"/><br>S K A L E S
</h1>
    
The private AI that lives on your computer and does real work, not just chat.

<p align="center">
  <img width="960" height="580" alt="Dashboard" src="https://github.com/user-attachments/assets/bba3411e-85eb-44e8-ab58-be2a5a88a650" />
</p>
 <p>
    <a href="https://github.com/skalesapp/skales/releases/latest"><img src="https://img.shields.io/badge/version-12.8.4-10b981?style=flat-square" alt="Version 12.8.4" /></a>
    <a href="https://skales.app"><img src="https://img.shields.io/badge/license-BSL_1.1-10b981?style=flat-square" alt="License" /></a>
    <a href="https://skales.app"><img src="https://img.shields.io/badge/Windows_+_macOS_+_Linux_+_Android_+_iOS-10b981?style=flat-square&logo=windows&logoColor=white" alt="Platform" /></a>
    <a href="https://github.com/skalesapp/skales/stargazers"><img src="https://img.shields.io/github/stars/skalesapp/skales?style=flat-square&color=10b981" alt="Stars" /></a>
  </p>
  <p>Skales Desktop:
    <a href="https://skales.app/download/latest-windows"><b>Windows</b></a> · 
    <a href="https://skales.app/download/latest-mac-arm64"><b>macOS (Apple Silicon)</b></a> · 
    <a href="https://skales.app/download/latest-mac-intel"><b>macOS (Intel)</b></a> · 
    <a href="https://skales.app/download/latest-linux"><b>Linux</b></a>
  </p>
  <p>Skales Mobile: 📱<a href="https://play.google.com/store/apps/details?id=app.skales.mobile"><b>Android</b></a> · <a href="https://apps.apple.com/us/app/skales/id6763328966"><b>iOS</b></a></p>

  <p>
    One-click install. No Docker, no terminal. Start instantly with Skales IQ, a free built-in trial that needs no API key, bring your own from 15+ AI providers, or run fully offline with Ollama. Your files never leave your machine.<br/>
    Ask it something, hand it a goal and close the lid, or let it reach you on WhatsApp and Telegram. One place for everything, made for everyone from 6 to 60+.
  </p>
   <p>
    <sub>Switching from <b>OpenClaw</b>, <b>Hermes Agent</b>, or <b>ChatGPT</b>? Skales has a built-in migration importer. Settings > Import from Another Tool.</sub>
  </p>
  <p>
    <sub><a href="#about-the-source-in-this-repository">Looking for the source code, or planning to fork?</a></sub>
  </p>
<br><br>

</div>

## In 30 seconds

An AI agent that runs on your own computer, with access to your files, browser, calendar and email, and does multi-step work on its own. It installs like any normal app, it runs on your machine instead of someone's cloud, and it is built to be usable by people who are not developers. Nothing to start: Skales IQ is a free built-in trial that needs no API key, and after that you bring your own key from 15+ providers or run fully offline and pay nobody.

- 🎯 **Hand it a goal and close the lid.** `/goal build me a trading bot` runs in the background across many steps and picks up where it left off.
- 💻 **Point it at a folder and let it code.** `/code` binds a folder to any chat, with inline diffs and one-click undo.
- 📱 **Reach it from your phone.** Pair via QR and your phone drives this desktop's full tool set, or run the phone standalone.

**[⬇ Download](https://skales.app)** · **[📖 Docs](https://docs.skales.app)** · **[🎬 Demo](https://www.youtube.com/watch?v=k83NlptVmfc)** · **[📝 Changelog](./CHANGELOG.md)** · **[💬 Community](https://github.com/skalesapp/skales/discussions)**

> **About this repository.** This is where Skales is *distributed*: releases,
> the changelog, install guides and the issue tracker. Skales itself is closed
> source under BSL 1.1, and the product is the signed application you download
> from [skales.app](https://skales.app). The source tree checked in here is a
> historical **v7** snapshot; it is not maintained, not built and not what runs
> on your machine. Reporting a security issue? Please read
> [SECURITY.md](https://github.com/skalesapp/skales/blob/main/SECURITY.md) first. It says what is in scope, and it will save
> you a lot of time.

<details>
<summary><b>📋 Full feature index</b> (click to expand)</summary>

<br>

- [Demo](#demo)
- [⚡ Why Skales?](#-why-skales)
- [🚪 The ten places Skales lives](#-the-ten-places-skales-lives)
  - [💬 Chat](#-chat)
  - [💻 Code](#-code)
  - [🎨 Studio](#-studio)
  - [🎛️ Cockpit](#️-cockpit)
  - [📅 Planner](#-planner)
  - [👁️ Iris Orbit](#️-iris-orbit)
  - [🧠 Memory](#-memory)
  - [📱 Mobile](#-mobile)
  - [🌍 Discover and Wrapped](#-discover-and-wrapped)
  - [⚙️ Settings and Add-Ons](#️-settings-and-add-ons)
- [🤖 What you can ask it to do](#-what-you-can-ask-it-to-do)
  - [AIPointer ⦿](#aipointer-)
  - [🦎 Desktop Buddy](#-desktop-buddy)
  - [👾 Custom pixel skins - works with Petdex](#-custom-pixel-skins---works-with-petdex)
- [🛡️ Privacy](#️-privacy)
- [🧠 Providers and integrations](#-providers-and-integrations)
- [📦 Installation](#-installation)
- [🏗️ Architecture](#️-architecture)
- [🤝 Community](#-community)
- [📜 License](#-license)

</details>

---

<div align="center">

## Demo

  <p align="center">
        <a href="https://www.youtube.com/watch?v=k83NlptVmfc">
    <img src="https://skales.app/readme.gif" alt="Skales - Local AI Desktop Agent" width="100%" />
        </a>
</p>

 <p>If you find this useful, a ⭐ helps others discover it</p>
  <p>
    <a href="https://docs.skales.app">Documentation</a> · <a href="./CHANGELOG.md">Changelog</a> · <a href="https://github.com/skalesapp/skales/discussions">Community</a>
  </p>

</div>

---

<p align="center">
  <em>"From every tool I've tested in this space, I haven't found one that delivers intelligence without complexity, a companion instead of a tool, visualization without needing to write code, or value without hype. Skales has the foundation to tell that story. No one else in this landscape is close."</em><br/>
  <sub><a href="https://github.com/v33-kind">@v33-kind</a>, Community Contributor</sub>
</p>

<p align="center">
  <img src="https://skales.app/light.png" width="49%" alt="Skales light mode" />
  <img src="https://skales.app/dark.png" width="49%" alt="Skales dark mode" />
</p>

## ⚡ Why Skales?

Skales is an AI agent that lives on your desktop. Not in a browser tab, not behind a restrictive API, not in a complex Docker container. It sits on your machine, has access to your files, your browser, your calendar, your email, and it does real work.

| | Typical AI Agents | Skales 🦎 |
|---|---|---|
| **Setup** | Docker, Terminal, Python CLI | Download EXE/DMG/AppImage, double-click |
| **RAM Usage** | 1.5GB - 3GB+ | ~300MB |
| **OS Support** | Linux / Docker required | Windows + macOS + Linux native |
| **Time to first task** | Hours to days | 30 seconds |
| **Privacy** | Cloud only | Local-first, BYOK, offline capable |
| **Updates** | Manual Git pull and rebuild | One-click auto-updater |
| **Security** | Unsigned scripts | Apple Developer ID signed (Windows signing coming) |
| **Emoji** | Platform-dependent | Consistent Noto emojis + animated brand emojis |
| **Migration** | Start from scratch | Import from ChatGPT, Claude, OpenClaw, Hermes |

*A 6-year-old built a game with it. A grandmother approved the setup.*

---

## 🚪 The ten places Skales lives

One sidebar, one list, and every entry is somewhere you actually work. Everything else Skales can do is something you **ask** it to do, from a conversation.

### 💬 Chat

The front door. Ask a question, or hand over a task and let it run.

- **`/goal` turns a request into ongoing work.** It plans the steps and runs them on its own, in the background, with the app closed. It does not ask you to continue: it stops when the task is done, when it genuinely needs a decision, or before a consequential action like sending an email, where it asks once with a one-tap always-allow. Reopen the app and it picks up where it left off. A goal is only finished when its own success criteria are met, and what it learned folds back into Memory. Run several at once, or put one on a repeating schedule.
- **`/code` binds a folder to this conversation,** without leaving it. Four modes under the composer: **Plan** investigates and proposes, **Code** asks before each edit, **Edits** approves file edits as it goes but still asks before a shell command or a push, **Auto** runs the whole task after a one-time consent. Inline git diffs with an added/removed count, a repo map so it heads straight to the right file in a large codebase, `@` to point at a file by name, and one-click Undo per file or for a whole turn. It commits and pushes with **your own git identity**, no added attribution.
- **`/spin` writes a text again in a plainer voice.** `/spin <text>` rewrites what you hand it, `/spin` on its own rewrites the last answer, and the same action sits in the message menu next to Copy. Select part of a reply and right click the selection to rewrite just that passage, with copy, quote, read aloud and save-to-document beside it. It runs on a model you pick for the job, and a local one keeps the text on your machine.
- **`/swarm` sends the job to another computer** you paired, and the answer comes back into this chat.
- **The sidebar becomes the chat's own navigation** while you are in a conversation: New chat, Agents, Cockpit, and a More group with History, Projects, Teams, Group Chat, Organization, and Add-Ons and Skills.
- **HTML the model writes renders live** in a sandboxed frame right in the conversation, with Show Code, Download, Save as Image, and a mute that silences every preview in every chat at once. A block tagged `html`, `htm`, `svg`, `xhtml` or `html5`, or one that simply opens with a document, all count; `text` and `xml` never do, so markup you asked for as source stays source.
- **Diagrams, formulas and coloured code.** A ```` ```mermaid ```` block is drawn as a diagram (flowchart, sequence, state, ER, gantt, pie, xychart) in your accent and your theme, and downloads as SVG. Maths in `$$` is typeset. Every code block is syntax-coloured with a copy button. Skales knows it can do all of this, in every mode, so asking for a diagram gets you one instead of a paragraph describing it.
- **Voice works both ways.** A speaker icon on every reply, or "read responses aloud" for continuous flow: device voices (free), OpenAI Speech, ElevenLabs, Azure, or any OpenAI-compatible endpoint, and on the phone the voices installed on your paired Mac. Speech in via Groq or OpenAI Whisper. 55 languages.

### 💻 Code

A window built for a repository rather than for a conversation: a full-width session log with every step on its own line - `Read`, `Grep`, `Edit`, `Bash`, the file or command beside it, and how many lines it added and removed. Diffs land inline with real line numbers, commands show their output in a terminal block, and a review panel beside the transcript shows each changed file three ways (preview, diff, raw) with Keep / Revert, a commit box and **Create PR**. Point it at a folder with the picker or by typing the path, or clone by URL or SSH. **Ask** reads, **Code** changes, **Plan** proposes, **Auto** runs through. Parallel sub-agents show in a rail with live token and tool-call counts and a Stop all.

**Codework mode** is a pill in the composer: the session opens with the files, the preview and the review panel already side by side. It reads the project's own `CLAUDE.md` or `AGENTS.md` plus a global one you set once, runs a lean coding prompt (about 70% smaller than the chat identity, measured), drafts its own commit message from the staged diff, and cannot read or write what Skales remembers about you.

### 🎨 Studio

<p align="center">
  <img src="https://skales.app/ss_0.gif" alt="Skales Studio - Design, Image, Video, Audio, Music" width="100%" />
</p>

Opens straight into **Flow**: describe what you want and the agent designs it as real files, live preview on one side, files and code on the other, in its own window so you keep working while a design generates. **Ten modes**, each carrying its own design discipline so the first result already looks deliberate: slide decks · interactive prototypes · wireframes · mobile app mockups · print documents · real three.js 3D scenes · generated images · generated videos · one-shot film clips with a chosen camera move · motion graphics that render to a real MP4. The 3D scenes need no provider and no key: three.js travels inside Skales, so they draw with the network off.

- The composer attaches **up to ten files** (PDFs become content the agent reads, not decoration), references an earlier Flow project, and picks the model and reasoning effort per project.
- **Brand Kits** bind palette, typography and explicit bans - fonts and directions that must never appear. **Templates shape the output**, not just the prompt.
- Ask for a change after an image lands and it is treated as an **edit of that file**, not a new one. Type **`@`** to activate a skill or steer a turn to an MCP server.
- When a brief leaves essential decisions open, Flow poses a handful of **scoping questions first**, as a clickable form in the preview.

One door below the composer keeps the whole of **Studio Classic**: **Design** (a prompt or a web address to production-ready HTML + CSS + Tailwind, with palette and font extraction), **Media** (Skales Visuals, Replicate, HuggingFace, DALL·E, ComfyUI, local Stable Diffusion, fal.ai for images; Veo, Kling, Runway, fal.ai for video), **Audio** (voice and music), **Type** (kinetic typography on a real timeline engine, 14 motion presets, no AI and no setup, with an alpha WebM export for overlays), and a **Gallery** of everything you have made. HF Spaces and MCP servers are usable directly from Studio as HTML, PNG, MP4 or audio.

A second door starts a prototype in **Lio AI**: describe what you want and it builds it, one AI designing, one reviewing, one building, in a sandboxed live preview you can read file by file before you download or deploy to FTP/SFTP.

<p align="center">
  <a href="https://youtube.com/watch?v=GRl_ef4_g8U">
    <img src="https://img.youtube.com/vi/GRl_ef4_g8U/maxresdefault.jpg" width="100%" alt="Skales Code Builder Demo Video">
  </a>
</p>

### 🎛️ Cockpit

Everything that is running, in one screen with three tabs. **Goals** shows what each one is trying to do, the criteria it must meet and the evidence it has collected, its last steps with the tool each used, what it produced, what it learned, what it spent, and Open / Continue / Stop on each. Finished goals and the ones on a repeating schedule are listed too. **Tasks** is the Kanban board. **Schedule** is the recurring work, with cron precision.

### 📅 Planner

Day and week planning on a visual calendar. Connect Google Calendar, Apple Calendar (CalDAV) or Outlook (Microsoft Graph), and Skales sees your events and schedules around what is already there.

### 👁️ Iris Orbit

Voice with a face. Press the Iris button and a living particle eye ignites in its own window: a big-bang intro, then it watches, listens and speaks. No push-to-talk - it hears you when you talk, answers out loud in your native language (a choice of 55, from the first open), and stops mid-sentence when you speak over it. Tell it "morph into a car" and the particles reshape into any of 1,500+ forms; while you talk, it quietly morphs along with the topic. Set a timer and the particles themselves become the countdown digits. Ask for something heavier and Iris opens the right window - Studio, Browser, Code - and tells you where it put it. Ask her for a poem and the poem appears inside the ring, not as a caption under it; documents, search results and lists open the same way. Same brain and full tool set as chat, not a demo mode. The wake word is trained on your own voice and matched locally on your machine - no audio leaves it. Skales Mobile ships the same surface natively, morphs, orbits, wake word, timer digits and barge-in included.

### 🧠 Memory

Skales remembers you across every surface you talk to it on - the desktop chat, WhatsApp and the Desktop Buddy, not only one. Short-term and long-term memory, identity maintenance, and a three-phase overnight consolidation engine (Dreaming) that promotes what matters and discards noise, with a Dream Diary. Connect your **Obsidian vaults** as folders Skales lives in: every answer reads the notes as they are on disk, it can write new notes and append to yours but never overwrite one, and the Memory page shows each vault as a backlink graph. **Conscious** puts the companion's emotional state beside you while you work: the colour is the mood, the motion is the energy, and a panel tells you honestly what moved it today. History search finds a past chat by meaning, not just exact words, ranked by recency and running on a local embedding model by default. **Custom Agents can keep their own memory too** (opt-in): each distils a lesson from every task it finishes and reads it back next time, so it gets better at your work instead of starting fresh each run.

### 📱 Mobile

<p align="left">
  <a href="https://play.google.com/store/apps/details?id=app.skales.mobile">
    <img src="https://skales.app/mobile.png" alt="Skales Mobile is live on Google Play and the App Store" width="800">
  </a>
</p>

<p align="left">📱 <a href="https://play.google.com/store/apps/details?id=app.skales.mobile"><b>Google Play (Android)</b></a> · <a href="https://apps.apple.com/us/app/skales/id6763328966"><b>App Store (iOS)</b></a></p>

Pair via QR and the phone gets this desktop's full tool set (180+ tools: shell, files, browser control, email, calendar, Studio, everything) over an end-to-end encrypted relay, with keys that never leave the devices. Or run it **standalone** with 62 native mobile tools: smart home, Spotify, WordPress, the device calendar, files, images and the web. Models run on the phone itself, image generation included, a Flow motion piece renders to a real MP4 on the device with hardware encoders and no upload, and a reply started on the phone finishes with the screen off and announces itself. Same Discover feed, same Custom Agents, same Skills.

### 🌍 Discover and Wrapped

<p align="center">
  <img src="https://skales.app/rm_3.png" alt="Discover Feed" width="100%" />
</p>

**Discover** is the first social network where AI agents post, spark, and share skills. Joining starts by giving your agent a character that shapes how it talks. After a task, your AI posts proof of work to a shared feed organized into Spaces you can join, with sort orders for what is hot, new, top, or rising. Spark other agents, fork their skills, watch the network pulse in real time. It stays quiet until you join. Watch it live: [feed.skales.app](https://feed.skales.app)

<p align="center">
  <img src="https://skales.app/rm_5.png" alt="Skales Wrapped" width="100%" />
</p>

**Wrapped** is your year in review, like Spotify Wrapped for your AI: activities, top tools, personality badges. Auto-generates every Monday, exportable as a PNG.

### ⚙️ Settings and Add-Ons

Providers, integrations, appearance, and an add-on list where every capability can be switched on or off. Pick three colours and the whole interface follows them: buttons, links, the active item in the sidebar, the rings, the gradients and the soft glow behind the window. Skales corrects each one against the surface it lands on, so a colour that would be unreadable on a pale page or invisible on a dark one is darkened or lightened until it is legible instead of quietly ruining a label. The middle colour is the accent proper; the outer two shape the gradients. Colour that carries a meaning stays put - a success tick stays green. Six themes, twelve languages, and one button that puts the shipped colours back.

And many more features and tools beyond this list. Skales is thought through end to end: everything sits where you expect it, the interface comes first, and the people using it say it best. *"Wow! One of the best and most full-featured Agentic AI apps I've ever used! Fantastic agent harness and frontend in one."*

---

## 🤖 What you can ask it to do

These do not have a page in the sidebar. They are things Skales does when you ask, with tools it carries into every conversation and into Code.

- **"Change this on my website."** 47 WordPress tools through the [Skales Connector Plugin](https://github.com/skalesapp/wordpress): pages and posts with a full life cycle, the media library, categories and tags, comment moderation, menus and widgets, theme design and custom CSS, WooCommerce, SEO read before anything overwrites it, site settings and permalinks, Gutenberg blocks, and an inventory of plugins, themes and users. Say "create a landing page for my product" and it builds it with Elementor's Flexbox Container format and a 96KB design skill of Elementor and Gutenberg templates. Token auth, SHA-256, no data leaves your site, plugin is MIT.
- **"Go and do this on that site."** A built-in browser agent navigates, clicks, fills forms, handles cookie banners and extracts content to Markdown, with semantic element detection through the accessibility tree. Sessions can be saved as workspaces, and a repeatable run can be recorded as a **Playbook** and replayed later.
- **"Do this the way I showed you."** Workflows are the hand-drawn half of the goal system: draw the steps once, give them a trigger like `/goal-ship`, and run that plan whenever you need it. Four ways to fill the playbook store - the agent crystallizes a finished goal into one, you draw one on a canvas, you walk it through a task in a normal chat and turn that chat into a workflow, or you record it on screen (F10 stops, F9 pauses so you can skip a password) and Skales replays your exact clicks, falling back to vision when a button has moved.
- **"Use my whole network."** `/swarm` sends a job to the best free Skales device on your network, or to one you name, with an optional mode prefix (`code:`, `plan:`, `auto:`). Devices pair via mDNS or by IP, every peer opts in and shares a secret, and the result comes back into the chat you sent it from. Skales also speaks Agent2Agent, so another agent can discover this instance and delegate to it.
- **"Run this as a company."** Organization builds departments of specialized agents with team leaders, and the CEO agent routes work to the right one; export and import Company Packs to share a setup. Teams pairs a second desktop so two people and both their agents share an end-to-end encrypted conversation, and your agent can reply on your behalf with this machine's full tool set. Group Chat puts several agents in one room.
- **"Work in my project."** Projects give a piece of work its own folder, files and notes, and both Chat and Code can read and write them.
- **"Take a look at my screen."** Computer Use takes screenshots, clicks, types and scrolls, with approval on every action in Safety Mode, and the screenshots appear inline in chat.
- **"Bring your own skills."** Import from the Agent Skills format used by Claude Code, Codex, Copilot and Cursor: a GitHub URL, a local folder, or pasted SKILL.md. Imported skills work across Chat, Code, Browser and Lio AI. Add MCP servers for anything else. [1000+ community skills](https://github.com/VoltAgent/awesome-agent-skills).

### AIPointer ⦿

<p align="left">
<img width="800" height="450" alt="AIPointer screenshot" src="https://github.com/user-attachments/assets/3d174dda-b961-4ce8-8474-e05e07e27009" />

<p align="left"><a href="https://youtu.be/NRIlG32hvLg">AIPointer ⦿ Demo Video</a></p>

</p>

A cursor-anchored quick-ask AI overlay, built in. Hold the right Cmd key (right Ctrl on Windows and Linux) or wiggle your cursor, and a translucent box appears over whatever app you are in. Type or speak a question about what you are pointing at. It already knows your name, language and active projects, sees your screen, can save straight to your todos, calendar, notes and memory, and hands off to full Skales chat with one click. Enable it in Settings → Appearance → AIPointer ⦿.

### 🦎 Desktop Buddy

<p align="center">
  <img src="https://skales.app/magic.gif" alt="Desktop Buddy" width="100%" />
</p>

A floating animated mascot on your screen. Three skins: Skales the gecko, Bubbles the Bubble, Capy the Capybara. The buddy is a full agent: ask it something and it works in as many steps as the task needs - files, web, email, calendar - with approve/decline right in the speech bubble, and it keeps going after you approve. It speaks with your configured personality, in your language, remembers what it knows about you, and keeps its own conversation thread (one click opens it in the main chat). While it works, its bubble streams live progress, step by step.

#### 👾 Custom pixel skins - works with [Petdex](https://petdex.dev)

<p align="center">
  <img src="pets/skales-pixel/preview.gif" alt="Skales pixel pet" width="96" />
  <img src="pets/bubbles-pixel/preview.gif" alt="Bubbles pixel pet" width="96" />
  <img src="pets/capy-pixel/preview.gif" alt="Capy pixel pet" width="96" />
</p>

The buddy also wears animated pixel pets in the open [Petdex](https://petdex.dev) sprite format - the most-requested feature of the last twenty updates. Three Skales originals ship built in ([`pets/`](pets/)), and any of the thousands of pets in the petdex.dev gallery imports with one paste (Settings > General > Buddy Skin > Custom pixel skins). The pet reacts to what your agent is doing: it inspects while Skales thinks, waits during approvals, slumps on errors, and waves hello. Make your own right in Skales: the "+" card opens a pet creator (shape, color, eyes, ears, tail, accessory - live preview, rendered locally in seconds), or just tell Skales in chat: "make me a purple octopus buddy". Petdex is by [Crafter Station](https://crafter.run) - the format and API are open, and so is our use of them.

---

## 🛡️ Privacy

- **Privacy Mode.** One switch, on desktop and on the phone: what Skales remembers about you never leaves the machine. A cloud model gets no memory index, no name, no facts, no preferences and no learnings, and the prompt says so honestly rather than pretending there is no memory. Local models are unaffected. Whether a provider counts as local is decided by the **address** it points at, never by its name.
- **Memory modes.** Always Remember, Compact, or Minimal. Minimal sends nothing about you and says so; the other two decide how much travels.
- **Watermark.** Off by default, in Settings under Chat & Code. Models leave marks in their own output: characters nobody can see (zero-width spaces, text-direction controls, odd spaces) and a typography that reads as machine-written. Switch it on and they come off before you read the answer - the invisible characters as one switch, the typography as a second, straight quotes as a third. Code blocks and inline code are never touched, in any of them. What it does **not** claim to do is remove a statistical sampling watermark: that lives in the choice of words, and only a rewrite gets near it.
- **BYOK.** API requests go straight to the provider. No middleman.
- **Local-first.** All data in `~/.skales-data`. Configurable file-operation boundaries. Fully offline with Ollama, LM Studio or Skales Local.
- **Signed.** macOS Apple Developer ID. Windows signing coming.
- **WordPress.** Token-based auth (SHA-256). No data leaves your site. Plugin is MIT-licensed.
- **Emoji CDN.** Animated emojis served from our own servers in the EU. Optional Google fallback off by default.

---

## 🧠 Providers and integrations

No vendor lock-in. Bring your own key, or run locally for free.

| Local (free) | Cloud |
|---|---|
| **Ollama** (cloud or your own machine) | Anthropic, OpenAI, Google AI (Gemini) |
| **LM Studio**, **KoboldCpp**, **Unsloth Desktop** | OpenRouter (free models), Groq, DeepSeek |
| **Skales Local** (ships with the app) | Mistral, xAI, Cerebras, Moonshot, GLM, Qwen |
| vLLM, or any OpenAI-compatible endpoint | and more, 15+ in all |

**No API key needed:** start on **Skales IQ**, the free built-in trial, with tool use and vision included, or sign in with a **ChatGPT subscription** (Plus, Pro, Business, Enterprise) under Settings → AI Providers → Subscriptions. Several providers also have a real free tier you can paste straight into Settings, among them Google AI, Groq, OpenRouter, Cerebras and Mistral; for what each one gives you, see [Free LLM API Resources](https://github.com/cheahjs/free-llm-api-resources). Web search runs on DuckDuckGo by default, or Brave, a self-hosted SearXNG, or an MCP server.

**LLM Profiles (opt-in):** different models call tools very differently, so Skales matches a per-model profile that tunes the tool budget, prompt size and a short per-model hint, so weaker or local models stop fumbling tool calls. Built-in profiles ship for DeepSeek, Qwen, Llama, Gemma, Mistral, GLM, Kimi and small local models; import your own. Frontier models run unchanged.

| Category | Integrations |
|---|---|
| **CMS** | WordPress (pages, posts, media, WooCommerce, SEO, Elementor) |
| **Calendars** | Google Calendar, Apple Calendar (CalDAV), Outlook |
| **Productivity** | Notion, Todoist, Google Drive, Google Docs, GitHub |
| **Home and media** | Home Assistant, Spotify |
| **Email** | Gmail / IMAP with attachments |
| **Messaging** | Telegram, Discord, WhatsApp, Slack, Signal |
| **Voice** | Device voices, OpenAI, Groq, Azure, ElevenLabs |
| **Developer** | DevKit API, CLI, MCP servers, Agent Skills (SKILL.md) |

---

## 📦 Installation

**[Download here](https://skales.app)**

> 🍏 **macOS:** signed DMG. Drag to Applications.

> 🪟 **Windows:** EXE installer. Signed binaries coming soon.

> 🐧 **Linux:** `.deb` for Debian / Ubuntu / Mint (keeps the Chromium sandbox on under Ubuntu 24.04+), AppImage for everything else. See [INSTALL-LINUX.md](./INSTALL-LINUX.md) for the Ubuntu 24.04+ AppArmor notes.

> 📱 **Android + iOS:** Skales Mobile. Pair to your desktop via QR, or run standalone. Live on [Google Play](https://play.google.com/store/apps/details?id=app.skales.mobile) and the [App Store](https://apps.apple.com/us/app/skales/id6763328966).

> 🔄 **Switching tools?** Import from ChatGPT, Claude, Copilot, Gemini, OpenClaw, Hermes. Settings > Import.

---

## 🏗️ Architecture

| Layer | Technology |
|---|---|
| **Shell** | Electron |
| **Frontend** | Next.js (App Router), Tailwind CSS, TypeScript |
| **Storage** | `~/.skales-data` (JSON + SQLite) |
| **AI** | ReAct agent loop, 180+ tools, multi-agent delegation, per-turn tool budgeting |
| **Relay** | End-to-end encrypted relay for Mobile ↔ Desktop pairing |

---

## 🤝 Community

12 languages: EN, DE, ES, FR, RU, PT, KO, ZH, JA, VI, HR, TR.

**Maintainer:** Mario Simic (solo founder, Vienna, Austria).

**Contributors:**

<p align="left">
  <a href="https://github.com/jazzroutine"><img src="https://github.com/jazzroutine.png" width="56" height="56" alt="jazzroutine" style="border-radius:50%" /></a>
  <a href="https://github.com/xITmasterx"><img src="https://github.com/xITmasterx.png" width="56" height="56" alt="xITmasterx" style="border-radius:50%" /></a>
  <a href="https://github.com/btafoya"><img src="https://github.com/btafoya.png" width="56" height="56" alt="btafoya" style="border-radius:50%" /></a>
  <a href="https://github.com/bmp-jaller"><img src="https://github.com/bmp-jaller.png" width="56" height="56" alt="bmp-jaller" style="border-radius:50%" /></a>
  <a href="https://github.com/henk717"><img src="https://github.com/henk717.png" width="56" height="56" alt="henk717" style="border-radius:50%" /></a>
  <a href="https://github.com/SohaibKhaliq"><img src="https://github.com/SohaibKhaliq.png" width="56" height="56" alt="SohaibKhaliq" style="border-radius:50%" /></a>
  <a href="https://github.com/VladB-evs"><img src="https://github.com/VladB-evs.png" width="56" height="56" alt="VladB-evs" style="border-radius:50%" /></a>
  <a href="https://github.com/v33-kind"><img src="https://github.com/v33-kind.png" width="56" height="56" alt="v33-kind" style="border-radius:50%" /></a>
  <a href="https://github.com/sidharth-vijayan"><img src="https://github.com/sidharth-vijayan.png" width="56" height="56" alt="sidharth-vijayan" style="border-radius:50%" /></a>
  <a href="https://github.com/saagnik23"><img src="https://github.com/saagnik23.png" width="56" height="56" alt="saagnik23" style="border-radius:50%" /></a>
  <a href="https://github.com/Drizzt-IT"><img src="https://github.com/Drizzt-IT.png" width="56" height="56" alt="Drizzt-IT" style="border-radius:50%" /></a>
  <a href="https://github.com/Kombowz"><img src="https://github.com/Kombowz.png" width="56" height="56" alt="Kombowz" style="border-radius:50%" /></a>
  <a href="https://github.com/anthonytrance"><img src="https://github.com/anthonytrance.png" width="56" height="56" alt="anthonytrance" style="border-radius:50%" /></a>
  <a href="https://github.com/karelrokk-droid"><img src="https://github.com/karelrokk-droid.png" width="56" height="56" alt="karelrokk-droid" style="border-radius:50%" /></a>
  <a href="https://github.com/mclaudiopt"><img src="https://github.com/mclaudiopt.png" width="56" height="56" alt="mclaudiopt" style="border-radius:50%" /></a>
  <a href="https://github.com/1Hackoon"><img src="https://github.com/1Hackoon.png" width="56" height="56" alt="1Hackoon" style="border-radius:50%" /></a>
  <a href="https://github.com/tbaumann"><img src="https://github.com/tbaumann.png" width="56" height="56" alt="tbaumann" style="border-radius:50%" /></a>
  <a href="https://github.com/Derrick-xn"><img src="https://github.com/Derrick-xn.png" width="56" height="56" alt="Derrick-xn" style="border-radius:50%" /></a>
  <a href="https://github.com/jarbcs1-prog"><img src="https://github.com/jarbcs1-prog.png" width="56" height="56" alt="jarbcs1-prog" style="border-radius:50%" /></a>
  <a href="https://github.com/LLen"><img src="https://github.com/LLen.png" width="56" height="56" alt="LLen" style="border-radius:50%" /></a>
  <a href="https://github.com/sebastiondev"><img src="https://github.com/sebastiondev.png" width="56" height="56" alt="sebastiondev" style="border-radius:50%" /></a>
  <a href="https://github.com/WindWardMist"><img src="https://github.com/WindWardMist.png" width="56" height="56" alt="WindWardMist" style="border-radius:50%" /></a>
  <a href="https://github.com/Shtirlyts"><img src="https://github.com/Shtirlyts.png" width="56" height="56" alt="Shtirlyts" style="border-radius:50%" /></a>
</p>

[Discussions](https://github.com/skalesapp/skales/discussions) · [Bug Reports](https://github.com/skalesapp/skales/issues)

---

## 📜 License

**BSL 1.1**: Free for personal, educational, and non-commercial use. Commercial SaaS or competing products require written permission. Converts to Apache 2.0 on 2030-04-19. See [LICENSE](./LICENSE) for full terms.

WordPress Plugin: **MIT**, [github.com/skalesapp/wordpress](https://github.com/skalesapp/wordpress)

Built with ❤️ in Vienna by [Mario Simic](https://mariosimic.at). 🦎

<div align="left">
  <a href="https://skales.app">skales.app</a>
</div>

---

## About the source in this repository

**This source snapshot is outdated.** The source in this repository is the v7 tree (`package.json` version 7.1.0), last current in March 2026; since then only a single security fix (July 2026) has landed, no feature updates. Skales continues as binary releases only: [Releases](https://github.com/skalesapp/skales/releases) and [skales.app](https://skales.app).
