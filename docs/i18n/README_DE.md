# ACE-Step Skills for Any Agents

[![en](https://img.shields.io/badge/lang-English-blue.svg)](../../README.md) [![zh](https://img.shields.io/badge/lang-中文-red.svg)](README_ZH.md) [![ja](https://img.shields.io/badge/lang-日本語-green.svg)](README_JA.md) [![ko](https://img.shields.io/badge/lang-한국어-yellow.svg)](README_KO.md) [![de](https://img.shields.io/badge/lang-Deutsch-black.svg)](README_DE.md) [![fr](https://img.shields.io/badge/lang-Français-purple.svg)](README_FR.md) [![ru](https://img.shields.io/badge/lang-Русский-orange.svg)](README_RU.md)

Benutzerdefinierte Skills für AI Agents (Claude Code, OpenAI Codex, etc.) zur Musikgenerierung über [ACE-Step](https://github.com/ace-step/ACE-Step-1.5) API.

## Funktionen

- **Text-zu-Musik** - Musik aus Beschreibungen generieren
- **Liedtext-Generierung** - Automatisch oder manuell
- **Audio-Fortsetzung** - Von bestehendem Audio fortsetzen
- **Audio-Repainting** - Bestimmte Teile ändern
- **Zufallsgenerierung** - Zufällige Musikproben

## Voraussetzungen

- **ACE-Step API Server** - [ACE-Step V1.5](https://github.com/ace-step/ACE-Step-1.5) API-Dienst erforderlich

## Installation

### Claude Code

`skills/acestep` Ordner kopieren nach:

**Projektebene**:
```
your-project/.claude/skills/acestep/
```

**Global**:
```
~/.claude/skills/acestep/
```

### OpenAI Codex

`skills/acestep` Ordner kopieren nach:

**Projektebene**:
```
your-project/.codex/skills/acestep/
```

**Global**:
```
~/.codex/skills/acestep/
```

## Konfiguration

`scripts/config.json` bearbeiten:

```json
{
  "api_url": "http://127.0.0.1:8001",
  "api_key": "",
  "generation": {
    "thinking": true,
    "audio_format": "mp3"
  }
}
```

## Verwendung

### Im Agent

```
Benutzer: Erstelle einen fröhlichen Popsong
Benutzer: Generiere Jazz-Hintergrundmusik
```

### Kommandozeile

```bash
./scripts/acestep.sh health
./scripts/acestep.sh generate "Pop music with guitar"
./scripts/acestep.sh random
```

## Referenzen

- [SKILL.md](../../skills/acestep/SKILL.md) - API-Dokumentation
- [ACE-Step](https://github.com/ace-step/ACE-Step-1.5) - ACE-Step Projekt
