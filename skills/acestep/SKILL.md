---
name: acestep
description: Use ACE-Step API to generate music, edit songs, and remix music. Supports text-to-music, lyrics generation, audio continuation, and audio repainting. Use this skill when users mention generating music, creating songs, music production, remix, or audio continuation.
allowed-tools: Read, Write, Bash
---

# ACE-Step Music Generation Skill

Use ACE-Step V1.5 API for music generation and editing.

## GPU Memory Auto-Configuration

The API server automatically adapts to your GPU's VRAM:

| GPU VRAM | Recommended LM Model | CPU Offload | Notes |
|----------|---------------------|-------------|-------|
| ≤6GB | None (DiT only) | Auto | LM disabled by default |
| 6-12GB | `acestep-5Hz-lm-0.6B` | Auto | Lightweight, good balance |
| 12-16GB | `acestep-5Hz-lm-1.7B` | Auto | Better quality |
| ≥16GB | `acestep-5Hz-lm-4B` | Off | Best quality |

Environment variables to override auto-detection:
- `ACESTEP_INIT_LLM`: Force enable/disable LM initialization
- `ACESTEP_OFFLOAD_TO_CPU`: Force CPU offload on/off
- `ACESTEP_LM_MODEL_PATH`: Specify LM model path

## Output Files

After generation, the script automatically saves results to the `acestep_output` folder in the project root (same level as `.claude`):

```
project_root/
├── .claude/
│   └── skills/acestep/...
├── acestep_output/          # Output directory
│   ├── <job_id>.json         # Complete task result (JSON)
│   ├── <job_id>_1.mp3        # First audio file
│   ├── <job_id>_2.mp3        # Second audio file (if batch_size > 1)
│   └── ...
└── ...
```

## Configuration

The script uses `scripts/config.json` to manage default settings.

### Configuration Priority Rules

**Important**: Configuration follows this priority (high to low):

1. **Command line arguments** > **config.json defaults**
2. User-specified parameters **temporarily override** defaults but **do not modify** config.json
3. Only `config --set` command **permanently modifies** config.json

**Example**:
```bash
# config.json has thinking=true

# Use default config (thinking=true)
./scripts/acestep.sh generate "Pop music"

# Temporary override (thinking=false for this run, config.json unchanged)
./scripts/acestep.sh generate "Pop music" --no-thinking

# Permanently modify default config
./scripts/acestep.sh config --set generation.thinking false
```

### API Connection Flow

1. **Load config**: Read `scripts/config.json` (use built-in defaults if not exists)
2. **Health check**: Request `/health` endpoint to verify service availability
3. **Connection failure**: Prompt user for correct API address and save to config.json

### Default Config File (`scripts/config.json`)

```json
{
  "api_url": "http://127.0.0.1:8001",
  "api_key": "",
  "generation": {
    "thinking": true,
    "use_format": true,
    "use_cot_caption": true,
    "use_cot_language": true,
    "batch_size": 1,
    "audio_format": "mp3",
    "vocal_language": "en"
  }
}
```

### Configuration Options

| Option | Default | Description |
|--------|---------|-------------|
| `api_url` | `http://127.0.0.1:8001` | API server address |
| `api_key` | `""` | API authentication key (optional) |
| `generation.thinking` | `true` | Enable 5Hz LM model (high quality mode) |
| `generation.use_format` | `true` | Use LM to enhance caption/lyrics |
| `generation.use_cot_caption` | `true` | Use CoT to enhance caption |
| `generation.use_cot_language` | `true` | Use CoT to enhance language detection |
| `generation.audio_format` | `mp3` | Output format |
| `generation.vocal_language` | `en` | Vocal language |

## Script Usage

### Config Management (Permanently modify config.json)

```bash
# View all config
./scripts/acestep.sh config

# List all config options and current values
./scripts/acestep.sh config --list

# Get single config value
./scripts/acestep.sh config --get generation.thinking

# Permanently modify config value (writes to config.json)
./scripts/acestep.sh config --set generation.thinking false
./scripts/acestep.sh config --set api_url http://192.168.1.100:8001

# Reset to default config
./scripts/acestep.sh config --reset
```

### Generate Music (Command line args temporarily override, don't modify config.json)

Supports two generation modes:

**Caption Mode** - Directly specify music style description
```bash
./scripts/acestep.sh generate "Pop music with guitar"
./scripts/acestep.sh generate -c "Lyrical pop" -l "[Verse] Lyrics content"
```

**Simple Mode** - Use simple description, LM auto-generates caption and lyrics
```bash
./scripts/acestep.sh generate -d "A cheerful song about spring"
./scripts/acestep.sh generate -d "A love song for February"
```

