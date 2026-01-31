# Upstream 동기화 요약 (157개 커밋)

**동기화 일시**: 2026-01-31 15:00 KST
**버전 범위**: v3.1.9 → v3.1.10

---

## 한 줄 요약

> **플러그인 초기화 데드락 수정, Kimi Provider 통합, Tmux 완전 구현, MCP OAuth 2.1, LSP vscode-jsonrpc 마이그레이션, 테스트 15배 속도 향상**

---

## 🔥 Critical Updates

### 1. 플러그인 초기화 데드락 해결 (#1304)
- **문제**: config handler ↔ OpenCode 서버 간 교착상태로 플러그인 시작 불가
- **해결**: cache-only 모드로 fetchAvailableModels 호출
- **영향**: 플러그인 시작 안정성 대폭 향상

### 2. Momus 에이전트 프롬프트 리팩토링
- 392줄 → 125줄 단순화
- APPROVAL BIAS: 기본 승인, blocker만 거부
- 최대 3개 이슈만 거부 (overwhelming feedback 방지)

### 3. 테스트 스위트 15배 속도 향상 (#1284)
- FakeTimers 구현: 104.6s → 7.01s
- FakeTimeouts: ~26s → ~6.8s
- CI 안정성 크게 개선

---

## 🎯 주요 신규 기능

### Model Resolution 개선
**3-tier Fallback System**:
1. provider-models cache
2. models.json
3. client.model.list() API

**Kimi Provider 통합**:
- kimi-for-coding 프로바이더 추가
- Model ID: k2p5 (kimi-k2.5)
- Atlas/Sisyphus/Prometheus fallback chain에 추가

**RequiresModel Field**:
- 조건부 에이전트 활성화 (model 가용성 체크)

### Agent & Category 개편
**Ultrabrain Category**:
- Deep work mindset 재설계
- 코드 스타일 요구사항 강화 (codebase pattern 검색 필수)

**Artistry Category**:
- Ultrawork-mode에 추가
- 비관습적 문제 처리 (Oracle은 conventional problems)

**Subagent UI Model Selection 제외**:
- explore/librarian/oracle은 자체 fallback chain 사용

### Tmux Integration 완전 구현 (#1125)
**State-first Architecture**:
- Decision engine with replace action
- 2D grid layout (divider-aware)
- MIN_PANE_WIDTH: 53 → 52

**Features**:
- tmux respawn-pane으로 layout 보존
- Mass eviction 방지
- Background/sync sessions pane spawn callbacks

### MCP OAuth 2.1 (#1169)
**RFC 준수**:
- RFC 7591, 9728, 8414, 8707

**CLI Commands**:
- `mcp oauth login/logout/status`

**보안**:
- Secure token storage
- Step-up authorization
- 5분 timeout, credential redaction

### LSP vscode-jsonrpc 마이그레이션 (#1095)
- Custom JSON-RPC 구현 대체
- ~60줄 코드 감소
- Protocol handling 개선

---

## 🔧 주요 버그 수정

