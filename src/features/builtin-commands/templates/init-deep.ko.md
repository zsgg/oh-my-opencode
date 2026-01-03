# /init-deep

계층적인 AGENTS.md 파일을 생성합니다. 루트 디렉토리와 복잡도 점수가 매겨진 하위 디렉토리를 포함합니다.

## 사용법

```
/init-deep                      # 업데이트 모드: 기존 파일 수정 + 필요한 경우 새 파일 생성
/init-deep --create-new         # 기존 파일 읽기 → 모두 삭제 → 처음부터 다시 생성
/init-deep --max-depth=2        # 디렉토리 깊이 제한 (기본값: 3)
```

---

## 워크플로우 (상위 레벨)

1. **탐색 및 분석** (병렬 수행)
   - 백그라운드 탐색(explore) 에이전트를 즉시 실행
   - 메인 세션: bash 구조 분석 + LSP 코드맵 생성 + 기존 AGENTS.md 읽기
2. **점수 산정 및 결정** - 통합된 분석 결과를 바탕으로 AGENTS.md 위치 결정
3. **생성** - 루트 디렉토리부터 시작하여 하위 디렉토리를 병렬로 생성
4. **검토** - 중복 제거, 내용 정리, 검증

<critical>
**모든 단계를 TodoWrite 하세요. 실시간으로 in_progress → completed로 업데이트합니다.**
```
TodoWrite([
  { id: "discovery", content: "Fire explore agents + LSP codemap + read existing", status: "pending", priority: "high" },
  { id: "scoring", content: "Score directories, determine locations", status: "pending", priority: "high" },
  { id: "generate", content: "Generate AGENTS.md files (root + subdirs)", status: "pending", priority: "high" },
  { id: "review", content: "Deduplicate, validate, trim", status: "pending", priority: "medium" }
])
```
</critical>

---

## Phase 1: 탐색 및 분석 (병렬 수행)

**"discovery"를 in_progress로 표시합니다.**

### 백그라운드 탐색 에이전트를 즉시 실행

대기하지 마세요 — 메인 세션이 작동하는 동안 이들은 비동기로 실행됩니다.

```
// 한 번에 모두 실행하고 나중에 결과를 수집합니다
background_task(agent="explore", prompt="Project structure: PREDICT standard patterns for detected language → REPORT deviations only")
background_task(agent="explore", prompt="Entry points: FIND main files → REPORT non-standard organization")
background_task(agent="explore", prompt="Conventions: FIND config files (.eslintrc, pyproject.toml, .editorconfig) → REPORT project-specific rules")
background_task(agent="explore", prompt="Anti-patterns: FIND 'DO NOT', 'NEVER', 'ALWAYS', 'DEPRECATED' comments → LIST forbidden patterns")
background_task(agent="explore", prompt="Build/CI: FIND .github/workflows, Makefile → REPORT non-standard patterns")
background_task(agent="explore", prompt="Test patterns: FIND test configs, test structure → REPORT unique conventions")
```

<dynamic-agents>
**동적 에이전트 생성**: bash 분석 후, 프로젝트 규모에 따라 추가 탐색(explore) 에이전트를 생성합니다:

| 요소 | 임계값 | 추가 에이전트 |
|--------|-----------|-------------------|
| **총 파일 수** | >100 | 100개 파일당 +1 |
| **총 라인 수** | >10k | 10k 라인당 +1 |
| **디렉토리 깊이** | ≥4 | 깊은 탐색을 위해 +2 |
| **대형 파일 (>500 라인)** | >10개 파일 | 복잡도 핫스팟을 위해 +1 |
| **모노레포** | 감지됨 | 패키지/워크스페이스당 +1 |
| **다중 언어** | >1 | 언어당 +1 |

```bash
# 먼저 프로젝트 규모를 측정합니다
total_files=$(find . -type f -not -path '*/node_modules/*' -not -path '*/.git/*' | wc -l)
total_lines=$(find . -type f \( -name "*.ts" -o -name "*.py" -o -name "*.go" \) -not -path '*/node_modules/*' -exec wc -l {} + 2>/dev/null | tail -1 | awk '{print $1}')
large_files=$(find . -type f \( -name "*.ts" -o -name "*.py" \) -not -path '*/node_modules/*' -exec wc -l {} + 2>/dev/null | awk '$1 > 500 {count++} END {print count+0}')
max_depth=$(find . -type d -not -path '*/node_modules/*' -not -path '*/.git/*' | awk -F/ '{print NF}' | sort -rn | head -1)
```

