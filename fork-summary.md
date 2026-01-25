# Upstream 동기화 요약 (62개 커밋)

**동기화 일시**: 2026-01-26 21:00
**버전**: v3.0.1

---

## 한 줄 요약

> **v3.0.0 정식 릴리스, 브라우저 자동화 지원, tmux pane 관리, agent key 소문자 정규화 완료**

---

## 핵심 변경사항

### 1. 🎉 v3.0.0 & v3.0.1 정식 릴리스

- v3.0.0-beta.14 → v3.0.0-beta.15 → v3.0.0-beta.16 → **v3.0.0** → **v3.0.1**
- 안정화된 정식 버전 출시

---

### 2. 🌐 브라우저 자동화 (신규 기능)

**3개 관련 커밋**

| 커밋 | 내용 |
|------|------|
| 3af30b0 | agent-browser option for browser automation 추가 |
| bccc943 | dev-browser skill (Windows 지원 포함) 추가 |
| 05904ca | agent-browser 상세 설치 가이드 (Playwright troubleshooting) |

**영향**: Playwright 기반 브라우저 자동화가 가능해짐

---

### 3. 🖥️ tmux pane 관리 (aead4ae)

- background agent sessions를 위한 tmux pane 관리 추가
- 백그라운드 에이전트 실행 시 tmux 창에서 관리 가능

---

### 4. 🔤 Agent Key 소문자 정규화 완료

**대규모 리팩토링 - 모든 agent key가 소문자로 통일됨**

| 커밋 | 영역 |
|------|------|
| cc4deed | schema |
| 90292db | prometheus-hook |
| 91060c3 | agents utils |
| 12c9029 | plugin |
| dfc57d0 | model-requirements |
| c2247ae | prometheus agent 추가 및 정규화 |
| 7ed7bf5 | agents API calls |
| 444fbe3 | delegate-task |

**영향**: 설정 파일에서 agent 이름 작성 시 대소문자 신경 쓸 필요 없어짐

---

### 5. 🆕 새로운 기능들

| 커밋 | 기능 |
|------|------|
| 212baa6 | `/remove-deadcode` slash command 추가 (LSP-verified dead code removal) |
| 0aa8f48 | sisyphus-junior-notepad hook (조건부 notepad rules injection) |
| b55fd8d | explore fallback chain에 github-copilot/gpt-5-mini 추가 |
| f1a279a | config schema에 xhigh reasoningEffort 추가 |
| 063c759 | background_cancel(all=true) 시 상세 task 정보 표시 |

---

### 6. 🔧 Delegate Task 스키마 변경 (14f450b, 5a1da39)

**중요 변경사항**

- `resume` 파라미터 → `session_id`로 이름 변경
- `command` 파라미터 추가
- ultrawork에서 plan agent 참조를 명시적 `delegate_task(subagent_type="plan")` 구문으로 대체

---

### 7. 🐛 주요 버그 수정

| 이슈 | 해결 |
|------|------|
| skill/slashcommand descriptions 비동기 문제 | 208af05 |
| loadBuiltinCommands TypeError | 1c76e05 |
| ralph-loop 무한 루프 | 20cca35 |
| MCP disabled flag 서버 제거 안 됨 | cf23204 |
| built-in commands slashcommand에서 누락 | 9532680 |
| BackgroundManager concurrency limits 미적용 | 2a945dd |
| todo-continuation 무한 루프 | 58bb921 |
| question labels 30자 초과 문제 | 3a22c24, ec32dd6 |
| multimodal-looker fallback chain order | faf172a |
| model names OpenCode Zen catalog 불일치 | 04633ba |

---

### 8. 🧹 Dead Code 제거

| 커밋 | 제거된 항목 |
|------|------------|
| 043b1a3 | tools barrel의 dead re-exports |
| 512952f | deprecated config-path.ts |
| d9723e7 | unused background-compaction hook module |

---

### 9. 🌍 Website 초기화 (후에 제거됨)

**Next.js 15 웹사이트 프로젝트가 추가되었다가 CI 문제로 제거됨**

- ba93c42: Next.js 15 + @opennextjs/cloudflare 초기화
- 894a0fa: next-intl i18n, dark mode 지원
- 58459e6: header, sidebar, footer, navigation
- 0e1d4e5: website 디렉토리 제거 (CI test failures)

---

### 10. 📜 CLA 서명 (9명)

@kvokka, @potb, @jsl9208, @sadnow, @ThanhNguyxn, @AamiRobin, @AndersHsueh, @gongxh0901, @RouHim

---

## 통계

| 카테고리 | 개수 |
|----------|------|
| Features | 12개 |
| Fixes | 18개 |
| Refactor | 10개 |
| Docs | 4개 |
| Tests | 2개 |
| Chore | 7개 |
| CLA | 9개 |
| **총합** | **62개** |

---

## 영향 범위

### ✅ 개발자
- Agent key 소문자 정규화로 설정 간소화
- delegate_task 스키마 변경 (resume → session_id)
- 새 prometheus agent 사용 가능

### ✅ 사용자
- 브라우저 자동화 기능 추가
- /remove-deadcode 명령어로 dead code 정리 가능
- background task 관리 개선

---

## 백업 & 복원

**백업 태그**: `pre-sync/YYYYMMDD-HHMM` (rebase 전 자동 생성)

문제 발생 시 복원:
```bash
git reset --hard pre-sync/YYYYMMDD-HHMM
```

---

## 다음 단계

1. ✅ fork.md 작성 완료
2. ✅ fork-summary.md 작성 완료
3. ✅ rebase 및 force push 완료
4. ✅ 빌드 완료
