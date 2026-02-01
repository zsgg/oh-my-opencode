# Fork Upstream 동기화 리포트

## 현재 동기화 상태

**동기화 일시**: 2026-02-01 21:00 KST
**비교 범위**: HEAD (7b26edc) → upstream/dev (62c8a67)
**총 커밋 수**: 48개
**버전 범위**: v3.1.10 → v3.2.0
**새 태그**: v3.1.11, v3.2.0

---

## v3.2.0 주요 변경사항

### 🎉 Major Release Highlights

#### 신규 에이전트: Hephaestus
- **[6482515]** `feat(agents): add Hephaestus - autonomous deep worker agent (#1287)`
  - 자율적으로 심층 작업을 수행하는 새로운 에이전트
  - 복잡한 멀티스텝 태스크 처리 능력 강화
  - 고도의 자율성과 문제 해결 능력

#### unstable-agent-babysitter 기본 활성화
- **[b6da473]** `feat(babysitting): make unstable-agent-babysitter always-on by default`
  - unstable 모델 자동 모니터링 기본 활성화
  - 불안정한 에이전트 실시간 감시 및 자동 복구
  - 프로덕션 안정성 대폭 향상

#### Windows 호환성 대폭 개선
- **[011eb48]** `fix: improve Windows compatibility and fix event listener issues (#1102)`
  - 이벤트 리스너 이슈 해결
  - Windows 환경 안정성 향상
- **[62c8a67]** `fix(ci): add shell: bash to retry action for Windows compatibility`
  - CI retry 액션 Windows 호환성
- **[d157940]** `fix(lsp): add Bun version check for Windows LSP segfault bug`
  - LSP segfault 버그 방지

---

## 카테고리별 상세 변경사항

### ✨ Features

#### 백그라운드 에이전트 & 모니터링
- **[64356c5]** `feat(hooks): add unstable-agent-babysitter hook for monitoring unstable background agents`
  - unstable 에이전트 모니터링용 훅
  - 실시간 상태 추적 및 복구 메커니즘
- **[a5b2ae2]** `feat(background-agent): add isUnstableAgent flag for unstable model detection`
  - unstable 모델 자동 감지 플래그
- **[520bf9c]** `feat: add thinking_max_chars option to background_output tool`
  - 백그라운드 출력 thinking 최대 문자 수 옵션
  - 출력 크기 제어 가능

#### 태스크 관리 & UI
- **[dbe1b25]** `feat(todo-continuation): show remaining tasks list in continuation prompt`
  - continuation 프롬프트에 남은 태스크 목록 표시
  - 작업 진행 상황 가시성 향상
- **[e63c568]** `feat(hooks): add /stop-continuation command to halt all continuation mechanisms (#1316)`
  - 모든 continuation 메커니즘 중단 명령어
  - 사용자 제어 강화

#### 시스템 & 환경
- **[d780707]** `feat(doctor): detect OpenCode desktop GUI installations on all platforms (#1352)`
  - 모든 플랫폼에서 OpenCode desktop GUI 설치 자동 감지
  - 환경 진단 기능 개선
- **[de6f4b2]** `feat(think-mode): add GLM-4.7 thinking mode support`
  - GLM-4.7 thinking mode 지원
  - 추론 능력 강화

#### CI/CD & 개발 도구
- **[c83150d]** `feat(ci): auto-generate structured release notes from conventional commits`
  - conventional commit에서 릴리스 노트 자동 생성
  - 릴리스 프로세스 자동화
- **[3808fd3]** `feat(command): add Oracle safety review for deployment check`
  - 배포 전 안전성 검토 추가
- **[c73314f]** `feat(skill-mcp-manager): enhance manager with improved test coverage`
  - MCP 매니저 기능 향상 및 테스트 커버리지 개선

---

### 🐛 Fixes

#### 프로세스 & 메모리 관리
- **[3e9a0ef]** `fix(background-agent): abort session on task completion to prevent zombie attach processes`
  - 태스크 완료 시 세션 중단으로 좀비 attach 프로세스 방지
  - 리소스 누수 해결
- **[bb181ee]** `fix(background-agent): track and cancel completion timers to prevent memory leaks (#1058)`
  - completion 타이머 추적 및 취소
  - 메모리 누수 방지
- **[b03e463]** `fix: prevent zombie processes with proper process lifecycle management (#1306)`
  - 적절한 프로세스 라이프사이클 관리
  - 좀비 프로세스 완전 제거
- **[6389da3]** `fix(tmux): send Ctrl+C before kill-pane and respawn-pane to prevent orphaned processes (#1329)`
  - tmux 고아 프로세스 방지
  - kill-pane 전 Ctrl+C 전송

#### 설정 & 프롬프트
- **[ffbca5e]** `fix(config): properly handle prompt_append for Prometheus agent (#1271)`
  - Prometheus 에이전트 prompt_append 올바른 처리
