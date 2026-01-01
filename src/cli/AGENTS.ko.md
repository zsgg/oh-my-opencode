# CLI 지식 베이스 (CLI KNOWLEDGE BASE)

## 개요 (OVERVIEW)

oh-my-opencode를 위한 커맨드 라인 인터페이스(CLI)입니다. 대화형 설치 프로그램, 상태 진단 도구(doctor), 런타임 명령어 등을 제공합니다. 엔트리 포인트는 `bunx oh-my-opencode`입니다.

## 구조 (STRUCTURE)

```
cli/
├── index.ts              # Commander.js 엔트리 포인트, 서브커맨드 라우팅
├── install.ts            # 대화형 TUI 설치 프로그램
├── config-manager.ts     # 설정 감지, 파싱, 병합 (669라인)
├── types.ts              # CLI 전용 타입 정의
├── doctor/               # 상태 진단 시스템
│   ├── index.ts          # Doctor 커맨드 엔트리
│   ├── constants.ts      # 체크 카테고리, 설명
│   ├── types.ts          # 체크 결과 인터페이스
│   └── checks/           # 17개의 개별 상태 체크 항목
├── get-local-version/    # 버전 감지 유틸리티
│   ├── index.ts
│   └── formatter.ts
└── run/                  # OpenCode 세션 실행기
    ├── index.ts
    └── completion.test.ts
```

## CLI 명령어 (CLI COMMANDS)

| 명령어 (Command) | 용도 (Purpose) | 주요 파일 (Key File) |
|---------|---------|----------|
| `install` | 대화형 설정 마법사 | `install.ts` |
| `doctor` | 환경 상태 진단 체크 | `doctor/index.ts` |
| `run` | OpenCode 세션 실행 | `run/index.ts` |

## DOCTOR 체크 항목 (DOCTOR CHECKS)

`doctor/checks/`에 포함된 17개 체크 항목:

| 체크 항목 (Check) | 검증 내용 (Validates) |
|-------|-----------|
| `version.ts` | OpenCode 버전 >= 1.0.150 여부 |
| `config.ts` | opencode.json에 플러그인 등록 여부 |
| `bun.ts` | Bun 런타임 사용 가능 여부 |
| `node.ts` | Node.js 버전 호환성 |
| `git.ts` | Git 설치 여부 |
| `anthropic-auth.ts` | Claude 인증 상태 |
| `openai-auth.ts` | OpenAI 인증 상태 |
| `google-auth.ts` | Google/Gemini 인증 상태 |
| `lsp-*.ts` | 언어 서버(LSP) 사용 가능 여부 |
| `mcp-*.ts` | MCP 서버 연결성 |

## 설치 흐름 (INSTALLATION FLOW)

1. **감지 (Detection)**: 기존 `opencode.json` 또는 `opencode.jsonc` 파일을 찾습니다.
2. **TUI 질문 (TUI Prompts)**: Claude, ChatGPT, Gemini 구독 여부를 묻습니다.
3. **설정 생성 (Config Generation)**: 답변을 바탕으로 `oh-my-opencode.json` 파일을 생성합니다.
4. **플러그인 등록 (Plugin Registration)**: opencode.json의 `plugin` 배열에 추가합니다.
5. **인증 안내 (Auth Guidance)**: `opencode auth login` 실행을 위한 지침을 제공합니다.

## 설정 관리자 (CONFIG-MANAGER)

가장 큰 파일(669라인)로 다음을 처리합니다:

- **JSONC 지원**: 주석과 트레일링 콤마가 포함된 JSON을 파싱합니다.
- **다중 소스 감지**: 사용자 설정(~/.config/opencode/)과 프로젝트 설정(.opencode/)을 모두 지원합니다.
- **스키마 검증**: Zod를 사용한 설정 값 검증을 수행합니다.
- **마이그레이션**: 레거시 설정 형식을 최신 형식으로 변환합니다.
- **에러 수집**: doctor 도구에서 보여줄 파싱 에러를 취합합니다.

## DOCTOR 체크 추가 방법 (HOW TO ADD A DOCTOR CHECK)

1. `src/cli/doctor/checks/my-check.ts` 파일 생성:
   ```typescript
   import type { DoctorCheck } from "../types"
   
   export const myCheck: DoctorCheck = {
     name: "my-check",
     category: "environment",
     check: async () => {
       // { status: "pass" | "warn" | "fail", message: string } 반환
     }
   }
   ```
2. `src/cli/doctor/checks/index.ts`에 추가
3. 새로운 카테고리인 경우 `constants.ts` 업데이트

## 안티 패턴 - CLI (ANTI-PATTERNS - CLI)

- **비-TTY 환경에서의 블로킹 질문**: TUI를 실행하기 전에 `process.stdout.isTTY`를 확인하세요.
- **경로 직접 입력(Hardcoded paths)**: 설정 경로 확인을 위해 공용 유틸리티를 사용하세요.
- **JSONC 무시**: 사용자 설정 파일에는 주석이 포함될 수 있음을 고려하세요.
- **침묵하는 실패**: Doctor 체크는 항상 명확한 상태와 메시지를 반환해야 합니다.
