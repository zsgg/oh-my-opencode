## Oh-My-OpenCode Fork 동기화 요약 (v3.2.1 → v3.3.2)

**동기화 일시**: 2026-02-08 KST
**비교 범위**: v3.2.1 → v3.3.2
**커밋 수**: 276개
**새 릴리즈**: v3.2.2, v3.2.3, v3.2.4, v3.3.0, v3.3.1, v3.3.2

---

## 한 줄 요약

> **promptAsync 마이그레이션, 모듈 분할(200 LOC 규칙), 모델 claude-opus-4-6/gpt-5.3-codex 업데이트, 다중 웹검색 프로바이더 지원, 그리고 대규모 안정성 수정 포함**

---

## 주요 변경사항 (버전별)

### v3.3.0 ~ v3.3.2 (메이저 마일스톤)

#### 1. promptAsync 전면 마이그레이션
- 모든 hooks, tools, background-agent, core shim에서 `session.prompt`를 `promptAsync`로 전환
- race condition 수정 및 비동기 일관성 확보
- 영향: 전체 코드베이스의 프롬프트 호출 패턴 변경

#### 2. 모듈 분할 (200 LOC 규칙)
- background-task, interactive-bash-session, tmux-subagent, call-omo-agent 등 큰 파일을 200 LOC 미만 모듈로 분할
- delegate-task에 skill-resolver 모듈 분리
- 유지보수성 및 가독성 대폭 개선

#### 3. CLI run 커맨드 대폭 확장
- `port`, `attach`, `session-id`, `on-complete`, `json` 옵션 추가
- 기본 에이전트 우선순위 (`default_run_agent`) 설정 지원
- 세션 권한 관리 기능 추가
- Question 도구 CLI 모드에서 비활성화

#### 4. MCP/웹검색 개선
- 다중 프로바이더 웹 검색 지원 (websearch provider schema)
- `~/.claude/.mcp.json` 및 `~/.claude.json` 에서 사용자 MCP 설정 읽기
- Exa MCP URL에 API 키 자동 추가, 중복 헤더 제거
- UserPromptSubmitHooks 매 프롬프트마다 발동 (#594)

#### 5. 설정 마이그레이션 안전성 강화
- config 마이그레이션을 deep copy에서 수행, 성공적 쓰기에만 rawConfig 적용 (#1660)
- 모델 마이그레이션 1회만 실행 (`_migrations` 필드)

### v3.2.2 ~ v3.2.4

#### 6. 모델 업데이트
- `claude-opus-4-5` → `claude-opus-4-6` 참조 전면 업데이트
- `gpt-5.2-codex` → `gpt-5.3-codex` 참조 전면 업데이트
- anthropic fallback 체인에서 claude-opus-4-6 우선순위 지정
- Opus 4.6용 anthropic-effort hook (effort=max 자동 주입)

#### 7. Task 시스템 성숙
- 글로벌 설정 디렉토리로 저장소 마이그레이션
- ULTRAWORK_TASK_LIST_ID / CLAUDE_CODE_TASK_LIST_ID 환경 변수 지원
- Claude Code 스타일 개별 도구로 리팩토링 (TaskCreate/Get/Update/List)
- 실시간 OpenCode API todo 동기화
- task-continuation-enforcer 추가 후 제거 (실험적)

#### 8. 에이전트 개선
- Hephaestus 자율 문제 해결 행동 개선
- explore 에이전트에 grok-code-fast-1 기본 모델 추가
- Atlas 모듈 디렉토리 재구조화 (모델 기반 라우팅)
- sisyphus-junior에 GPT-5.2 최적화 프롬프트
- plan 에이전트를 prometheus에서 분리 (aliasing 제거)
- /handoff 빌트인 명령어 추가

#### 9. 스킬 시스템 개선
- 중첩 스킬 디렉토리 지원
- disabled skills 기능 구현
- 스킬 이름 충돌의 결정적 처리
- browserProvider 기반 스킬 필터링
- github-pr-triage 스킬 추가

#### 10. Desktop 앱 호환성
- tools에서 `process.cwd()` 대신 `ctx.directory` 사용
- 오버라이드 도구(glob, grep)에서 ctx.directory 적용

---

## 안정성 수정 주요 항목

| 영역 | 수정 내용 | 이슈 |
|------|----------|------|
| Plugin 로딩 | safeCreateHook + 타임아웃 에러 경계 | #1559 |
| Windows | matcher.hooks null 체크, Bun spawn segfault 방지 | #441 |
| LSP | safety block 리셋, didChange 동기화 | #1366 |
| Background Agent | 부모 알림 직렬화, 세션 중단 처리 | #1582 |
| Compaction | 중복 제거 연결, 하드코딩 모델 제거 | #96 |
| 태스크 | 하위 태스크 연쇄 취소, lock 안전성 | #114 |
| 인증 | Desktop 서버 인증 수정, 서버 auth fallback | #1399 |
| MCP | EXA_API_KEY 인코딩, 중복 헤더 제거 | #1627 |

---

## 변경사항 통계

| 카테고리 | 개수 |
|----------|------|
| Features | ~44 |
| Fixes | ~92 |
| Refactor | ~24 |
| Tests | ~20 |
| Docs | ~12 |
| Releases | 7 |
| CLA Signatures | 19 |
| Style/Chore | ~8 |
| **총합** | **276개** |

---

## Breaking Changes

### 주의 필요

1. **promptAsync 마이그레이션**: session.prompt 사용 커스텀 hook이 있다면 promptAsync로 전환 필요
2. **모델 참조 변경**: claude-opus-4-5 → claude-opus-4-6, gpt-5.2-codex → gpt-5.3-codex
3. **Task 저장 경로 변경**: 글로벌 설정 디렉토리로 이동
4. **Plan 에이전트 분리**: prometheus aliasing 제거로 독립 에이전트로 작동

---

## 권장 조치

### 즉시 적용
- v3.3.2로 업그레이드
- 커스텀 hook에서 session.prompt → promptAsync 전환 확인
- 모델 설정에서 claude-opus-4-6 / gpt-5.3-codex 반영

### 선택적 적용
- `default_run_agent` 설정으로 CLI run 기본 에이전트 지정
- `disabled_skills` 설정으로 불필요한 스킬 비활성화
- websearch provider 설정으로 다중 검색 프로바이더 활용

---

## 이전 동기화 이력

### v3.2.0 → v3.2.1 (2026-02-01, 5개 커밋)
- 통합 Claude Tasks 시스템 (#1356)
- 백그라운드 에이전트 동시성 슬롯 누수 수정
- GitHub Copilot Gemini 모델명 수정

### v3.1.9 → v3.2.0 (2026-02-01, 48개 커밋)
- Hephaestus 에이전트 추가
- Windows 호환성 대폭 개선

### v3.1.9 → v3.1.10 (2026-01-31, 157개 커밋)
- 테스트 15배 속도 향상
- Kimi Provider, Tmux, MCP OAuth 2.1 통합

자세한 내용은 [fork.md](./fork.md) 참조.

---

**생성일**: 2026-02-08 KST
**생성자**: zsgg (Oh-My-OpenCode fork maintainer)
**도구**: sync-fork 슬래시 커맨드 with Claude Opus 4.6
