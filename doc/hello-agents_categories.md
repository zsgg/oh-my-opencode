## sync-models-config 탐색 결과

**생성일:** 2026-02-02T00:19:35+09:00

---

### 2-1. 에이전트 기술 데이터

소스코드에서 탐색한 에이전트별 기본값:

- **atlas**
  - 원본 모델: `kimi-for-coding/k2p5` → fallback `anthropic/claude-sonnet-4-5`
  - variant: (없음)
  - temperature: 0.1 (`src/agents/atlas.ts`)
  - 비고: kimi-for-coding 미지원으로 fallback 적용

- **explore**
  - 원본 모델: `anthropic/claude-haiku-4-5`
  - variant: (없음)
  - temperature: 0.1 (`src/agents/explore.ts`)
  - 비고: claude-haiku 미지원으로 claude-sonnet 변환

- **hephaestus**
  - 원본 모델: `openai/gpt-5.2-codex`
  - variant: `medium`
  - temperature: (미명시)
  - 비고: GPT 5.2 Codex 전용, "The Legitimate Craftsman"

- **librarian**
  - 원본 모델: `zai-coding-plan/glm-4.7`
  - variant: (없음)
  - temperature: 0.1 (`src/agents/librarian.ts`)
  - 비고: zai-coding-plan 미지원으로 claude-sonnet 변환

- **metis**
  - 원본 모델: `anthropic/claude-opus-4-5`
  - variant: `max`
  - temperature: 0.3 (`src/agents/metis.ts`)
  - 비고: Pre-planning consultant

- **momus**
  - 원본 모델: `openai/gpt-5.2`
  - variant: `medium`
  - temperature: 0.1 (`src/agents/momus.ts`)
  - 비고: Plan reviewer

- **multimodal-looker**
  - 원본 모델: `google/gemini-3-flash`
  - variant: (없음)
  - temperature: 0.1 (`src/agents/multimodal-looker.ts`)
  - 비고: PDF/image 분석용

- **oracle**
  - 원본 모델: `openai/gpt-5.2`
  - variant: `high`
  - temperature: 0.1 (`src/agents/oracle.ts`)
  - 비고: Read-only 컨설턴트

- **prometheus**
  - 원본 모델: `anthropic/claude-opus-4-5`
  - variant: `max`
  - temperature: (미명시)
  - 비고: Strategic planner (Interview/Consultant mode)

- **sisyphus**
  - 원본 모델: `anthropic/claude-opus-4-5`
  - variant: `max`
  - temperature: (미명시)
  - 비고: Primary orchestrator

- **sisyphus-junior**
  - 원본 모델: `anthropic/claude-sonnet-4-5`
  - variant: (없음)
  - temperature: 0.1 (`src/agents/sisyphus-junior.ts`)
  - 비고: Category-spawned executor

---

### 2-2. 에이전트 성격 정보 (참조용)

- **sisyphus**
  - 목적: Primary orchestrator. SF Bay Area 엔지니어 페르소나
  - 장점:
    - 복잡한 작업 분해 및 위임 능력
    - 코드베이스 성숙도에 따른 적응력
    - 병렬 실행을 통한 최대 처리량
    - todo 기반 진행 추적
  - 장점예시:
    - 프론트엔드 작업 → visual-engineering 위임
    - 깊은 연구 → 병렬 백그라운드 에이전트
    - 복잡한 아키텍처 → Oracle 상담
  - 단점:
    - 고비용 모델 (Opus 4.5)
    - 단순 작업에 과잉
    - 위임 오버헤드
  - 단점예시:
    - 한 줄 수정에 오케스트레이션 불필요
    - 직접 실행이 빠른 trivial 작업

- **hephaestus**
  - 목적: Autonomous deep worker. 목표 지향적 실행
  - 장점:
    - 철저한 연구 후 결정적 행동
    - 단계별 지시 불필요
    - End-to-end 완료
    - 기존 패턴 매칭
  - 장점예시:
    - 복잡한 리팩토링 자율 수행
    - 2-5개 explore agent 병렬 실행
    - 검증까지 완료
  - 단점:
    - gpt-5.2-codex 필수
    - fallback 없음
    - 긴 실행 시간
  - 단점예시:
    - 모델 미지원 시 사용 불가
    - 단순 작업에 과잉

