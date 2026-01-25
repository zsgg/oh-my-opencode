## Upstream Changelog

**동기화 일시**: 2026-01-26 21:00
**비교 범위**: HEAD..upstream/dev

### 변경사항 요약

#### Features
- [aead4ae] tmux pane management for background agent sessions 추가
- [bccc943] dev-browser skill (Windows 지원 포함) 추가
- [3af30b0] agent-browser option for browser automation 추가
- [b55fd8d] explore fallback chain에 github-copilot/gpt-5-mini 추가
- [0aa8f48] sisyphus-junior-notepad hook 추가 (조건부 notepad rules injection)
- [212baa6] /remove-deadcode slash command 추가 (LSP-verified dead code removal)
- [063c759] background_cancel(all=true) 시 상세 task 정보 및 resume 안내 표시
- [f1a279a] config schema에 xhigh reasoningEffort 추가
- [58459e6] website layout (header, sidebar, footer, navigation) 추가
- [894a0fa] website에 next-intl i18n 및 dark mode 지원 추가
- [ba93c42] Next.js 15 project with @opennextjs/cloudflare 초기화
- [c2247ae] prometheus agent 추가 및 agent key lookup 정규화

#### Fixes
- [208af05] skill/slashcommand descriptions 동기 생성 (pre-provided인 경우)
- [24d065c] 문서에서 skills parameter 대신 load_skills 사용하도록 수정
- [1c76e05] loadBuiltinCommands에서 누락된 name property 추가 (TypeError 해결)
- [c8cc94c] gpt-5-nano model mapping에서 github-copilot 연관 제거
- [20cca35] ralph-loop에서 transcript completion detection 시 user messages 스킵
- [6e9ebaf] writing category migration에 gemini-3-flash 누락 추가
- [ec32dd6] question-label-truncator type errors 수정 및 test coverage 추가
- [04fb339] agent/category configs에서 model fallback 추가
- [3a22c24] 30자 초과 question option labels 자동 truncate
- [cf23204] MCP disabled flag로 previously loaded servers 제거 안 되는 문제 수정
- [9532680] slashcommand discovery에 built-in commands(start-work 등) 포함
- [2a945dd] BackgroundManager에 config 전달 (concurrency limits 적용)
- [58bb921] todo-continuation에서 compaction agent 필터링 (무한 루프 방지)
- [faf172a] multimodal-looker fallback chain order 수정
- [04633ba] model names를 OpenCode Zen catalog와 일치하도록 수정
- [21c7d29] @opennextjs/cloudflare 및 test configuration 문제 해결
- [444fbe3] delegate-task에서 lowercase sisyphus-junior agent name 사용
- [7ed7bf5] agents에서 lowercase agent names 사용

#### Refactor
- [14f450b] delegate_task schema sync (resume→session_id, command param 추가)
- [5a1da39] ultrawork에서 plan agent 참조를 명시적 delegate_task 구문으로 대체
- [043b1a3] tools barrel에서 dead re-exports 제거
- [512952f] deprecated config-path.ts 제거 (dead code)
- [d9723e7] unused background-compaction hook module 제거
- [dfc57d0] model-requirements에서 lowercase agent keys 사용
- [12c9029] plugin에서 lowercase agent keys 사용
- [91060c3] agents utils에서 lowercase config keys 사용
- [90292db] prometheus-hook에서 lowercase config key 사용
- [cc4deed] schema에서 lowercase agent config keys 사용

#### Docs
- [05904ca] agent-browser 상세 설치 가이드 (Playwright troubleshooting 포함)
- [fd72ce5] AGENTS.md knowledge base 업데이트
- [aa244e8] example config에서 atlas agent name case 수정
- [1486ebb] 3.0 stable release용 READMEs 업데이트

#### Chore
- [a5db86e] release: v3.0.1
- [b8a0eee] release: v3.0.0
- [0b784d2] release: v3.0.0-beta.16
- [ad86e58] release: v3.0.0-beta.15
- [1c562a9] release: v3.0.0-beta.14
- [0e1d4e5] website directory 제거 (CI test failures 수정)
- [81d27af] changes by sisyphus-dev-ai
- [c0fb4b7] changes by sisyphus-dev-ai

#### Tests
- [1c9588f] agent key normalization integration tests 추가
- [5d73ac8] lowercase agent keys용 CLI tests 업데이트

#### CLA Signatures
- [6cb2f30] @kvokka signed CLA
- [f116ea1] @potb signed CLA
- [6aa0674] @jsl9208 signed CLA
- [2b82862] @sadnow signed CLA
- [e60ccb9] @ThanhNguyxn signed CLA
- [6f60f03] @AamiRobin signed CLA
- [5c7dd40] @AndersHsueh signed CLA
- [acc7b8b] @gongxh0901 signed CLA
- [8c90838] @RouHim signed CLA

