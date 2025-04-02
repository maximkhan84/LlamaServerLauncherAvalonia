<h1 align="center">Corrallama</h1>

<p align="center">
  <img src="docs/images/preview.png" alt="Corrallama" width="350" />
</p>

<p align="center">
  <a href="README_ru.md">Русский</a> &nbsp;·&nbsp; <a href="CHANGELOG.md">Changelog</a>
</p>

<p align="center">
  <a href="https://github.com/pytraveler/LlamaServerLauncherAvalonia/releases/latest"><img alt="Latest release" src="https://img.shields.io/github/v/release/pytraveler/LlamaServerLauncherAvalonia?display_name=tag"></a>
  <a href="https://github.com/pytraveler/LlamaServerLauncherAvalonia/releases"><img alt="Downloads" src="https://img.shields.io/github/downloads/pytraveler/LlamaServerLauncherAvalonia/total"></a>
  <a href="LICENSE"><img alt="License" src="https://img.shields.io/github/license/pytraveler/LlamaServerLauncherAvalonia"></a>
  <img alt="Platforms" src="https://img.shields.io/badge/platform-Windows%20%7C%20Linux%20%7C%20macOS-blue">
  <img alt=".NET 8" src="https://img.shields.io/badge/.NET-8.0-512BD4">
  <a href="https://www.youtube.com/watch?v=vcPGTI_VA4k"><img alt="YouTube" src="https://img.shields.io/badge/YouTube-review-FF0000?logo=youtube&logoColor=white"></a>
</p>

