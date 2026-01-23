## Upstream Changelog

**동기화 일시**: 2026-01-23 21:26
**비교 범위**: HEAD..upstream/dev

### 변경사항 요약

#### Features (5개)
- [629a4d3] feat(shared): add agent display names module
- [8806ed1] feat(publish): add platform binary verification steps
- [37e1a06] feat(agents): add aggressive resume instructions to Atlas prompt
- [3062277] feat(agents): add zai-coding-plan/glm-4.6v fallback for multimodal-looker
- [e16bbbc] feat: show warning toast when model cache is not available

#### Fixes (18개)
- [9b12e2a] fix(cli): update zai-coding-plan hints to include multimodal-looker
- [7093583] fix(lsp): add data dir to LSP server detection paths
- [810dd93] fix(skill): enforce agent restriction in createSkillTool
- [1a901a5] fix(ci): build Windows binary natively to fix segfault
- [f8155e7] fix(session): preserve custom agent after switching
- [39d2d44] fix(tools): conditionally register look_at when multimodal-looker enabled
- [15c4637] fix(hooks): use unix shell syntax for bash tool on all platforms
- [599fad0] fix(atlas): capture stderr from git commands to prevent help text leak
- [afbdf69] fix(model-resolver): use first fallback entry when model cache unavailable
- [57b1043] fix(agents): use resolved variant from fallback chain instead of requirement default
- [75158ca] fix(atlas): register tool.execute.before and pass backgroundManager
- [ab3e622] fix: use cache file for model availability instead of SDK calls
- [f434888] fix: model fallback properly falls through to system default
- [71474bb] fix(delegate-task): reset model cache between tests
- [8df5679] fix(test): update model-fallback snapshots for github-copilot model naming
- [6e84a14] fix(model-resolver): return variant from fallback chain, handle model name normalization
- [be9d6c0] fix(doctor): improve AST-Grep NAPI detection for bunx environments
- [45fe957] fix(doctor): handle file:// protocol for local dev plugin detection

#### Refactor (5개)
- [4e42888] refactor(migration): normalize agent keys to lowercase
- [c6d6bd1] refactor(models): update agent/category fallback chains
- [6dfe091] refactor(atlas): rewrite prompt with lean orchestrator structure (78% 축소)
- [aa6355c] refactor(atlas): improve delegation guidance in validation step
- [0e18efc] refactor(keyword-detector): change keyword injection from synthetic to direct message transform

#### Docs (6개)
- [bee8b37] docs: add model configuration section to overview and quick start to configurations
- [fc47a7a] docs: update multimodal-looker model name and fallback chain
- [262c711] docs(agents): update AGENTS.md with current commit hash and line counts
- [3268782] docs: rename Orchestrator-Sisyphus to Atlas
- [7de376e] docs: regenerate all AGENTS.md files with updated structure
- [91d85d3] docs: add Korean (한국어) README

#### Others (4개)
- [e2f8729] @veetase has signed the CLA
- [af9beee] @Ssoon-m has signed the CLA
- [2c81c8e] @l3aro has signed the CLA
- [dab3e1e] release: v3.0.0-beta.13

---

### 전체 커밋 목록

#### 4e42888: refactor(migration): normalize agent keys to lowercase
에이전트 키를 소문자로 정규화

#### 629a4d3: feat(shared): add agent display names module
에이전트 표시 이름 모듈 추가

#### 8806ed1: feat(publish): add platform binary verification steps
플랫폼 바이너리 검증 단계 추가:
- STEP 8.5: publish-platform 워크플로우 완료 대기
- STEP 8.6: npm에서 7개 플랫폼 바이너리 패키지 검증
- 플랫폼별 실패 에러 핸들링 추가

#### e2f8729: @veetase has signed the CLA in code-yeongyu/oh-my-opencode#985
CLA 서명 (veetase)

#### bee8b37: docs: add model configuration section to overview and quick start to configurations
overview 및 quick start에 모델 설정 섹션 추가

#### 37e1a06: feat(agents): add aggressive resume instructions to Atlas prompt
Atlas 프롬프트에 적극적인 resume 지시사항 추가

#### fc47a7a: docs: update multimodal-looker model name and fallback chain
multimodal-looker 모델명 및 fallback chain 업데이트

#### 9b12e2a: fix(cli): update zai-coding-plan hints to include multimodal-looker
CLI 힌트에 multimodal-looker 포함

#### 3062277: feat(agents): add zai-coding-plan/glm-4.6v fallback for multimodal-looker
multimodal-looker에 zai-coding-plan/glm-4.6v fallback 추가

