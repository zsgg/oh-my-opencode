# 공용 유틸리티 지식 베이스 (SHARED UTILITIES KNOWLEDGE BASE)

## 개요 (OVERVIEW)

에이전트, 훅, 도구 및 기능 전반에서 공통적으로 사용되는 유틸리티 함수들입니다. 경로 확인, 설정 관리, 텍스트 처리, Claude Code 호환성 헬퍼 기능 등을 포함합니다.

## 구조 (STRUCTURE)

```
shared/
├── index.ts              # 배럴 내보내기 (import { x } from "../shared")
├── claude-config-dir.ts  # ~/.claude 디렉토리 확인
├── command-executor.ts   # 변수 확장이 포함된 쉘 명령어 실행
├── config-errors.ts      # 전역 설정 에러 추적
├── config-path.ts        # 사용자/프로젝트 설정 경로 확인
├── data-path.ts          # XDG 데이터 디렉토리 확인
├── deep-merge.ts         # 타입 안전한 재귀적 객체 병합
├── dynamic-truncator.ts  # 토큰 인식 기반 출력 잘라내기
├── file-reference-resolver.ts  # @filename 구문 확인
├── file-utils.ts         # 심볼릭 링크 확인, 마크다운 감지
├── frontmatter.ts        # YAML 프론트매터 파싱
├── hook-disabled.ts      # 설정에서 훅 비활성화 여부 확인
├── jsonc-parser.ts       # 주석이 포함된 JSON(JSONC) 파싱
├── logger.ts             # OS 임시 디렉토리에 파일 기반 로깅
├── migration.ts          # 레거시 이름 호환성 관리 (omo -> Sisyphus)
├── model-sanitizer.ts    # 모델 이름 정규화
├── pattern-matcher.ts    # 와일드카드를 이용한 도구 이름 매칭
├── snake-case.ts         # 객체 키의 케이스 변환
└── tool-name.ts          # 도구 이름을 PascalCase로 정규화
```

## 유틸리티 카테고리 (UTILITY CATEGORIES)

| 카테고리 (Category) | 유틸리티 (Utilities) | 사용처 (Used By) |
|----------|-----------|---------|
| 경로 확인 (Path Resolution) | `getClaudeConfigDir`, `getUserConfigPath`, `getProjectConfigPath`, `getDataDir` | Features, Hooks |
| 설정 관리 (Config Management) | `deepMerge`, `parseJsonc`, `isHookDisabled`, `configErrors` | index.ts, CLI |
| 텍스트 처리 (Text Processing) | `resolveCommandsInText`, `resolveFileReferencesInText`, `parseFrontmatter` | Commands, Rules |
| 출력 제어 (Output Control) | `dynamicTruncate` | Tools (Grep, LSP) |
| 정규화 (Normalization) | `transformToolName`, `objectToSnakeCase`, `sanitizeModelName` | Hooks, Agents |
| 호환성 (Compatibility) | `migration.ts` | 설정 로딩 시 |

## 유틸리티 사용 가이드 (WHEN TO USE WHAT)

| 작업 (Task) | 유틸리티 (Utility) | 비고 (Notes) |
|------|---------|-------|
| Claude Code 설정 찾기 | `getClaudeConfigDir()` | `~/.claude`를 코드에 직접 입력(Hardcode)하지 마세요. |
| 설정 병합 (기본 → 사용자 → 프로젝트) | `deepMerge(base, override)` | 배열은 교체되고, 객체는 병합됩니다. |
| 사용자 설정 파일 파싱 | `parseJsonc()` | 주석과 트레일링 콤마를 지원합니다. |
| 훅 실행 여부 확인 | `isHookDisabled(name, disabledHooks)` | `disabled_hooks` 설정을 존중합니다. |
| 대용량 도구 출력 잘라내기 | `dynamicTruncate(text, budget, reserved)` | 토큰을 인식하여 오버플로우를 방지합니다. |
| `@file` 참조 확인 | `resolveFileReferencesInText()` | 무한 루프 방지를 위해 maxDepth=3으로 제한됩니다. |
| 쉘 명령어 실행 | `resolveCommandsInText()` | `!`\`command\`\` 구문을 지원합니다. |
| 레거시 에이전트 이름 처리 | `migrateLegacyAgentNames()` | `omo`를 `Sisyphus`로 변환합니다. |

## 주요 패턴 (CRITICAL PATTERNS)

### 동적 잘라내기 (Dynamic Truncation)
```typescript
import { dynamicTruncate } from "../shared"
// 50%의 여유 공간 유지, 최대 50k 토큰으로 제한
const output = dynamicTruncate(result, remainingTokens, 0.5)
```

### 딥 머지 우선순위 (Deep Merge Priority)
```typescript
const final = deepMerge(defaults, userConfig)
final = deepMerge(final, projectConfig) // 프로젝트 설정이 최종 승리
```

### 안전한 JSONC 파싱 (Safe JSONC Parsing)
```typescript
const { config, error } = parseJsoncSafe(content)
if (error) return fallback
```

## 안티 패턴 - 공용 유틸리티 (ANTI-PATTERNS - SHARED)

- **경로 직접 입력(Hardcoding)**: `getClaudeConfigDir()`, `getUserConfigPath()`를 사용하세요.
- **수동 JSON.parse**: 사용자 파일(주석 허용)에는 `parseJsonc()`를 사용하세요.
- **잘라내기 무시**: 대용량 출력에는 반드시 `dynamicTruncate`를 사용해야 합니다.
- **설정 병합 시 직접 문자열 결합**: 올바른 우선순위 적용을 위해 `deepMerge`를 사용하세요.
