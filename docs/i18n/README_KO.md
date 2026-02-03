# ACE-Step Skills for Any Agents

[![en](https://img.shields.io/badge/lang-English-blue.svg)](../../README.md) [![zh](https://img.shields.io/badge/lang-中文-red.svg)](README_ZH.md) [![ja](https://img.shields.io/badge/lang-日本語-green.svg)](README_JA.md) [![ko](https://img.shields.io/badge/lang-한국어-yellow.svg)](README_KO.md) [![de](https://img.shields.io/badge/lang-Deutsch-black.svg)](README_DE.md) [![fr](https://img.shields.io/badge/lang-Français-purple.svg)](README_FR.md) [![ru](https://img.shields.io/badge/lang-Русский-orange.svg)](README_RU.md)

Claude Code, OpenAI Codex 등 AI Agent용 커스텀 Skill. [ACE-Step](https://github.com/ace-step/ACE-Step-1.5) API로 음악을 생성합니다.

## 기능

- **텍스트-음악 변환** - 설명으로 음악 생성
- **가사 생성** - 자동 생성 또는 수동 지정
- **오디오 연속** - 기존 오디오에서 계속 생성
- **오디오 리페인팅** - 오디오의 특정 부분 수정
- **랜덤 생성** - 랜덤 음악 샘플 생성

## 전제 조건

- **ACE-Step API 서버** - [ACE-Step V1.5](https://github.com/ace-step/ACE-Step-1.5) API 서비스 필요

## 설치

### Claude Code

`skills/acestep` 폴더를 복사:

**프로젝트 레벨**:
```
your-project/.claude/skills/acestep/
```

**글로벌 레벨**:
```
~/.claude/skills/acestep/
```

### OpenAI Codex

`skills/acestep` 폴더를 복사:

**프로젝트 레벨**:
```
your-project/.codex/skills/acestep/
```

**글로벌 레벨**:
```
~/.codex/skills/acestep/
```

### 디렉토리 구조

```
skills/acestep/
├── SKILL.md
└── scripts/
    ├── acestep.sh
    └── config.json
```

## 설정

`scripts/config.json` 편집:

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

### 옵션

| 옵션 | 기본값 | 설명 |
|-----|--------|------|
| `api_url` | `http://127.0.0.1:8001` | API 서버 주소 |
| `api_key` | `""` | API 키 (선택) |
| `generation.thinking` | `true` | 5Hz LM 활성화 |
| `generation.audio_format` | `mp3` | 출력 형식 |

## 사용법

### Agent에서 사용

대화에서 음악 생성 언급:

```
사용자: 밝은 팝송 만들어줘
사용자: 봄에 대한 노래 만들어줘
```

### 명령줄

```bash
# API 상태 확인
./scripts/acestep.sh health

# 음악 생성
./scripts/acestep.sh generate "Pop music with guitar"

# 랜덤 생성
./scripts/acestep.sh random
```

## 출력

결과는 `acestep_output` 폴더에 저장:

```
project_root/
├── acestep_output/
│   ├── <job_id>.json
│   ├── <job_id>_1.mp3
│   └── ...
```

## GPU 메모리

| VRAM | LM 모델 | 비고 |
|------|---------|------|
| ≤6GB | 없음 | LM 비활성화 |
| 6-12GB | `acestep-5Hz-lm-0.6B` | 경량 |
| 12-16GB | `acestep-5Hz-lm-1.7B` | 고품질 |
| ≥16GB | `acestep-5Hz-lm-4B` | 최고 품질 |

## 참고

- [SKILL.md](../../skills/acestep/SKILL.md) - API 문서
- [ACE-Step](https://github.com/ace-step/ACE-Step-1.5) - ACE-Step 프로젝트
