# oh-my-opencode 심볼릭 링크 지원 추가

## 배경 (Background)

oh-my-opencode에서 `.claude/commands/` 내 심볼릭 링크 파일들이 로드되지 않는 버그 수정.

## 원인 (Cause)

`src/shared/file-utils.ts`의 `isMarkdownFile` 함수가 `entry.isFile()`만 체크함.
심볼릭 링크는 `isFile()`이 false 반환 → 필터링됨.

## 수정 대상 (Target)

**파일**: `src/shared/file-utils.ts`

**현재 코드** (line 4-6):

```typescript
export function isMarkdownFile(entry: { name: string; isFile: () => boolean }): boolean {
  return !entry.name.startsWith(".") && entry.name.endsWith(".md") && entry.isFile()
}
```

**수정 코드**:

```typescript
export function isMarkdownFile(entry: { name: string; isFile: () => boolean; isSymbolicLink?: () => boolean }): boolean {
  const isFileOrSymlink = entry.isFile() || (entry.isSymbolicLink?.() ?? false)
  return !entry.name.startsWith(".") && entry.name.endsWith(".md") && isFileOrSymlink
}
```

## 테스트 방법 (Testing)

1. `.claude/commands/`에 심볼릭 링크 `.md` 파일 생성
2. opencode 실행 후 해당 command가 `/` 목록에 나오는지 확인

## 관련 파일 (Related Files)

### 수정 대상
- `src/shared/file-utils.ts` - `isMarkdownFile` 함수

### 영향받는 파일 (6개)
- `src/features/claude-code-agent-loader/loader.ts`
- `src/features/claude-code-command-loader/loader.ts`
- `src/features/claude-code-plugin-loader/loader.ts` (2곳)
- `src/features/opencode-skill-loader/loader.ts`
- `src/tools/slashcommand/tools.ts`

---

## 적용 가이드 (Implementation Guide)

### 1. 수정 & 빌드

```bash
# 의존성 설치
bun install

# 코드 수정 후 빌드
bun run build

# 타입 체크
bun run typecheck

# 테스트 실행
bun test
```

### 2. 로컬에서 사용하기

```bash
# oh-my-opencode 디렉토리에서 글로벌 등록 + opencode에 링크
bun link && bun link oh-my-opencode --cwd ~/.config/opencode
```

### 3. 변경사항 반영 (수정할 때마다)

```bash
# oh-my-opencode 디렉토리에서
bun run build

# link 사용 시 변경사항 자동 반영됨
```

**링크 적용 확인:**

```bash
# 심볼릭 링크가 로컬 빌드 디렉토리를 가리키는지 확인
ls -la ~/.config/opencode/node_modules/oh-my-opencode

# 출력 예시 (로컬 빌드 사용 중):
# oh-my-opencode -> ../../../../Shared/Code-Personal/oh-my-opencode
```

### 4. 원본으로 복귀하는 방법

```bash
bun unlink oh-my-opencode --cwd ~/.config/opencode
```

또는 설치 스크립트 재실행:

```bash
bunx oh-my-opencode install --no-tui --claude=<yes|no|max20> --chatgpt=<yes|no> --gemini=<yes|no>
```