#### 7093583: fix(lsp): add data dir to LSP server detection paths (#992)
LSP 서버 감지 경로에 data 디렉토리 추가:
- OpenCode는 LSP 서버를 ~/.local/share/opencode/bin에 다운로드
- isServerInstalled()가 ~/.config/opencode/bin만 확인하던 문제 수정
- ~/.local/share/opencode/bin을 감지 경로에 추가

#### 810dd93: fix(skill): enforce agent restriction in createSkillTool (#1018)
createSkillTool에서 에이전트 제한 강제:
- agent-restricted skill이 ctx 또는 ctx.agent가 undefined일 때도 실행되던 문제 수정
- 조건 변경: `skill.definition.agent && ctx?.agent && ...` → `skill.definition.agent && (!ctx?.agent || ...)`
- 정확히 매칭되는 에이전트가 있을 때만 제한된 스킬 실행 가능

#### 1a901a5: fix(ci): build Windows binary natively to fix segfault (#1019)
Windows 바이너리 세그멘테이션 폴트 수정:
- Bun의 Linux→Windows 크로스 컴파일이 충돌 유발
- windows-latest runner 사용으로 네이티브 빌드
- Fixes #873, #844

#### f8155e7: fix(session): preserve custom agent after switching (#1017)
에이전트 전환 후 커스텀 에이전트 유지:
- chat.message 핸들러에서 updateSessionAgent 대신 setSessionAgent 사용
- UI 스위치로 설정한 커스텀 에이전트가 기본 에이전트로 덮어써지지 않도록 수정
- Fixes #893

#### 39d2d44: fix(tools): conditionally register look_at when multimodal-looker enabled (#1016)
multimodal-looker 활성화 시에만 look_at 툴 등록

#### 15c4637: fix(hooks): use unix shell syntax for bash tool on all platforms (#1015)
모든 플랫폼에서 bash 툴에 unix shell 문법 사용:
- bash 툴은 Windows에서도 Unix-like shell(Git Bash, WSL 등) 사용
- 동적 shell 타입 감지 대신 항상 unix export 문법 사용
- Fixes #983, #889

#### 262c711: docs(agents): update AGENTS.md with current commit hash and line counts
AGENTS.md를 현재 커밋 해시 및 라인 수로 업데이트

#### 599fad0: fix(atlas): capture stderr from git commands to prevent help text leak
git 명령어 stderr 캡처하여 help 텍스트 누출 방지:
- git 명령 실패 시 stderr로 출력되는 help 텍스트가 터미널에 표시되던 문제
- stdio: ['pipe', 'pipe', 'pipe'] 추가하여 stderr 캡처

#### afbdf69: fix(model-resolver): use first fallback entry when model cache unavailable
모델 캐시 없을 때 첫 번째 fallback 항목 사용:
- availableModels가 비어있을 때 systemDefault 대신 fallbackChain 첫 항목 사용
- CI에서 캐시 파일 없어도 설정된 모델 사용 보장
- model-resolution 체크가 'warn' 대신 'pass' 반환하도록 수정

#### af9beee: @Ssoon-m has signed the CLA in code-yeongyu/oh-my-opencode#1014
CLA 서명 (Ssoon-m)

#### c6d6bd1: refactor(models): update agent/category fallback chains
에이전트/카테고리 fallback chain 업데이트:
- quick: openai fallback → opencode/grok-code로 교체
- writing: sonnet과 gpt 사이에 zai-coding-plan/glm-4.7 추가
- unspecified-low: gpt-5.2 → gpt-5.2-codex (medium)
- Sisyphus: openai 전에 zai/glm-4.7 추가, gpt-5.2-codex (medium) 사용
- Momus & Metis: gemini-3-pro에 variant 'max' 추가
- explore: haiku (anthropic/opencode) → grok-code (opencode)로 단순화

#### 57b1043: fix(agents): use resolved variant from fallback chain instead of requirement default
fallback chain의 resolved variant 사용:
- resolveModelWithFallback()가 반환한 variant를 무시하던 문제 수정
- oracle 같은 에이전트가 undefined 대신 variant 'high'를 정확히 받도록 수정

#### 6dfe091: refactor(atlas): rewrite prompt with lean orchestrator structure
Atlas 프롬프트를 lean orchestrator 구조로 재작성:
- 프롬프트 크기 ~1280 → ~280 라인으로 축소 (78% 감소)
- 프롬프트 엔지니어링 원칙 적용: 모델이 이미 아는 내용 제거
- 깔끔한 XML 섹션 사용: identity, mission, delegation_system, workflow 등
- 6-section delegation 포맷 채택 (TASK, EXPECTED OUTCOME, REQUIRED TOOLS, MUST DO, MUST NOT DO, CONTEXT)
- 유지: identity, category+skills 시스템, notepad 프로토콜, 병렬화, 프로젝트 레벨 QA
- 중요 override를 끝부분에 강력한 프레이밍과 함께 통합