A cross-platform desktop application for launching and managing [llama.cpp](https://github.com/ggerganov/llama.cpp) server instances with an intuitive graphical interface.

Built with [Avalonia UI](https://avaloniaui.net/) and .NET 8.

<p align="center">
  <img src="docs/images/main_window_preview_EN.png" alt="Main window" width="820" />
</p>

## Contents

- [Features](#features)
  - [Server Configuration](#server-configuration)
  - [Model Parameters](#model-parameters)
  - [Will It Fit in VRAM](#will-it-fit-in-vram)
  - [Generation Parameters](#generation-parameters)
  - [Speculative Decoding](#speculative-decoding)
  - [Advanced Options](#advanced-options)
  - [Feature Detection](#feature-detection)
  - [Built-in Help](#built-in-help)
  - [GGUF Model Insights](#gguf-model-insights)
  - [Multi-Instance Server Management](#multi-instance-server-management)
  - [Hardware Monitor](#hardware-monitor)
  - [Benchmarks](#benchmarks)
  - [MCP Servers](#mcp-servers)
  - [On-Demand Model Proxy (OpenAI-compatible)](#on-demand-model-proxy-openai-compatible)
  - [ComfyUI Integration](#comfyui-integration)
  - [Scenarios](#scenarios)
  - [Logging & Monitoring](#logging--monitoring)
  - [llama.cpp Integration](#llamacpp-integration)
  - [App Updates](#app-updates)
  - [System Integration](#system-integration)
  - [Docker Support](#docker-support)
  - [Profile Management](#profile-management)
  - [Drag & Drop](#drag--drop)
  - [System Tray](#system-tray)
  - [Localization](#localization)
  - [Appearance & Themes](#appearance--themes)
  - [Data Management](#data-management)
- [Requirements](#requirements)
- [Installation](#installation)
- [Verifying releases](#verifying-releases)
- [Build from Source](#build-from-source)
  - [Prerequisites](#prerequisites)
  - [Build Commands](#build-commands)
- [Tests](#tests)
- [Usage](#usage)
  - [Managing Profiles](#managing-profiles)
  - [Working with Scenarios](#working-with-scenarios)
  - [Running Benchmarks](#running-benchmarks)
  - [Connecting MCP Servers](#connecting-mcp-servers)
  - [Log Stream Server](#log-stream-server)
  - [On-Demand Model Proxy](#on-demand-model-proxy)
- [Architecture](#architecture)
  - [Project Structure](#project-structure)
- [Acknowledgments](#acknowledgments)
- [License](#license)

## Features

### Server Configuration
- **Executable Path** — Select the `llama-server` binary, or download llama.cpp directly from the app
- **Model Selection** — Choose a specific model file (.gguf), set a models directory, or specify a HuggingFace repo (`--hf-repo`) and file (`--hf-file`)
- **Model Picker** — Scan a folder (optionally recursive) for `.gguf` files and pick from a filterable list with metadata and file size. Every row also carries what the model would ask of the card — in green, orange or red, depending on whether it fits into the VRAM free right now — and a model split across several shards appears as one row with the size of all its parts. The last folder is remembered
- **Projector Picker** — the MMProj field has a list of its own, showing projector files only; they are kept out of the model list, where they could not be launched anyway
- **HuggingFace downloader** — a window that searches the Hub, lists the quants of a repository with their sizes and downloads the chosen one straight into your models folder. It takes search words, an `owner/name`, a repository link or a link to a file inside one. A split model is one row at the size of all its parts. Progress, speed and time left are shown; an interrupted download leaves a `.part` next to the target and resumes from that point instead of starting over. The finished file is put into the **Model** field. Gated and private repositories take a read token entered in the same window
- **Network Settings** — Configure host address (default: 127.0.0.1) and port (default: 8080)
- **API Key** — Set authentication API key for the server
- **Offline mode** — Force cache-only operation with no network access (`--offline`)
- **Input history** — Path and value fields remember recently used entries for quick reuse

### Model Parameters
- Context size (`-c`, `--ctx-size`)
- Number of threads (`-t`, `--threads`) and batch threads (`-tb`, `--threads-batch`)
- GPU layers (`-ngl`, `--gpu-layers`, `--n-gpu-layers`)
- Batch size (`-b`, `--batch-size`)
- UBatch size (`-ub`, `--ubatch-size`)
- MMProj path (`-mm`, `--mmproj`)
- Cache type K (`-ctk`, `--cache-type-k`)
- Cache type V (`-ctv`, `--cache-type-v`)
- Parallel slots (`-np`, `--parallel`)
- Timeout (`-to`, `--timeout`)
- Seed (`-s`, `--seed`)

### Will It Fit in VRAM
- **A verdict before the launch** — a line under the fields on the **Generation** tab says whether the model, with the values currently on the form, fits into the VRAM free right now. It is recomputed as you type and comes in three colours: it fits with room to spare, it fits but only just, or it does not fit
- **The number broken down** — weights, KV cache, compute buffers, headroom, and the vision projector when there is one, plus how many blocks would go to the card and how much would be left in system memory. All of it is read out of the GGUF itself, down to the size of every block in the tensor table
- **Three clickable hints** — the largest layer count that fits at the current context, the largest context that fits with the current layer count, and, for MoE models, how many expert blocks to leave on the CPU. A hint hides itself when what it offers is already set
- **Restart-aware** — while a server is running with the same model, whatever it holds counts as free again and the line is marked "(after a restart)", because applying new settings restarts the server and that frees the old memory before it takes the new
- **Checked against reality** — with **Verbose logging** on, a third line shows what llama.cpp actually took, in the same breakdown, and by how much the estimate was over or under; the same comparison goes into the application log every time a model finishes loading

### Generation Parameters
- Temperature (`--temp`, `--temperature`)
- Max tokens (`-n`, `--predict`, `--n-predict`)
- Min-P sampling (`--min-p`)
- Top-K sampling (`--top-k`)
- Top-P sampling (`--top-p`)
- Repeat penalty (`--repeat-penalty`)
- Presence penalty (`--presence-penalty`)
- Frequency penalty (`--frequency-penalty`)
- Reasoning mode (`-rea`, `--reasoning`)
- Reasoning budget (`--reasoning-budget`)

### Speculative Decoding
- Speculative decoding type (`--spec-type`)
- Draft model (`-md`, `--spec-draft-model`) or HuggingFace draft repo (`--hf-repo-draft`)
- Draft GPU layers (`-ngld`)
- Draft N-Max / N-Min (`--spec-draft-n-max`, `--spec-draft-n-min`)
- Draft P-Split / P-Min (`--spec-draft-p-split`, `--spec-draft-p-min`)

### Advanced Options
- Flash Attention (`-fa`, `--flash-attn`)
- Continuous batching (`-cb`, `--cont-batching`)
- WebUI (`--webui`, `--no-webui`)
- Embedding mode (`--embedding`, `--embeddings`)
- Slots management (`--slots`, `--no-slots`)
- Metrics endpoint (`--metrics`)
- Cache prompt (`--cache-prompt`, `--no-cache-prompt`)
- Context shift (`--context-shift`, `--no-context-shift`)
- Memory lock (`--mlock`)
- Memory map (`--mmap`, `--no-mmap`) — on builds where these are deprecated, both switches are emitted as a single `--load-mode`; older builds keep the old spelling
- Vision projector on GPU (`--mmproj-offload`, `--no-mmproj-offload`) — Off keeps the projector in system memory and hands the card back a gigabyte or two, at the price of image preprocessing running on the CPU
- Jinja chat templates (`--jinja`, `--no-jinja`) — recent builds enable them by default; tool calls and MCP servers do not work without them
- API key authentication (`--api-key`)
- Alias (`-a`, `--alias`)
- Custom command-line arguments (with toggleable enable/disable per argument)

### Feature Detection
The app automatically parses `llama-server --help` to detect which flags your binary supports. Unsupported options are visually indicated in the UI. When that probe fails, the log says why — including the case where the binary crashes on `--help` and will therefore crash on start as well — and flag filtering switches itself off instead of stripping the command line.

### Built-in Help
- **Section help** — a button at the bottom of the navigation rail (or the `F1` key) opens a short guide for the section you are currently on: what its fields do, what changing them costs, and where to start
- The **Benchmarks**, **Benchmark launch**, **Scenarios** and **Optimization** windows have their own help buttons
- Help ships inside the executable and works offline; its language follows the UI language. Each page ends with a link to the full README on GitHub
- **Meaningful empty states** — with no runs recorded yet, the benchmark comparison window explains where runs come from and offers to start the first one right there
- `Ctrl+F1` — the keyboard shortcut list

### GGUF Model Insights
- **Model info badge** — architecture, quantization, parameter size, layer/expert count, and vision projector info are read directly from the GGUF file and shown next to the model path
- **Max context detection** — the model's training context length is detected and displayed; the context-size slider is capped to it
- **Tensor table** — beyond the metadata, the file's tensor table is read as well, so the size of every block is known individually; that is what the VRAM estimate is built on
- **Split models** — a model published as several `.gguf` shards is read as one: the shards are found by name, their metadata merged and their sizes summed
- **Smart hints** — a warning when an mmproj projector file is selected as the model, and a clickable "offload all N layers" GPU-layers suggestion

### Multi-Instance Server Management
- Run multiple server instances simultaneously, each with its own profile/configuration
- Per-instance controls: start, stop, restart, unload model, open in browser
- **Model-load indicator** — after start, the app polls `/health` and shows "Loading model…" until the server is actually ready to serve
- **Live inference stats** — prompt/generation tokens per second (parsed from server output) and busy/total slots (polled via `/slots`) shown per running instance
- **Copy menu** — copy the server URL, the OpenAI-compatible base URL (`…/v1`), or a ready-to-run `curl` chat-completion command for any running instance
- **Crash advisor** — recognizes pinned-memory / CUDA-init failures caused by `--no-mmap` and suggests disabling it in a sticky toast
- **No leftover processes** — the server and everything it spawns (MCP servers and the applications they start) are held in a Windows job object, so stopping the server, closing the launcher or even a launcher crash takes them all down; can be turned off on the **Behavior** tab (Windows only)
- Per-instance auto-restart on crash and log toggle
- Short-lived server error indicator (shows if instance exits within 5 seconds of starting)
- Instance view in system tray menu with full per-instance controls

### Hardware Monitor
- CPU, RAM, GPU utilization, VRAM, and GPU temperature gauges displayed above the instance list (multi-GPU aware)
- NVIDIA GPUs via `nvidia-smi`, AMD GPUs via `rocm-smi`; CPU/RAM metrics on Windows, Linux, and macOS
- Automatically pauses while a model is loading so GPU polling cannot interfere with CUDA/HIP initialization
- Can be disabled on the **Behavior** tab

### Benchmarks
- **Run & save benchmark** — launch a profile in benchmark mode from the Start-server split-button flyout, with an editable llama-server argument line (with per-argument-group toggles)
- Captures the server's `/metrics` endpoint (Prometheus) and tokens-per-second from the log; optionally drives a built-in standard HTTP workload against the live server
- **Prompt run** — ask the model your own questions as part of a run: set a system prompt and a list of requests (separated by `---` lines), sent as a conversation or independently; the launcher starts the server, records the answers with per-request speeds and stops it, and the full transcript lands in `prompt-run.md` next to the report
- Each run is stored per profile in the data directory (`benchmarks/<profile>/<runId>/`: config, command line, server log, metrics, report, prompt-run transcript)
- **Comparison window** — compare saved runs side by side as Markdown tables, pick which rows (metrics, launch parameters, environment) the table shows, save named comparison sets together with that row selection, export reports as `.md`, and pin extra files to a run

### MCP Servers
Attach Model Context Protocol servers to a profile so the launched model can call external tools straight from the llama.cpp WebUI (requires a llama.cpp build with `--mcp-servers-config` support).
- **Per-profile server list** on the **MCP** tab — each server is a row with an on/off switch; on launch the app generates a Cursor-compatible `mcp.json` and passes it to llama-server via `--mcp-servers-config`
- **Server editor** — command, arguments, environment variables, working directory and timeout, with browse buttons; the **Test** button starts the command the way llama-server would and lists the tools it reports, before any model is loaded
- **Import** — read servers from an existing `mcp.json` (Cursor / Claude Desktop format) or copy them from another profile via the import button's menu
- **Tools query** — ask a running server `GET /tools` and see the discovered tools grouped by MCP server
- MCP problems that llama-server only mentions in its log (a server it could not spawn, one that died) surface as toast notifications
- **Docker aware** — the generated config directory is mounted into the container and the path rewritten
- The tab warns about the practical caveats: servers run as child processes with the launcher's rights, enabling MCP limits CORS to localhost unless set explicitly, and tool calls need the jinja templates that the **Jinja templates** switch controls

### On-Demand Model Proxy (OpenAI-compatible)
A built-in reverse proxy that loads the right profile on demand when an API request arrives — point a client like Cherry Studio at it and the matching model is loaded automatically, served, and unloaded when idle.
- **OpenAI-style endpoints** — `/v1/chat/completions`, `/v1/completions`, `/v1/embeddings`, `/v1/rerank`, `/infill`, plus `/v1/models` (advertises your profiles as models) and `/health`
- **Profile = model** — send the profile name in the request's `model` field; the proxy starts that profile, waits until it is healthy, then proxies the request and streams the response back (SSE supported)
- **One model in VRAM** — starting a profile evicts any other running one, so only a single model stays loaded
- **Idle auto-unload** — stops the server after a configurable idle timeout (set to `0` to disable)
- **LAN-accessible** — listens on all interfaces; optional Bearer API key for access control
- Routing-mode profiles (`--models-dir`) are excluded from the advertised model list

### ComfyUI Integration
- **Free ComfyUI VRAM before loading a profile** — optionally calls ComfyUI's `/free` endpoint to unload its models and release GPU memory before any profile starts (manual start or via the on-demand proxy), so you can hand the GPU between ComfyUI and llama.cpp without juggling them by hand. Configured on the **Behavior** tab (toggle + ComfyUI URL)

### Scenarios
- Define sequences of profiles that run in order with configurable time intervals
- Auto-start scenarios on application launch
- Create, edit, rename, and delete scenarios
- Drag-and-drop profile ordering within a scenario
- Clone profile directly into a scenario

### Logging & Monitoring
- Log file output (`--log-file`)
- Verbose logging (`-v`, `--verbose`) — a three-way switch on the **Options** tab, stored per profile; llama.cpp prints its memory breakdown only at this verbosity, and that breakdown is what the VRAM panel compares its estimate against
- Real-time log viewer with auto-scroll
- Server status display with process ID
- Auto-restart on crash
- Automatic log file rotation (configurable max file count and size)
- Health/slots polling heartbeats are filtered out of the log view
- **A crash is explained, not just numbered** — an exit code from the OS is decoded in the log (`-1073741819 (0xC0000005, STATUS_ACCESS_VIOLATION)`) together with what usually causes it; a server that died without printing a single line is called out as such, and the versions of the MSVC runtime DLLs found in System32 and next to the binary are written down, since a missing or outdated Visual C++ Redistributable is a common reason
- **Referenced paths are checked at every launch** — whichever way the server is started, a model, projector, draft model or file behind a custom argument that is no longer on disk is named in the log and in a notification
- **Built-in Log Stream Server** — WebSocket-based log streaming with HTTP API endpoints:
  - `/ws` — Real-time WebSocket log streaming with optional token authentication
  - `/api/logs/history` — JSON endpoint for log history
  - `/api/status` — Stream server status
  - Built-in HTML log viewer page with auto-scroll, clear, and reconnect controls

### llama.cpp Integration
- **One-click download** — Download official llama.cpp releases directly from GitHub
- **Backend auto-detect** — the download dialog detects the GPU vendor (NVIDIA / AMD / Intel / Apple) and pre-selects the best-matching build (CUDA / Vulkan / HIP / SYCL / CPU) with an "Auto-detected" hint; the choice can still be changed manually
- **Update notifications** — Automatically checks for new llama.cpp releases
- **Version management** — Install and switch between different versions
- **PATH integration** — Optionally add llama.cpp directory to PATH
- **Experimental build repositories** — Add custom GitHub release sources (e.g. [llama-cpp-turboquant](https://github.com/pytraveler/llama-cpp-turboquant)) with tag filters and periodic update checks to download experimental builds
- **Rollback of the replaced build** — an update moves the build it replaces into `llama.cpp.prev` next to the working directory instead of deleting it, and the download window puts it back on request — instantly and without a network. The rollback swaps the two, so it can be undone the same way; exactly one build is kept, and a checkbox on the **Updates** tab restores the old delete-on-update behaviour
- **A download into the managed folder is questioned** — picking a target folder inside the launcher's own llama.cpp directory, which every update overwrites, asks for confirmation first
- **An empty build list explains itself** — a release still being reread by tag says so, and after that the dialog distinguishes a release with no files at all, one carrying no llama.cpp builds for any platform (the experimental `v0.*` tags),