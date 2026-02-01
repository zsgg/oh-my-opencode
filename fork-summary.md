# Oh-My-OpenCode Fork 동기화 요약 (v3.2.0 → v3.2.1)

**동기화 일시**: 2026-02-01 23:18 KST
**비교 범위**: v3.2.0 → v3.2.1
**커밋 수**: 5개
**새 릴리즈**: v3.2.1

---

## 한 줄 요약

> **통합 Claude Tasks 시스템 도입으로 태스크 관리 단순화 및 백그라운드 에이전트 안정성 개선**

---

## 🔥 Critical Updates

### 1. 통합 Claude Tasks 시스템 (#1356) 🎉

**핵심 변경**
- 레거시 sisyphus-tasks, sisyphus-swarm 완전 제거
- 5가지 작업을 단일 multi-action tool로 통합
  - TaskCreate: 새 태스크 생성
  - TaskGet: 태스크 조회
  - TaskUpdate: 태스크 업데이트 (상태 전환, 소유자 검증)
  - TaskList: 전체 태스크 요약
  - TaskDelete: 태스크 삭제

**기술적 개선**
- **Zod 스키마**: 타입 안전 검증
- **원자적 파일 쓰기**: temp file + rename 패턴
- **잠금 메커니즘**: 파일 기반, 30초 stale threshold
- **순차 ID 생성**: lock 기반 동시성 제어

**설정 변경**
- `new_task_system_enabled`: 최상위 플래그로 기능 게이팅
- `disabled_tools`: 특정 도구 비활성화 옵션 추가
- Atlas, Sisyphus, Prometheus, Sisyphus-junior에 `task_*`, `teammate` 권한 부여

**사용자 경험**
- 10턴마다 task 도구 사용 리마인더
- tasks-todowrite-disabler 훅으로 TodoWrite 대체

**영향**: 태스크 관리 워크플로우 완전히 재설계, 안정성 및 단순성 향상

---

### 2. 백그라운드 에이전트 동시성 슬롯 누수 수정

**문제 분석**
- startTask()에서 에러 발생 시 슬롯 해제했으나, processKey() catch 블록은 acquire()와 task.concurrencyKey 할당 사이 에러 시 슬롯 미해제
- 결과: 동시성 슬롯 고갈 → 'Task failed to start within timeout' 에러

**해결책**
- **슬롯 소유권 통합**: processKey()가 task.concurrencyKey 설정 전까지 슬롯 소유
- startTask()의 모든 사전 전송 release() 호출 제거
- processKey() catch에 조건부 release 추가: task.concurrencyKey 미설정 시에만 해제
- createResult.data?.id 검증 추가하여 잘못된 API 응답 포착

**효과**
- 백그라운드 작업 시작 실패 시 안정성 향상
- 리소스 누수 방지
- 타임아웃 에러 해결

---

### 3. GitHub Copilot Gemini 모델명 수정

**문제**
- CLI 설치 명령어가 잘못된 모델명 생성
  - 기존: `gemini-3-pro`, `gemini-3-flash`
  - 필요: `gemini-3-pro-preview`, `gemini-3-flash-preview`
- GitHub Copilot API는 `-preview` 접미사 필수

**해결**
- 올바른 모델 식별자로 수정

**효과**
- GitHub Copilot 사용자의 설치 프로세스 즉시 개선
- API 호환성 확보

---

## 📊 변경사항 통계

| 카테고리 | 개수 | 주요 내용 |
|----------|------|----------|
| Features | 1 | Claude Tasks 시스템 통합 |
| Fixes | 2 | 동시성 슬롯 누수, Gemini 모델명 |
| Refactor | 3 | bun-types 고정, tsconfig, 타입체크 |
| Releases | 1 | v3.2.1 |
| CLA | 1 | @hichoe95 |
| **총합** | **5개** | - |

---

## 🎯 영향 범위

### ✅ 개발자
- **Claude Tasks**: 단순화된 태스크 관리
- **타입 안전성**: Zod 스키마 검증
- **원자적 작업**: 파일 쓰기 안정성
- **도구 비활성화**: disabled_tools 옵션

