# Upstream 동기화 요약 (38개 커밋)

**동기화 일시**: 2026-01-23 21:26
**버전**: v3.0.0-beta.13

---

## 한 줄 요약

> **Atlas 프롬프트 대폭 간소화(78% 축소), 모델 fallback 체계 개선, Windows 빌드 안정성 향상**

---

## 핵심 변경사항

### 1. 🚀 Atlas 프롬프트 최적화 (6dfe091)

**가장 큰 성능 개선**

- 프롬프트 크기: **1,280 라인 → 280 라인** (78% 감소!)
- 모델이 이미 아는 내용 제거, 프롬프트 엔지니어링 원칙 적용
- 깔끔한 XML 구조 사용: `identity`, `mission`, `delegation_system`, `workflow` 등
- 6-section delegation 포맷: TASK, EXPECTED OUTCOME, REQUIRED TOOLS, MUST DO, MUST NOT DO, CONTEXT

**영향**: Atlas 에이전트 응답 속도 및 효율성 대폭 향상 예상

---

### 2. 🔧 모델 Fallback 시스템 개선

**여러 개선사항이 누적됨**

| 커밋 | 내용 |
|------|------|
| afbdf69 | 캐시 없을 때도 첫 번째 fallback 항목 사용 |
| 57b1043 | fallback chain의 variant를 올바르게 반영 |
| 6e84a14 | variant 반환, 모델명 정규화 처리 |
| ab3e622 | SDK 호출 대신 캐시 파일로 모델 가용성 확인 |
| f434888 | system default로 올바르게 fall through |
| c6d6bd1 | agent/category fallback chain 업데이트 |

**주요 개선**:
- CI 환경에서 캐시 없어도 설정된 모델 사용 보장
- Claude 모델 hyphen/period 차이 정규화
- github-copilot 모델명 변환 처리
- 플러그인 시작 지연 방지

**Fallback chain 업데이트**:
- quick: openai → `opencode/grok-code`
- writing: sonnet-gpt 사이에 `zai-coding-plan/glm-4.7` 추가
- Sisyphus: openai 전에 `zai/glm-4.7` 추가
- Momus & Metis: gemini-3-pro에 variant `max` 추가

---

### 3. 🪟 Windows 빌드 안정성 (1a901a5)

**세그멘테이션 폴트 완전 해결**

- Bun의 Linux→Windows 크로스 컴파일이 충돌 유발하던 문제
- windows-latest runner로 네이티브 빌드 사용
- Fixes #873, #844

---

### 4. 🤖 에이전트 & 도구 개선

**에이전트**:
- 표시 이름 모듈 추가 (629a4d3)
- 에이전트 키 소문자 정규화 (4e42888)
- Atlas에 적극적인 resume 지시사항 추가 (37e1a06)

**Multimodal-looker**:
- zai-coding-plan/glm-4.6v fallback 추가 (3062277)
- CLI 힌트에 포함 (9b12e2a)
- look_at 툴 조건부 등록 (39d2d44)

**기타**:
- 모델 캐시 없을 때 경고 토스트 표시 (e16bbbc)
- Atlas hook 등록 및 backgroundManager 전달 (75158ca)

---

### 5. 🐛 주요 버그 수정

| 이슈 | 해결 | Fixes |
|------|------|-------|
| 에이전트 전환 시 커스텀 에이전트 손실 | setSessionAgent 사용 (f8155e7) | #893 |
| 에이전트 제한 스킬 실행 | 조건 강화 (810dd93) | - |
| Windows bash 문법 이슈 | 항상 unix 문법 사용 (15c4637) | #983, #889 |
| LSP 서버 감지 실패 | data 디렉토리 경로 추가 (7093583) | #992 |
| git help 텍스트 누출 | stderr 캡처 (599fad0) | - |
| AST-Grep 감지 실패 (bunx) | dynamic import 사용 (be9d6c0) | #898 |

---

### 6. 📚 문서 개선

- **한국어 README 추가** (91d85d3) 🇰🇷
- 모델 설정 문서 추가 (bee8b37)
- multimodal-looker 문서 업데이트 (fc47a7a)
- Orchestrator-Sisyphus → Atlas 이름 변경 (3268782)
- AGENTS.md 파일들 재생성 (7de376e)

---

### 7. 🔨 Refactor & 기타

**Refactor**:
- Atlas delegation 가이던스 개선 (aa6355c)
- keyword injection 방식 변경 (0e18efc)
  - synthetic message → 직접 text 수정
  - 포맷: 키워드 메시지 + '---' + 원본 텍스트

**Others**:
- CLA 서명: @veetase, @Ssoon-m, @l3aro
- v3.0.0-beta.13 릴리스
- 테스트 간 모델 캐시 초기화 (71474bb)

---

## 통계

| 카테고리 | 개수 |
|----------|------|
| Features | 5개 |
| Fixes | 18개 |
| Refactor | 5개 |
| Docs | 6개 |
| Others | 4개 |
| **총합** | **38개** |

---

## 영향 범위

### ✅ 개발자
- Atlas 프롬프트 효율성 대폭 개선
- 모델 fallback 안정성 증가
- Windows 환경 빌드 안정화
- 에이전트 제한 스킬 올바른 작동

### ✅ 사용자
- 모델 캐시 없을 때 경고 표시
- 에이전트 전환 안정성 향상
- LSP 서버 감지 개선

---

## 백업 & 복원

**백업 태그**: `pre-sync/20260123-2124`

문제 발생 시 복원:
```bash
git reset --hard pre-sync/20260123-2124
```

---

## 다음 단계

1. ✅ 빌드 완료
2. ⏭️ 로컬 환경 테스트
3. ⏭️ 개선된 Atlas 에이전트 활용
