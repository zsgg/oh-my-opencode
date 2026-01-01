# 도구 지식 베이스 (TOOLS KNOWLEDGE BASE)

## 개요 (OVERVIEW)

에이전트 기능을 확장하는 사용자 정의 도구들입니다. LSP 통합 (11개 도구), AST 인식 코드 검색/교체, 타임아웃이 적용된 파일 작업, 백그라운드 작업 관리 등을 지원합니다.

## 구조 (STRUCTURE)

```
tools/
├── ast-grep/           # AST 인식 코드 검색/교체 (25개 언어 지원)
│   ├── cli.ts          # @ast-grep/cli 서브프로세스
│   ├── napi.ts         # @ast-grep/napi 네이티브 바인딩 (권장)
│   ├── constants.ts, types.ts, tools.ts, utils.ts
├── background-task/    # 비동기 에이전트 작업 관리
├── call-omo-agent/     # explore/librarian 에이전트 생성
├── glob/               # 파일 패턴 매칭 (타임아웃 안전)
├── grep/               # 내용 검색 (타임아웃 안전)
├── interactive-bash/   # Tmux 세션 관리
├── look-at/            # 멀티모달 분석 (PDF, 이미지)
├── lsp/                # 11개의 LSP 도구
│   ├── client.ts       # LSP 연결 라이프사이클
│   ├── config.ts       # 서버 설정
│   ├── tools.ts        # 도구 구현
│   └── types.ts
├── session-manager/    # OpenCode 세션 파일 관리
│   ├── constants.ts    # 저장 경로, 설명
│   ├── types.ts        # 세션 데이터 인터페이스
│   ├── storage.ts      # 파일 I/O 작업
│   ├── utils.ts        # 포맷팅, 필터링
│   └── tools.ts        # 도구 구현
├── skill/              # 스킬 로딩 및 실행
├── skill-mcp/          # 스킬 내장 MCP 호출
├── slashcommand/       # 슬래시 명령어 실행
└── index.ts            # builtinTools 내보내기(export)
```

## 도구 카테고리 (TOOL CATEGORIES)

| 카테고리 (Category) | 도구 (Tools) | 용도 (Purpose) |
|----------|-------|---------|
| LSP | lsp_hover, lsp_goto_definition, lsp_find_references, lsp_document_symbols, lsp_workspace_symbols, lsp_diagnostics, lsp_servers, lsp_prepare_rename, lsp_rename, lsp_code_actions, lsp_code_action_resolve | IDE와 같은 코드 인텔리전스 제공 |
| AST | ast_grep_search, ast_grep_replace | 패턴 기반 코드 검색 및 교체 |
| 파일 검색 (File Search) | grep, glob | 내용 및 파일 패턴 매칭 |
| 세션 (Session) | session_list, session_read, session_search, session_info | OpenCode 세션 파일 관리 |
| 백그라운드 (Background) | background_task, background_output, background_cancel | 비동기 에이전트 오케스트레이션 |
| 멀티모달 (Multimodal) | look_at | Gemini를 통한 PDF/이미지 분석 |
| 터미널 (Terminal) | interactive_bash | Tmux 세션 제어 |
| 명령어 (Commands) | slashcommand | 슬래시 명령어 실행 |
| 스킬 (Skills) | skill, skill_mcp | 스킬 로드 및 스킬 내장 MCP 호출 |
| 에이전트 (Agents) | call_omo_agent | explore/librarian 생성 |

## 도구 추가 방법 (HOW TO ADD A TOOL)

1. 디렉토리 생성: `src/tools/my-tool/`
2. 파일 생성:
   - `constants.ts`: `TOOL_NAME`, `TOOL_DESCRIPTION` 정의
   - `types.ts`: 파라미터 및 결과 인터페이스 정의
   - `tools.ts`: 도구 구현 (OpenCode 도구 객체 반환)
   - `index.ts`: 배럴 내보내기 (Barrel export)
   - `utils.ts`: 헬퍼 함수 (선택 사항)
3. `src/tools/index.ts`의 `builtinTools`에 추가

## LSP 상세 (LSP SPECIFICS)

- **클라이언트 라이프사이클**: 첫 사용 시 지연 초기화(Lazy init), 유휴 상태 시 자동 종료
- **설정 우선순위**: opencode.json > oh-my-opencode.json > 기본값(defaults)
- **지원 서버**: typescript-language-server, pylsp, gopls, rust-analyzer 등
- **사용자 정의 서버**: oh-my-opencode.json의 `lsp` 설정을 통해 추가 가능

## AST-GREP 상세 (AST-GREP SPECIFICS)

- **메타 변수**: `$VAR` (단일 노드), `$$$` (복수 노드)
- **언어**: 25개 언어 지원 (typescript, tsx, python, rust, go 등)
- **바인딩**: @ast-grep/napi (네이티브)를 선호하며, 실패 시 @ast-grep/cli로 폴백
- **패턴 제약**: 유효한 AST 형태여야 함 (예: `export async function $NAME($$$) { $$$ }`, 코드 파편은 지원하지 않음)

## 안티 패턴 - 도구 (ANTI-PATTERNS - TOOLS)

- **타임아웃 누락**: 파일 작업 시 항상 타임아웃을 사용하세요 (기본 60초)
- **메인 스레드 차단**: async/await를 사용하고, 동기(sync) 파일 작업은 지양하세요
- **LSP 에러 무시**: 서버를 찾을 수 없거나 충돌이 발생한 경우를 우아하게 처리하세요
- **ast-grep에 원시 서브프로세스 사용**: 성능을 위해 napi 바인딩을 선호하세요