**Other Options**
```bash
# Temporarily disable thinking mode (this run only, config file unchanged)
./scripts/acestep.sh generate "EDM" --no-thinking

# Temporarily disable format mode
./scripts/acestep.sh generate "Classical piano" --no-format

# Temporarily specify other parameters
./scripts/acestep.sh generate "Jazz" --steps 16 --guidance 8.0

# Random generation
./scripts/acestep.sh random

# Query task status (completed tasks auto-download audio)
./scripts/acestep.sh status <job_id>

# List available models
./scripts/acestep.sh models

# Check API health
./scripts/acestep.sh health
```

### Shell Script (Linux/macOS/Git Bash, requires curl + jq)

```bash
# Config management
./scripts/acestep.sh config --list
./scripts/acestep.sh config --set generation.thinking false

# Caption mode (auto-save results and download audio on completion)
./scripts/acestep.sh generate "Pop music with guitar"
./scripts/acestep.sh generate -c "Lyrical pop" -l "[Verse] Lyrics content"

# Simple mode (LM auto-generates caption/lyrics)
./scripts/acestep.sh generate -d "A cheerful song about spring"

# Random generation
./scripts/acestep.sh random

# Other commands
./scripts/acestep.sh status <job_id>
./scripts/acestep.sh models
./scripts/acestep.sh health
```

## Script Dependencies

| Script | Dependencies | Platform |
|------|------|------|
| `acestep.sh` | curl, jq | Linux/macOS/Git Bash |

Install jq:
- Ubuntu/Debian: `apt install jq`
- macOS: `brew install jq`
- Windows (choco): `choco install jq`

## API Endpoints

| Endpoint | Method | Description | Wrapped |
|----------|--------|-------------|---------|
| `/health` | GET | Health check | Yes |
| `/release_task` | POST | Create music generation task | Yes |
| `/query_result` | POST | Batch query task results | Yes |
| `/v1/models` | GET | List available DiT models | Yes |
| `/v1/stats` | GET | Get server statistics | Yes |
| `/v1/audio?path={path}` | GET | Download generated audio file | No |
| `/create_random_sample` | POST | Get random sample parameters | Yes |
| `/format_input` | POST | Format and enhance lyrics/caption via LLM | Yes |

**Wrapped**: Response wrapped in `{"data": ..., "code": 200, "error": null, "timestamp": ..., "extra": null}`

## Main Parameters

### Basic Parameters

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `prompt` | string | "" | Music description text (Caption mode) |
| `sample_query` | string | "" | Simple description, LM auto-generates caption/lyrics |
| `lyrics` | string | "" | Lyrics content |
| `thinking` | bool | false | Enable 5Hz LM for audio code generation |
| `sample_mode` | bool | false | Random sampling mode |
| `use_format` | bool | false | Use LM to enhance caption/lyrics |
| `model` | string | - | Specify DiT model name |
| `batch_size` | int | 1 | Number of audio files to generate |

### Music Attributes

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `bpm` | int | - | Tempo (beats per minute) |
| `key_scale` | string | "" | Key (e.g. "C Major") |
| `time_signature` | string | "" | Time signature (e.g. "4/4") |
| `vocal_language` | string | "en" | Vocal language |
| `audio_duration` | float | - | Audio duration (seconds) |
| `audio_format` | string | "mp3" | Output format (mp3/wav/flac) |

### Generation Parameters

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `inference_steps` | int | 8 | Number of diffusion steps |
| `guidance_scale` | float | 7.0 | Classifier-free guidance scale |
| `seed` | int | -1 | Random seed (-1 for random) |
| `use_random_seed` | bool | true | Use random seed |
| `infer_method` | string | "ode" | Diffusion method (ode/sde) |
| `shift` | float | 3.0 | Timestep shift (1.0~5.0, base models only) |
| `timesteps` | string | - | Custom timesteps (comma-separated) |

### LM Parameters

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `use_cot_caption` | bool | true | Use CoT to enhance caption |
| `use_cot_language` | bool | true | Use CoT for language detection |
| `lm_model_path` | string | - | LM model path (auto-selected by GPU) |
| `lm_backend` | string | "vllm" | LM backend (vllm/pt) |
| `lm_temperature` | float | 0.85 | LM sampling temperature |
| `lm_top_p` | float | 0.9 | LM top-p sampling |
| `constrained_decoding` | bool | true | Use constrained decoding |
| `allow_lm_batch` | bool | true | Allow LM batch processing |

### Audio Task Parameters

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `task_type` | string | "text2music" | Task type |
| `reference_audio_path` | string | - | Reference audio for style |
| `src_audio_path` | string | - | Source audio for continuation |
| `audio_code_string` | string | "" | Pre-generated audio codes |
| `repainting_start` | float | 0.0 | Repainting start position |
| `repainting_end` | float | - | Repainting end position |
| `audio_cover_strength` | float | 1.0 | Audio cover strength |

