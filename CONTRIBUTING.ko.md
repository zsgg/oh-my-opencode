# Oh My OpenCode 기여 가이드 (Contributing to Oh My OpenCode)

우선 시간을 내어 기여해 주셔서 감사합니다! 이 문서는 oh-my-opencode에 기여하기 위한 가이드라인과 지침을 제공합니다.

## 목차 (Table of Contents)

- [행동 강령](#행동-강령)
- [시작하기](#시작하기)
  - [필수 조건](#필수-조건)
  - [개발 환경 설정](#개발-환경-설정)
  - [로컬에서 변경 사항 테스트하기](#로컬에서 변경 사항 테스트하기)
- [프로젝트 구조](#프로젝트-구조)
- [개발 워크플로우](#개발-워크플로우)
  - [빌드 명령어](#빌드-명령어)
  - [코드 스타일 및 관례](#코드-스타일-및-관례)
- [변경 사항 만들기](#변경-사항-만들기)
  - [새로운 에이전트 추가](#새로운-에이전트-추가)
  - [새로운 훅 추가](#새로운-훅-추가)
  - [새로운 도구 추가](#새로운-도구-추가)
  - [새로운 MCP 서버 추가](#새로운-mcp-서버-추가)
- [Pull Request 프로세스](#pull-request-프로세스)
- [배포 (Publishing)](#배포-publishing)
- [도움 받기](#도움-받기)

## 행동 강령 (Code of Conduct)

서로 존중하고 포용하며 건설적인 태도를 유지해 주세요. 우리는 더 나은 도구를 함께 만들기 위해 여기 모였습니다.

## 시작하기 (Getting Started)

### 필수 조건 (Prerequisites)

- **Bun** (최신 버전) - 유일하게 지원되는 패키지 매니저입니다.
- **TypeScript 5.7.3 이상** - 타입 체크 및 선언 파일을 위해 필요합니다.
- **OpenCode 1.0.150 이상** - 플러그인 테스트를 위해 필요합니다.

### 개발 환경 설정 (Development Setup)

```bash
# 저장소 복제
git clone https://github.com/code-yeongyu/oh-my-opencode.git
cd oh-my-opencode

# 의존성 설치 (Bun만 사용 - npm/yarn 사용 금지)
bun install

# 프로젝트 빌드
bun run build
```

### 로컬에서 변경 사항 테스트하기 (Testing Your Changes Locally)

변경 사항을 만든 후, OpenCode에서 로컬 빌드를 테스트할 수 있습니다:

1. **프로젝트 빌드**:
   ```bash
   bun run build
   ```

2. **OpenCode 설정 업데이트** (`~/.config/opencode/opencode.json` 또는 `opencode.jsonc`):
   ```json
   {
     "plugin": [
       "file:///absolute/path/to/oh-my-opencode/dist/index.js"
     ]
   }
   ```
   
   예를 들어, 프로젝트가 `/Users/yourname/projects/oh-my-opencode`에 있는 경우:
   ```json
   {
     "plugin": [
       "file:///Users/yourname/projects/oh-my-opencode/dist/index.js"
     ]
   }
   ```
   
   > **참고**: npm 버전과의 충돌을 방지하기 위해 플러그인 배열에 기존 `"oh-my-opencode"`가 있다면 제거하세요.

3. **OpenCode 재시작**하여 변경 사항을 로드합니다.

4. OmO 에이전트 사용 가능 여부나 시작 메시지를 확인하여 플러그인이 성공적으로 로드되었는지 **검증**합니다.

## 프로젝트 구조 (Project Structure)

```
oh-my-opencode/
├── src/
│   ├── agents/        # AI 에이전트 (OmO, oracle, librarian, explore 등)
│   ├── hooks/         # 21개의 라이프사이클 훅
│   ├── tools/         # LSP (11개), AST-Grep, Grep, Glob 등
│   ├── mcp/           # MCP 서버 통합 (context7, websearch_exa, grep_app)
│   ├── features/      # Claude Code 호환 레이어
│   ├── config/        # Zod 스키마 및 TypeScript 타입
│   ├── auth/          # Google Antigravity OAuth
│   ├── shared/        # 공용 유틸리티
│   └── index.ts       # 메인 플러그인 엔트리 (OhMyOpenCodePlugin)
├── script/            # 빌드 유틸리티 (build-schema.ts, publish.ts)
├── assets/            # JSON 스키마
└── dist/              # 빌드 출력물 (ESM + .d.ts)
```

## 개발 워크플로우 (Development Workflow)

### 빌드 명령어 (Build Commands)

```bash
# 타입 체크만 수행
bun run typecheck

# 전체 빌드 (ESM + TypeScript 선언 파일 + JSON 스키마)
bun run build

# 빌드 출력물 삭제 후 다시 빌드
bun run rebuild

# 스키마만 빌드 (src/config/schema.ts 수정 후 실행)
bun run build:schema
```

### 코드 스타일 및 관례 (Code Style & Conventions)

| 관례 (Convention) | 규칙 (Rule) |
|------------|------|
| 패키지 매니저 | **Bun 전용** (`bun run`, `bun build`, `bunx`) |
| 타입 (Types) | `@types/node` 대신 `bun-types` 사용 |
| 디렉토리 명명 | kebab-case 사용 (`ast-grep/`, `claude-code-hooks/`) |
| 파일 작업 | 코드 내에서 파일 생성을 위해 bash 명령어(mkdir/touch/rm) 사용 금지 |
| 도구 구조 | 각 도구별로 `index.ts`, `types.ts`, `constants.ts`, `tools.ts`, `utils.ts` 포함 |
| 훅 패턴 | `createXXXHook(input: PluginInput)` 형태의 함수 명명 |
| 내보내기 (Exports) | 배럴 패턴 (`index.ts`에서 `export * from "./module"`) 사용 |

**안티 패턴 (지양해야 할 사항)**:
- Bun 대신 npm/yarn 사용
- `bun-types` 대신 `@types/node` 사용
- `as any`, `@ts-ignore`, `@ts-expect-error`를 사용한 TypeScript 에러 억제
- AI가 생성한 무의미하고 장황한 주석
- 직접 `bun publish` 실행 (GitHub Actions만 사용)
- `package.json`의 로컬 버전 수정

## 변경 사항 만들기 (Making Changes)

### 새로운 에이전트 추가

1. `src/agents/`에 새로운 `.ts` 파일 생성
2. 기존 패턴을 따라 에이전트 설정 정의
3. `src/agents/index.ts`의 `builtinAgents`에 추가
4. 필요한 경우 `src/agents/types.ts` 업데이트
5. `bun run build:schema`를 실행하여 JSON 스키마 업데이트

```typescript
// src/agents/my-agent.ts
import type { AgentConfig } from "./types";

export const myAgent: AgentConfig = {
  name: "my-agent",
  model: "anthropic/claude-sonnet-4-5",
  description: "이 에이전트가 수행하는 작업에 대한 설명",
  prompt: `에이전트의 시스템 프롬프트를 여기에 작성`,
  temperature: 0.1,
  // ... 기타 설정
};
```

### 새로운 훅 추가

1. `src/hooks/`에 새로운 디렉토리 생성 (kebab-case)
2. 이벤트 핸들러를 반환하는 `createXXXHook()` 함수 구현
3. `src/hooks/index.ts`에서 내보내기(export)

```typescript
// src/hooks/my-hook/index.ts
import type { PluginInput } from "@opencode-ai/plugin";

export function createMyHook(input: PluginInput) {
  return {
    onSessionStart: async () => {
      // 여기에 훅 로직 작성
    },
  };
}
```

### 새로운 도구 추가

1. `src/tools/`에 다음 필수 파일들을 포함하는 새로운 디렉토리 생성:
   - `index.ts` - 메인 내보내기
   - `types.ts` - TypeScript 인터페이스
   - `constants.ts` - 상수 및 도구 설명
   - `tools.ts` - 도구 구현
   - `utils.ts` - 헬퍼 함수
2. `src/tools/index.ts`의 `builtinTools`에 추가

### 새로운 MCP 서버 추가

1. `src/mcp/`에 설정 생성
2. `src/mcp/index.ts`에 추가
3. 외부 설정이 필요한 경우 README에 문서화

## Pull Request 프로세스 (Pull Request Process)

1. 저장소를 **포크(Fork)**하고 `master` 브랜치에서 자신의 브랜치를 만듭니다.
2. 위에서 언급한 관례에 따라 **변경 사항을 만듭니다.**
3. 로컬에서 **빌드 및 테스트**를 수행합니다:
   ```bash
   bun run typecheck  # 타입 에러가 없는지 확인
   bun run build      # 빌드가 성공하는지 확인
   ```
4. 위에서 설명한 로컬 빌드 방법을 사용하여 **OpenCode에서 테스트**합니다.
5. 명확하고 설명적인 메시지로 **커밋**합니다:
   - 현재 시제 사용 ("Add feature", "Added feature" 아님)
   - 해당되는 경우 이슈 번호 참조 ("Fix #123")
6. 포크한 저장소에 **푸시(Push)**하고 Pull Request를 생성합니다.
7. PR 설명란에 변경 사항을 명확히 **기술**합니다.

### PR 체크리스트 (PR Checklist)

- [ ] 코드가 프로젝트 관례를 따르고 있는가?
- [ ] `bun run typecheck`를 통과하는가?
- [ ] `bun run build`가 성공하는가?
- [ ] OpenCode에서 로컬 테스트를 완료했는가?
- [ ] 필요한 경우 문서(README, AGENTS.md)를 업데이트했는가?
- [ ] `package.json`의 버전 변경이 없는가?

## 배포 (Publishing)

**중요**: 배포는 GitHub Actions를 통해서만 독점적으로 처리됩니다.

- **절대로** 직접 `bun publish` 하지 마세요 (OIDC 프로버넌스 문제 발생).
- **절대로** 로컬에서 `package.json` 버전을 수정하지 마세요.
- 관리자는 GitHub Actions의 `workflow_dispatch`를 사용합니다:
  ```bash
  gh workflow run publish -f bump=patch  # minor 또는 major도 가능
  ```

## 도움 받기 (Getting Help)

- **프로젝트 지식**: 상세한 프로젝트 문서는 `AGENTS.md`를 확인하세요.
- **코드 패턴**: `src/`에 있는 기존 구현 사례들을 검토해 보세요.
- **이슈**: 버그 보고나 기능 제안은 Issue를 생성해 주세요.
- **토론**: 질문이나 아이디어는 Discussion을 시작해 주세요.

---

Oh My OpenCode에 기여해 주셔서 감사합니다! 여러분의 노력 덕분에 AI 지원 코딩 환경이 모두에게 더 나아질 수 있습니다.