- **[4b5e38f]** `fix(hooks): make /stop-continuation one-time only and respect in session recovery`
  - /stop-continuation 1회성 실행
  - 세션 복구 시 설정 존중

#### 테스트 안정성
- **[7f9fcc7]** `fix(tests): properly stub notifyParentSession and fix timer-based tests`
  - notifyParentSession 스텁 적절히 설정
  - 타이머 기반 테스트 안정화
- **[96e7b39]** `fix: use _resetForTesting() consistently to prevent flaky tests (#1318)`
  - _resetForTesting() 일관된 사용
  - flaky 테스트 제거
- **[08439a5]** `fix(test): add missing ToolContext fields to test mocks`
  - 테스트 mock ToolContext 필드 추가

#### 환경 변수 & Git
- **[8bf3202]** `fix(non-interactive-env): always inject env vars for git commands`
  - git 명령어에 환경 변수 항상 주입
  - 비대화형 환경 지원

#### CI/CD
- **[e8cdab8]** `fix(ci): add retry logic for platform binary builds`
  - 플랫폼 바이너리 빌드 재시도 로직
  - 빌드 안정성 향상
- **[6667ace]** `fix(ci): remove deleted compaction-context-injector from test paths`
  - 삭제된 파일 테스트 경로에서 제거

#### 의존성
- **[f9bc23b]** `fix: regenerate bun.lock to restore vscode-jsonrpc dependency`
  - vscode-jsonrpc 의존성 복원

#### Rules Injector
- **[e48be69]** `fix(rules-injector): remove dead batch code, add .sisyphus support`
  - 사용하지 않는 batch 코드 제거
  - .sisyphus 파일 지원 추가

---

### 🔄 Refactor

#### 백그라운드 에이전트
- **[6bcc3c3]** `refactor(background-agent): show category in task completion notification`
  - 태스크 완료 알림에 카테고리 정보 표시
- **[09e738c]** `refactor(background-agent): optimize task timing and constants management`
  - 태스크 타이밍 최적화
  - 상수 관리 개선

#### Delegate Task
- **[6080bc8]** `refactor(delegate-task): improve session title format and add task_metadata block`
  - 세션 타이틀 포맷 개선
  - task_metadata 블록 추가

#### 에이전트 프롬프트
- **[ae6f4c5]** `refactor(agents): improve explore/librarian prompt examples with 4-part context structure`
  - explore/librarian 프롬프트 4부 구조로 개선
  - 컨텍스트 명확성 향상

#### 코드베이스 정리
- **[f146aef]** `refactor: major codebase cleanup - BDD comments, file splitting, bug fixes (#1350)`
  - 주요 코드베이스 정리
  - BDD 주석, 파일 분할, 버그 수정
- **[4a82ff4]** `Consolidate duplicate patterns and simplify codebase (#1317)`
  - 중복 패턴 통합
  - 코드베이스 단순화
- **[cbbc7bd]** `refactor: remove orphaned compaction-context-injector hook`
  - 고아 훅 제거

---

### 🧹 Chores

- **[0dafdde]** `chore: regenerate config schema`
- **[08c699d]** `chore: add test type declarations`
- **[ab54e6c]** `chore: treat minimax as unstable model requiring background monitoring`
- **[ac33b76]** `chore(command): remove hardcoded model from get-unpublished-changes`
- **[a24f1e9]** `chore: fix bun-build gitignore pattern to catch all variants`

---

### 📚 Docs

- **[72a8806]** `docs(background-task): enhance background_output tool description with full_session parameter`

---

### 🤝 CLA Signatures

- **[5f053cd]** `@code-yeongyu has signed the CLA in code-yeongyu/oh-my-opencode#1102`
- **[69e3bbe]** `@edxeth has signed the CLA in code-yeongyu/oh-my-opencode#1348`
- **[8c3feb8]** `@dmealing has signed the CLA in code-yeongyu/oh-my-opencode#1296`
- **[8b2c134]** `@taetaetae has signed the CLA in code-yeongyu/oh-my-opencode#1333`

---

### 🏷️ Releases

- **[711a347]** `release: v3.1.11`
- **[b3edd88]** `release: v3.2.0`

---

## 전체 커밋 목록

