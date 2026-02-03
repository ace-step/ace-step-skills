# ACE-Step Skills for Any Agents

[![en](https://img.shields.io/badge/lang-English-blue.svg)](../../README.md) [![zh](https://img.shields.io/badge/lang-中文-red.svg)](README_ZH.md) [![ja](https://img.shields.io/badge/lang-日本語-green.svg)](README_JA.md) [![ko](https://img.shields.io/badge/lang-한국어-yellow.svg)](README_KO.md) [![de](https://img.shields.io/badge/lang-Deutsch-black.svg)](README_DE.md) [![fr](https://img.shields.io/badge/lang-Français-purple.svg)](README_FR.md) [![ru](https://img.shields.io/badge/lang-Русский-orange.svg)](README_RU.md)

Skills personnalisés pour AI Agents (Claude Code, OpenAI Codex, etc.) pour générer de la musique via [ACE-Step](https://github.com/ace-step/ACE-Step-1.5) API.

## Fonctionnalités

- **Texte vers musique** - Générer de la musique à partir de descriptions
- **Génération de paroles** - Automatique ou manuelle
- **Continuation audio** - Continuer à partir d'un audio existant
- **Repeinture audio** - Modifier des parties spécifiques
- **Génération aléatoire** - Échantillons musicaux aléatoires

## Prérequis

- **Serveur API ACE-Step** - Service [ACE-Step V1.5](https://github.com/ace-step/ACE-Step-1.5) requis

## Installation

### Claude Code

Copier `skills/acestep` vers:

**Niveau projet**:
```
your-project/.claude/skills/acestep/
```

**Niveau global**:
```
~/.claude/skills/acestep/
```

### OpenAI Codex

Copier `skills/acestep` vers:

**Niveau projet**:
```
your-project/.codex/skills/acestep/
```

**Niveau global**:
```
~/.codex/skills/acestep/
```

## Configuration

Éditer `scripts/config.json`:

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

## Utilisation

### Dans l'Agent

```
Utilisateur: Crée une chanson pop joyeuse
Utilisateur: Génère de la musique jazz
```

### Ligne de commande

```bash
./scripts/acestep.sh health
./scripts/acestep.sh generate "Pop music with guitar"
./scripts/acestep.sh random
```

## Références

- [SKILL.md](../../skills/acestep/SKILL.md) - Documentation API
- [ACE-Step](https://github.com/ace-step/ACE-Step-1.5) - Projet ACE-Step
