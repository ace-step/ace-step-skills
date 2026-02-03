# ACE-Step Skills for Any Agents

[![en](https://img.shields.io/badge/lang-English-blue.svg)](README.md) [![zh](https://img.shields.io/badge/lang-中文-red.svg)](docs/i18n/README_ZH.md) [![ja](https://img.shields.io/badge/lang-日本語-green.svg)](docs/i18n/README_JA.md) [![ko](https://img.shields.io/badge/lang-한국어-yellow.svg)](docs/i18n/README_KO.md) [![de](https://img.shields.io/badge/lang-Deutsch-black.svg)](docs/i18n/README_DE.md) [![fr](https://img.shields.io/badge/lang-Français-purple.svg)](docs/i18n/README_FR.md) [![ru](https://img.shields.io/badge/lang-Русский-orange.svg)](docs/i18n/README_RU.md)

Custom Skills for AI Agents (Claude Code, OpenAI Codex, etc.) to generate music via [ACE-Step](https://github.com/ace-step/ACE-Step-1.5) API.

## Features

- **Text-to-Music** - Generate music from descriptions
- **Lyrics Generation** - Auto-generate or manually specify lyrics
- **Audio Continuation** - Continue from existing audio
- **Audio Repainting** - Modify specific parts of audio
- **Random Generation** - Generate random music samples

## Prerequisites

- **ACE-Step API Server** - Running [ACE-Step V1.5](https://github.com/ace-step/ACE-Step-1.5) API service

## Installation

### Claude Code

Copy `skills/acestep` folder to:

**Project level** (current project only):
```
your-project/.claude/skills/acestep/
```

**Global level** (all projects):
```
~/.claude/skills/acestep/
```

### OpenAI Codex

Copy `skills/acestep` folder to:

**Project level**:
```
your-project/.codex/skills/acestep/
```

**Global level**:
```
~/.codex/skills/acestep/
```

### Directory Structure

```
skills/acestep/
├── SKILL.md
└── scripts/
    ├── acestep.sh
    └── config.json
```

## Configuration

Edit `scripts/config.json` to configure API connection and defaults:

```json
{
  "api_url": "http://127.0.0.1:8001",
  "api_key": "",
  "generation": {
    "thinking": true,
    "use_format": true,
    "audio_format": "mp3",
    "vocal_language": "en"
  }
}
```

### Options

| Option | Default | Description |
|--------|---------|-------------|
| `api_url` | `http://127.0.0.1:8001` | API server address |
| `api_key` | `""` | API key (optional) |
| `generation.thinking` | `true` | Enable 5Hz LM (high quality) |
| `generation.use_format` | `true` | Enhance caption/lyrics via LM |
| `generation.audio_format` | `mp3` | Output format (mp3/wav/flac) |
| `generation.vocal_language` | `en` | Vocal language |

## Usage

### In Agent

After installation, mention music generation in conversation:

```
User: Generate a cheerful pop song
User: Create a song about spring
User: Generate jazz background music
```

### Command Line

```bash
# Check API status
./scripts/acestep.sh health

# Generate music - Caption mode
./scripts/acestep.sh generate "Pop music with guitar"

# Generate music - Simple mode (LM auto-generates)
./scripts/acestep.sh generate -d "A cheerful song about spring"

# With lyrics
./scripts/acestep.sh generate -c "Lyrical pop" -l "[Verse] Hello world"

# Random generation
./scripts/acestep.sh random

# Check task status
./scripts/acestep.sh status <job_id>
```

### Options

| Option | Description |
|--------|-------------|
| `-c, --caption` | Music style description |
| `-d, --description` | Simple description, LM auto-generates |
| `-l, --lyrics` | Lyrics content |
| `--no-thinking` | Disable thinking mode |
| `--steps` | Diffusion steps |
| `--guidance` | Guidance scale |
| `--duration` | Audio duration (seconds) |
| `--bpm` | Tempo |

## Output

Results saved to `acestep_output` folder:

```
project_root/
├── acestep_output/
│   ├── <job_id>.json      # Task result (JSON)
│   ├── <job_id>_1.mp3     # Audio file
│   └── ...
```

## GPU Memory

| VRAM | LM Model | Notes |
|------|----------|-------|
| ≤6GB | None (DiT only) | LM disabled |
| 6-12GB | `acestep-5Hz-lm-0.6B` | Lightweight |
| 12-16GB | `acestep-5Hz-lm-1.7B` | Better quality |
| ≥16GB | `acestep-5Hz-lm-4B` | Best quality |

## References

- [SKILL.md](skills/acestep/SKILL.md) - Full API documentation
- [ACE-Step](https://github.com/ace-step/ACE-Step-1.5) - ACE-Step project