에이전트 생성 예시:
```
// 500개 파일, 50k 라인, 깊이 6, 15개 대형 파일 → 5+5+2+1 = 13개의 추가 에이전트 생성
background_task(agent="explore", prompt="Large file analysis: FIND files >500 lines, REPORT complexity hotspots")
background_task(agent="explore", prompt="Deep modules at depth 4+: FIND hidden patterns, internal conventions")
background_task(agent="explore", prompt="Cross-cutting concerns: FIND shared utilities across directories")
// ... 계산에 따라 더 많이 추가
```
</dynamic-agents>

### 메인 세션: 병렬 분석

**백그라운드 에이전트가 실행되는 동안**, 메인 세션에서는 다음을 수행합니다:

#### 1. Bash 구조 분석
```bash
# 디렉토리 깊이 + 파일 수
find . -type d -not -path '*/\.*' -not -path '*/node_modules/*' -not -path '*/venv/*' -not -path '*/dist/*' -not -path '*/build/*' | awk -F/ '{print NF-1}' | sort -n | uniq -c

# 디렉토리별 파일 수 (상위 30개)
find . -type f -not -path '*/\.*' -not -path '*/node_modules/*' | sed 's|/[^/]*$||' | sort | uniq -c | sort -rn | head -30

# 확장자별 코드 밀집도
find . -type f \( -name "*.py" -o -name "*.ts" -o -name "*.tsx" -o -name "*.js" -o -name "*.go" -o -name "*.rs" \) -not -path '*/node_modules/*' | sed 's|/[^/]*$||' | sort | uniq -c | sort -rn | head -20

# 기존 AGENTS.md / CLAUDE.md 확인
find . -type f \( -name "AGENTS.md" -o -name "CLAUDE.md" \) -not -path '*/node_modules/*' 2>/dev/null
```

#### 2. 기존 AGENTS.md 읽기
```
발견된 각 기존 파일에 대해:
  Read(filePath=file)
  핵심 통찰, 컨벤션, 안티 패턴 추출
  EXISTING_AGENTS 맵에 저장
```

`--create-new`인 경우: 먼저 모든 기존 파일을 읽고(컨텍스트 보존) → 모두 삭제 → 다시 생성합니다.

#### 3. LSP 코드맵 (사용 가능한 경우)
```
lsp_servers()  # 가용성 확인

# 엔트리 포인트 (병렬)
lsp_document_symbols(filePath="src/index.ts")
lsp_document_symbols(filePath="main.py")

# 주요 심볼 (병렬)
lsp_workspace_symbols(filePath=".", query="class")
lsp_workspace_symbols(filePath=".", query="interface")
lsp_workspace_symbols(filePath=".", query="function")

# 주요 export의 중심성(Centrality) 확인
lsp_find_references(filePath="...", line=X, character=Y)
```

**LSP 폴백(Fallback)**: 사용 불가능한 경우, 탐색(explore) 에이전트와 AST-grep에 의존합니다.

### 백그라운드 결과 수집

```
// 메인 세션 분석이 완료된 후, 모든 작업 결과를 수집합니다
for each task_id: background_output(task_id="...")
```

**병합: bash + LSP + 기존 파일 + 탐색 결과. "discovery"를 완료로 표시합니다.**

---

## Phase 2: 점수 산정 및 위치 결정

**"scoring"을 in_progress로 표시합니다.**

### 점수 산정 매트릭스 (Scoring Matrix)

| 요소 | 가중치 | 높은 임계값 | 출처 |
|--------|--------|----------------|--------|
| 파일 수 | 3x | >20 | bash |
| 하위 디렉토리 수 | 2x | >5 | bash |
| 코드 비율 | 2x | >70% | bash |
| 고유 패턴 | 1x | 자체 설정 파일 보유 | explore |
| 모듈 경계 | 2x | index.ts/__init__.py 보유 | bash |
| 심볼 밀도 | 2x | >30개 심볼 | LSP |
| Export 수 | 2x | >10개 export | LSP |
| 참조 중심성 | 3x | >20개 참조 | LSP |

### 결정 규칙

