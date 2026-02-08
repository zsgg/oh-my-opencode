## Upstream Changelog

**동기화 일시**: 2026-02-08 KST
**비교 범위**: HEAD (5fe305e6) → upstream/dev (34e5eddb)
**총 커밋 수**: 276개
**버전 범위**: v3.2.1 → v3.3.2
**새 태그**: v3.2.2, v3.2.3, v3.2.4, v3.3.0, v3.3.1, v3.3.2

---

## 변경사항 요약

### Features

- [1324fee3] CLI run 커맨드 및 백그라운드 에이전트에 세션 권한 관리 추가
- [e343e625] CLI run 커맨드에 port, attach, session-id, on-complete, json 옵션 확장
- [a7a847eb] CLI run 커맨드에 기본 에이전트 우선순위 구현
- [961ce194] CLI run 모드에서 Question 도구 비활성화
- [9c2c8b4d] default_run_agent 설정 스키마 옵션 추가
- [a5489718] /handoff 빌트인 명령어 추가 (프로그래밍 컨텍스트 합성)
- [4c4e1687] auto-slash-command에 빌트인 명령어 지원 추가
- [dea13a37] Claude Code 스펙 정렬 실험적 task 시스템 추가 (#1415)
- [92639ca3] Task를 Claude Code 스타일 개별 도구로 리팩토링
- [b71fe66a] TaskUpdate 도구에 additive blocks/blockedBy 및 metadata merge 구현
- [0ea92124] OpenCode API를 통한 실시간 단일 태스크 todo 동기화
- [6717349e] CLAUDE_CODE_TASK_LIST_ID 환경 변수 지원 추가
- [bf31e728] 태스크 저장소를 글로벌 설정 디렉토리로 마이그레이션 (ULTRAWORK_TASK_LIST_ID 지원)
- [7b820492] 글로벌 저장소용 task config 스키마 업데이트
- [abc448b1] new_task_system_enabled일 때 todowrite/todoread 도구 비활성화
- [23bca2b4] background_output task_id 제목 resolve 기능
- [83a05630] delegate-task에 skill-resolver 모듈 추가
- [d7679e14] plan 에이전트 프롬프트에 실행 가능한 TODO 리스트 템플릿 추가
- [6febebc1] Opus 4.6용 anthropic-effort hook 추가 (effort=max 주입)
- [ec520e62] anthropic-effort hook을 플러그인 라이프사이클에 등록
- [551dbc95] task-continuation-enforcer를 플러그인 라이프사이클에 등록
- [f4a9d0c3] TDD 기반 task-continuation-enforcer 구현
- [7e5a657f] gpt-5.2-codex 및 claude-opus-4-5용 모델 버전 마이그레이션 추가
- [11d0005e] anthropic fallback 체인에서 claude-opus-4-6 우선순위 지정
- [b8f15aff] hephaestus 가용성 체크에 특정 모델 대신 프로바이더 연결성 확인
- [ca317963] 기본 포트 사용 중일 때 자동 포트 선택
- [d099b025] look_at에 clipboard/붙여넣기 이미지 지원 (image_data 파라미터)
- [00f57686] 다중 프로바이더 웹 검색 지원 추가
- [4840864e] 웹 검색 프로바이더 스키마 추가
- [d80adac3] explore 에이전트에 grok-code-fast-1 기본 모델 추가
- [b62519b4] Atlas 모델 해상도에 uiSelectedModel 반영 (#1410)
- [62e16874] 에이전트 fallback 및 선제적 compaction 복원 추가
- [89278473] github-pr-triage 스킬 및 github-issue-triage 업데이트
- [8e17819f] triage 스킬에 스트리밍 모드 및 todo 추적 추가
- [ddf878e5] 기존 파일 덮어쓰기 방지 write-existing-file-guard hook 추가
- [64b29ea0] 중첩 스킬 디렉토리 지원
- [bf87bf47] sisyphus-junior에 GPT-5.2 최적화 프롬프트 추가
- [671e320b] 에이전트에 useTaskSystem 플래그 추가 (조건부 todo/task 규율 프롬프트)
- [1ae7d7d6] plugin_load_timeout_ms 및 safe_hook_creation 실험적 플래그 추가
- [f9742ddf] 에러 안전 hook 생성 유틸리티(safeCreateHook) 추가
- [aa447765] git diff 통계 유틸리티 및 인프라 개선
- [7abefcca] Anthropic assistant 메시지 prefill 에러 자동 복구
- [3a823eb2] 작업 전 태스크 등록 강조 메시지 강화
- [1a0cc424] todowrite-disabler 에러 메시지에 실행 가능한 워크플로우 안내 추가

### Fixes

- [441fda91] deep copy에서 config 마이그레이션, 성공적 파일 쓰기에만 rawConfig 적용 (#1660)
- [3d4ed912] look-at에서 race condition 수정 위해 동기 프롬프트 사용 (#1620 회귀)
- [0cbbdd56] CLI 부모 커맨드에서 positional options 활성화 (passThroughOptions)
- [ac6e7d00] CLI 관리 사용자 MCP 설정 위해 ~/.claude/.mcp.json도 읽기
- [1760367a] ~/.claude.json에서 사용자 수준 MCP 설정 읽기 (#814)
- [d1659152] 첫 번째 프롬프트뿐 아니라 매 프롬프트마다 UserPromptSubmitHooks 발동 (#594)
- [b8221a88] promptWithModelSuggestionRetry를 promptAsync로 전환
- [46e02b94] 모든 hooks에서 session.prompt를 promptAsync로 전환
- [5f21ddf4] background-agent에서 session.prompt를 promptAsync로 전환
- [108e860d] core 호환성 shim을 promptAsync로 전환
- [55dc6484] delegate-task 및 call-omo-agent에서 promptAsync로 전환
- [fad7354b] look-at의 isJsonParseError 임시 조치 제거 (근본 원인 수정됨)
- [747edcb6] browserProvider로 발견된 스킬 필터링 (#1563)
- [0eddd28a] plan 유사 에이전트에 ultrawork 주입 건너뛰기 (#1501)
- [36e54acc] task_system 백업 쓰기 중단 (#1561)
- [844ac26e] prompt-too-long 에러에 대한 compaction recovery에 중복 제거 연결 (#96)
- [9040383d] 부모 세션 삭제 시 하위 태스크 연쇄 취소 (#114)
- [f94ae203] 잘린 결과가 maxLength 한도 내에 유지되도록 보장
- [476f154e] Desktop 앱 지원을 위해 tools에서 ctx.directory 사용 (process.cwd() 대신)
- [f980e256] boulder continuation이 /stop-continuation 가드 준수
- [b9869723] 플랫폼 인식 바이너리 감지 (Windows: where, Unix: which)
- [66419918] _migrations 필드에 이력 저장하여 모델 마이그레이션 1회만 실행
- [eb5cc873] 도구 이름 공백 제거하여 잘못된 도구 호출 방지
- [847d9941] Prometheus가 .sisyphus/*.md 계획 파일 덮어쓰기 허용
- [1c0b41aa] 사용자 설정 에이전트 모델이 시스템 기본값보다 우선
- [cb2169f3] anthropic-effort hook에서 undefined modelID 방어 처리
- [d8b29da1] category-skill-reminder에 사용자 우선순위로 가용 스킬 동적 포함
- [60bbeb73] compaction hooks에서 하드코딩된 Claude 모델 제거
- [3a0d7e8d] sisyphus-junior가 UI 선택 모델 상속하지 않도록 수정
- [aec56241] atlas 반복 프롬프트 실패 시 continuation 재시도 루프 중단
- [53537a9a] Zod 스키마를 실제 구현과 동기화
- [3c32ae04] disabled_tools 필터링 시행
- [bc782ca4] 패턴 매처에서 정규식 특수 문자 이스케이프
- [b88a8681] plan 에이전트가 명시적 미설정 시 prometheus 모델 설정 상속
- [7f4338b6] sync continuation에서 variant 보존하여 thinking budget 유지
- [d769b958] sync 태스크에서 polling 대신 blocking prompt 사용
- [72cf9087] plan 에이전트를 prometheus에서 분리 - aliasing 제거
- [f035be84] orchestrator delegation 프롬프트에 커스텀 에이전트 포함 (#1623)
- [a85da593] URL 쿼리 파라미터에 추가 전 EXA_API_KEY 인코딩
- [a0636408] subagent_type 경로에서 사용자 에이전트 모델 설정 resolve (#1357)
- [f6fc30ad] task 도구의 load_skills 파라미터에 기본값 추가 (#1493)
- [f1fcc26a] background-agent 부모 알림 직렬화 (#1582)
- [09999587] env var 설정 시 Exa MCP URL에 EXA_API_KEY 추가 (#1627)
- [06611a76] 중복 x-api-key 헤더 제거 (#1627)
- [44415e3f] Exa 설정에서 중복 x-api-key 헤더 제거 (#1627)
- [676ff513] ralph/ULW 루프에서 완료 태그 감지하여 반복 중단 (#1233)
- [4738379a] 서버 재시작 시 safety block 리셋하여 영구 차단 방지 (#1366)
- [e7f4f6dd] task_system 활성화 시 에이전트별 todowrite/todoread 거부
- [bb865232] prometheus↔plan 상호 차단 및 태스크 권한용 isPlanFamily 추가
- [582e0ead] load_skills 기본값 되돌리고 프롬프트로 시행
- [321b319b] init 데드락 방지를 위해 클라이언트 API 대신 설정 데이터 사용 (#1623)
- [c6fafd66] task-continuation-enforcer 제거 및 태스크 도구 제목 복원
- [42dbc8f3] Prometheus 에이전트의 bash 권한 거부 (#1428)
- [050e6a21] safeCreateHook + 방어적 옵셔널 체이닝으로 hook 생성 래핑 (#1559)
- [7ede8e04] loadAllPluginComponents에 타임아웃 + 에러 경계 추가 (#1559)
- [bbe08f0e] matcher.hooks에 방어적 null 체크 추가하여 Windows 크래시 방지 (#441)
- [80297f89] fallback에서 연결된 프로바이더 준수
- [224afadb] async 스킬 해상도에서 disabledSkills 준수
- [e073412d] 서버 인증 주입에 우아한 fallback 추가
- [0dd42e29] bash env prefix에 unix export 구문 강제
- [f79f164c] 스킬 이름의 결정적 충돌 처리
- [1e383f44] 모델 제안 재시도 실패 시 background-agent 세션 중단
- [1411ca25] delegate-task에서 sisyphus-junior보다 명시적 카테고리 모델 우선
- [5ffecb60] Gemini 호환성을 위해 propertyNames 회피 (#1465)
- [b954afca] gemini-3-pro에 지원되는 variant 사용 (#1463)
- [faae3d0f] fuzzyMatchModel에서 정확한 모델 ID 매치 선호 (#1460)
- [6a66bfcc] 사용자 설정 에이전트 variant 준수 (#1464)
- [2f9004f0] 서브에이전트 spawn 시 opencode desktop 서버 인증 버그 수정 (#1399)
- [6151d1cb] Prometheus 모드에서 권한 설정 준수하도록 bash 명령 차단 (#1449)
- [13e1d7cb] 하드코딩된 'unix' 대신 detectShellType() 사용 (#1459)
- [5a2ab009] 웹 검색 비활성화 시 lazy evaluation으로 크래시 방지
- [2236a940] 스킬 해상도에서 비활성화 스킬 기능 구현
- [49c93396] 사용자가 명시적으로 태스크 취소 시 알림 건너뛰기
- [f0309927] plan 에이전트가 demote 시 prometheus 프롬프트 상속 방지
- [9d217b05] demote 시 plan 프롬프트 보존 (#1416)
- [ec1cb5db] prometheus 경로 제약 및 원자적 쓰기 프로토콜 시행 (#1414)
- [8441f70c] sisyphus-junior 모델 override 우선순위 준수 (#1404)
- [5c68ae3b] 에이전트 variant override 준수 (#1394)
- [527c21ea] 오버라이드 도구(glob, grep)에서 ctx.directory 사용 (#1394)
- [134dc768] task ID 유효성 검증 및 lock 획득 안전성 개선
- [4c40c3ad] 설정 경로 중복 제거하여 이중 hook 실행 방지
- [ba129784] 권한 마이그레이션을 통해 도구 override 준수
- [3bb4289b] didChange 동기화로 오래된 진단 방지 (LSP)
- [8ff9c246] Windows에서 Bun spawn segfault 방지를 위해 Node.js child_process 사용 (LSP)
- [bd3a3bcf] provider-models 캐시에서 string[] 및 object[] 형식 모두 처리
- [48cb2033] notifyParentSession에서 중단된 부모 세션 우아하게 처리
- [8886879b] 플러그인 무효화에 CACHE_DIR 대신 USER_CONFIG_DIR 사용
- [d8137c0c] boulder state에서 에이전트 추적하여 세션 continuation 수정 (#927)
- [81a2317f] doctor에서 사용자 설정 variant 표시
- [8e349aad] 크로스 플랫폼 경로 감지에 path.isAbsolute() 사용
- [e257bff3] config-handler 테스트에서 `as any` 타입 단언 제거

### Refactor

- [bdaa8fc6] delegate-task 스킬 해상도 및 타입 안전성 강화
- [7788ba3d] 모델 가용성 및 해상도 모듈 구조 개선
- [ee72c455] background-task tools.ts를 200 LOC 미만의 집중 모듈로 분할
- [9377c7eb] interactive-bash-session 모놀리식 hook을 모듈로 분할
- [f1316bc8] tmux-subagent manager.ts를 집중 모듈로 분할
- [6bb9a3b7] call-omo-agent tools.ts를 200 LOC 미만의 집중 모듈로 분할
- [d8e7e4f1] atlas hook에서 git worktree parser 추출
- [817c593e] 마이그레이션 모델 및 카테고리 헬퍼 분리 (#1561)
- [3ccef5d9] 마이그레이션 에이전트 및 hook 맵 추출 (#1561)
- [2727f0f4] context window recovery hook 추출
- [a691a3ac] delegate_task를 메타데이터 수정과 함께 task 도구로 마이그레이션
- [6288251a] task 스키마를 Claude Code 필드명으로 업데이트 (subject, blockedBy, blocks 등)
- [1b9303ba] ultrawork 워크플로우 단순화 및 병렬 컨텍스트 수집 적용 (#1412)
- [7ebafe22] plan 프롬프트를 전용 설정으로 분리 (#1413)
- [e36dde6e] background-agent 라이프사이클 최적화 및 도구 단순화 (#1411)
- [db787b73] oracle 프롬프트를 GPT-5.2용 XML 구조 및 상세 제약으로 최적화
- [2e0d0c98] atlas 에이전트를 모델 기반 라우팅의 모듈 디렉토리로 재구조화
- [e969ca55] prometheus에서 바이너리 검증을 계층형 에이전트 실행 QA로 교체
- [159fccdd] background-agent 캐시 타이머 라이프사이클 및 결과 처리 최적화
- [f08d4ecd] formatCustomSkillsBlock 추출하여 중복 제거
- [d66e29a8] task-list 경로 해상도를 getTaskDir로 통합
- [2224183b] 죽은 코드 제거
- [e663d7b3] model-availability 테스트를 분할 모듈에 맞게 업데이트

### Others (chore, docs, style, test, release, CLA)

#### Releases
- [139f392d] release: v3.3.2
- [ff94aa30] release: v3.3.1
- [d0c4085a] release: v3.3.1
- [825a5e70] release: v3.3.0
- [f1c794e6] release: v3.2.4
- [819c5b5d] release: v3.2.3
- [a62cf303] release: v3.2.2

#### Model Updates
- [1f649204] claude-opus-4-5 참조를 claude-opus-4-6으로 업데이트
- [4c721540] gpt-5.2-codex 참조를 gpt-5.3-codex로 업데이트

#### CLA Signatures
- @QiRaining, @quantmind-br, @mkusaka, @itsnebulalol, @shaunmorris, @Mang-Joo, @code-yeongyu, @kaizen403, @wydrox, @filipemsilv4, @sk0x0y, @Stranmor, @ualtinok, @ilarvne, @dan-myles, @pierrecorsini, @gburch, @YanzheL, @hichoe95

#### Style
- [0dad85ea] hephaestus 색상 개선
- [30990f7f] Hephaestus 및 Prometheus 색상 업데이트

---

## 전체 커밋 목록

```
441fda91 fix: migrate config on deep copy, apply to rawConfig only on successful file write (#1660)
006e6ade test(delegate-task): reset Bun mocks per test
aa447765 feat(shared/git-worktree, features): add git diff stats utility and infrastructure improvements
bdaa8fc6 refactor(tools/delegate-task): enhance skill resolution and type safety
7788ba3d refactor(shared): improve model availability and resolution module structure
1324fee3 feat(cli/run, background-agent): manage session permissions for CLI and background tasks
e663d7b3 refactor(shared): update model-availability tests to use split modules
e257bff3 fix(plugin-handlers): remove `as any` type assertions in config-handler tests
23bca2b4 feat(tools/background-task): resolve background_output task_id title
83a05630 feat(tools/delegate-task): add skill-resolver module
6717349e feat(claude-tasks): add CLAUDE_CODE_TASK_LIST_ID env var support
ee72c455 refactor(tools/background-task): split tools.ts into focused modules under 200 LOC
9377c7eb refactor(hooks/interactive-bash-session): split monolithic hook into modules
f1316bc8 refactor(tmux-subagent): split manager.ts into focused modules
1f8f7b59 docs(AGENTS): update line counts and stats across all AGENTS.md files
c6fafd66 fix: remove task-continuation-enforcer and restore task tool titles
42dbc8f3 Fix Issue #1428: Deny bash permission for Prometheus agent
6bb9a3b7 refactor(tools/call-omo-agent): split tools.ts into focused modules under 200 LOC
bb865232 fix: add isPlanFamily for prometheus↔plan mutual blocking and task permission
a5489718 feat(commands): add /handoff builtin command with programmatic context synthesis
582e0ead fix: revert load_skills default and enforce via prompts instead
321b319b fix(agents): use config data instead of client API to avoid init deadlock (#1623)
a3dd1dba test(mcp): restore Tavily tests and add encoding edge case (#1627)
06611a76 fix(mcp): remove duplicate x-api-key header, add test (#1627)
676ff513 fix: detect completion tags in ralph/ULW loop to stop iteration (#1233)
4738379a fix(lsp): reset safety block on server restart to prevent permanent blocks (#1366)
44415e3f fix(mcp): remove duplicate x-api-key header from Exa config (#1627)
e7f4f6dd fix: deny todowrite/todoread per-agent when task_system is enabled
d8e7e4f1 refactor: extract git worktree parser from atlas hook
6b4e1498 test: assert variant forwarded in sync continuation
7f4338b6 fix: preserve variant in sync continuation to maintain thinking budget
d769b958 fix(delegation): use blocking prompt for sync tasks instead of polling
72cf9087 fix(delegation): decouple plan agent from prometheus - remove aliasing
f035be84 fix(agents): include custom agents in orchestrator delegation prompt (#1623)
6ce48266 refactor: extract git worktree parser from atlas hook
a85da593 fix: encode EXA_API_KEY before appending to URL query parameter
b88a8681 fix(config): plan agent inherits model settings from prometheus when not explicitly configured
7abefcca feat: auto-recover from Anthropic assistant message prefill errors
a0636408 fix(delegate-task): resolve user agent model config in subagent_type path (#1357)
104b9fbb test: add regression tests for sisyphus-junior model override in category delegation (#1295)
f6fc30ad fix: add default value for load_skills parameter in task tool (#1493)
f1fcc26a fix(background-agent): serialize parent notifications (#1582)
09999587 fix(mcp): append EXA_API_KEY to Exa MCP URL when env var is set (#1627)
139f392d release: v3.3.2
cbeeee40 @QiRaining has signed the CLA in code-yeongyu/oh-my-opencode#1641
737bda68 @quantmind-br has signed the CLA in code-yeongyu/oh-my-opencode#1634
ff94aa30 release: v3.3.1
d0c4085a release: v3.3.1
b2661be8 test: fix ralph-loop tests by adding promptAsync to mock
3d4ed912 fix(look-at): use synchronous prompt to fix race condition (#1620 regression)
9a338b16 @mkusaka has signed the CLA in code-yeongyu/oh-my-opencode#1629
471bc6e5 @itsnebulalol has signed the CLA in code-yeongyu/oh-my-opencode#1622
0cbbdd56 fix(cli): enable positional options on parent command for passThroughOptions
825a5e70 release: v3.3.0
414cecd7 test: add promptAsync mocks to all test files for promptAsync migration
ac6e7d00 fix(mcp-loader): also read ~/.claude/.mcp.json for CLI-managed user MCP config
fa77be0d chore: remove testing guide from branch
13da4ef4 docs: add comprehensive local testing guide for acp-json-error branch
6451b212 test(todo-continuation): add promptAsync mocks for migrated hook
fad7354b fix(look-at): remove isJsonParseError band-aid (root cause fixed)
55dc6484 fix(tools): switch session.prompt to promptAsync in delegate-task and call-omo-agent
e984a5c6 test(shared): update model-suggestion-retry tests for promptAsync passthrough
46e02b94 fix(hooks): switch session.prompt to promptAsync in all hooks
5f21ddf4 fix(background-agent): switch session.prompt to promptAsync
108e860d fix(core): switch compatibility shim to promptAsync
b8221a88 fix(shared): switch promptWithModelSuggestionRetry to use promptAsync
cf29cd13 test: isolate user-level MCP config test from real homedir
d1659152 fix(hooks): fire UserPromptSubmitHooks on every prompt, not just first (#594)
1760367a fix(mcp-loader): read user-level MCP config from ~/.claude.json (#814)
747edcb6 fix(skill-loader): filter discovered skills by browserProvider (#1563)
0eddd28a fix: skip ultrawork injection for plan-like agents (#1501)
36e54acc fix(migration): stop task_system backup writes (#1561)
817c593e refactor(migration): split model and category helpers (#1561)
3ccef5d9 refactor(migration): extract agent and hook maps (#1561)
403457f9 fix: rewrite dedup recovery test to mock module instead of filesystem
1df025ad fix: use lazy storage dir resolution to fix CI test flakiness
844ac26e fix: wire deduplication into compaction recovery for prompt-too-long errors (#96)
2727f0f4 refactor: extract context window recovery hook
180fcc3e fix: register compaction todo preserver
3947084c fix: add compaction todo preserver hook
67f701cd fix: avoid invented compaction constraints
f94ae203 fix: ensure truncated result stays within maxLength limit
9040383d fix: cascade cancel descendant tasks when parent session is deleted (#114)
c688e978 fix: update session-manager tests to use factory pattern
a0201e17 fix: use character limit instead of sentence split for skill description (#358)
e4bbd6bf fix: allow string values for commit_footer config (#919)
476f154e fix: use ctx.directory instead of process.cwd() in tools for Desktop app support
9a8f0346 fix: normalize resolvedPath before startsWith check
2bb82c25 fix: expand ALLOWED_AGENTS to include all subagent-capable agents
f980e256 fix: boulder continuation now respects /stop-continuation guard
38169523 fix: anchor .sisyphus path check to ctx.directory to prevent false positives
b9869723 fix: use platform-aware binary detection (where on Windows, which on Unix)
d5b6a7c5 fix: allow dash-prefixed arguments in CLI run command
7fdbabb2 fix: don't fallback to system 'sg' command for ast-grep
b3ebf6c1 fix: allow dash-prefixed arguments in CLI run command
66419918 fix: make model migration run only once by storing history in _migrations field
5e316499 fix: explicitly pass encoding/callback args through stdout.write wrapper
266c045b fix(test): remove shadowed consoleErrorSpy declarations in on-complete-hook tests
eafcac15 fix: address cubic 4/5 review issues
4059d020 fix(test): mock SDK and port-utils in integration test to prevent CI failure
c2dfcadb fix: clear race timeout after plugin loading settles
e343e625 feat(cli): extend run command with port, attach, session-id, on-complete, and json options
050e6a21 fix(index): wrap hook creation with safeCreateHook + add defensive optional chaining (#1559)
7ede8e04 fix(config-handler): add timeout + error boundary around loadAllPluginComponents (#1559)
1ae7d7d6 feat(config): add plugin_load_timeout_ms and safe_hook_creation experimental flags
f9742ddf feat(shared): add safeCreateHook utility for error-safe hook creation
eb5cc873 fix: trim whitespace from tool names to prevent invalid tool calls
847d9941 fix: allow Prometheus to overwrite .sisyphus/*.md plan files
bbe08f0e fix(hooks): add defensive null check for matcher.hooks to prevent Windows crash (#441)
4454753b chore: changes by sisyphus-dev-ai
1c0b41aa fix: respect user-configured agent models over system defaults
4c6b31e5 Revert "Merge pull request #1578 from code-yeongyu/fix/user-configured-model-override"
dbf584af fix: respect user-configured agent models over system defaults
cb2169f3 fix: guard against undefined modelID in anthropic-effort hook
ec520e62 feat: register anthropic-effort hook in plugin lifecycle
6febebc1 feat: add anthropic-effort hook to inject effort=max for Opus 4.6
98f4adbf chore: add modular code enforcement rule and unignore .sisyphus/rules/
a691a3ac refactor: migrate delegate_task to task tool with metadata fixes
f1c794e6 release: v3.2.4
4692809b Regenerate AGENTS.md hierarchy with latest codebase state
d8b29da1 fix(category-skill-reminder): dynamically include available skills with user priority
60bbeb73 fix(compaction): remove hardcoded Claude model from compaction hooks
3a0d7e8d fix(config): stop sisyphus-junior from inheriting UI-selected model
aec56241 fix(atlas): stop continuation retry loop on repeated prompt failures
53537a9a fix: sync Zod schemas with actual implementations
6b560ebf fix(delegate-task): make plan agent categories/skills dynamic
ca8ec494 docs: fix stale references in AGENTS.md files
3be722b3 test: add literal match assertions for regex special char escaping tests
3c32ae04 fix: enforce disabled_tools filtering
bc782ca4 fix: escape regex special chars in pattern matcher
7e5a657f feat(migration): add model version migration for gpt-5.2-codex and claude-opus-4-5
161a864e fix: remove redundant duplicate claude-opus-4-6 fallback entries
93d3acce @shaunmorris has signed the CLA in code-yeongyu/oh-my-opencode#1541
25e436a4 fix: update snapshots and remove duplicate key in switcher for model version update
1f649204 chore: update claude-opus-4-5 references to claude-opus-4-6 (excludes antigravity models)
4c721540 chore: update gpt-5.2-codex references to gpt-5.3-codex
01594a67 fix(hooks): compose session recovery callbacks for continuation enforcers
551dbc95 feat(hooks): register task-continuation-enforcer in plugin lifecycle
f4a9d0c3 feat(hooks): implement task-continuation-enforcer with TDD
f796fdbe feat(hooks): add TASK_CONTINUATION system directive and hook name
b8f15aff feat: check provider connectivity instead of specific model for hephaestus availability
04576c30 @Mang-Joo has signed the CLA in code-yeongyu/oh-my-opencode#1526
11d0005e feat: prioritize claude-opus-4-6 over claude-opus-4-5 in anthropic fallback chains
2224183b refactor: remove dead code
b8d7723f feat(agents): improve Hephaestus autonomous problem-solving behavior
b7f7cb43 fix(model-requirements): use supported variant for gemini-3-pro
02e10432 @code-yeongyu has signed the CLA in code-yeongyu/oh-my-opencode#741
8ff9c246 fix(lsp): use Node.js child_process on Windows to avoid Bun spawn segfault
bd3a3bcf fix: handle both string[] and object[] formats in provider-models cache
48cb2033 fix(background-agent): gracefully handle aborted parent session in notifyParentSession
ca317963 feat: auto port selection when default port is busy
a644d386 fix: properly restore env vars using delete when originally undefined
a4598138 Fix skill discovery priority and deduplication tests
18e941b6 fix: correct skill priority order and improve test coverage
86ac39fb fix: include custom skills in delegate_task load_skills resolution
7621aada feat: auto port selection when default port is busy
71ac09bb fix: use process.cwd() instead of ctx.directory for glob/grep tools
ddf878e5 feat(write-existing-file-guard): add hook to prevent write tool from overwriting existing files
8886879b fix(auto-update): use USER_CONFIG_DIR instead of CACHE_DIR for plugin invalidation
f08d4ecd refactor(agents): extract formatCustomSkillsBlock to eliminate duplication
a298a2f0 fix(atlas): separate custom skills in Atlas buildSkillsSection()
ddc52bfd fix(agents): emphasize user-installed skills in delegation prompts
38b40bca fix(prometheus-md-only): prioritize boulder state agent over message files
169ccb6b fix: use boulder agent instead of hardcoded Atlas check for continuation
d8137c0c fix: track agent in boulder state to fix session continuation (fixes #927)
81a2317f fix(doctor): display user-configured variant in model resolution output
80297f89 fix(model-availability): honor connected providers for fallback
819c5b5d release: v3.2.3
8e349aad fix(tasks): use path.isAbsolute() for cross-platform path detection
17129070 docs(tasks): update AGENTS.md for global storage architecture
d66e39a8 refactor(tasks): consolidate task-list path resolution to use getTaskDir
ace26881 chore: regenerate schema after Task 1 changes
bf31e728 feat(tasks): migrate storage to global config dir with ULTRAWORK_TASK_LIST_ID support
7b820492 feat(config): update task config schema for global storage
224afadb fix(skill-loader): respect disabledSkills in async skill resolution
953b1f98 fix(ci): use regex variables for bash 5.2+ compatibility in changelog generation
e073412d fix(auth): add graceful fallback for server auth injection
0dd42e29 fix(non-interactive-env): force unix export syntax for bash env prefix
85932fad test(skill-loader): fix test isolation by resetting skill content
65043a7e fix: remove broken TOC links in translated READMEs
f79f164c fix(skill-loader): deterministic collision handling for skill names
0dad85ea hephaestus color improvement
1e383f44 fix(background-agent): abort session on model suggestion retry failure
30990f7f style(agents): update Hephaestus and Prometheus colors
d099b025 feat(look_at): add image_data parameter for clipboard/pasted image support
1411ca25 fix(delegate-task): honor explicit category model over sisyphus-junior
4330f25f revert(call-omo-agent): remove metis/momus from ALLOWED_AGENTS
737fac43 fix(agent-restrictions): add read-only restrictions for metis and momus
49a4a1bf fix(call-omo-agent): allow Prometheus to call Metis and Momus (#1462)
5ffecb60 fix(skill-mcp): avoid propertyNames for Gemini compatibility (#1465)
b954afca fix(model-requirements): use supported variant for gemini-3-pro (#1463)
faae3d0f fix(model-availability): prefer exact model ID match in fuzzyMatchModel (#1460)
c57c0a6b docs: clarify Prometheus invocation workflow (#1466)
6a66bfcc fix(doctor): respect user-configured agent variant (#1464)
b19bc857 fix(docs): instruct curl over WebFetch for installation (#1461)
2f9004f0 fix(auth): opencode desktop server unauthorized bugfix on subagent spawn (#1399)
6151d1cb fix: block bash commands in Prometheus mode to respect permission config (#1449)
13e1d7cb fix(non-interactive-env): use detectShellType() instead of hardcoded 'unix' (#1459)
5361cd0a @kaizen403 has signed the CLA in code-yeongyu/oh-my-opencode#1449
437abd8c @wydrox has signed the CLA in code-yeongyu/oh-my-opencode#1436
9a2a6a69 fix(test): use try/finally for guaranteed env restoration
5a2ab009 fix(mcp): lazy evaluation prevents crash when websearch disabled
17cb4954 fix(mcp): rewrite tests to call createWebsearchConfig directly
fea7bd2d docs(mcp): document websearch provider configuration
ef3d0afa test(mcp): add websearch provider tests
00f57686 feat(mcp): add multi-provider websearch support
4840864e feat(config): add websearch provider schema
9f509477 @filipemsilv4 has signed the CLA in code-yeongyu/oh-my-opencode#1435
45290b5b @sk0x0y has signed the CLA in code-yeongyu/oh-my-opencode#1434
9343f384 @Stranmor has signed the CLA in code-yeongyu/oh-my-opencode#1432
bf83712a @ualtinok has signed the CLA in code-yeongyu/oh-my-opencode#1393
374acb3a fix: update tests to reflect changes in skill resolution for async handling and disabled skills
ba2a9a90 fix: update skill resolution to support disabled skills functionality
2236a940 fix: implement disabled skills functionality in skill resolution
976ffaeb @ilarvne has signed the CLA in code-yeongyu/oh-my-opencode#1422
a62cf303 release: v3.2.2
49c93396 fix(background-cancel): skip notification when user explicitly cancels tasks
1b7fd32b docs: add Task system documentation
3a823eb2 feat(tasks-todowrite-disabler): add strong emphasis to register tasks before working
a651e7f0 docs(agents): regenerate AGENTS.md hierarchy with init-deep
d7679e14 feat(delegate-task): add actionable TODO list template to plan agent prompt
4c4e1687 feat(auto-slash-command): add builtin commands support and improve part extraction
f0309927 fix(config): prevent plan agent from inheriting prometheus prompt on demote
bf87bf47 feat(agents): add GPT-5.2 optimized prompt for sisyphus-junior
1a0cc424 feat(tasks-todowrite-disabler): improve error message with actionable workflow guidance
671e320b feat(agents): add useTaskSystem flag for conditional todo/task discipline prompts
dd120085 feat(agents): add Todo Discipline section to Hephaestus prompt
9d217b05 fix(config-handler): preserve plan prompt when demoted (#1416)
1b9303ba refactor(ultrawork): simplify workflow and apply parallel context gathering (#1412)
ec1cb5db fix(prometheus): enforce path constraints and atomic write protocol (#1414)
7ebafe22 refactor(config-handler): separate plan prompt into dedicated configuration (#1413)
e36dde6e refactor(background-agent): optimize lifecycle and simplify tools (#1411)
b62519b4 feat(agents): respect uiSelectedModel in Atlas model resolution (#1410)
dea13a37 feat(task-system): add experimental task system with Claude Code spec alignment (#1415)
1e587c55 docs(AGENTS): add critical sections for PR workflow and OpenCode source reference
db787b73 refactor(oracle): optimize prompt for GPT-5.2 with XML structure and verbosity constraints
ac9e22cc fix(prompts): add missing run_in_background and load_skills params to examples
8441f70c fix(delegate-task): honor sisyphus-junior model override precedence (#1404)
72268364 atlas reminder reinforce
0f81d4c1 @dan-myles has signed the CLA in code-yeongyu/oh-my-opencode#1399
62e16874 feat: add agent fallback and preemptive-compaction restoration
99ee4a02 docs: update AGENTS.md with explore agent model change (grok-code-fast-1)
d80adac3 feat(agents): add grok-code-fast-1 as primary model for explore agent
159fccdd refactor(background-agent): optimize cache timer lifecycle and result handling
9f84da1d feat(skills): set triage category ratio to 1:2:1 (unspecified-low:writing:quick)
8e17819f feat(skills): add streaming mode and todo tracking to triage skills
b01e2469 docs(issue-templates): add AI agent consultation to prerequisite checklist
2e0d0c98 refactor(agents): restructure atlas agent into modular directory with model-based routing
5c68ae3b fix: honor agent variant overrides (#1394)
527c21ea fix(tools): for overridden tools (glob, grep) path should use ctx.directory. OpenCode Desktop might not send path as a param and cwd might resolve to "/"
d165a682 @pierrecorsini has signed the CLA in code-yeongyu/oh-my-opencode#1386
76623454 test(task): improve todo-sync tests with bun-types and inline assertions
f68a6f7d  fix: remove redundant removeCodeBlocks call
ce62da92 fix: remove broken TOC links pointing to non-existent sections
0ea92124 feat(task): add real-time single-task todo sync via OpenCode API
418cf358 format: apply prettier to index.ts
e969ca55 refactor(prometheus): replace binary verification with layered agent-executed QA
92639ca3 feat(task): refactor to Claude Code style individual tools
6288251a refactor(task): update schema to Claude Code field names (subject, blockedBy, blocks, etc.)
961ce194 feat(cli): deny Question tool in CLI run mode
b71fe66a feat(task): implement TaskUpdate tool with additive blocks/blockedBy and metadata merge
874d51a9 test(cli): add default agent resolution tests
dd3f93d3 docs(cli): improve run command help with agent options
a7a847eb feat(cli): implement default agent priority in run command
9c2c8b4d feat(config): add default_run_agent schema option
89278473 feat(skills): add github-pr-triage skill and update github-issue-triage
08889b88 @gburch has signed the CLA in code-yeongyu/oh-my-opencode#1382
abc448b1 feat(config): disable todowrite/todoread tools when new_task_system_enabled
523ef0d2 @YanzheL has signed the CLA in code-yeongyu/oh-my-opencode#1371
134dc768 fix(task-tool): add task ID validation and improve lock acquisition safety
914a4801 @code-yeongyu has signed the CLA in code-yeongyu/oh-my-opencode#1029
9293cb52 @code-yeongyu has signed the CLA in code-yeongyu/oh-my-opencode#580
4c40c3ad fix(claude-code-hooks): deduplicate settings paths to prevent double hook execution
ba129784 fix(agents): honor tools overrides via permission migration
3bb4289b fix(lsp): prevent stale diagnostics by syncing didChange
64b29ea0 feat(skill-loader): support nested skill directories
```

---

## 이전 동기화 이력

### v3.2.0 → v3.2.1 (2026-02-01)

- 통합 Claude Tasks 시스템 (#1356) - 레거시 sisyphus-tasks/swarm 완전 교체
- GitHub Copilot Gemini 모델명 -preview 접미사 수정
- 백그라운드 에이전트 동시성 슬롯 누수 수정

### v3.1.9 → v3.2.0 (2026-02-01)

- Hephaestus 자율 에이전트 추가
- unstable-agent-babysitter 기본 활성화
- Windows 호환성 대폭 개선
- /stop-continuation 명령어 추가

### v3.1.9 → v3.1.10 (2026-01-31)

- 플러그인 초기화 데드락 해결 (#1304)
- 테스트 스위트 최적화: 104.6s → 7.01s (15배)
- Kimi Provider 완전 통합
- Tmux Integration 완전 구현 (#1125)

---

*생성 일시: 2026-02-08 KST*
*생성자: zsgg (Oh-My-OpenCode fork maintainer)*
*생성 도구: sync-fork 슬래시 커맨드*