### Delegate Task
- **Category UserModel Chain 복원** (#1227): resolved.model이 category default 포함하도록 수정

### Run Command
- **Race Condition 수정** (#1263): hasReceivedMeaningfulWork 플래그로 premature exit 방지

### Background Agent
- **Zombie Process 방지** (#1240, #1243): shutdown 시 child session abort

### Model Resolver
- **UI Model Selection 존중** (#1158): 우선순위 명확화
- **Connected Providers Cache 사용** (#1227): availableModels empty 시 fallback
- **Fallback Chain Skip**: cache 없을 때 OpenCode defaultModel 사용

### Config
- **Override.category 확장** (#1219, #1235): concrete config properties로 변환
- **'dev-browser' 추가**: BrowserAutomationProviderSchema validation

### Look-at
- **JSON Parse Errors 처리** (#1216): user-friendly error message

### Version Detection
- **npm Global Install 수정** (#1194): process.execPath fallback

---

## 🚀 CI/CD 개선

### OIDC Trusted Publishing 전환
**Benefits**:
- Fresh OIDC token at publish time
- 토큰 rotation 불필요
- 빌드/퍼블리시 분리 (아티팩트 재사용)

**Platform Publish**:
- 7개 플랫폼 동시 빌드
- Windows 7z 지원, explorer 사용 (shell injection 방지)

### Test Isolation
- Mock.module pollution 방지 (sequential execution)
- Configurable timing (timing.ts)
- Spy restore, afterEach cleanup

---

## 📦 v3.1.1-v3.1.3 주요 변경사항

### Ultrawork & Plan Agent
- Plan agent 강제 호출 (MANDATORY section)
- TL;DR, agent profile, execution waves
- Prometheus mode 'all' (delegate_task 허용)

### Prometheus Config
- Fallback chain: opus → gpt-5.2 → gemini-3-pro
- Self-delegation block

### Subagent Question 차단
- SDK-level + hook-level blocking
- 자율 작업 보장

### Agent Variant Resolution
- Current model 기반 (static config 아님)

---

## 🔧 v3.1.0 주요 변경사항

### Connected Providers Cache (#1121)
- Model availability 체크
- provider-models.json 대체

### Hooks & Compaction
- **category-skill-reminder hook** (#1123)
- **Active working context**: compaction summary에 files/code/references/state 포함

### Documentation
- Tmux integration (full options)
- Server mode & shell functions

---

## 📚 기타 주요 변경사항

### Environment Variables (#1157)
- OPENCODE_SERVER_PORT/HOSTNAME: 병렬 mission 포트 충돌 방지

### Configuration Documentation (#1186)
- 누락 옵션 전체 추가 (disabled_commands, sisyphus tasks/swarm, dynamic_context_pruning)

### Ollama NDJSON (#1197)
- stream: false 필수 (NDJSON vs JSON mismatch)
- Troubleshooting guide

### System-Reminder 키워드 트리거 방지 (#1155)
- removeSystemReminders() (keyword detection 전 strip)

### Skill Allowed-Tools YAML Array (#1163)
- YAML array format 지원

### systemDefaultModel Optional (#1136)
- OpenCode built-in model fallback 사용

### Reverts
- v2.x to v3.x migration guide (AI agent 무단 merge)
- oh-my-opencode-slim (외부 fork promotion)

### Sisyphus Foundation (Wave 1)
- SisyphusTasksConfig/SwarmConfig schema
- Task JSON schema, Mailbox IPC protocol

---

## 📊 통계

| 카테고리 | 개수 |
|----------|------|
| Features | 30+ |
| Fixes | 40+ |
| Refactor | 20+ |
| Docs | 15+ |
| Tests | 20+ |
| Chore | 10+ |
| CLA Signatures | 30+ |
| **총합** | **157개** |

---

## 🎯 영향 범위

### ✅ 개발자
- **Kimi Provider**: 새로운 모델 옵션
- **Tmux Integration**: Visual Multi-Agent 워크플로우
- **MCP OAuth**: 보안 강화
- **LSP vscode-jsonrpc**: 안정성 향상
- **Config Override**: category 확장, thinking/reasoningEffort/providerOptions
- **Delegate Task**: category userModel chain 복원

### ✅ 사용자
- **플러그인 안정성**: 데드락 해결
- **테스트 속도**: 15배 향상
- **Model Resolution**: 3-tier fallback, fuzzy matching
- **Agent 개편**: Ultrabrain, Artistry
- **Background Agent**: zombie process 방지
- **Ollama 지원**: NDJSON troubleshooting

### ✅ CI/CD
- **OIDC Publishing**: 토큰 rotation 불필요
- **Build Parallelism**: 7개 플랫폼 동시
- **Test Isolation**: mock.module pollution 방지

---

## 🔐 보안 개선

- **MCP OAuth 2.1**: RFC 준수, secure token storage
- **Command Injection 방지**: explorer 사용 (Windows)
- **Agent Isolation**: subagent question tool 차단
- **Token Redaction**: credential leakage 방지

---

## 📖 Migration Guide

### Kimi Provider
```json
{
  "providers": {
    "kimi-for-coding": {
      "api_key": "$KIMI_API_KEY"
    }
  }
}
```

### Tmux Integration
```json
{
  "tmux": {
    "enabled": true,
    "layout": "two-column"
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

### MCP OAuth
```bash
mcp oauth login --server-url https://example.com/oauth
mcp oauth status
mcp oauth logout --server-url https://example.com/oauth
```

### Ollama Provider
```json
{
  "providers": {
    "ollama": {
      "base_url": "http://localhost:11434",
      "stream": false  // REQUIRED
    }
  }
}
```

### Category Override
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

---

## 🔗 주요 PR & Issues

### Critical PRs
- #1304: Plugin initialization deadlock fix ⭐
- #1284: Test suite optimization (15배 속도) 🏆
- #1169: MCP OAuth 2.1 🔐
- #1095: LSP vscode-jsonrpc migration
- #1125: Tmux state-first architecture

### Major PRs
- #1227: Delegate task category userModel chain
- #1263: Run command race condition
- #1240, #1243: Background agent zombie process
- #1219, #1235: Config override expansion
- #1197: Ollama NDJSON streaming guide

### Issues
- #1298: Prometheus sessions
- #1301: Plugin deadlock
- #1124: Ollama NDJSON
- #1182: Version detection
- #1129: systemDefaultModel required

---

## 🙏 기여자 감사

### Co-authors
- justsisyphus (multiple PRs)
- Sisyphus (Ultrawork collaboration)
- @robin-watcha (deadlock fix)
- TheEpTic (system-reminder fix)
- DC (category model resolution docs)
- wangxiaoya.2000@bytedance.com
- 김연규
- GitHub Actions

### CLA Signers (30+)
- @robin-watcha, @khduy, @KonaEspresso94, @kunal70006, @Zacks-Zhang, @Hisir0909, @gabriel-ecegi, @LeekJay, @Lynricsy, @mrdavidlaing, @KennyDizi, @youming-ai, @rooftop-Owl, @boguan, @misyuari, @ghtndl, @itsmylife44, @acamq, @craftaholic, @orientpine, @Jeremy-Kr, @moha-abdi, @MoerAI, @agno01, @zycaskevin

---

## ⚡ Performance Highlights

- **Test Suite**: 104.6s → 7.01s (15배 ⚡)
- **CI/CD**: 7개 플랫폼 동시 빌드
- **Model Resolution**: 3-tier fallback (빠른 cache)

---

## 🎉 주목할 만한 변경사항

1. **플러그인 초기화 데드락 해결**: 가장 critical한 수정
2. **테스트 15배 속도 향상**: FakeTimers/FakeTimeouts
3. **Kimi Provider 통합**: 새로운 모델 옵션
4. **Tmux 완전 구현**: Visual Multi-Agent 워크플로우
5. **MCP OAuth 2.1**: 보안 강화
6. **LSP vscode-jsonrpc**: 안정성 향상
7. **Momus 프롬프트 단순화**: 392줄 → 125줄
8. **OIDC Publishing**: 토큰 rotation 불필요

---

## 📝 다음 단계

1. ✅ fork.md 작성 완료
2. ✅ fork-summary.md 작성 완료
3. ⏳ Upstream remote 확인
4. ⏳ 백업 태그 생성
5. ⏳ Rebase upstream/dev
6. ⏳ Force push to origin
7. ⏳ 빌드 및 검증

---

## 💡 권장 사항

### 즉시 적용 권장
- **플러그인 안정성**: v3.1.10으로 업그레이드
- **테스트 환경**: CI timeout 개선 혜택

### 선택적 적용
- **Kimi Provider**: API key 있으면 fallback chain에 추가
- **Tmux Integration**: Visual Multi-Agent 워크플로우 필요 시
- **MCP OAuth**: MCP 서버 인증 필요 시

### 주의 사항
- **Ollama**: stream: false 필수 설정
- **Category Override**: thinking/reasoningEffort/providerOptions 활용

---

**생성일**: 2026-01-31 15:00 KST
**생성자**: zsgg (Oh-My-OpenCode fork maintainer)
**도구**: Claude Sonnet 4.5 with oh-my-opencode sync-fork workflow