| 점수 | 작업 |
|-------|--------|
| **루트 (.)** | 항상 생성 |
| **>15** | AGENTS.md 생성 |
| **8-15** | 고유한 도메인인 경우 생성 |
| **<8** | 건너뜀 (상위 디렉토리에서 커버) |

### 출력 결과
```
AGENTS_LOCATIONS = [
  { path: ".", type: "root" },
  { path: "src/hooks", score: 18, reason: "high complexity" },
  { path: "src/api", score: 12, reason: "distinct domain" }
]
```

**"scoring"을 완료로 표시합니다.**

---

## Phase 3: AGENTS.md 생성

**"generate"를 in_progress로 표시합니다.**

### 루트 AGENTS.md (상세 작성)

```markdown
# PROJECT KNOWLEDGE BASE

**Generated:** {TIMESTAMP}
**Commit:** {SHORT_SHA}
**Branch:** {BRANCH}

## OVERVIEW
{1-2문장: 프로젝트 개요 + 핵심 스택}

## STRUCTURE
\\\`\\\`\\\`
{root}/
├── {dir}/    # {자명하지 않은 용도만 설명}
└── {entry}
\\\`\\\`\\\`

## WHERE TO LOOK
| Task | Location | Notes |
|------|----------|-------|

## CODE MAP
{LSP 결과 - 사용 불가능하거나 파일이 10개 미만인 경우 생략}

| Symbol | Type | Location | Refs | Role |
|--------|------|----------|------|------|

## CONVENTIONS
{표준에서 벗어난 고유한 컨벤션만 작성}

## ANTI-PATTERNS (THIS PROJECT)
{이 프로젝트에서 명시적으로 금지된 사항}

## UNIQUE STYLES
{프로젝트 고유의 스타일}

## COMMANDS
\\\`\\\`\\\`bash
{dev/test/build}
\\\`\\\`\\\`

## NOTES
{주의 사항/Gotchas}
```

**품질 게이트**: 50-150 라인, 일반적인 조언 금지, 뻔한 정보 제외.

### 하위 디렉토리 AGENTS.md (병렬 수행)

각 위치에 대해 문서 작성(document-writer) 에이전트를 실행합니다:

```
for loc in AGENTS_LOCATIONS (루트 제외):
  background_task(agent="document-writer", prompt=\`
    Generate AGENTS.md for: ${loc.path}
    - Reason: ${loc.reason}
    - 최대 30-80 라인
    - 상위 디렉토리의 내용을 절대 반복하지 말 것
    - 섹션: OVERVIEW (1라인), STRUCTURE (하위 디렉토리가 5개 이상인 경우), WHERE TO LOOK, CONVENTIONS (다른 경우), ANTI-PATTERNS
  \`)
```

**모든 작업이 끝날 때까지 대기합니다. "generate"를 완료로 표시합니다.**

---

## Phase 4: 검토 및 중복 제거

**"review"를 in_progress로 표시합니다.**

생성된 각 파일에 대해:
- 일반적인 조언 제거
- 상위 디렉토리와 중복되는 내용 제거
- 크기 제한에 맞게 정리
- 간결한(telegraphic) 스타일 검증

**"review"를 완료로 표시합니다.**

---

## 최종 보고서 (Final Report)

```
=== init-deep Complete ===

모드: {update | create-new}

파일:
  ✓ ./AGENTS.md (root, {N} 라인)
  ✓ ./src/hooks/AGENTS.md ({N} 라인)

분석된 디렉토리: {N}
생성된 AGENTS.md: {N}
업데이트된 AGENTS.md: {N}

계층 구조:
  ./AGENTS.md
  └── src/hooks/AGENTS.md
```

---

## 안티 패턴

- **정적인 에이전트 수**: 프로젝트 크기/깊이에 따라 에이전트 수를 반드시 조정해야 함
- **순차적 실행**: 반드시 병렬로 수행해야 함 (탐색 + LSP 동시 진행)
- **기존 내용 무시**: `--create-new` 옵션을 사용하더라도 항상 기존 내용을 먼저 읽어야 함
- **과도한 문서화**: 모든 디렉토리에 AGENTS.md가 필요한 것은 아님
- **중복성**: 하위 디렉토리는 상위 디렉토리의 내용을 반복하지 않음
- **일반적인 내용**: 모든 프로젝트에 적용되는 내용은 제거
- **장황한 스타일**: 간결하게 작성하거나 아예 작성하지 말 것