- **oracle**
  - 목적: Read-only 컨설턴트. 고난도 디버깅/아키텍처
  - 장점:
    - 높은 추론 능력
    - 실용적 미니멀리즘 권고
    - 구체적 행동 계획 제공
    - 노력 추정 포함
  - 장점예시:
    - 2+ 실패 후 디버깅 상담
    - 복잡한 아키텍처 결정
    - 코드 리뷰 핵심 이슈 도출
  - 단점:
    - 수정 불가 (read-only)
    - 고비용
    - 과다 사용 시 비효율
  - 단점예시:
    - 첫 시도에 Oracle 호출 불필요
    - 단순 질문에 과잉

- **explore**
  - 목적: 빠른 코드베이스 탐색 (Contextual Grep)
  - 장점:
    - 저비용
    - 병렬 실행 최적화
    - 다양한 검색 도구 활용
    - 구조화된 결과 출력
  - 장점예시:
    - "Where is X implemented?"
    - 3+ 도구 동시 실행
    - 절대 경로 + 설명 반환
  - 단점:
    - Read-only
    - 단순 검색에는 직접 도구가 빠름
  - 단점예시:
    - 파일 위치를 아는 경우 직접 read

- **librarian**
  - 목적: 외부 문서/오픈소스 검색
  - 장점:
    - 공식 문서 접근
    - GitHub 코드 검색
    - 실제 구현 예시 제공
    - 버전별 문서 확인
  - 장점예시:
    - "How do I use [library]?"
    - 공식 문서 sitemap 탐색
    - GitHub permalink 생성
  - 단점:
    - Read-only
    - 외부 의존성
    - API rate limit 가능
  - 단점예시:
    - 내부 코드베이스 검색에는 explore 사용

- **multimodal-looker**
  - 목적: 미디어 파일 분석 (PDF, 이미지, 다이어그램)
  - 장점:
    - 텍스트로 읽을 수 없는 파일 해석
    - 시각적 콘텐츠 설명
    - 요청된 정보만 추출
  - 장점예시:
    - PDF 특정 섹션 데이터 추출
    - UI 레이아웃 설명
    - 다이어그램 관계 설명
  - 단점:
    - Read-only
    - 정확한 텍스트 필요 시 Read 사용
  - 단점예시:
    - 소스 코드는 Read로 읽기

- **prometheus**
  - 목적: Strategic planner. 인터뷰/컨설턴트 모드
  - 장점:
    - 사용자 요구 심층 분석
    - 명확해질 때까지 질문
    - 작업 의존성 그래프 생성
    - 병렬 실행 최적화
  - 장점예시:
    - 복잡한 기능 요청 → 단계별 계획
    - 불명확한 요구사항 명확화
    - Momus 리뷰 연동
  - 단점:
    - 고비용 (Opus 4.5)
    - 단순 작업에 과잉
    - .md 파일만 작성 가능
  - 단점예시:
    - 한 줄 수정에 계획 불필요

- **metis**
  - 목적: Pre-planning consultant. 계획 전 분석
  - 장점:
    - 숨겨진 의도 식별
    - 모호성 탐지
    - AI 실패 패턴 방지
    - 명확화 질문 생성
  - 장점예시:
    - Refactoring → 안전성 분석
    - Build from scratch → 패턴 탐색 먼저
    - 모호한 요청 → 의도 분류
  - 단점:
    - Read-only
    - 추가 단계 오버헤드
    - 단순 작업에 불필요
  - 단점예시:
    - 명확한 요구사항에는 스킵 가능

- **momus**
  - 목적: Plan reviewer. 실행 가능성 검증
  - 장점:
    - 참조 파일 존재 확인
    - BLOCKING 이슈만 지적
    - 실용적 접근 (80% 명확하면 OK)
    - 최대 3개 이슈만 보고
  - 장점예시:
    - 계획 내 파일 참조 검증
    - 실행 불가 태스크 식별
    - 불필요한 완벽주의 방지
  - 단점:
    - Read-only
    - 디자인 의견 없음
    - 성능/보안 검토 안 함
  - 단점예시:
    - 단순 계획에는 스킵 가능