### Task Types

| Value | Description |
|-------|-------------|
| `text2music` | Text to music (default) |
| `continuation` | Audio continuation |
| `repainting` | Audio repainting |

## Task Status

| Status | Description |
|--------|-------------|
| `queued` | Waiting in queue |
| `running` | Generating |
| `succeeded` | Generation successful |
| `failed` | Generation failed |

## Response Examples

### Standard Response Format

All API responses are wrapped in a standard format:

```json
{
  "data": <actual_response_data>,
  "code": 200,
  "error": null,
  "timestamp": 1704067200000,
  "extra": null
}
```

### Create Task Response (`/release_task`)

Uses `_wrap_response` wrapper:

```json
{
  "data": {
    "task_id": "abc123-def456",
    "status": "queued",
    "queue_position": 1
  },
  "code": 200,
  "error": null,
  "timestamp": 1704067200000,
  "extra": null
}
```

### Query Result Request (`/query_result`)

```json
{
  "task_id_list": ["abc123-def456"]
}
```

### Query Result Response (Success)

Response is wrapped with `_wrap_response`:

```json
{
  "data": [
    {
      "task_id": "abc123-def456",
      "status": 1,
      "result": "[{\"file\":\"/v1/audio?path=...\",\"status\":1,\"metas\":{\"bpm\":120,\"duration\":60,\"keyscale\":\"C Major\",\"timesignature\":\"4/4\"}}]"
    }
  ],
  "code": 200,
  "error": null,
  "timestamp": 1704067200000,
  "extra": null
}
```

### Models Response (`/v1/models`)

Uses `_wrap_response` wrapper:

```json
{
  "data": {
    "models": [
      {"name": "acestep-v15-turbo", "is_default": true},
      {"name": "acestep-v15-base", "is_default": false}
    ],
    "default_model": "acestep-v15-turbo"
  },
  "code": 200,
  "error": null,
  "timestamp": 1704067200000,
  "extra": null
}
```

### Stats Response (`/v1/stats`)

Uses `_wrap_response` wrapper:

```json
{
  "data": {
    "jobs": {"total": 10, "queued": 2, "running": 1, "succeeded": 6, "failed": 1},
    "queue_size": 2,
    "queue_maxsize": 200,
    "avg_job_seconds": 45.5
  },
  "code": 200,
  "error": null,
  "timestamp": 1704067200000,
  "extra": null
}
```

### Health Response (`/health`)

Uses `_wrap_response` wrapper:

```json
{
  "data": {
    "status": "ok",
    "service": "ACE-Step API",
    "version": "1.0"
  },
  "code": 200,
  "error": null,
  "timestamp": 1704067200000,
  "extra": null
}
```

### Create Random Sample Response (`/create_random_sample`)

Uses `_wrap_response` wrapper:

```json
{
  "data": {
    "description": "a soft Bengali love song for a quiet evening",
    "instrumental": false,
    "vocal_language": "bn"
  },
  "code": 200,
  "error": null,
  "timestamp": 1704067200000,
  "extra": null
}
```

Input parameters:
- `sample_type`: "simple_mode" (default) or "custom_mode"

### Format Input Response (`/format_input`)

Uses `_wrap_response` wrapper:

```json
{
  "data": {
    "caption": "Enhanced caption...",
    "lyrics": "Formatted lyrics...",
    "bpm": 120,
    "key_scale": "C Major",
    "time_signature": "4/4",
    "duration": 60,
    "vocal_language": "en"
  },
  "code": 200,
  "error": null,
  "timestamp": 1704067200000,
  "extra": null
}
```

Input parameters:
- `prompt`: Caption text
- `lyrics`: Lyrics text
- `temperature`: LM temperature (default: 0.85)
- `param_obj`: JSON object with metadata (bpm, duration, key, time_signature, language)

Status codes: `0` = processing, `1` = success, `2` = failed

## Notes

1. **GPU Auto-Config**: Server auto-detects GPU VRAM and selects appropriate LM model and offload settings
2. **Config priority**: Command line args > config.json defaults
3. **Thinking mode**: Uses 5Hz LM for audio codes, higher quality but slower
4. **Async tasks**: All generation tasks are async, poll via `POST /query_result`
5. **Auto download**: Script auto-saves JSON and downloads audio to `acestep_output/`
6. **Status codes**: 0=processing, 1=success, 2=failed
7. **LM Model Selection**: Auto-selected based on GPU tier (0.6B/1.7B/4B)

## References
- Shell script: [scripts/acestep.sh](scripts/acestep.sh) (Linux/macOS/Git Bash)
- Default config: [scripts/config.json](scripts/config.json)
- Output directory: `acestep_output/` in project root
