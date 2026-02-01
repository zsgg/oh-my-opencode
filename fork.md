# Fork Upstream 동기화 리포트

## 현재 동기화 상태

**동기화 일시**: 2026-02-01 23:18 KST
**비교 범위**: HEAD (e83ac8e) → upstream/dev (4f78eac)
**총 커밋 수**: 5개
**버전 범위**: v3.2.0 → v3.2.1
**새 태그**: v3.2.1

---

## v3.2.1 주요 변경사항

### 🎉 Major Release Highlights

#### 통합 Claude Tasks 시스템 (#1356)
- **[8d29a1c]** `Implement unified Claude Tasks system with single multi-action tool (#1356)`
  - 레거시 sisyphus-tasks, sisyphus-swarm 제거
  - 새로운 통합 task 시스템으로 교체
  - 5가지 작업(create, get, update, list, delete)을 단일 multi-action tool로 통합
  - Zod 스키마 기반 검증
  - 원자적 파일 쓰기 및 잠금 메커니즘
  - 태스크 상태: pending, in_progress, completed, deleted
  - Atlas, Sisyphus, Prometheus, Sisyphus-junior 에이전트에 task_* 권한 부여
  - 10턴마다 task 사용 리마인더 훅 추가
  - disabled_tools 설정 옵션 추가
  - new_task_system_enabled 최상위 플래그로 기능 게이팅

---

## 카테고리별 상세 변경사항

### ✨ Features

#### 태스크 시스템 통합
- **[8d29a1c]** `Implement unified Claude Tasks system with single multi-action tool (#1356)`
  - **파일 추가/수정**:
    - `src/tools/task/schema.ts`: Zod 기반 task CRUD 스키마
    - `src/tools/task/storage.ts`: 원자적 파일 작업 및 잠금 유틸리티
    - `src/tools/task/index.ts`: 통합 task tool (TaskCreate, TaskGet, TaskUpdate, TaskList, TaskDelete)
    - `src/hooks/task-reminder.ts`: 10턴마다 task 도구 리마인더
    - `src/hooks/tasks-todowrite-disabler.ts`: TodoWrite 도구 비활성화 훅
  - **리팩터링**:
    - 레거시 sisyphus-tasks 제거
    - 레거시 sisyphus-swarm (mailbox, swarm config) 제거
    - team namespace 제거, 플랫 task 디렉토리 구조 사용
  - **설정**:
    - `new_task_system_enabled` 최상위 플래그 추가
    - `disabled_tools` 설정 옵션으로 특정 도구 비활성화 가능
  - **권한**: Atlas, Sisyphus, Prometheus, Sisyphus-junior에 task_*, teammate 권한 부여
  - **테스트**: task.test.ts, storage.test.ts 추가
  - **기여자**: YeonGyu-Kim, justsisyphus, Sisyphus

---

### 🐛 Fixes

#### CLI 설정 수정
- **[6136103]** `fix(cli): add -preview suffix for GitHub Copilot Gemini model names`
  - GitHub Copilot이 공식적으로 `gemini-3-pro-preview`, `gemini-3-flash-preview` 식별자 사용
  - 설치 명령어가 잘못된 모델명(gemini-3-pro, gemini-3-flash) 생성하던 문제 수정
  - 사용자 보고: install 명령어가 GitHub Copilot API에서 작동하지 않는 잘못된 모델명 생성

#### 백그라운드 에이전트 동시성
- **[25dcd2a]** `fix(background-agent): prevent concurrency slot leaks on task startup failures`
  - **문제**: startTask()에서 에러 발생 시(session.create catch, createResult.error) 슬롯 해제했으나, processKey() catch 블록은 acquire()와 task.concurrencyKey 할당 사이 에러 시 슬롯 해제하지 않아 슬롯 누수 발생
  - **해결**: 슬롯 소유권 통합 - processKey()가 task.concurrencyKey 설정 전까지 슬롯 소유
    - startTask()의 모든 사전 전송 release() 호출 제거
    - processKey() catch에 조건부 release 추가: task.concurrencyKey 미설정 시에만 해제
    - createResult.data?.id 검증 추가하여 잘못된 API 응답 포착
  - **효과**: 동시성 슬롯 고갈로 인한 'Task failed to start within timeout' 에러 수정

---

### 🔄 Refactor

#### 종속성 관리
- **[8d29a1c]** `chore: pin bun-types to 1.3.6`
  - bun-types 버전 1.3.6으로 고정
- **[8d29a1c]** `chore: exclude test files and script from tsconfig`
  - 테스트 파일 및 스크립트 tsconfig에서 제외

#### 타입 시스템
- **[8d29a1c]** `fix: resolve typecheck and test failures`
  - createTask 함수에 명시적 ToolDefinition 반환 타입 추가
  - planDemoteConfig를 'all' 대신 'subagent' 모드 사용하도록 수정

