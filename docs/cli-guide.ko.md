# Oh-My-OpenCode CLI 가이드

이 문서는 Oh-My-OpenCode CLI 도구 사용법에 대한 종합 가이드를 제공함.

## 1. 개요

Oh-My-OpenCode는 `bunx oh-my-opencode` 명령어를 통해 접근 가능한 CLI 도구를 제공함. CLI는 플러그인 설치, 환경 진단, 세션 실행 등 다양한 기능을 지원함.

```bash
# 기본 실행 (도움말 표시)
bunx oh-my-opencode

# 또는 npx로 실행
npx oh-my-opencode
```

---

## 2. 사용 가능한 명령어

| 명령어 | 설명 |
|---------|-------------|
| `install` | 대화형 설정 마법사 |
| `doctor` | 환경 진단 및 상태 확인 |
| `run` | OpenCode 세션 실행기 |
| `auth` | Google Antigravity 인증 관리 |
| `version` | 버전 정보 표시 |

---

## 3. `install` - 대화형 설정 마법사

Oh-My-OpenCode 초기 설정을 위한 대화형 설치 도구. `@clack/prompts` 기반의 아름다운 TUI(Text User Interface)를 제공함.

### 사용법

```bash
bunx oh-my-opencode install
```

### 설치 과정

1. **프로바이더 선택**: Claude, ChatGPT, Gemini 중에서 AI 프로바이더를 선택함.
2. **API 키 입력**: 선택한 프로바이더의 API 키를 입력함.
3. **설정 파일 생성**: `opencode.json` 또는 `oh-my-opencode.json` 파일을 생성함.
4. **플러그인 등록**: OpenCode 설정에 oh-my-opencode 플러그인을 자동으로 등록함.

### 옵션

| 옵션 | 설명 |
|--------|-------------|
| `--no-tui` | TUI 없이 비대화형 모드로 실행 (CI/CD 환경용) |
| `--verbose` | 상세한 로그 표시 |

---

## 4. `doctor` - 환경 진단

Oh-My-OpenCode가 올바르게 작동하는지 환경을 진단함. 17개 이상의 상태 확인을 수행함.

### 사용법

```bash
bunx oh-my-opencode doctor
```

### 진단 카테고리

| 카테고리 | 확인 항목 |
|----------|-------------|
| **Installation** | OpenCode 버전 (>= 1.0.150), 플러그인 등록 상태 |
| **Configuration** | 설정 파일 유효성, JSONC 파싱 |
| **Authentication** | Anthropic, OpenAI, Google API 키 유효성 |
| **Dependencies** | Bun, Node.js, Git 설치 상태 |
| **Tools** | LSP 서버 상태, MCP 서버 상태 |
| **Updates** | 최신 버전 확인 |

### 옵션

| 옵션 | 설명 |
|--------|-------------|
| `--category <name>` | 특정 카테고리만 확인 (예: `--category authentication`) |
| `--json` | JSON 형식으로 결과 출력 |
| `--verbose` | 상세한 정보 포함 |

### 출력 예시

```
oh-my-opencode doctor

┌──────────────────────────────────────────────────┐
│  Oh-My-OpenCode Doctor                           │
└──────────────────────────────────────────────────┘

Installation
  ✓ OpenCode version: 1.0.155 (>= 1.0.150)
  ✓ Plugin registered in opencode.json

Configuration
  ✓ oh-my-opencode.json is valid
  ⚠ categories.visual-engineering: using default model

Authentication
  ✓ Anthropic API key configured
  ✓ OpenAI API key configured
  ✗ Google API key not found

Dependencies
  ✓ Bun 1.2.5 installed
  ✓ Node.js 22.0.0 installed
  ✓ Git 2.45.0 installed

Summary: 10 passed, 1 warning, 1 failed
```

---

## 5. `run` - OpenCode 세션 실행기

OpenCode 세션을 실행하고 작업 완료를 모니터링함.

### 사용법

```bash
bunx oh-my-opencode run [prompt]
```

### 옵션

| 옵션 | 설명 |
|--------|-------------|
| `--enforce-completion` | 모든 TODO가 완료될 때까지 세션 유지 |
| `--timeout <seconds>` | 최대 실행 시간 설정 |

---

## 6. `auth` - 인증 관리

Google Antigravity OAuth 인증을 관리함. Gemini 모델 사용에 필요함.

### 사용법

```bash
# 로그인
bunx oh-my-opencode auth login

# 로그아웃
bunx oh-my-opencode auth logout

# 현재 상태 확인
bunx oh-my-opencode auth status
```

---

## 7. 설정 파일

CLI는 다음 위치에서 설정 파일을 검색함 (우선순위 순서):

1. **프로젝트 레벨**: `.opencode/oh-my-opencode.json`
2. **사용자 레벨**: `~/.config/opencode/oh-my-opencode.json`

### JSONC 지원

설정 파일은 **JSONC (JSON with Comments)** 형식을 지원함. 주석과 trailing comma를 사용할 수 있음.

```jsonc
{
  // 에이전트 설정
  "sisyphus_agent": {
    "disabled": false,
    "planner_enabled": true,
  },

  /* Category 커스터마이징 */
  "categories": {
    "visual-engineering": {
      "model": "google/gemini-3-pro-preview",
    },
  },
}
```

---

## 8. 문제 해결

### "OpenCode version too old" 오류

```bash
# OpenCode 업데이트
npm install -g opencode@latest
# 또는
bun install -g opencode@latest
```

### "Plugin not registered" 오류

```bash
# 플러그인 재설치
bunx oh-my-opencode install
```

### Doctor 확인 실패

```bash
# 상세한 정보로 진단
bunx oh-my-opencode doctor --verbose

# 특정 카테고리만 확인
bunx oh-my-opencode doctor --category authentication
```

---

## 9. 비대화형 모드

CI/CD 환경에서는 `--no-tui` 옵션을 사용함.

```bash
# CI 환경에서 doctor 실행
bunx oh-my-opencode doctor --no-tui --json

# 결과를 파일로 저장
bunx oh-my-opencode doctor --json > doctor-report.json
```

---

## 10. 개발자 정보

### CLI 구조

```
src/cli/
├── index.ts              # Commander.js 기반 메인 진입점
├── install.ts            # @clack/prompts 기반 TUI 설치기
├── config-manager.ts     # JSONC 파싱, 다중 소스 설정 관리
├── doctor/               # 상태 확인 시스템
│   ├── index.ts          # Doctor 명령어 진입점
│   └── checks/           # 17개 이상의 개별 확인 모듈
├── run/                  # 세션 실행기
└── commands/auth.ts      # 인증 관리
```

### 새로운 Doctor 확인 추가하기

1. `src/cli/doctor/checks/my-check.ts` 생성:

```typescript
import type { DoctorCheck } from "../types"

export const myCheck: DoctorCheck = {
  name: "my-check",
  category: "environment",
  check: async () => {
    // 확인 로직
    const isOk = await someValidation()

    return {
      status: isOk ? "pass" : "fail",
      message: isOk ? "Everything looks good" : "Something is wrong",
    }
  },
}
```

2. `src/cli/doctor/checks/index.ts`에 등록:

```typescript
export { myCheck } from "./my-check"
```