### ✅ 사용자
- **백그라운드 안정성**: 슬롯 누수 해결
- **GitHub Copilot**: 설치 프로세스 개선
- **태스크 리마인더**: 작업 추적 개선

### ✅ 시스템
- **레거시 제거**: sisyphus-tasks, sisyphus-swarm
- **잠금 메커니즘**: 동시성 문제 해결
- **원자적 쓰기**: 데이터 무결성

---

## 🔄 마이그레이션 가이드

### 필수 조치

#### 1. 새로운 Claude Tasks 시스템 활성화
```json
{
  "new_task_system_enabled": true
}
```

#### 2. 레거시 시스템 제거 확인
- `sisyphus-tasks` 의존성 제거
- `sisyphus-swarm` 설정 제거
- team namespace 사용 중단

### 선택 조치

#### 1. 도구 비활성화 (필요 시)
```json
{
  "disabled_tools": ["TodoWrite"]
}
```

#### 2. 태스크 저장 경로 커스터마이징
```json
{
  "sisyphus": {
    "tasks": {
      "storage_path": "~/.opencode/tasks",
      "claude_code_compat": false
    }
  }
}
```

#### 3. GitHub Copilot 사용자
- 설정 재생성 권장 (`opencode init` 또는 수동으로 모델명 수정)

---

## 💡 Breaking Changes

### ⚠️ 레거시 태스크 시스템 제거
- **영향 범위**: sisyphus-tasks, sisyphus-swarm 의존 워크플로우
- **마이그레이션 경로**:
  1. `new_task_system_enabled: true` 설정
  2. 기존 태스크 데이터 백업 (필요 시)
  3. 새로운 Claude Tasks API 사용
- **타임라인**: 즉시

### 🔄 설정 스키마 변경
- `SisyphusTasksConfigSchema`에서 `enabled` 제거
- `storage_path`, `claude_code_compat` 유지
- `new_task_system_enabled` 최상위 플래그 추가

---

## 🏆 주요 기술적 개선

### 원자적 파일 작업
```typescript
// temp file + rename 패턴으로 안전한 쓰기
writeJsonAtomic(path, data)
```

### 파일 기반 잠금
```typescript
// 30초 stale threshold로 데드락 방지
acquireLock(lockPath, { staleThreshold: 30000 })
```

### 타입 안전 검증
```typescript
// Zod 스키마로 런타임 검증
TaskSchema.parse(taskData)
```

### 슬롯 소유권 통합
```typescript
// processKey()가 task.concurrencyKey 설정 전까지 슬롯 소유
if (!task.concurrencyKey) {
  concurrencyManager.release(taskKey)
}
```

---

## 📖 새로운 API 사용 예제

### TaskCreate
```typescript
{
  "action": "create",
  "subject": "구현 태스크 제목",
  "description": "상세 설명",
  "activeForm": "구현 중" // spinner 표시용
}
```

### TaskUpdate
```typescript
{
  "action": "update",
  "taskId": "1",
  "status": "in_progress", // pending → in_progress → completed
  "owner": "agent-name",
  "addBlocks": ["2"], // 종속성 설정
  "metadata": { "key": "value" }
}
```

### TaskList
```typescript
{
  "action": "list"
}
// → 모든 태스크 요약 (id, subject, status, owner, blockedBy)
```

---

## 🔐 보안 & 안정성

### 동시성 제어
- 파일 기반 잠금으로 race condition 방지
- 슬롯 누수 완전 해결

### 데이터 무결성
- 원자적 쓰기로 부분 업데이트 방지
- Zod 검증으로 잘못된 데이터 차단

### 리소스 관리
- 정확한 슬롯 해제 타이밍
- API 응답 검증 강화

---

## 🎉 주목할 만한 변경사항

1. **Claude Tasks 시스템**: 단일 multi-action tool로 통합
2. **원자적 파일 작업**: temp + rename 패턴
3. **잠금 메커니즘**: 동시성 문제 완전 해결
4. **슬롯 누수 수정**: 백그라운드 안정성 향상
5. **Gemini 모델명**: GitHub Copilot 호환성

---

## 🔗 관련 PR & Issues

