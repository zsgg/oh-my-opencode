# Upstream Changelog

**동기화 일시**: 2026-01-31 15:00 KST
**비교 범위**: HEAD (c77c9ce) → upstream/dev (ddfbdbb)
**총 커밋 수**: 157개
**버전 범위**: v3.1.9 → v3.1.10

---

## 변경사항 요약

### 🚨 Critical Fixes (v3.1.10)

#### 플러그인 초기화 데드락 해결 (#1304)
- **문제**: config handler와 createBuiltinAgents가 fetchAvailableModels를 client와 함께 호출 → OpenCode 서버 API 요청 → 플러그인 초기화는 서버 응답 대기, 서버는 플러그인 초기화 완료 대기 → 교착상태
- **해결**: client 대신 undefined 전달하여 cache-only 모드 사용. cache 없으면 fallback chain의 첫 번째 모델 사용
- **테스트**: config-handler.test.ts, utils.test.ts에서 deadlock 방지 regression tests 추가
- **Co-authors**: @robin-watcha, justsisyphus

#### /start-work Prometheus 세션 수정 (#1298)
- **문제**: start-work에서 Prometheus 세션이 잘못된 agent 사용
- **해결**: atlas로 항상 전환하도록 수정

#### Momus 에이전트 프롬프트 리팩토링
- **변경**: 392줄 → 125줄로 단순화
- **핵심**: APPROVAL BIAS 추가 - 기본 승인, blocker만 거부
- **제한**: 최대 3개 이슈만 거부 (overwhelming feedback 방지)
- **톤**: 'ruthlessly critical' → 'practical reviewer' 접근
- **기준**: 80% clear = pass ('good enough' criteria)

#### GitHub Issue Triage 스킬 문서 강화
- **--limit 500 필수화**: 100 대신 500 사용 강제
- **Phase 2 체크리스트**: 진행 전 검증 단계
- **Anti-patterns**: CRITICAL/HIGH/MEDIUM severity 레벨
- **페이지네이션 강조**: 결과 카운팅 및 추가 페이지 fetch 필요 시 명시

---

### 🎯 v3.1.9 주요 업데이트

#### 테스트 스위트 최적화 (#1284) 🏆
- **FakeTimers 구현**: ~100줄 custom implementation
- **속도 향상**: 104.6s → 7.01s (15배)
- **Race condition 수정**:
  - Bun.fetch.bind(Bun) 사용 (globalThis.fetch mock 간섭 회피)
  - Promise.all 패턴으로 concurrent fetch/waitForCallback
  - afterEach에서 Bun.sleep(10)으로 port release
- **Concurrency 테스트**: expect(true).toBe(true) → getCount() 검증 6개 교체
- **Executor 테스트**: FakeTimeouts로 ~26s → ~6.8s
- **Gemini model mock**: artistry unstable mode 수정

#### 모델 Resolution 개선
**3-tier Fallback System**:
1. provider-models cache
2. models.json
3. client.model.list() API

**Atlas Fallback Chain**:
- uiSelectedModel 제거
- k2p5 (kimi-k2.5)를 primary로 사용
- provider-models.json 항상 갱신 (세션 시작 시)

**Look-at Tool**:
- registered agent의 model을 session.prompt에 명시적 전달
- multimodal-looker가 정확한 model(gemini-3-flash-preview) 사용 보장

**Fuzzy Matching & Cross-Provider**:
- 모든 documentation 업데이트 (AGENTS.md, features.md, configurations.md, category-skill-guide.md)

#### Kimi Provider 완전 통합
**Provider Setup**:
- kimi-for-coding 프로바이더 추가 (installer 포함)
- Model ID: k2p5 (kimi-k2.5)
- opencode/kimi-k2.5-free fallback

**Fallback Chains 업데이트**:
- sisyphus: opus → **kimi-k2.5** → glm-4.7 → gpt-5.2-codex → gemini-3-pro
- atlas: sonnet-4-5 → **kimi-k2.5** → gpt-5.2 → gemini-3-pro
- prometheus/metis: opus → **kimi-k2.5** → gpt-5.2 → gemini-3-pro
- multimodal-looker: gemini-flash → gpt-5.2 → glm-4.6v → **kimi-k2.5** → haiku → gpt-5-nano
- visual-engineering: gpt-5.2 제거, **glm-4.7** 추가

**RequiresModel Field**:
- shared/model-requirements.ts에 requiresModel 필드 추가
- isModelAvailable helper 함수
- delegate-task, agents에서 조건부 활성화 체크
- 테스트 추가

