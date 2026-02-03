# ACE-Step Skills for Any Agents

[![en](https://img.shields.io/badge/lang-English-blue.svg)](../../README.md) [![zh](https://img.shields.io/badge/lang-中文-red.svg)](README_ZH.md) [![ja](https://img.shields.io/badge/lang-日本語-green.svg)](README_JA.md) [![ko](https://img.shields.io/badge/lang-한국어-yellow.svg)](README_KO.md) [![de](https://img.shields.io/badge/lang-Deutsch-black.svg)](README_DE.md) [![fr](https://img.shields.io/badge/lang-Français-purple.svg)](README_FR.md) [![ru](https://img.shields.io/badge/lang-Русский-orange.svg)](README_RU.md)

适用于 Claude Code、OpenAI Codex 等 AI Agent 的自定义 Skill，通过 [ACE-Step](https://github.com/ace-step/ACE-Step-1.5) API 生成音乐。

## 功能特性

- **文本生成音乐** - 通过描述生成音乐
- **歌词生成** - 自动生成或手动指定歌词
- **音频续写** - 基于现有音频继续生成
- **音频重绘** - 修改音频的特定部分
- **随机生成** - 随机生成音乐样本

## 前置要求

- **ACE-Step API 服务器** - 需要运行 [ACE-Step V1.5](https://github.com/ace-step/ACE-Step-1.5) API 服务

## 安装配置

### Claude Code

将 `skills/acestep` 文件夹复制到：

**项目级别**（仅当前项目可用）：
```
your-project/.claude/skills/acestep/
```

**全局级别**（所有项目可用）：
```
~/.claude/skills/acestep/
```

### OpenAI Codex

将 `skills/acestep` 文件夹复制到：

**项目级别**：
```
your-project/.codex/skills/acestep/
```

**全局级别**：
```
~/.codex/skills/acestep/
```

### 目录结构

```
skills/acestep/
├── SKILL.md
└── scripts/
    ├── acestep.sh
    └── config.json
```

## 配置说明

编辑 `scripts/config.json` 配置 API 连接和默认参数：

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

### 配置项

| 配置项 | 默认值 | 说明 |
|--------|--------|------|
| `api_url` | `http://127.0.0.1:8001` | API 服务器地址 |
| `api_key` | `""` | API 认证密钥（可选） |
| `generation.thinking` | `true` | 启用 5Hz LM（高质量模式） |
| `generation.use_format` | `true` | 使用 LM 增强描述/歌词 |
| `generation.audio_format` | `mp3` | 输出格式 (mp3/wav/flac) |
| `generation.vocal_language` | `en` | 人声语言 |

## 使用方式

### 在 Agent 中使用

安装后，在对话中提及音乐生成相关需求：

```
用户: 帮我生成一首欢快的流行音乐
用户: 创作一首关于春天的歌曲
用户: 生成一段爵士风格的背景音乐
```

### 命令行使用

```bash
# 检查 API 状态
./scripts/acestep.sh health

# 生成音乐 - Caption 模式
./scripts/acestep.sh generate "Pop music with guitar"

# 生成音乐 - Simple 模式
./scripts/acestep.sh generate -d "一首关于春天的欢快歌曲"

# 带歌词生成
./scripts/acestep.sh generate -c "Lyrical pop" -l "[Verse] Hello world"

# 随机生成
./scripts/acestep.sh random

# 查询任务状态
./scripts/acestep.sh status <job_id>
```

### 生成选项

| 选项 | 说明 |
|------|------|
| `-c, --caption` | 音乐风格描述 |
| `-d, --description` | 简单描述，LM 自动生成 |
| `-l, --lyrics` | 歌词内容 |
| `--no-thinking` | 禁用 thinking 模式 |
| `--steps` | 扩散步数 |
| `--guidance` | 引导强度 |
| `--duration` | 音频时长（秒） |
| `--bpm` | 节拍速度 |

## 输出文件

结果保存到 `acestep_output` 文件夹：

```
project_root/
├── acestep_output/
│   ├── <job_id>.json      # 任务结果
│   ├── <job_id>_1.mp3     # 音频文件
│   └── ...
```

## GPU 显存配置

| 显存 | LM 模型 | 说明 |
|------|---------|------|
| ≤6GB | 无（仅 DiT） | LM 禁用 |
| 6-12GB | `acestep-5Hz-lm-0.6B` | 轻量级 |
| 12-16GB | `acestep-5Hz-lm-1.7B` | 更好质量 |
| ≥16GB | `acestep-5Hz-lm-4B` | 最佳质量 |

## 参考文档

- [SKILL.md](../../skills/acestep/SKILL.md) - 完整 API 文档
- [ACE-Step](https://github.com/ace-step/ACE-Step-1.5) - ACE-Step 项目