### Major PRs
- **#1356**: 통합 Claude Tasks 시스템 구현 ⭐⭐⭐
  - 기여자: YeonGyu-Kim, justsisyphus, Sisyphus
  - 변경: 레거시 제거, 새 시스템 구현, 테스트 추가
- **#1358**: CLA 서명 (@hichoe95)

---

## 🙏 기여자

- **YeonGyu-Kim** (@code-yeongyu) - 주요 개발, 버그 수정
- **justsisyphus** (@justsisyphus) - Claude Tasks 시스템
- **Sisyphus** (@sisyphuslabs.ai) - Claude Tasks 시스템
- **hichoe95** (@hichoe95) - CLA 기여
- **github-actions[bot]** - 자동화 및 릴리스

---

## 📝 이전 동기화 이력

### v3.2.0 (2026-02-01, 48개 커밋)

#### 주요 하이라이트
- **Hephaestus 에이전트**: 자율 심층 작업 처리
- **unstable-agent-babysitter**: 기본 활성화
- **Windows 호환성**: 대폭 개선
- **프로세스 관리**: 좀비 프로세스 제거, 메모리 누수 방지
- **/stop-continuation**: 사용자 제어 강화
- **코드베이스 정리**: 유지보수성 향상

### v3.1.9 → v3.1.10 (2026-01-31, 157개 커밋)

#### Critical Fixes
- **플러그인 초기화 데드락**: config handler ↔ OpenCode 서버 교착상태 해결
- **테스트 15배 속도 향상**: FakeTimers (104.6s → 7.01s)

#### 주요 통합
- **Kimi Provider**: kimi-for-coding 프로바이더
- **Tmux Integration**: State-first Architecture, 2D Grid
- **MCP OAuth 2.1**: RFC 준수, secure token
- **LSP vscode-jsonrpc**: ~60줄 코드 감소

자세한 내용은 [fork.md](./fork.md) 이전 동기화 이력 참조.

---

## 💡 권장 조치

### 즉시 적용
- ✅ **v3.2.1 업그레이드**: Claude Tasks 시스템, 슬롯 누수 수정
- ✅ **새 설정 추가**: `new_task_system_enabled: true`
- ✅ **레거시 제거**: sisyphus-tasks, sisyphus-swarm 설정 삭제

### 선택적 적용
- 🔸 **GitHub Copilot**: 설정 재생성 (모델명 수정)
- 🔸 **도구 비활성화**: `disabled_tools` 설정 (필요 시)
- 🔸 **태스크 저장 경로**: 커스터마이징 (필요 시)

### 주의 사항
- ⚠️ **Breaking Change**: 레거시 태스크 시스템 제거됨
- ⚠️ **마이그레이션**: 기존 태스크 데이터 백업 권장
- ⚠️ **설정 변경**: SisyphusTasksConfigSchema에서 `enabled` 제거

---

## 📈 영향도 평가

| 카테고리 | 영향도 | 상세 |
|---------|--------|------|
| 태스크 시스템 | 🔴 **High** | 레거시 제거, 새 시스템 마이그레이션 필수 |
| 백그라운드 안정성 | 🔴 **High** | 슬롯 누수 해결, 안정성 즉시 향상 |
| CLI 설정 | 🟡 **Medium** | GitHub Copilot 사용자 영향 |
| 타입 안전성 | 🟢 **Low** | 내부 개선, 투명한 변경 |
| 의존성 관리 | 🟢 **Low** | bun-types 고정, 내부 개선 |

---

## 🚀 Next Steps

### 개발자
1. `new_task_system_enabled: true` 설정
2. 새로운 Claude Tasks API 익히기
3. disabled_tools 활용 (필요 시)

### 사용자
1. v3.2.1로 업그레이드
2. 백그라운드 작업 안정성 확인
3. GitHub Copilot 설정 재생성 (해당 시)

### 운영자
1. 레거시 태스크 데이터 백업
2. 마이그레이션 검증
3. 모니터링 강화

---

**생성일**: 2026-02-01 23:18 KST
**생성자**: zsgg (Oh-My-OpenCode fork maintainer)
**도구**: sync-fork 슬래시 커맨드 with Claude Sonnet 4.5
