# Upstream 동기화 요약 (48개 커밋)

**동기화 일시**: 2026-02-01 21:00 KST
**버전 범위**: v3.1.10 → v3.2.0
**새 태그**: v3.1.11, v3.2.0

---

## 한 줄 요약

> **Hephaestus 에이전트 추가, unstable-agent-babysitter 기본 활성화, Windows 호환성 대폭 개선, 프로세스 라이프사이클 관리 강화, 테스트 안정성 향상**

---

## 🔥 Critical Updates

### 1. Hephaestus 에이전트 추가 (#1287)
- 자율적으로 심층 작업을 수행하는 새로운 에이전트
- 복잡한 멀티스텝 태스크 처리 능력 강화
- 고도의 자율성과 문제 해결 능력
- **영향**: 복잡한 작업 자동화 능력 대폭 향상

### 2. unstable-agent-babysitter 기본 활성화
- unstable 모델 자동 모니터링 기본 활성화
- 불안정한 에이전트 실시간 감시 및 자동 복구
- 프로덕션 안정성 대폭 향상
- **영향**: 프로덕션 환경 안정성 크게 개선

### 3. Windows 호환성 대폭 개선 (#1102)
- Windows 이벤트 리스너 이슈 해결
- LSP segfault 버그 방지 (Bun 버전 체크)
- CI retry 액션 Windows 호환성
- **영향**: Windows 사용자 경험 크게 향상