### 전체 커밋 목록
- aead4ae: Add tmux pane management for background agent sessions (#1094)
- bccc943: feat(skills): add dev-browser skill with Windows support (#1093)
- 05904ca: docs(agent-browser): add detailed installation guide with Playwright troubleshooting
- 3af30b0: feat(skills): add agent-browser option for browser automation (#1090)
- b55fd8d: feat(explore): add github-copilot/gpt-5-mini to fallback chain (#1091)
- 208af05: fix: generate skill/slashcommand descriptions synchronously when pre-provided (#1087)
- 0aa8f48: feat(hooks): add sisyphus-junior-notepad hook for conditional notepad rules injection (#1092)
- a5db86e: release: v3.0.1
- 14f450b: refactor: sync delegate_task schema with OpenCode Task tool (resume→session_id, add command param)
- 5a1da39: refactor(ultrawork): replace vague plan agent references with explicit delegate_task(subagent_type="plan") invocation syntax
- 24d065c: fix: update documentation to use load_skills instead of skills parameter (#1088)
- fd72ce5: docs: update AGENTS.md knowledge base (043b1a33)
- 043b1a3: refactor: remove dead re-exports from tools barrel (getTmuxPath, DelegateTaskToolOptions, DEFAULT_CATEGORIES, CATEGORY_PROMPT_APPENDS)
- 512952f: refactor: remove deprecated config-path.ts (dead code, 0 references)
- d9723e7: refactor: remove unused background-compaction hook module
- 212baa6: feat(commands): add /remove-deadcode slash command for LSP-verified dead code removal
- 1c76e05: fix: add missing name property in loadBuiltinCommands causing TypeError on slashcommand
- c8cc94c: fix: remove github-copilot association from gpt-5-nano model mapping
- 20cca35: fix(ralph-loop): skip user messages in transcript completion detection (#622) (#1086)
- 81d27af: chore: changes by sisyphus-dev-ai
- 6cb2f30: @kvokka has signed the CLA in code-yeongyu/oh-my-opencode#1084
- f116ea1: @potb has signed the CLA in code-yeongyu/oh-my-opencode#1083
- 6aa0674: @jsl9208 has signed the CLA in code-yeongyu/oh-my-opencode#1082
- 2b82862: @sadnow has signed the CLA in code-yeongyu/oh-my-opencode#1080
- e60ccb9: @ThanhNguyxn has signed the CLA in code-yeongyu/oh-my-opencode#1075
- aa244e8: docs: fix atlas agent name case in example config
- 6f60f03: @AamiRobin has signed the CLA in code-yeongyu/oh-my-opencode#1067
- b8a0eee: release: v3.0.0
- 1486ebb: docs: update READMEs for 3.0 stable release
- 063c759: feat: show detailed task info and resume instructions on background_cancel(all=true) (#1062)
- 6e9ebaf: fix: add missing gemini-3-flash to writing category migration (#1061)
- 0e1d4e5: chore: remove website directory (fixes CI test failures)
- c0fb4b7: chore: changes by sisyphus-dev-ai
- ec32dd6: fix(question-label-truncator): fix type errors and add test coverage
- 04fb339: fix: add model fallback from agent/category configs
- 3a22c24: fix: auto-truncate question option labels exceeding 30 characters
- cf23204: Fix MCP disabled flag not removing previously loaded servers (#985)
- 9532680: fix(slashcommand): include built-in commands (like start-work) in discovery (#1031)
- 2a945dd: fix(background-task): pass config to BackgroundManager for concurrency limits
- 58bb921: fix(todo-continuation): filter compaction agent to prevent infinite loop
- f1a279a: Add xhigh reasoningEffort to config schema (#965)
- faf172a: fix(multimodal-looker): update fallback chain order (#1050)
- 04633ba: fix(models): update model names to match OpenCode Zen catalog (#1048)
- 58459e6: feat(website): add layout with header, sidebar, footer and navigation
- 894a0fa: feat(website): add next-intl i18n and dark mode support
- 21c7d29: fix(website): resolve @opennextjs/cloudflare and test configuration issues
- ba93c42: feat(website): initialize Next.js 15 project with @opennextjs/cloudflare
- 5c7dd40: @AndersHsueh has signed the CLA in code-yeongyu/oh-my-opencode#1042
- acc7b8b: @gongxh0901 has signed the CLA in code-yeongyu/oh-my-opencode#1037
- 8c90838: @RouHim has signed the CLA in code-yeongyu/oh-my-opencode#1031
- 0b784d2: release: v3.0.0-beta.16
- 444fbe3: fix(delegate-task): use lowercase sisyphus-junior agent name in API calls
- ad86e58: release: v3.0.0-beta.15
- 7ed7bf5: fix(agents): use lowercase agent names in API calls
- 1c562a9: release: v3.0.0-beta.14
- c2247ae: refactor(agents): add prometheus agent and normalize agent key lookups
- 1c9588f: test: add integration tests for agent key normalization
- 5d73ac8: test: update CLI tests for lowercase agent keys
- dfc57d0: refactor(model-requirements): use lowercase agent keys
- 12c9029: refactor(plugin): use lowercase agent keys throughout
- 91060c3: refactor(agents): use lowercase config keys in utils
- 90292db: refactor(prometheus-hook): use lowercase config key
- cc4deed: refactor(schema): use lowercase agent config keys
