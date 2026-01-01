# 프로젝트 지식 베이스

**생성일:** 2026-01-02T00:10:00+09:00
**커밋:** b0c39e2
**브랜치:** dev

## 개요 (OVERVIEW)

Claude Code/AmpCode 기능을 구현하는 OpenCode 플러그인입니다. 멀티 모델 에이전트 오케스트레이션 (GPT-5.2, Claude, Gemini, Grok), LSP 도구 (11개), AST-Grep 검색, MCP 통합 (context7, websearch_exa, grep_app)을 지원합니다. OpenCode를 위한 "oh-my-zsh"와 같은 역할을 합니다.

## 구조 (STRUCTURE)

```
oh-my-opencode/
├── src/
│   ├── agents/        # AI 에이전트 (7개): Sisyphus, oracle, librarian, explore, frontend, document-writer, multimodal-looker
│   ├── hooks/         # 22개 라이프사이클 훅 - src/hooks/AGENTS.md 참조
│   ├── tools/         # LSP, AST-Grep, Grep, Glob 등 - src/tools/AGENTS.md 참조
│   ├── mcp/           # MCP 서버: context7, websearch_exa, grep_app
│   ├── features/      # Claude Code 호환성 + 핵심 기능 - src/features/AGENTS.md 참조
│   ├── config/        # Zod 스키마, TypeScript 타입
│   ├── auth/          # Google Antigravity OAuth - src/auth/AGENTS.md 참조
│   ├── shared/        # 유틸리티: deep-merge, pattern-matcher, logger 등 - src/shared/AGENTS.md 참조
│   ├── cli/           # CLI 설치 프로그램, doctor, run - src/cli/AGENTS.md 참조
│   └── index.ts       # 메인 플러그인 엔트리 (OhMyOpenCodePlugin)
├── script/            # build-schema.ts, publish.ts, generate-changelog.ts
├── assets/            # JSON 스키마
└── dist/              # 빌드 출력 (ESM + .d.ts)
```

## 주요 위치 (WHERE TO LOOK)

| 작업 (Task) | 위치 (Location) | 비고 (Notes) |
|------|----------|-------|
| 에이전트 추가 | `src/agents/` | .ts 파일 생성, index.ts의 builtinAgents에 추가, types.ts 업데이트 |
| 훅(Hook) 추가 | `src/hooks/` | createXXXHook()으로 디렉토리 생성, index.ts에서 export |
| 도구(Tool) 추가 | `src/tools/` | index/types/constants/tools.ts 포함 디렉토리 생성, builtinTools에 추가 |
| MCP 추가 | `src/mcp/` | 설정 생성, index.ts 및 types.ts에 추가 |
| 스킬(Skill) 추가 | `src/features/builtin-skills/` | SKILL.md 포함 스킬 디렉토리 생성 |
| LSP 동작 | `src/tools/lsp/` | client.ts (연결), tools.ts (핸들러) |
| AST-Grep | `src/tools/ast-grep/` | @ast-grep/napi 바인딩을 위한 napi.ts |
| Google OAuth | `src/auth/antigravity/` | Google/Gemini 모델을 위한 OAuth 플러그인 |
| 설정 스키마 | `src/config/schema.ts` | Zod 스키마, 변경 후 `bun run build:schema` 실행 |
| Claude Code 호환성 | `src/features/claude-code-*-loader/` | 명령어, 스킬, 에이전트, MCP 로더 |
| 백그라운드 에이전트 | `src/features/background-agent/` | 작업 관리를 위한 manager.ts |
| 스킬 MCP | `src/features/skill-mcp-manager/` | 스킬에 내장된 MCP 서버 |
| 대화형 터미널 | `src/tools/interactive-bash/` | tmux 세션 관리 |
| CLI 설치 프로그램 | `src/cli/install.ts` | 대화형 TUI 설치 |
| Doctor 체크 | `src/cli/doctor/checks/` | 환경 헬스 체크 |
| 공통 유틸리티 | `src/shared/` | 횡단 관심사 유틸리티 |
| 슬래시 명령어 | `src/hooks/auto-slash-command/` | `/command` 패턴 자동 감지 및 실행 |
| Ralph 루프 | `src/hooks/ralph-loop/` | 완료될 때까지의 자기 참조 개발 루프 |

## 관례 (CONVENTIONS)

- **패키지 매니저**: Bun 전용 (`bun run`, `bun build`, `bunx`)
- **타입**: bun-types 사용 (node @types 아님)
- **빌드**: 이중 출력 - `bun build` (ESM) + `tsc --emitDeclarationOnly`
- **Exports**: 배럴 패턴 (Barrel pattern) - index.ts에서 `export * from "./module"` 사용
- **디렉토리 명명**: kebab-case 사용 (`ast-grep/`, `claude-code-hooks/`)
- **도구 구조**: index.ts, types.ts, constants.ts, tools.ts, utils.ts
- **훅 패턴**: 이벤트 핸들러를 반환하는 `createXXXHook(input: PluginInput)` 함수 관례
- **테스트 스타일**: BDD 주석 `#given`, `#when`, `#then` 사용 (AAA와 동일)

## 안티 패턴 (ANTI-PATTERNS)