### 4. 프로세스 라이프사이클 관리 강화
- 좀비 프로세스 완전 제거 (#1306)
- 메모리 누수 방지 (#1058)
- tmux 고아 프로세스 방지 (#1329)
- 백그라운드 에이전트 세션 중단 개선
- **영향**: 리소스 관리 및 안정성 최적화

---

## 🎯 주요 신규 기능

### 태스크 관리 & UI 개선
**Todo Continuation**:
- continuation 프롬프트에 남은 태스크 목록 표시
- 작업 진행 상황 가시성 향상

**/stop-continuation 명령어** (#1316):
- 모든 continuation 메커니즘 중단
- 사용자 제어권 강화
- 세션 복구 시 설정 존중 (1회성 실행)

### 백그라운드 에이전트 개선
**isUnstableAgent 플래그**:
- unstable 모델 자동 감지
- minimax를 unstable 모델로 처리

**thinking_max_chars 옵션**:
- 백그라운드 출력 크기 제어
- 출력 최적화

**세션 타이틀 개선**:
- task_metadata 블록 추가
- 카테고리 정보 표시

### 시스템 & 환경
**OpenCode GUI 감지** (#1352):
- 모든 플랫폼에서 desktop GUI 설치 자동 감지
- 환경 진단 기능 개선

**GLM-4.7 Thinking Mode 지원**:
- 추론 능력 강화
- thinking mode 옵션 확장

### CI/CD & 개발 도구
**자동 릴리스 노트 생성**:
- conventional commit에서 구조화된 릴리스 노트 자동 생성
- 릴리스 프로세스 자동화

**Oracle 안전성 검토**:
- 배포 전 안전성 체크 추가

**MCP 매니저 개선**:
- 테스트 커버리지 향상
- 기능 강화

---

## 🔧 주요 버그 수정

### 프로세스 & 메모리 관리
- **좀비 attach 프로세스 방지**: 태스크 완료 시 세션 중단
- **메모리 누수 방지**: completion 타이머 추적 및 취소
- **좀비 프로세스 제거**: 적절한 프로세스 라이프사이클 관리
- **tmux 고아 프로세스 방지**: kill-pane 전 Ctrl+C 전송

### 설정 & 프롬프트
- **Prometheus prompt_append 처리**: 올바른 설정 적용
- **/stop-continuation 개선**: 1회성 실행, 세션 복구 시 존중

### 테스트 안정성
- **notifyParentSession 스텁**: 타이머 기반 테스트 안정화
- **_resetForTesting() 일관성**: flaky 테스트 제거 (#1318)
- **ToolContext 필드 추가**: 테스트 mock 개선

### 환경 변수 & Git
- **비대화형 환경 지원**: git 명령어에 환경 변수 항상 주입

### CI/CD
- **플랫폼 빌드 재시도**: 빌드 안정성 향상
- **테스트 경로 정리**: 삭제된 파일 제거

### 의존성
- **vscode-jsonrpc 복원**: bun.lock 재생성

### Rules Injector
- **dead batch 코드 제거**: .sisyphus 파일 지원 추가

---

## 🔄 주요 리팩토링

### 백그라운드 에이전트
- 태스크 완료 알림에 카테고리 정보 표시
- 태스크 타이밍 최적화
- 상수 관리 개선

### Delegate Task
- 세션 타이틀 포맷 개선
- task_metadata 블록 추가

### 에이전트 프롬프트
- explore/librarian 프롬프트 4부 구조로 개선
- 컨텍스트 명확성 향상

### 코드베이스 정리 (#1350, #1317)
- BDD 주석, 파일 분할, 버그 수정
- 중복 패턴 통합
- 코드베이스 단순화
- 고아 compaction-context-injector 훅 제거

---

## 📊 통계

| 카테고리 | 개수 |
|----------|------|
| Features | 10 |
| Fixes | 15 |
| Refactor | 7 |
| Docs | 1 |
| Chore | 5 |
| Releases | 2 |
| CLA Signatures | 4 |
| **총합** | **48개** |

---

## 🎯 영향 범위

### ✅ 개발자
- **Hephaestus 에이전트**: 복잡한 작업 자동화
- **unstable-agent-babysitter**: 프로덕션 안정성
- **thinking_max_chars**: 출력 크기 제어
- **/stop-continuation**: 사용자 제어 강화
- **GLM-4.7 thinking mode**: 추론 능력 향상

### ✅ 사용자
- **Windows 호환성**: 안정성 대폭 향상
- **프로세스 관리**: 좀비 프로세스 제거, 메모리 누수 방지
- **테스트 안정성**: flaky 테스트 제거
- **Todo continuation**: 작업 진행 상황 가시성

### ✅ CI/CD
- **자동 릴리스 노트**: 릴리스 프로세스 자동화
- **빌드 재시도**: 안정성 향상
- **테스트 개선**: 안정적인 CI 파이프라인

---

## 🔐 보안 개선

- **unstable-agent-babysitter**: 불안정한 모델 자동 감시
- **프로세스 격리**: 좀비 프로세스 방지
- **리소스 관리**: 메모리 누수 방지

---

## 📖 Migration Guide

### Hephaestus 에이전트 사용
```bash
# 복잡한 멀티스텝 작업에 활용
opencode delegate --agent hephaestus
```

### /stop-continuation 명령어
```bash
# 세션 중 continuation 중단 필요 시
/stop-continuation
```

### thinking_max_chars 옵션
```json
{
  "background_output": {
    "thinking_max_chars": 1000
  }
}
```

### unstable-agent-babysitter (자동 활성화)
- 별도 설정 불필요 (기본 활성화)
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

### GLM-4.7 Thinking Mode
```json
{
  "agents": {
    "atlas": {
      "thinking": true,
      "model": "zai-coding-plan/glm-4.7"
    }
  }
}
```

---

## 🔗 주요 PR & Issues

### Critical PRs
- #1287: Hephaestus 에이전트 추가 ⭐
- #1352: OpenCode GUI 감지 개선
- #1102: Windows 호환성 개선 🏆
- #1350: 코드베이스 대규모 정리

### Major PRs
- #1316: /stop-continuation 명령어
- #1318: 테스트 안정성 개선
- #1317: 중복 패턴 제거
- #1329: tmux 프로세스 관리
- #1306: 좀비 프로세스 방지
- #1058: 메모리 누수 방지
- #1271: Prometheus 설정 처리

---

## 🙏 기여자 감사

### Contributors
- **YeonGyu-Kim** (@code-yeongyu) - 주요 기능 개발, 릴리스 관리
- **justsisyphus** (@justsisyphus) - 테스트 안정성, 백그라운드 에이전트, CI/CD 개선
- **Sisyphus** (@sisyphus-dev-ai) - Think mode, LSP 개선
- **Nguyễn Văn Tín** (@edxeth) - Windows 호환성
- **gabriel-ecegi** (@gabriel-ecegi) - Prometheus 설정
- **itsmylife44** (@itsmylife44) - Tmux 프로세스 관리
- **Nguyen Khac Trung Kien** (@dmealing) - 좀비 프로세스 방지
- **taetaetae** (@taetaetae) - CLA 기여
- **github-actions[bot]** - 자동화 및 릴리스

---

## ⚡ Performance Highlights

- **프로세스 관리**: 좀비 프로세스 제거, 메모리 누수 방지
- **테스트 안정성**: flaky 테스트 제거
- **CI/CD**: 빌드 재시도 로직으로 안정성 향상

---

## 🎉 주목할 만한 변경사항

1. **Hephaestus 에이전트**: 자율적 심층 작업 처리
2. **unstable-agent-babysitter 기본 활성화**: 프로덕션 안정성 향상
3. **Windows 호환성 대폭 개선**: Windows 사용자 경험 크게 향상
4. **프로세스 라이프사이클 관리**: 좀비 프로세스 제거, 메모리 누수 방지
5. **테스트 안정성 향상**: flaky 테스트 제거
6. **코드베이스 정리**: 유지보수성 향상
7. **/stop-continuation 명령어**: 사용자 제어 강화
8. **자동 릴리스 노트 생성**: CI/CD 자동화

---

## 📝 이전 동기화 (v3.1.9 → v3.1.10)

### 주요 변경사항
- **플러그인 초기화 데드락 해결** (#1304): config handler ↔ OpenCode 서버 교착상태 해결
- **테스트 15배 속도 향상** (#1284): FakeTimers 구현 (104.6s → 7.01s)
- **Kimi Provider 통합**: kimi-for-coding 프로바이더 추가
- **Tmux 완전 구현** (#1125): State-first Architecture, 2D Grid Layout
- **MCP OAuth 2.1** (#1169): RFC 준수, secure token storage
- **LSP vscode-jsonrpc 마이그레이션** (#1095): ~60줄 코드 감소

자세한 내용은 fork.md "이전 동기화 이력" 섹션 참조.

---

## 💡 권장 사항

### 즉시 적용 권장
- **v3.2.0 업그레이드**: Hephaestus 에이전트, unstable-agent-babysitter, Windows 호환성 개선
- **프로세스 관리 개선**: 좀비 프로세스 제거, 메모리 누수 방지
- **테스트 안정성**: CI/CD 안정성 향상

### 선택적 적용
- **Hephaestus 에이전트**: 복잡한 작업 자동화 필요 시
- **unstable-agent-babysitter**: 기본 활성화 (필요 시 disable)
- **/stop-continuation**: continuation 제어 필요 시
- **GLM-4.7 thinking mode**: 추론 능력 강화 필요 시

### 주의 사항
- **unstable-agent-babysitter**: 기본 활성화됨 (필요 시 명시적으로 disable)
- **compaction-context-injector**: 제거됨 (deprecated)

---

**생성일**: 2026-02-01 21:00 KST
**생성자**: zsgg (Oh-My-OpenCode fork maintainer)
**도구**: Claude Sonnet 4.5 with oh-my-opencode sync-fork workflow
