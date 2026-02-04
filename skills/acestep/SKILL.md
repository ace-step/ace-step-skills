---
name: acestep
description: Use ACE-Step API to generate music, edit songs, and remix music. Supports text-to-music, lyrics generation, audio continuation, and audio repainting. Use this skill when users mention generating music, creating songs, music production, remix, or audio continuation.
allowed-tools: Read, Write, Bash
---

# ACE-Step Music Generation Skill

Use ACE-Step V1.5 API for music generation. Script: `scripts/acestep.sh` (requires curl + jq).

**WORKFLOW**: For user requests requiring vocals, you should:
1. Consult [Music Creation Guide](./music-creation-guide.md) for lyrics writing, caption creation, duration/BPM/key selection
2. Write complete, well-structured lyrics yourself based on the guide
3. Generate using Caption mode with `-c` and `-l` parameters

Only use Simple/Random mode (`-d` or `random`) for quick inspiration or instrumental exploration.

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

### JSON Result Structure

**Important**: When LM enhancement is enabled (`use_format=true`), the final synthesized content may differ from your input. Check the JSON file for actual values:

| Field | Description |
|-------|-------------|
| `prompt` | **Actual caption** used for synthesis (may be LM-enhanced) |
| `lyrics` | **Actual lyrics** used for synthesis (may be LM-enhanced) |
| `metas.prompt` | Original input caption |
| `metas.lyrics` | Original input lyrics |
| `metas.bpm` | BPM used |
| `metas.keyscale` | Key scale used |
| `metas.duration` | Duration in seconds |
| `generation_info` | Detailed timing and model info |
| `seed_value` | Seeds used (for reproducibility) |
| `lm_model` | LM model name |
| `dit_model` | DiT model name |

To get the actual synthesized lyrics, parse the JSON and read the top-level `lyrics` field, not `metas.lyrics`.

## Script Commands

**CRITICAL - Complete Lyrics Input**: When providing lyrics via the `-l` parameter, you MUST pass ALL lyrics content WITHOUT any omission:
- If user provides lyrics, pass the ENTIRE text they give you
- If you generate lyrics yourself, pass the COMPLETE lyrics you created
- NEVER truncate, shorten, or pass only partial lyrics
- Missing lyrics will result in incomplete or incoherent songs

**Music Parameters**: Refer to [Music Creation Guide](./music-creation-guide.md) for how to calculate duration, choose BPM, key scale, and time signature.

```bash
# need to cd skills path
cd {project_root}/{.claude or .codex}/skills/acestep/

# Caption mode - RECOMMENDED: Write lyrics first, then generate
./scripts/acestep.sh generate -c "Electronic pop, energetic synths" -l "[Verse] Your complete lyrics
[Chorus] Full chorus here..." --duration 120 --bpm 128

# Instrumental only
./scripts/acestep.sh generate "Jazz with saxophone"

# Quick exploration (Simple/Random mode)
./scripts/acestep.sh generate -d "A cheerful song about spring"
./scripts/acestep.sh random

# Options
./scripts/acestep.sh generate "Rock" --duration 60 --batch 2
./scripts/acestep.sh generate "EDM" --no-thinking    # Faster

# Other commands
./scripts/acestep.sh status <job_id>
./scripts/acestep.sh health
./scripts/acestep.sh models
```

## Configuration

**Important**: Configuration follows this priority (high to low):

1. **Command line arguments** > **config.json defaults**
2. User-specified parameters **temporarily override** defaults but **do not modify** config.json
3. Only `config --set` command **permanently modifies** config.json

### Default Config File (`scripts/config.json`)

```json
{
  "api_url": "http://127.0.0.1:8001",
  "api_key": "",
  "generation": {
    "thinking": true,
    "use_format": false,
    "use_cot_caption": true,
    "use_cot_language": false,
    "batch_size": 1,
    "audio_format": "mp3",
    "vocal_language": "en"
  }
}
```

| Option | Default | Description |
|--------|---------|-------------|
| `api_url` | `http://127.0.0.1:8001` | API server address |
| `api_key` | `""` | API authentication key (optional) |
| `generation.thinking` | `true` | Enable 5Hz LM (higher quality, slower) |
| `generation.audio_format` | `mp3` | Output format (mp3/wav/flac) |
| `generation.vocal_language` | `en` | Vocal language |

## API Reference

All responses wrapped: `{"data": <payload>, "code": 200, "error": null, "timestamp": ...}`

| Endpoint | Method | Description |
|----------|--------|-------------|
| `/health` | GET | Health check |
| `/release_task` | POST | Create generation task |
| `/query_result` | POST | Query task status, body: `{"task_id_list": ["id"]}` |
| `/v1/models` | GET | List available models |
| `/v1/audio?path={path}` | GET | Download audio file |

### Query Result Response

```json
{
  "data": [{
    "task_id": "xxx",
    "status": 1,
    "result": "[{\"file\":\"/v1/audio?path=...\",\"metas\":{\"bpm\":120,\"duration\":60,\"keyscale\":\"C Major\"}}]"
  }]
}
```

Status codes: `0` = processing, `1` = success, `2` = failed

## Request Parameters (`/release_task`)

Parameters can be placed in `param_obj` object.

### Generation Modes

| Mode | Usage | When to Use |
|------|-------|-------------|
| **Caption** (Recommended) | `generate -c "style" -l "lyrics"` | For vocal songs - write lyrics yourself first |
| **Simple** | `generate -d "description"` | Quick exploration, LM generates everything |
| **Random** | `random` | Random generation for inspiration |

### Core Parameters

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `prompt` | string | "" | Music style description (Caption mode) |
| `lyrics` | string | "" | **Full lyrics content** - Pass ALL lyrics without omission. Use `[inst]` for instrumental. Partial/truncated lyrics = incomplete songs |
| `sample_mode` | bool | false | Enable Simple/Random mode |
| `sample_query` | string | "" | Description for Simple mode |
| `thinking` | bool | false | Enable 5Hz LM for audio code generation |
| `use_format` | bool | false | Use LM to enhance caption/lyrics |
| `model` | string | - | DiT model name |
| `batch_size` | int | 1 | Number of audio files to generate |

### Music Attributes

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `audio_duration` | float | - | Duration in seconds |
| `bpm` | int | - | Tempo (beats per minute) |
| `key_scale` | string | "" | Key (e.g. "C Major") |
| `time_signature` | string | "" | Time signature (e.g. "4/4") |
| `vocal_language` | string | "en" | Language code (en, zh, ja, etc.) |
| `audio_format` | string | "mp3" | Output format (mp3/wav/flac) |

### Generation Parameters

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `inference_steps` | int | 8 | Diffusion steps |
| `guidance_scale` | float | 7.0 | CFG scale |
| `seed` | int | -1 | Random seed (-1 for random) |
| `infer_method` | string | "ode" | Diffusion method (ode/sde) |

### Audio Task Parameters

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `task_type` | string | "text2music" | text2music / continuation / repainting |
| `src_audio_path` | string | - | Source audio for continuation |
| `repainting_start` | float | 0.0 | Repainting start position (seconds) |
| `repainting_end` | float | - | Repainting end position (seconds) |

### Example Request (Simple Mode)

```json
{
  "sample_mode": true,
  "sample_query": "A cheerful pop song about spring",
  "thinking": true,
  "param_obj": {
    "duration": 60,
    "bpm": 120,
    "language": "en"
  },
  "batch_size": 2
}
```