```
62c8a67 fix(ci): add shell: bash to retry action for Windows compatibility
b3edd88 release: v3.2.0
dbe1b25 feat(todo-continuation): show remaining tasks list in continuation prompt
6bcc3c3 refactor(background-agent): show category in task completion notification
b6da473 feat(babysitting): make unstable-agent-babysitter always-on by default
6080bc8 refactor(delegate-task): improve session title format and add task_metadata block
d780707 feat(doctor): detect OpenCode desktop GUI installations on all platforms (#1352)
6482515 feat(agents): add Hephaestus - autonomous deep worker agent (#1287)
5f053cd @code-yeongyu has signed the CLA in code-yeongyu/oh-my-opencode#1102
011eb48 fix: improve Windows compatibility and fix event listener issues (#1102)
ffbca5e fix(config): properly handle prompt_append for Prometheus agent (#1271)
6389da3 fix(tmux): send Ctrl+C before kill-pane and respawn-pane to prevent orphaned processes (#1329)
c73314f feat(skill-mcp-manager): enhance manager with improved test coverage
09e738c refactor(background-agent): optimize task timing and constants management
7f9fcc7 fix(tests): properly stub notifyParentSession and fix timer-based tests
8bf3202 fix(non-interactive-env): always inject env vars for git commands
ae6f4c5 refactor(agents): improve explore/librarian prompt examples with 4-part context structure
ab54e6c chore: treat minimax as unstable model requiring background monitoring
0dafdde chore: regenerate config schema
08c699d chore: add test type declarations
72a8806 docs(background-task): enhance background_output tool description with full_session parameter
64356c5 feat(hooks): add unstable-agent-babysitter hook for monitoring unstable background agents
a5b2ae2 feat(background-agent): add isUnstableAgent flag for unstable model detection
520bf9c feat: add thinking_max_chars option to background_output tool
3e9a0ef fix(background-agent): abort session on task completion to prevent zombie attach processes
e8cdab8 fix(ci): add retry logic for platform binary builds
f146aef refactor: major codebase cleanup - BDD comments, file splitting, bug fixes (#1350)
c83150d feat(ci): auto-generate structured release notes from conventional commits
711a347 release: v3.1.11
6667ace fix(ci): remove deleted compaction-context-injector from test paths
e48be69 fix(rules-injector): remove dead batch code, add .sisyphus support
3808fd3 feat(command): add Oracle safety review for deployment check
ac33b76 chore(command): remove hardcoded model from get-unpublished-changes
a24f1e9 chore: fix bun-build gitignore pattern to catch all variants
08439a5 fix(test): add missing ToolContext fields to test mocks
cbbc7bd refactor: remove orphaned compaction-context-injector hook
f9bc23b fix: regenerate bun.lock to restore vscode-jsonrpc dependency
69e3bbe @edxeth has signed the CLA in code-yeongyu/oh-my-opencode#1348
8c3feb8 @dmealing has signed the CLA in code-yeongyu/oh-my-opencode#1296
8b2c134 @taetaetae has signed the CLA in code-yeongyu/oh-my-opencode#1333
96e7b39 fix: use _resetForTesting() consistently to prevent flaky tests (#1318)
bb181ee fix(background-agent): track and cancel completion timers to prevent memory leaks (#1058)
b03e463 fix: prevent zombie processes with proper process lifecycle management (#1306)
4a82ff4 Consolidate duplicate patterns and simplify codebase (#1317)
4b5e38f fix(hooks): make /stop-continuation one-time only and respect in session recovery
e63c568 feat(hooks): add /stop-continuation command to halt all continuation mechanisms (#1316)
d157940 fix(lsp): add Bun version check for Windows LSP segfault bug
de6f4b2 feat(think-mode): add GLM-4.7 thinking mode support
```

---

## 주요 변경 영향도 분석

### 🔴 High Impact (즉시 영향)