#### 설정 관리
- **[8d29a1c]** `refactor(config): use new_task_system_enabled as top-level flag`
  - OhMyOpenCodeConfigSchema에 new_task_system_enabled 추가
  - SisyphusTasksConfigSchema에서 enabled 제거 (storage_path, claude_code_compat 유지)
  - index.ts에서 new_task_system_enabled 게이팅 업데이트
  - plugin-config.ts 기본값 업데이트

---

### 🏷️ Releases

- **[491df05]** `release: v3.2.1`

---

### 🤝 CLA Signatures

- **[4f78eac]** `@hichoe95 has signed the CLA in code-yeongyu/oh-my-opencode#1358`

---

## 전체 커밋 목록

```
4f78eac @hichoe95 has signed the CLA in code-yeongyu/oh-my-opencode#1358
8d29a1c Implement unified Claude Tasks system with single multi-action tool (#1356)
491df05 release: v3.2.1
25dcd2a fix(background-agent): prevent concurrency slot leaks on task startup failures
6136103 fix(cli): add -preview suffix for GitHub Copilot Gemini model names
```

---

## 주요 변경 영향도 분석

### 🔴 High Impact (즉시 영향)

1. **통합 Claude Tasks 시스템** (#1356)
   - 레거시 시스템 완전 교체
   - 새로운 워크플로우로 태스크 관리
   - 단일 multi-action tool로 단순화
   - 원자적 파일 작업으로 안정성 향상
   - 잠금 메커니즘으로 동시성 문제 해결

2. **동시성 슬롯 누수 수정**
   - 백그라운드 작업 시작 실패 시 안정성 향상
   - 리소스 누수 방지
   - 타임아웃 에러 해결

### 🟡 Medium Impact (점진적 개선)

1. **GitHub Copilot Gemini 모델명 수정**
   - GitHub Copilot 사용자에게 즉각적인 영향
   - 설치 프로세스 개선

### 🟢 Low Impact (내부 개선)

1. bun-types 버전 고정
2. 테스트 파일 제외
3. 타입체크 수정
4. CLA 서명

---

## 마이그레이션 가이드

### Breaking Changes

#### 레거시 태스크 시스템 제거
- **영향**: sisyphus-tasks, sisyphus-swarm 사용 중인 워크플로우
- **조치**: 새로운 Claude Tasks 시스템으로 마이그레이션
  ```json
  {
    "new_task_system_enabled": true
  }
  ```

### Deprecated

- `sisyphus-tasks` - Claude Tasks로 교체
- `sisyphus-swarm` - Claude Tasks로 교체
- team namespace - 플랫 디렉토리 구조 사용

### 새로운 기능 활용

#### 1. 통합 Claude Tasks 시스템
```json
{
  "new_task_system_enabled": true,
  "sisyphus": {
    "tasks": {
      "storage_path": "~/.opencode/tasks",
      "claude_code_compat": false
    }
  }
}
```

#### 2. 도구 비활성화
```json
{
  "disabled_tools": ["TodoWrite"]
}
```

#### 3. 태스크 리마인더 훅
- 10턴마다 자동으로 task 도구 사용 리마인더 제공
- 작업 추적 개선

---

## 관련 이슈 & PR

### Major PRs
- #1356: 통합 Claude Tasks 시스템 구현
- #1358: CLA 서명 (@hichoe95)

---

## 기여자 (Contributors)

- **YeonGyu-Kim** (@code-yeongyu) - 주요 기능 개발, 버그 수정
- **justsisyphus** (@justsisyphus) - Claude Tasks 시스템
- **Sisyphus** (@sisyphuslabs.ai) - Claude Tasks 시스템
- **hichoe95** (@hichoe95) - CLA 기여
- **github-actions[bot]** - 자동화 및 릴리스

---

## 태그 정보

- **v3.2.1**: 2026-02-01 (현재 최신)

---

## 이전 동기화 이력

### v3.2.0 (2026-02-01)

#### 🎉 Major Release Highlights

- Hephaestus 자율 에이전트 추가
- unstable-agent-babysitter 기본 활성화
- Windows 호환성 대폭 개선
- 프로세스 라이프사이클 관리 개선
- /stop-continuation 명령어 추가
- 코드베이스 대규모 정리

자세한 내용은 이전 버전 문서 참조.

### v3.1.9 → v3.1.10 (2026-01-31)

#### 🚨 Critical Fixes

- 플러그인 초기화 데드락 해결 (#1304)
- /start-work Prometheus 세션 수정 (#1298)
- Momus 에이전트 프롬프트 리팩토링

#### 🎯 주요 업데이트

- 테스트 스위트 최적화 (#1284): 104.6s → 7.01s (15배)
- Kimi Provider 완전 통합
- Tmux Integration 완전 구현 (#1125)
- MCP OAuth 2.1 완전 구현 (#1169)
- LSP 클라이언트 vscode-jsonrpc 마이그레이션 (#1095)

자세한 내용은 이전 버전 문서 참조.

---

*생성 일시: 2026-02-01 23:18 KST*
*생성자: zsgg (Oh-My-OpenCode fork maintainer)*
*생성 도구: sync-fork 슬래시 커맨드*
