# ACE-Step Skills for Any Agents

[![en](https://img.shields.io/badge/lang-English-blue.svg)](../../README.md) [![zh](https://img.shields.io/badge/lang-中文-red.svg)](README_ZH.md) [![ja](https://img.shields.io/badge/lang-日本語-green.svg)](README_JA.md) [![ko](https://img.shields.io/badge/lang-한국어-yellow.svg)](README_KO.md) [![de](https://img.shields.io/badge/lang-Deutsch-black.svg)](README_DE.md) [![fr](https://img.shields.io/badge/lang-Français-purple.svg)](README_FR.md) [![ru](https://img.shields.io/badge/lang-Русский-orange.svg)](README_RU.md)

Пользовательские Skills для AI Agents (Claude Code, OpenAI Codex и др.) для генерации музыки через [ACE-Step](https://github.com/ace-step/ACE-Step-1.5) API.

## Возможности

- **Текст в музыку** - Генерация музыки из описания
- **Генерация текста** - Автоматическая или ручная
- **Продолжение аудио** - Продолжение существующего аудио
- **Перерисовка аудио** - Изменение отдельных частей
- **Случайная генерация** - Случайные музыкальные сэмплы

## Требования

- **ACE-Step API сервер** - Требуется [ACE-Step V1.5](https://github.com/ace-step/ACE-Step-1.5)

## Установка

### Claude Code

Скопируйте `skills/acestep` в:

**Уровень проекта**:
```
your-project/.claude/skills/acestep/
```

**Глобальный**:
```
~/.claude/skills/acestep/
```

### OpenAI Codex

Скопируйте `skills/acestep` в:

**Уровень проекта**:
```
your-project/.codex/skills/acestep/
```

**Глобальный**:
```
~/.codex/skills/acestep/
```

## Конфигурация

Редактируйте `scripts/config.json`:

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

## Использование

### В Agent

```
Пользователь: Создай весёлую поп-песню
Пользователь: Сгенерируй джазовую музыку
```

### Командная строка

```bash
./scripts/acestep.sh health
./scripts/acestep.sh generate "Pop music with guitar"
./scripts/acestep.sh random
```

## Ссылки

- [SKILL.md](../../skills/acestep/SKILL.md) - Документация API
- [ACE-Step](https://github.com/ace-step/ACE-Step-1.5) - Проект ACE-Step