1. **Hephaestus 에이전트 추가** (#1287)
   - 새로운 자율 에이전트로 복잡한 작업 처리
   - 심층 작업 능력 대폭 강화
   - 사용자 개입 최소화

2. **unstable-agent-babysitter 기본 활성화**
   - 불안정한 모델 자동 감시
   - 프로덕션 안정성 향상
   - 자동 복구 메커니즘

3. **Windows 호환성 대폭 개선** (#1102)
   - Windows 환경 안정성 향상
   - 이벤트 리스너 이슈 해결
   - LSP segfault 버그 방지

4. **프로세스 라이프사이클 관리 개선** (#1306, #1329, #1058)
   - 좀비 프로세스 완전 제거
   - 메모리 누수 방지
   - 리소스 관리 최적화

### 🟡 Medium Impact (점진적 개선)

1. **테스트 안정성 향상** (#1318)
   - flaky 테스트 제거
   - CI/CD 안정성 개선

2. **CI/CD 자동화 개선**
   - 릴리스 노트 자동 생성
   - 플랫폼 빌드 재시도 로직

3. **코드베이스 정리** (#1350, #1317)
   - 유지보수성 향상
   - 중복 코드 제거

4. **/stop-continuation 명령어** (#1316)
   - continuation 제어 강화
   - 사용자 제어권 향상

### 🟢 Low Impact (내부 개선)

1. 설정 스키마 재생성
2. 타입 선언 추가
3. 문서 개선
4. CLA 서명

---

## 마이그레이션 가이드

### Breaking Changes
- 없음 (하위 호환성 유지)

### Deprecated
- `compaction-context-injector` 훅 제거됨

### 새로운 기능 활용

#### 1. Hephaestus 에이전트 사용
```bash
# 복잡한 멀티스텝 작업에 활용
opencode delegate --agent hephaestus
```

#### 2. /stop-continuation 명령어
```bash
# 세션 중 continuation 중단 필요 시
/stop-continuation
```

#### 3. thinking_max_chars 옵션
```json
{
  "background_output": {
    "thinking_max_chars": 1000
  }
}
```

#### 4. unstable-agent-babysitter (자동 활성화)
- 별도 설정 불필요
- unstable 모델 사용 시 자동 모니터링
- 필요 시 disable 가능:
```json
{
  "hooks": {
    "unstable-agent-babysitter": {
      "enabled": false
    }
  }
}
```

---

## 관련 이슈 & PR

### Major PRs
- #1287: Hephaestus 에이전트 추가
- #1352: OpenCode GUI 감지 개선
- #1102: Windows 호환성 개선
- #1350: 코드베이스 대규모 정리
- #1316: /stop-continuation 명령어
- #1318: 테스트 안정성 개선
- #1317: 중복 패턴 제거
- #1329: tmux 프로세스 관리
- #1306: 좀비 프로세스 방지
- #1058: 메모리 누수 방지
- #1271: Prometheus 설정 처리

---

## 기여자 (Contributors)

- **YeonGyu-Kim** (@code-yeongyu) - 주요 기능 개발
- **justsisyphus** (@justsisyphus) - 테스트 안정성, 백그라운드 에이전트
- **Sisyphus** (@sisyphus-dev-ai) - Think mode, LSP 개선
- **Nguyễn Văn Tín** (@edxeth) - Windows 호환성
- **gabriel-ecegi** (@gabriel-ecegi) - Prometheus 설정
- **itsmylife44** (@itsmylife44) - Tmux 프로세스 관리
- **Nguyen Khac Trung Kien** (@dmealing) - 좀비 프로세스 방지
- **taetaetae** (@taetaetae) - CLA 기여
- **github-actions[bot]** - 자동화 및 릴리스

---

## 태그 정보

- **v3.1.11**: 2026-02-01 (중간 릴리스)
- **v3.2.0**: 2026-02-01 (현재 최신)

---

## 이전 동기화 이력

### v3.1.9 → v3.1.10 (2026-01-31)

#### 🚨 Critical Fixes

##### 플러그인 초기화 데드락 해결 (#1304)
- **문제**: config handler와 createBuiltinAgents가 fetchAvailableModels를 client와 함께 호출 → OpenCode 서버 API 요청 → 플러그인 초기화는 서버 응답 대기, 서버는 플러그인 초기화 완료 대기 → 교착상태
- **해결**: client 대신 undefined 전달하여 cache-only 모드 사용. cache 없으면 fallback chain의 첫 번째 모델 사용
- **테스트**: config-handler.test.ts, utils.test.ts에서 deadlock 방지 regression tests 추가

##### /start-work Prometheus 세션 수정 (#1298)
- **문제**: start-work에서 Prometheus 세션이 잘못된 agent 사용
- **해결**: atlas로 항상 전환하도록 수정

##### Momus 에이전트 프롬프트 리팩토링
- **변경**: 392줄 → 125줄로 단순화
- **핵심**: APPROVAL BIAS 추가 - 기본 승인, blocker만 거부
- **제한**: 최대 3개 이슈만 거부

#### 🎯 주요 업데이트

##### 테스트 스위트 최적화 (#1284)
- **FakeTimers 구현**: ~100줄 custom implementation
- **속도 향상**: 104.6s → 7.01s (15배)
- **Race condition 수정**

##### Kimi Provider 완전 통합
- kimi-for-coding 프로바이더 추가
- Model ID: k2p5 (kimi-k2.5)
- 모든 주요 에이전트 fallback chain에 통합

##### Tmux Integration 완전 구현 (#1125)
- State-first Architecture
- 2D Grid Layout
- Decision Engine

##### MCP OAuth 2.1 완전 구현 (#1169)
- RFC 7591, 9728, 8414, 8707 준수
- Secure token storage
- Dynamic port OAuth callback server

##### LSP 클라이언트 vscode-jsonrpc 마이그레이션 (#1095)
- Custom JSON-RPC 구현 대체
- ~60줄 코드 감소

자세한 내용은 이전 섹션 참조.

---

*생성 일시: 2026-02-01 21:00 KST*
*생성자: zsgg (Oh-My-OpenCode fork maintainer)*
*생성 도구: sync-fork 슬래시 커맨드*