- **atlas**
  - 목적: Master orchestrator. Todo list 완료까지 조율
  - 장점:
    - 병렬 태스크 그룹 관리
    - 6-section 프롬프트 강제
    - Notepad 시스템으로 지식 축적
    - 프로젝트 레벨 QA
  - 장점예시:
    - .sisyphus/plans/*.md 실행
    - 병렬 가능 태스크 동시 위임
    - 검증 실패 시 세션 재개
  - 단점:
    - 직접 코드 작성 안 함
    - 긴 프롬프트 필수
    - 단일 태스크에 과잉
  - 단점예시:
    - 간단한 수정에는 sisyphus 사용

- **sisyphus-junior**
  - 목적: Focused executor. 위임받은 태스크 직접 실행
  - 장점:
    - 위임 불가 (구현 집중)
    - explore/librarian 호출 가능
    - Todo 규율 준수
    - 검증 필수
  - 장점예시:
    - Category 기반 태스크 실행
    - 구현 후 lsp_diagnostics 확인
    - 단일 책임 원칙
  - 단점:
    - 복잡한 조율 불가
    - delegate_task 차단됨
    - task 도구 차단됨
  - 단점예시:
    - 다중 에이전트 조율 필요 시 sisyphus 사용

---

### 2-3. 카테고리 기술 데이터

소스코드에서 탐색한 카테고리별 기본값 (`src/tools/delegate-task/constants.ts`):

- **artistry**
  - 원본 모델: `google/gemini-3-pro`
  - variant: `max`
  - temperature: (없음)
  - 비고: 창의적/예술적 태스크

- **deep**
  - 원본 모델: `openai/gpt-5.2-codex`
  - variant: `medium`
  - temperature: (없음)
  - 비고: 목표 지향 자율 문제 해결

- **quick**
  - 원본 모델: `anthropic/claude-haiku-4-5`
  - variant: (없음)
  - temperature: (없음)
  - 비고: 단순 태스크, claude-haiku 미지원으로 sonnet 변환

- **ultrabrain**
  - 원본 모델: `openai/gpt-5.2-codex`
  - variant: `xhigh`
  - temperature: (없음)
  - 비고: 깊은 논리적 추론, 복잡한 아키텍처

- **unspecified-high**
  - 원본 모델: `anthropic/claude-opus-4-5`
  - variant: `max`
  - temperature: (없음)
  - 비고: 분류 불가 고노력 태스크

- **unspecified-low**
  - 원본 모델: `anthropic/claude-sonnet-4-5`
  - variant: (없음)
  - temperature: (없음)
  - 비고: 분류 불가 저노력 태스크

- **visual-engineering**
  - 원본 모델: `google/gemini-3-pro`
  - variant: (없음)
  - temperature: (없음)
  - 비고: Frontend, UI/UX, 디자인

- **writing**
  - 원본 모델: `google/gemini-3-flash`
  - variant: (없음)
  - temperature: (없음)
  - 비고: 문서, 산문, 기술 문서

---

### 2-4. 카테고리 성격 정보 (참조용)

- **visual-engineering**
  - 목적: Frontend, UI/UX, 디자인, 스타일링, 애니메이션
  - 장점:
    - 대담한 미적 선택
    - 독특한 타이포그래피
    - 고영향 애니메이션
    - 분위기 있는 디자인
  - 장점예시:
    - 그라디언트 메시, 노이즈 텍스처
    - 비대칭 레이아웃
    - 단계적 reveal 애니메이션
  - 단점:
    - 일반적 폰트 사용 경향
    - 보라색 그라디언트 기본값
    - 예측 가능한 패턴
  - 단점예시:
    - Arial, Inter, Roboto 피해야 함
    - 쿠키커터 UI 방지 필요

- **ultrabrain**
  - 목적: 깊은 논리적 추론, 복잡한 아키텍처
  - 장점:
    - 전략적 조언자 마인드셋
    - 단순성 편향
    - 개발자 경험 우선
    - 노력 추정 포함
  - 장점예시:
    - 기존 코드/패턴 활용
    - 하나의 명확한 권고
    - Quick/Short/Medium/Large 태깅
  - 단점:
    - 고급 접근 신호 필요
    - 과잉 분석 가능
  - 단점예시:
    - 단순 태스크에 과잉 분석

- **deep**
  - 목적: 목표 지향 자율 문제 해결
  - 장점:
    - 철저한 연구 후 행동
    - 단계별 지시 불필요
    - 포괄적 솔루션 선호
    - 독립적 작업
  - 장점예시:
    - 5-15분 코드베이스 탐색
    - 합리적 가정으로 진행
    - 최소 상태 업데이트
  - 단점:
    - gpt-5.2-codex 필수
    - 긴 실행 시간
    - 모호한 목표에 약함
  - 단점예시:
    - 명확한 정의 없으면 가정으로 진행

- **artistry**
  - 목적: 고도로 창의적/예술적 태스크
  - 장점:
    - 관습적 경계 초월
    - 급진적, 비관습적 방향 탐색
    - 예상치 못한 반전
    - 풍부한 디테일
  - 장점예시:
    - 다양한 대담한 옵션 생성
    - 야생 실험 수용
    - 새로움과 일관성 균형
  - 단점:
    - 예측 불가
    - 실용성 부족 가능
  - 단점예시:
    - 기술적 제약 무시 가능

- **quick**
  - 목적: 단순 태스크 - 단일 파일, 오타 수정
  - 장점:
    - 빠르고 집중적
    - 최소 오버헤드
    - 직접적이고 간결
    - 단순 솔루션
  - 장점예시:
    - 즉시 핵심으로
    - 불필요한 추상화 스킵
    - 최소 구현
  - 단점:
    - 덜 유능한 모델 (haiku → sonnet)
    - 복잡한 추론 제한
    - 명시적 가드레일 필수
  - 단점예시:
    - 모호한 지시 → 예측 불가
    - MUST DO/MUST NOT DO 필수

- **unspecified-low**
  - 목적: 다른 카테고리에 맞지 않는 저노력 태스크
  - 장점:
    - 중간 수준 모델
    - 범용성
    - 적당한 비용
  - 장점예시:
    - 몇 개 파일/모듈 범위
    - 분류 불가 작업
  - 단점:
    - 기본 선택 아님
    - 특화 카테고리 우선
  - 단점예시:
    - UI 작업 → visual-engineering 사용

- **unspecified-high**
  - 목적: 다른 카테고리에 맞지 않는 고노력 태스크
  - 장점:
    - 고급 모델 (Opus 4.5)
    - 넓은 임팩트 작업
    - 조심스러운 조율 필요
  - 장점예시:
    - 다중 시스템/모듈 작업
    - 분류 불가 고노력
  - 단점:
    - 기본 선택 아님
    - 고비용
  - 단점예시:
    - 복잡하지만 분류 가능하면 해당 카테고리 사용

- **writing**
  - 목적: 문서, 산문, 기술 문서
  - 장점:
    - 명확하고 흐르는 산문
    - 적절한 톤과 목소리
    - 매력적이고 읽기 쉬움
    - 적절한 구조
  - 장점예시:
    - 청중 이해
    - 정성스러운 초안
    - 명확성과 임팩트를 위한 다듬기
  - 단점:
    - 코드 작성에 부적합
    - 기술적 깊이 제한
  - 단점예시:
    - README, 문서 작업에만 사용

---

## 변환 규칙 적용 결과

### 에이전트 변환

| 에이전트 | 원본 | 변환 후 | 이유 |
|---------|------|--------|------|
| atlas | kimi-for-coding/k2p5 | anthropic/claude-sonnet-4-5 | kimi 미지원 |
| explore | anthropic/claude-haiku-4-5 | anthropic/claude-sonnet-4-5 | haiku 미지원 |
| hephaestus | openai/gpt-5.2-codex | openai/gpt-5.2-codex | 지원 |
| librarian | zai-coding-plan/glm-4.7 | anthropic/claude-sonnet-4-5 | zai 미지원 |
| metis | anthropic/claude-opus-4-5 | anthropic/claude-opus-4-5 | 지원 |
| momus | openai/gpt-5.2 | openai/gpt-5.2 | 지원 |
| multimodal-looker | google/gemini-3-flash | github-copilot/gemini-3-flash-preview | google 미지원 |
| oracle | openai/gpt-5.2 | openai/gpt-5.2 | 지원 |
| prometheus | anthropic/claude-opus-4-5 | anthropic/claude-opus-4-5 | 지원 |
| sisyphus | anthropic/claude-opus-4-5 | anthropic/claude-opus-4-5 | 지원 |
| sisyphus-junior | anthropic/claude-sonnet-4-5 | anthropic/claude-sonnet-4-5 | 지원 |

### 카테고리 변환

| 카테고리 | 원본 | 변환 후 | 이유 |
|---------|------|--------|------|
| artistry | google/gemini-3-pro | github-copilot/gemini-3-pro-preview | google 미지원 |
| deep | openai/gpt-5.2-codex | openai/gpt-5.2-codex | 지원 |
| quick | anthropic/claude-haiku-4-5 | anthropic/claude-sonnet-4-5 | haiku 미지원 |
| ultrabrain | openai/gpt-5.2-codex | openai/gpt-5.2-codex | 지원 |
| unspecified-high | anthropic/claude-opus-4-5 | anthropic/claude-opus-4-5 | 지원 |
| unspecified-low | anthropic/claude-sonnet-4-5 | anthropic/claude-sonnet-4-5 | 지원 |
| visual-engineering | google/gemini-3-pro | github-copilot/gemini-3-pro-preview | google 미지원 |
| writing | google/gemini-3-flash | github-copilot/gemini-3-flash-preview | google 미지원 |