#### Agent & Category 개편
**Ultrabrain Category Revamp**:
- Deep work mindset로 재설계
- STRATEGIC_CATEGORY_PROMPT_APPEND → ULTRABRAIN_CATEGORY_PROMPT_APPEND
- variant: max for gemini-3-pro
- 코드 스타일 요구사항:
  - 기존 codebase pattern 검색 필수
  - 프로젝트 conventions 매칭 필수
  - 읽기 쉬운 human-friendly 코드

**Artistry Category**:
- Ultrawork-mode에 artistry specialist delegation 추가
- Oracle vs Artistry 구분:
  - Oracle: conventional problems (architecture, debugging, complex logic)
  - Artistry: non-conventional problems (different approach needed)
- MANDATORY CERTAINTY PROTOCOL 업데이트
- AGENTS UTILIZATION table에 'Hard problem (non-conventional)' row 추가

**Oracle Fallback Chain**:
- gemini-3-pro를 opus보다 우선 (variant: "max")

**Momus Fallback**:
- variant: max 추가 (opus-4-5 entry)

**Subagent UI Model Selection 제외** (#1274):
- explore/librarian/oracle 등 subagent는 자체 fallback chain 사용
- UI-selected model 상속 안 함
- AgentMode type 추가, static mode property (factory.mode)
- createBuiltinAgents()에서 source.mode 체크

**Explore Agent**:
- Grok Code → Claude Haiku 4.5 변경

**Model Mapping Cleanup**:
- big-pickle → glm-4.7-free

#### CI/CD: OIDC Trusted Publishing 전환
**배경**:
- npm classic token deprecation
- NODE_AUTH_TOKEN 의존성 제거 필요

**Platform Publish 개선** (#1304):
- 빌드/퍼블리시 job 분리
- 빌드: 바이너리 컴파일, 압축 아티팩트 업로드 (tar.gz/zip)
- 퍼블리시: 아티팩트 다운로드, npm OIDC 사용
- npm provenance 추가
- 타임아웃 증가 (40-120MB 바이너리)
- 빌드 병렬화: 7개 플랫폼 동시

**Benefits**:
- 퍼블리시 시점에 fresh OIDC token
- 토큰 rotation 불필요 (OIDC는 ephemeral)
- 빌드 실패와 퍼블리시 실패 분리
- 아티팩트 재사용 가능

**Windows 지원**:
- 7z 사용 (publish-platform)
- explorer 사용 (cmd /c start 대신, shell injection 방지)

**OIDC 설정 시리즈** (10+ 커밋):
- registry-url 제거
- NPM_CONFIG_PROVENANCE env
- .npmrc 수동 생성
- npm version 11.5.1+ 자동 업그레이드
- --registry 플래그 명시

#### Logging & Output 개선
**Silent Logging**:
- console.log/warn/error → file-based log()
- 파일: /tmp/oh-my-opencode.log
- 변경 파일:
  - index.ts
  - hook-message-injector/injector.ts
  - lsp/client.ts
  - ast-grep/downloader.ts
  - session-recovery/index.ts
  - comment-checker/downloader.ts
- CLI 도구는 console 출력 유지 (UX)

**CLI/Run 수정** (#1263):
- [undefine] 태그 수정: sessionID undefined 시 '[system]' 표시
- message.updated content 필드 수정: SDK의 EventMessageUpdated는 info metadata만 포함, content는 message.part.updated로 스트리밍
- text preview 추가: message.part.updated verbose logging
- MessageUpdatedProps type 업데이트

---

### 🚀 v3.1.8 주요 변경사항

#### Delegate Task Category UserModel Chain 복원 (#1227)
**문제**:
- PR #1227에서 resolved.model을 userModel chain에서 제거
- 가정: resolved.model이 main session model을 bypass한다고 착각
- 실제: resolved.model은 category의 DEFAULT_CATEGORIES model 포함 (e.g., quick → claude-haiku-4-5)

**증상**:
- connectedProvidersCache null + availableModels empty
- category model resolution이 systemDefaultModel(opus)로 fallthrough
- category default 사용 안 함

**수정**:
- resolved.model을 userModel chain에 복원

**우선순위** (최종):
1. User category model override
2. **Category default model** (from resolved.model)
3. sisyphusJuniorModel
4. Fallback chain
5. System default

#### Tmux Subagent 리팩토링 (#1267)
- Dependency injection 도입
- Testability 향상
- Co-author: justsisyphus

#### Run Command Race Condition 수정 (#1263)
**문제**:
- 세션 busy→idle 전환 시, LLM이 output 생성 전 (빈 응답 또는 API delay)
- checkCompletionConditions() returns true (0 incomplete todos + 0 busy children = complete)
- Runner가 실제 작업 전에 'All tasks completed'로 exit

**수정**:
- hasReceivedMeaningfulWork 플래그 추가 (EventState)
- 플래그 설정 조건:
  - assistant text content
  - tool execution
  - message update with actual content
  - (모두 main session scope)
- Completion check guard: meaningful work 관찰 전까지 skip

**테스트**:
- 6개 new test cases (race condition scenarios)

---

### 🎨 v3.1.7 주요 변경사항

#### MCP OAuth 2.1 완전 구현 (#1169)
**RFC 준수**:
- RFC 7591: Dynamic Client Registration
- RFC 9728: Proof of Resource Metadata (PRM)
- RFC 8414: Authorization Server discovery
- RFC 8707: Resource Indicators

**구현**:
- McpOAuthProvider: full-spec implementation
- Secure token storage: {host}/{resource} 키 포맷
- Dynamic port OAuth callback server
- Step-up authorization handler
- SkillMcpManager 통합

**CLI Commands**:
- `mcp oauth login --server-url <url>`
- `mcp oauth logout --server-url <url>` (server-url 필수)
- `mcp oauth status`
- listAllTokens() / listTokensByHost()

**Doctor Integration**:
- MCP OAuth token status check
- Token redaction (credential leakage 방지)

**보안 개선** (cubic review):
- Server resource leak 수정 (close + reject on missing code/state)
- Command injection 수정: spawn array args, cross-platform
- explorer 사용 (Windows, cmd /c start 대신)
- 5분 timeout (provider callback server, indefinite hang 방지)
- Client registration persistence (token storage, process restart 대응)
- WWW-Authenticate params: quoted/unquoted 모두 지원 (RFC 2617)

**테스트**:
- McpOAuthProvider mock (deterministic CI)
- Storage.test.ts: OPENCODE_CONFIG_DIR save/restore
- Callback-server: promise rejection ordering 수정
- Provider test port: 8912 → 19877

**Co-authors**: justsisyphus, Sisyphus

#### LSP 클라이언트 vscode-jsonrpc 마이그레이션 (#1095)
**변경**:
- Custom JSON-RPC 구현 대체
- MessageConnection with StreamMessageReader/Writer
- Bun↔Node stream bridges 구현
- 기존 기능 보존: warmup, cleanup, capabilities

**결과**:
- ~60줄 코드 감소
- Protocol handling 개선
- Timeout clear on successful response (unhandled rejections 방지)

**Co-author**: justsisyphus

#### Config Override 수정 (#1219, #1235)
**Bug 1: override.category 미확장**:
- createBuiltinAgents()에서 override.category가 concrete config properties로 변환 안 됨
- applyCategoryOverride() helper 추가
- Standard agent loop, Sisyphus path, Atlas path에 적용

**Bug 2: reasoningEffort Priority**:
- Prometheus config-handler에서 reasoningEffort/textVerbosity/thinking이 spread ordering에 의존
- Explicit priority chains 사용 (direct > category)
- variant 패턴과 일치

**우선순위** (최종):
1. Direct override properties
2. Override category properties
3. Resolved variant (model fallback chain)
4. Factory base defaults

**Thinking 수정**:
- undefined check 사용 (explicit false 허용)

#### Background Agent Zombie Process 방지 (#1240, #1243)
**문제**:
- Parent exit 시 orphaned opencode processes

**수정**:
- BackgroundManager.shutdown()에서 모든 child session abort (client.session.abort())
- State clear 전에 abort
- onShutdown callback 추가 (TmuxSessionManager.cleanup() 트리거)
- Interactive bash session hook: tracked subagent opencode sessions abort (defense-in-depth)

**테스트**:
- 4개 tests (shutdown abort behavior, callback invocation)

**Closes**: #1240

#### Test Stability
**Config-handler Tests**:
- mock.module → spyOn 마이그레이션
- Cross-file cache pollution 방지

**Agent Tests** (#1227):
- connected-providers-cache fallback behavior 정렬
- Oracle resolves to openai/gpt-5.2 via cache (systemDefault 아님)
- systemDefaultModel 없이도 agents 생성 (cache fallback)

**Agent Description 통일**:
- OhMyOpenCode attribution

---

### 📦 v3.1.4-v3.1.6 주요 변경사항

#### Model Resolver 개선
**Connected Providers Cache 사용** (#1227):
- availableModels empty 시 connected providers cache 사용
- Proper provider selection (e.g., github-copilot instead of google)
- resolved.model을 userModel에서 제거 (was bypassing fallback chain)

**Fallback Chain Skip**:
- Cache 없을 때 fallback chain 완전 skip
- OpenCode Provider.defaultModel() 사용
- Plugin load before providers connect 시 잘못된 모델 선택 방지

**Provider Cache Missing Warning**:
- hasConnectedProvidersCache() false 시 toast 알림
- Model filtering disabled 표시
- OpenCode restart 권장

**Category Default Model**:
- availableModels empty 시 category default model 사용

#### Test Suite 안정성
**Mock.module Pollution 방지**:
- Sequential test execution (CI)
- afterEach cleanup
- _resetForTesting()에 sessionAgentMap.clear() 추가
- Provider cache mock (delegate-task tests)

**Configurable Timing**:
- timing.ts 모듈 (test-only)
- getTimingConfig()로 hardcoded wait times 대체
- Ralph-loop, session-state tests 활성화
- ~2s 내 완료 (timeout 없음)

**Test Isolation**:
- Explicit reset (mainSessionID test)
- Parallel test safety
- Spy restore (utils.test.ts, config-handler.test.ts)

**CI**:
- Split test execution (mock.module pollution 방지)
- find/xargs로 mock-heavy test files 제외
- Flaky test skip (sync variant test, CI timeout)

#### Delegate Task
**Subagent Model 명시 전달** (#1225):
- subagent_type 사용 시 matched agent의 model object 추출
- session.prompt/manager.launch에 명시적 전달
- Plugin-registered agents의 string→object 변환 보장

**Closes**: #1225

#### Start-Work 수정 (#1201)
**Session Agent 덮어쓰기 방지**:
- Already set 시 overwrite 안 함
- Subagent types는 parent model 상속

**Variant Inclusion**:
- StoredMessage model structure에 variant 포함
- Hook message injection에도 variant 포함

#### Config Validation
**'dev-browser' 추가**:
- BrowserAutomationProviderSchema에 valid option 추가
- Config rejection 방지 (전체 config 거부 회피)
- tmux.enabled 등 silent disable 방지
- JSDoc description 추가
- JSON schema 재생성

#### Look-at JSON Parse Errors (#1216)
**문제**:
- multimodal-looker agent 빈/잘못된 응답 시 SDK throws 'JSON Parse error: Unexpected EOF'

**수정**:
- session.prompt() 주변에 try-catch 추가
- JSON parse errors: user-friendly error message with troubleshooting guidance
- Generic prompt failures: error handling
- 테스트: 2개 error scenarios

#### Version Detection (#1194)
**문제**:
- npm global install + compiled binary 실행 시
- import.meta.url returns virtual bun path ($bunfs)
- getCachedVersion() returns null → 'unknown' version display

**수정**:
- process.execPath fallback 추가 (actual binary location)
- Walk up to find package.json

**Closes**: #1182

---

### 🏗️ v3.1.1-v3.1.3 주요 변경사항

#### Ultrawork & Plan Agent 개편
**Plan Agent 강제 호출**:
- Ultrawork prompt에 MANDATORY section 추가
- delegate_task(subagent_type='plan') 필수
- 'DELEGATE by default, work yourself only when trivial' 원칙

**Parallel Execution Rules**:
- Anti-pattern/correct pattern 예시
- Emoji (checkmark/cross) 제거 (PLAN_AGENT_SYSTEM_PREPEND)
- 4-step sequence 명확화

**TL;DR Section**:
- Quick summary, deliverables, effort estimate
- prometheus-prompt

**Agent Profile 권장**:
- Category + skills per task
- prometheus-prompt

**Execution Waves & Dependency Matrix**:
- 병렬화 강화
- prometheus-prompt

**Prometheus Agent 참조**:
- Ultrawork에서 plan agent → prometheus agent invocation으로 변경
- session_id resume workflow 추가

**Mandatory Output**:
- Parallel Execution Waves structure
- Dependency Matrix format
- TODO List with category + skills + parallel group
- Agent Dispatch Summary table

#### Prometheus Config
**Mode 변경**:
- 'primary' → 'all' (delegate_task 호출 허용)

**Plan Agent Demote Logic**:
- Prometheus config를 base로 사용
- Model inheritance 수정:
  - Plan config model 명시 시 우선
  - 없으면 prometheus model fallback
  - OpenCode plan config 유지

**Fallback Chain**:
- claude-opus-4-5 → gpt-5.2 → gemini-3-pro
- name field 추가 (agent.name undefined error 수정)

**Self-Delegation Block**:
- Prometheus가 자기 자신에게 delegate_task 호출 차단
- Subagent로 호출 시 delegate_task permission 부여

#### Plan Agent System Prepend
**Mandatory Sections**:
- Task Dependency Graph: blockers/dependents/reasons
- Parallel Execution Graph: wave structure
- Category + Skills recommendations per task
- Response format specification (exact structure)

**Visual Emphasis**:
- ASCII art banners

**Background Context Gathering**:
- explore/librarian agents를 background로 launch
- User request summarize
- List uncertainties
- Ask clarifying questions (100% clear까지)

#### Config Schema 확장
**AgentOverrideConfigSchema**:
- thinking
- reasoningEffort
- textVerbosity
- providerOptions
- maxTokens

**Think-mode Hook**:
- Agent-level thinking settings 존중
- Disabled or custom providerOptions

**Tests**:
- Agent-level thinking configuration override behavior

#### Subagent Question 차단
**SDK-level Blocking**:
- session.create()에 permission: [{ permission: 'question', action: 'deny' }]
- background-agent, delegate-task 적용

**Fallback Hook**:
- subagent-question-blocker hook (backup layer)
- tool.execute.before event에서 question tool intercept

**목적**:
- Subagent 자율 작업 보장
- 사용자 질문 불가

#### Agent Variant Resolution (#1179)
- Current model 기반 (static config 아님)

#### Librarian Thinking (Reverted)
- feat(librarian): conditionally enable thinking based on model type
- isGeminiModel helper (Gemini는 thinking 미지원)
- 32000 token budget (other models)
- **Revert**: commit 518dcea

---

### 🔧 v3.1.0 주요 변경사항

#### Tmux Integration 완전 구현 (#1125)
**State-first Architecture**:
- Decision engine 도입
- Column-based splittable calculation (getColumnCount, getColumnWidth)

**Decision Tree**:
- splittable → split
- k=1 eviction → close+spawn
- else → replace

**Replace Action**:
- tmux respawn-pane 사용 (layout 보존)
- Mass eviction 방지
- Oldest pane in-place replacement

**2D Grid Layout**:
- Divider-aware calculations (1 char)
- MIN_PANE_WIDTH: 53 → 52 (standard terminal에서 2 columns fit)
- enforceMainPaneWidth: (windowWidth - divider) / 2
- Virtual mainPane handling (close-spawn eviction loop)

**Decision Engine Tests**:
- 23 test cases

**Pane Spawn Callbacks**:
- Background sessions
- Sync sessions

**Co-authors**: justsisyphus, Sisyphus

**Ultraworked with Sisyphus**

#### Hooks & Compaction
**category-skill-reminder Hook** (#1123):
- 카테고리/스킬 리마인더

**Active Working Context**:
- Compaction summary에 추가:
  - Files
  - Code in progress
  - External references
  - State/variables
- Seamless continuation after context compaction

**Co-authors**: justsisyphus, Sisyphus

#### Connected Providers Cache (#1121)
**목적**:
- Model availability 체크
- provider-models.json 대체

**Co-authors**: justsisyphus, Sisyphus

#### Documentation
**Tmux Integration**:
- configurations.md: 전체 옵션
- features.md: Visual Multi-Agent with Tmux 섹션
- interactive_bash tool 문서화

**Server Mode & Shell Functions**:
- --port flag 요구사항 (tmux subagent pane spawning)
- Fish shell function: 자동 port 할당
- Bash/Zsh equivalent
- Subagent panes 동작 방식 (opencode attach flow)
- OPENCODE_PORT environment variable
- opencode serve command 참조

#### AGENTS.md Regeneration
- /init-deep via knowledge base

#### Test Cleanup
- Agent name casing 동기화 (#1128)

**Co-author**: justsisyphus

---

### 📚 기타 주요 변경사항

#### Environment Variables (#1157)
**OPENCODE_SERVER_PORT**:
- Custom port for OpenCode server

**OPENCODE_SERVER_HOSTNAME**:
- Custom hostname for OpenCode server

**목적**:
- 병렬 mission 실행 시 포트 충돌 방지
- Open Agent 등 orchestration tools 지원
- Default port 4096 대신 unique port 사용

#### Configuration Documentation (#1186)
**추가된 옵션**:
- disabled_commands: available commands
- comment_checker: custom_prompt
- notification: force_enable
- sisyphus tasks & swarm
- staleTimeoutMs (background_task)
- dynamic_context_pruning (experimental):
  - deduplication
  - supersede_writes
  - purge_errors

**Skills 확장**:
- sources
- custom skills

**Agents 확장**:
- category
- variant
- maxTokens
- thinking
- reasoningEffort
- textVerbosity
- providerOptions

**Categories 확장**:
- description
- is_unstable_agent

**LSP 확장**:
- env
- initialization
- disabled
- Detailed examples

**Hooks 추가**:
- auto-slash-command
- sisyphus-junior-notepad
- start-work

**Available Agents**:
- Schema의 모든 agents 포함

**Co-author**: GitHub Actions

#### Model Resolver 개선
**UI Model Selection** (#1158):
- uiSelectedModel parameter to resolveModelWithFallback()
- 우선순위: UI Selection → Config Override → Fallback → System Default
- config.model as uiSelectedModel in createBuiltinAgents()
- ProviderModelNotFoundError 수정 (model unset in config but selected in UI)

#### Category Model Resolution (#1074)
**오해 해소**:
- 이전 문서: categories automatically use built-in default models
- 실제: explicitly configured only, otherwise system default fallback

**변경**:
- Explicit warning (model resolution priority)
- 7개 built-in categories 문서화 (이전 2개만)
- Complete example config (all categories)
- Wasteful fallback scenario 설명
- 'variant' to supported category options

**Co-author**: DC

#### Context7 MCP (#1133)
**Optional Authorization Header**:
- CONTEXT7_API_KEY 설정 시에만 bearer auth 전송
- websearch와 동일한 패턴
- headers: undefined (env var 없을 때)

#### Builtin MCPs Override 방지 (#956)
- User MCP configs 덮어쓰기 방지

#### Ollama NDJSON Streaming (#1197)
**문제**:
- JSON Parse error when stream: true with Ollama
- NDJSON vs single JSON object mismatch

**Solutions**:
1. stream: false (권장)
2. Avoid tool agents
3. Wait for SDK fix

**Documentation**:
- Ollama Provider section (configurations.md)
- Supported models: qwen3-coder, ministral-3, lfm2.5-thinking
- Troubleshooting guide
- curl test command

**NDJSON Parser Utility**:
- src/shared/ollama-ndjson-parser.ts
- parseOllamaStreamResponse() (merge NDJSON lines)
- isNDJSONResponse() (format detection)
- TypeScript interfaces (Ollama message structures)
- JSDoc with usage examples
- Edge cases: malformed lines, stats aggregation
- Contribution to Claude Code SDK 가능

**Logger**:
- console.warn → log()

**Fixes**: #1124

#### Directory Agents Injector Auto-disable (#1204)
**OpenCode 1.1.37+ Native Support**:
- OPENCODE_NATIVE_AGENTS_INJECTION_VERSION constant
- directory-agents-injector auto-disable
- Deprecation notes

**Co-author**: justsisyphus

#### Tools Permission (#1192, #1199)
**Consistency**:
- look_at, call_omo_agent에 permission field 추가
- delegate_task, background-agent 패턴 일치
- Better error messages (Unauthorized failures)
- Actionable guidance

**목적**:
- Session creation failures 방지

#### Hooks Null Guard (#1054)
**문제**:
- /review, built-in commands가 tool.execute.after hooks를 undefined output과 함께 트리거
- output.metadata, output.output 접근 시 crash

**수정**:
- Null guard 추가

**Fixes**: #1035

**Co-author**: sisyphus-dev-ai

#### System-Reminder 키워드 트리거 방지 (#1155)
**문제**:
- <system-reminder> tags가 [search-mode], [analyze-mode] 등 keyword modes 트리거
- "search", "find", "explore" 등 단어 포함 시

**변경**:
- removeSystemReminders() 추가 (keyword detection 전 strip)
- hasSystemReminder() utility
- keyword-detector에서 text clean 후 pattern matching
- Comprehensive test coverage

**수정**:
- Automated system notifications가 MAXIMUM SEARCH EFFORT mode로 잘못 진입하는 문제

**Co-author**: TheEpTic

#### Skill Allowed-Tools YAML Array (#1163)
**지원 포맷**:
- Space-separated string: 'allowed-tools: Read Write Edit Bash'
- YAML array: 'allowed-tools: [Read, Write, Edit, Bash]'
- Multi-line YAML array

**문제**:
- YAML array format skills가 silently fail
- <available_skills> list에 나타나지 않음

**변경**:
- parseAllowedTools() 업데이트 (loader.ts, async-loader.ts, merger.ts)
- string | string[] 모두 처리
- SkillMetadata type 업데이트
- 4개 test cases (all formats)

**Fixes**: #1021

#### JSON.parse Fallback (#1191)
**Guard**:
- Hook handlers에서 JSON.parse(result.stdout) || "{}"
- Fallback 추가

**Co-author**: wangxiaoya.2000@bytedance.com

#### CLA Allowlist (#1195)
**문제**:
- Repository owner (code-yeongyu)가 allowlist에 없음
- 자기 PR에 CLA signature 요구

**수정**:
- code-yeongyu allowlist 추가

**Co-author**: 김연규

#### Compaction Verification State (#1144)
- Agent verification state 보존

#### Plugin Detection False Positive (#1148)
- Notification 수정

#### Non-AVX2 CPU Baseline Builds (#1154)
- CLI baseline builds 추가

#### Model Not Configured Error (#1139)
- Delegate-task에서 clear error 추가

#### Reverts
**v2.x to v3.x Migration Guide** (commit 655d511):
- AI agent가 project owner 승인 없이 merge
- Revert commit 1cb6b3d

**oh-my-opencode-slim** (commit 7dedd6c):
- 외부 fork promotion
- README 수정은 'ULTRA SAFE'로 평가
- Technical safety만 체크, owner 승인 필요성 간과
- Revert commit 912a56d

#### Sisyphus Foundation (Wave 1)
**Schema**:
- SisyphusTasksConfig
- SisyphusSwarmConfig

**Task JSON Schema**:
- Zod validation

**Mailbox IPC Protocol**:
- Message schemas

**Storage Utilities**:
- Claude Code path compatibility

**Tests**:
- 25 tests passing

#### Workflow Providers
**ZAI Coding**:
- zai-coding-plan provider
- GLM 4.7, GLM 4.6v models
- unspecified-low category: glm-4.7

**OpenAI**:
- GPT-5.2 models

**Auth**:
- OPENCODE_AUTH_JSON secret

#### QA Automation
**Prometheus**:
- 'Manual QA' → 'Automated Verification Only'
- ZERO USER INTERVENTION principle
- Placeholder examples → concrete executable commands

**Metis**:
- QA automation directives (output format)
- CRITICAL RULES: forbid user-intervention criteria

#### systemDefaultModel Optional (#1136)
**변경**:
- Mandatory model requirement 제거
- OpenCode built-in model fallback 사용 (user 미지정 시)
- model-resolver: undefined systemDefaultModel 처리
- Throw errors 제거: config-handler, utils, atlas, delegate-task
- Optional model scenarios tests

**Closes**: #1129

**Co-author**: justsisyphus

#### CLI Version Display Test (#1134)
**조사 결과**:
- CLI code correctly reads version from package.json
- 보고된 issue (bunx showing old version)는 caching issue

**테스트**:
- package.json에서 valid semver 읽기 확인

**Closes**: #1063

**Co-author**: justsisyphus

---

## CLA Signatures (30명 이상)

v3.1.9-v3.1.10:
- @robin-watcha
- @khduy
- @KonaEspresso94
- @kunal70006
- @Zacks-Zhang
- @Hisir0909
- @gabriel-ecegi
- @LeekJay
- @Lynricsy
- @mrdavidlaing
- @KennyDizi
- @youming-ai
- @rooftop-Owl
- @boguan
- @misyuari
- @ghtndl
- @itsmylife44
- @acamq
- @craftaholic
- @orientpine
- @Jeremy-Kr
- @moha-abdi
- @MoerAI
- @agno01
- @zycaskevin

---

## Breaking Changes

없음. 모든 변경사항은 하위 호환성 유지.

---

## Migration Notes

### 1. Kimi Provider 사용
```json
{
  "providers": {
    "kimi-for-coding": {
      "api_key": "$KIMI_API_KEY"
    }
  },
  "agents": {
    "atlas": {
      "fallbackChain": [
        "anthropic/claude-sonnet-4-5",
        "kimi-for-coding/k2p5",
        ...
      ]
    }
  }
}
```

### 2. Tmux Integration
```json
{
  "tmux": {
    "enabled": true,
    "layout": "two-column" // or "grid-4", "three-pane", etc.
  }
}
```

**Shell Function (Fish)**:
```fish
function omo
    set -l port (math (random) % 10000 + 10000)
    opencode serve --port $port &
    set -l pid $last_pid
    sleep 1
    opencode attach --port $port $argv
    kill $pid
end
```

### 3. MCP OAuth
```bash
# Login
mcp oauth login --server-url https://example.com/oauth

# Status
mcp oauth status

# Logout
mcp oauth logout --server-url https://example.com/oauth
```

### 4. Ollama Provider
```json
{
  "providers": {
    "ollama": {
      "base_url": "http://localhost:11434",
      "stream": false  // REQUIRED: NDJSON vs JSON mismatch
    }
  }
}
```

**Troubleshooting**: docs/troubleshooting/ollama-ndjson.md

### 5. Category Override
```json
{
  "agents": {
    "metis": {
      "category": "ultrabrain",
      "thinking": true,
      "reasoningEffort": "xhigh",
      "providerOptions": {
        "customKey": "value"
      }
    }
  }
}
```

### 6. Background Agent Shutdown
```typescript
// onShutdown callback 사용 예시
const backgroundManager = new BackgroundManager(
  config,
  (sessionId) => {
    // Cleanup logic (e.g., TmuxSessionManager.cleanup())
  }
);
```

---

## Performance Improvements

### Test Suite
- **FakeTimers**: 104.6s → 7.01s (15배)
- **FakeTimeouts**: ~26s → ~6.8s (executor tests)
- **CI**: test isolation, sequential execution으로 timeout 해결

### CI/CD
- **Build parallelism**: 7개 플랫폼 동시
- **OIDC**: fresh token at publish time, no rotation overhead

### Model Resolution
- **3-tier fallback**: cache → models.json → API (빠른 fallback)
- **Connected providers cache**: 즉시 provider 확인

---

## Security Improvements

### MCP OAuth
- RFC 7591, 9728, 8414, 8707 준수
- Secure token storage
- Step-up authorization
- Token redaction (credential leakage 방지)
- 5분 timeout (indefinite hang 방지)

### Command Injection Prevention
- explorer 사용 (Windows, cmd /c start 대신)
- spawn array args (cross-platform)

### Agent Isolation
- Subagent question tool 차단 (SDK-level + hook-level)
- Background agent zombie process 방지

---

## Documentation Improvements

### New Guides
- Ollama NDJSON troubleshooting
- Tmux integration (full options)
- MCP OAuth usage
- agent-browser installation (Playwright troubleshooting)

### Updated Docs
- configurations.md: 누락 옵션 전체 추가
- features.md: Visual Multi-Agent with Tmux
- AGENTS.md: knowledge base regeneration
- Category model resolution priority 명확화

---

## 주요 PR & Issues

### PRs
- #1304: Plugin initialization deadlock fix
- #1284: Test suite optimization (FakeTimers)
- #1274: Subagent UI model selection exclusion
- #1267: Tmux subagent dependency injection
- #1263: Run command race condition
- #1227: Delegate task category userModel chain
- #1225: Subagent model explicit passing
- #1219, #1235: Config override expansion
- #1216: Look-at JSON parse errors
- #1201: Start-work session agent overwrite prevention
- #1197: Ollama NDJSON streaming guide
- #1194: Version detection npm global install
- #1186: Configuration documentation
- #1169: MCP OAuth 2.1
- #1158: UI model selection respect
- #1157: OPENCODE_SERVER_PORT/HOSTNAME
- #1155: System-reminder keyword trigger prevention
- #1163: Skill allowed-tools YAML array
- #1179: Agent variant resolution
- #1191: JSON.parse fallback
- #1195: CLA allowlist (repo owner)
- #1136: systemDefaultModel optional
- #1134: CLI version display test
- #1133: Context7 Authorization header
- #1125: Tmux state-first architecture
- #1123: category-skill-reminder hook
- #1121: connected-providers-cache
- #1095: LSP vscode-jsonrpc migration
- #1074: Category model resolution docs

### Issues
- #1298: Prometheus sessions (start-work)
- #1301: Plugin deadlock
- #1240: Zombie processes
- #1200: Atlas fallback
- #1182: Version detection
- #1129: systemDefaultModel required
- #1124: Ollama NDJSON
- #1063: CLI version caching
- #1054: Hooks null guard
- #1035: tool.execute.after undefined output
- #1034: Migration guide (item 4)
- #1021: Skill YAML array
- #956: Builtin MCPs override

---

## 기여자 감사

### Co-authors & Contributors
- justsisyphus (multiple PRs)
- Sisyphus (Ultrawork collaboration)
- @robin-watcha (deadlock fix)
- TheEpTic (system-reminder fix)
- DC (category model resolution docs)
- wangxiaoya.2000@bytedance.com (JSON.parse fallback)
- 김연규 (CLA allowlist)
- GitHub Actions (documentation)
- sisyphus-dev-ai (multiple commits)

### CLA Signers (30+)
v3.1.9-v3.1.10에서 30명 이상 추가

---

**생성일**: 2026-01-31 15:00 KST
**생성자**: zsgg (Oh-My-OpenCode fork maintainer)
**도구**: Claude Sonnet 4.5 with oh-my-opencode sync-fork workflow