#### 75158ca: fix(atlas): register tool.execute.before and pass backgroundManager
Atlas hook 등록 및 backgroundManager 전달:
- tool.execute.before 핸들러에서 atlasHook?.['tool.execute.before'] 호출 추가
- createAtlasHook에 backgroundManager 옵션 전달하여 bg task 체크
- backgroundManager 초기화 이후로 atlasHook 선언 이동

#### e16bbbc: feat: show warning toast when model cache is not available
모델 캐시 없을 때 경고 토스트 표시:
- isModelCacheAvailable() 추가하여 캐시 파일 존재 확인
- 세션 시작 시 캐시 누락되면 경고 토스트 표시
- 'opencode models --refresh' 실행 또는 재시작 제안

#### ab3e622: fix: use cache file for model availability instead of SDK calls
SDK 호출 대신 캐시 파일로 모델 가용성 확인:
- fetchAvailableModels가 ~/.cache/opencode/models.json에서 읽도록 변경
- SDK client.config.providers() 호출로 인한 플러그인 시작 지연 방지
- doctor model-resolution 체크에 캐시의 가용 모델 표시
- 캐시 정보 표시: provider 수, 모델 수, refresh 명령어

#### f434888: fix: model fallback properly falls through to system default
모델 fallback이 system default로 올바르게 fall through되도록 수정:
- model-resolver의 Step 3 제거 (unavailable해도 첫 fallbackChain 항목을 강제하던 로직)
- delegate_task에 sisyphusJuniorModel 옵션 추가하여 agents["Sisyphus-Junior"] 모델 override 반영
- 새로운 fallback 동작을 반영하도록 테스트 업데이트

#### 2c81c8e: @l3aro has signed the CLA in code-yeongyu/oh-my-opencode#999
CLA 서명 (l3aro)

#### 3268782: docs: rename Orchestrator-Sisyphus to Atlas
Orchestrator-Sisyphus를 Atlas로 이름 변경

#### dab3e1e: release: v3.0.0-beta.13
v3.0.0-beta.13 릴리스

#### 71474bb: fix(delegate-task): reset model cache between tests
테스트 간 모델 캐시 초기화

#### aa6355c: refactor(atlas): improve delegation guidance in validation step
validation 단계의 delegation 가이던스 개선

#### 8df5679: fix(test): update model-fallback snapshots for github-copilot model naming
github-copilot 모델 네이밍에 맞게 model-fallback 스냅샷 업데이트

#### 6e84a14: fix(model-resolver): return variant from fallback chain, handle model name normalization
fallback chain에서 variant 반환 및 모델명 정규화 처리:
- ModelResolutionResult 반환 타입에 variant 추가
- 매칭된 fallback 항목의 variant 반환
- Claude 모델의 hyphen/period 차이를 위한 normalizeModelName() 추가
- github-copilot 모델명을 위한 transformModelForProvider() 추가
- delegate-task가 resolved variant 사용 (사용자 설정 우선)
- 새 fallback 동작에 맞게 테스트 기대값 수정

#### 7de376e: docs: regenerate all AGENTS.md files with updated structure
업데이트된 구조로 모든 AGENTS.md 파일 재생성

#### 0e18efc: refactor(keyword-detector): change keyword injection from synthetic to direct message transform
키워드 주입 방식 변경 (synthetic → 직접 메시지 변환):
- collector.register() 대신 output.parts[textIndex].text 직접 수정
- 모든 키워드 타입(ultrawork, search, analyze)이 사용자 메시지 텍스트 앞에 추가
- 메시지 포맷: 키워드 메시지 + '---' 구분자 + 원본 텍스트
- collector 등록 대신 텍스트 변환을 검증하도록 테스트 업데이트
- 18개 테스트 모두 통과

#### 91d85d3: docs: add Korean (한국어) README
한국어 README 추가

#### be9d6c0: fix(doctor): improve AST-Grep NAPI detection for bunx environments
bunx 환경에서 AST-Grep NAPI 감지 개선:
- require.resolve() 대신 dynamic import 사용
- bunx로 실행 시 모듈이 ~/.config/opencode/node_modules에 존재해도 임시 실행 디렉토리에서 resolve되지 않던 문제 수정
- 일반적인 설치 위치에 대한 fallback 경로 체크 추가
- Fixes #898

#### 45fe957: fix(doctor): handle file:// protocol for local dev plugin detection
로컬 개발 플러그인 감지를 위한 file:// 프로토콜 처리