- **npm/yarn**: Bun만 사용할 것
- **@types/node**: bun-types를 사용할 것
- **Bash 파일 작업**: 코드 내에서 파일 생성을 위해 mkdir/touch/rm/cp/mv를 절대 사용하지 말 것
- **직접 bun publish**: GitHub Actions workflow_dispatch를 통해서만 수행 (OIDC 프로버넌스)
- **로컬 버전 범프**: 버전은 CI 워크플로우에서 관리됨
- **2024년**: 코드/프롬프트에서 2024년을 절대 사용하지 말 것 (현재 연도 사용)
- **섣부른 완료 표시**: 검증 없이 작업을 완료로 표시하지 말 것
- **과도한 탐색**: 충분한 문맥을 찾았다면 탐색을 중단할 것
- **높은 Temperature**: 코드 관련 에이전트에는 >0.3을 사용하지 말 것
- **광범위한 도구 접근**: 제한 없는 접근보다는 명시적인 `include`를 선호할 것
- **순차적 에이전트 호출**: 병렬 실행을 위해 `background_task`를 사용할 것
- **무거운 PreToolUse 로직**: 모든 도구 호출을 느리게 함
- **복잡한 작업의 직접 계획**: 대신 계획 에이전트 (Prometheus)를 생성할 것

## 고유 스타일 (UNIQUE STYLES)

- **플랫폼**: 유니온 타입 `"darwin" | "linux" | "win32" | "unsupported"`
- **선택적 속성**: 인터페이스 프로퍼티에 `?` 광범위하게 사용
- **유연한 객체**: 동적 설정을 위해 `Record<string, unknown>` 사용
- **에러 핸들링**: async/await와 함께 일관된 try/catch 사용
- **에이전트 도구**: `tools: { include: [...] }` 또는 `tools: { exclude: [...] }`
- **Temperature**: 일관성을 위해 대부분의 에이전트는 `0.1` 사용
- **훅 명명**: `createXXXHook` 함수 관례
- **팩토리 패턴**: `createXXX()` 함수를 통해 컴포넌트 생성

## 에이전트 모델 (AGENT MODELS)

| 에이전트 (Agent) | 모델 (Model) | 용도 (Purpose) |
|-------|-------|---------|
| Sisyphus | anthropic/claude-opus-4-5 | 기본 오케스트레이터 |
| oracle | openai/gpt-5.2 | 전략적 자문, 코드 리뷰 |
| librarian | anthropic/claude-sonnet-4-5 | 멀티 레포 분석, 문서 |
| explore | opencode/grok-code | 빠른 코드베이스 탐색 |
| frontend-ui-ux-engineer | google/gemini-3-pro-preview | UI 생성 |
| document-writer | google/gemini-3-pro-preview | 기술 문서 |
| multimodal-looker | google/gemini-3-flash | PDF/이미지 분석 |

## 명령어 (COMMANDS)

```bash
bun run typecheck      # 타입 체크
bun run build          # ESM + 선언 파일 + 스키마
bun run rebuild        # Clean + Build
bun run build:schema   # 스키마 전용
bun test               # 테스트 실행
```

## 배포 (DEPLOYMENT)

**GitHub Actions workflow_dispatch 전용**

1. package.json 버전을 로컬에서 절대 수정하지 마세요.
2. 변경 사항을 커밋하고 푸시하세요.
3. `publish` 워크플로우를 트리거하세요: `gh workflow run publish -f bump=patch`

**중요**: 절대로 직접 `bun publish` 하지 마세요. 로컬에서 버전을 올리지 마세요.

## CI 파이프라인 (CI PIPELINE)

- **ci.yml**: 병렬 테스트/타입체크, 빌드 검증, master 브랜치 스키마 자동 커밋, 롤링 `next` 드래프트 릴리스
- **publish.yml**: 수동 workflow_dispatch, 버전 범프, 변경 이력(changelog), OIDC npm 배포
- **sisyphus-agent.yml**: `@sisyphus-dev-ai` 멘션을 통한 자동화된 이슈 처리를 위한 CI용 에이전트

## 복잡성 핫스팟 (COMPLEXITY HOTSPOTS)

| 파일 (File) | 라인 수 (Lines) | 설명 (Description) |
|------|-------|-------------|
| `src/index.ts` | 723 | 메인 플러그인 오케스트레이션, 모든 훅/도구 초기화 |
| `src/cli/config-manager.ts` | 669 | JSONC 파싱, 환경 감지, 설치 |
| `src/auth/antigravity/fetch.ts` | 621 | 토큰 갱신, URL 재작성, 엔드포인트 폴백 |
| `src/tools/lsp/client.ts` | 611 | LSP 프로토콜, stdin/stdout 버퍼링, JSON-RPC |
| `src/auth/antigravity/response.ts` | 598 | 응답 변환, 스트리밍 |
| `src/auth/antigravity/thinking.ts` | 571 | Thinking 블록 추출/변환 |
| `src/hooks/anthropic-context-window-limit-recovery/executor.ts` | 554 | 세션 압축, 다단계 복구 파이프라인 |
| `src/agents/sisyphus.ts` | 504 | 오케스트레이터 프롬프트, 위임 전략 |

## 참고 사항 (NOTES)

- **테스트**: Bun 기본 테스트 (`bun test`), BDD 스타일 `#given/#when/#then`, 360개 이상의 테스트
- **OpenCode**: 1.0.150 이상 버전 필요
- **다국어 문서**: README.md (EN), README.ko.md (KO), README.ja.md (JA), README.zh-cn.md (ZH-CN)
- **설정**: `~/.config/opencode/oh-my-opencode.json` (사용자) 또는 `.opencode/oh-my-opencode.json` (프로젝트)
- **신뢰할 수 있는 의존성**: @ast-grep/cli, @ast-grep/napi, @code-yeongyu/comment-checker
- **JSONC 지원**: 설정 파일에서 주석(`// 주석`, `/* 블록 */`) 및 트레일링 콤마 지원
- **Claude Code 호환성**: settings.json 훅, 명령어, 스킬, 에이전트, MCP에 대한 완전한 호환 레이어
- **스킬 MCP**: 스킬은 YAML 프론트매터에 MCP 서버 설정을 포함할 수 있음
