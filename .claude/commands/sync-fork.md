---
description: Fork 저장소를 upstream과 동기화 (fetch, rebase, force push, build, link)
---

# Fork 저장소 Upstream 동기화

원본 저장소(upstream)의 변경사항을 fork된 저장소에 동기화.

## Phase 1: 변경사항 미리보기 (Sync 전 분석)

### 1.1 Upstream remote 확인 및 설정

```bash
git remote -v
```

- `upstream`이 없으면 추가:
```bash
git remote add upstream https://github.com/code-yeongyu/oh-my-opencode.git
```

### 1.2 최신 정보 가져오기

```bash
git fetch upstream
git fetch origin
```

### 1.3 변경사항 분석 및 리포트

**현재 fork의 HEAD와 upstream의 default branch(dev) 사이의 차이를 분석:**

```bash
# 현재 브랜치 확인
CURRENT_BRANCH=$(git branch --show-current)

# upstream에 있고 fork에 없는 커밋들 (새로 추가될 변경사항)
git log --oneline HEAD..upstream/dev
```

**변경사항 요약 리포트 생성:**

아래 정보를 **반드시** 사용자에게 보여줌:

1. **신규 커밋 수**: `git rev-list --count HEAD..upstream/dev`
2. **주요 변경사항** (commit message 기반):
   ```bash
   git log --pretty=format:"- %s (%h, %an)" HEAD..upstream/dev
   ```
3. **변경된 파일 통계**:
   ```bash
   git diff --stat HEAD...upstream/dev
   ```
4. **주요 영향 영역** (디렉토리별 변경):
   ```bash
   git diff --stat HEAD...upstream/dev | grep -E "^\s*(src|script|assets)/"
   ```

### 1.4 사용자 승인 요청

**위 분석 결과를 보여준 후, 다음과 같이 승인 요청:**

> 📋 **Upstream 변경사항 요약**
>
> - 신규 커밋: N개
> - 주요 변경사항:
>   - (commit message 리스트)
> - 변경 파일: N개 파일, +X줄/-Y줄
>
> ⚠️ **sync를 진행하면 위 변경사항이 fork에 적용됩니다.**
>
> 진행하시겠습니까? (yes/no)

**사용자가 승인할 때까지 Phase 2로 진행하지 않음!**

---

## Phase 2: 동기화 실행 (사용자 승인 후)

### 2.1 현재 상태 백업

```bash
git tag pre-sync/$(date +%Y%m%d-%H%M)
```

### 2.2 Upstream 브랜치에 rebase

```bash
git rebase upstream/dev
```

### 2.3 충돌 해결 (발생 시)

- 충돌 파일 확인: `git status`
- 해결 후: `git add <파일>` → `git rebase --continue`
- 충돌 해결 후 사용자에게 해결된 충돌 목록 보고

### 2.4 main-zsgg 브랜치에 force push

```bash
git checkout -B main-zsgg
git push -u origin main-zsgg --force --tags
```

---

## Phase 3: 빌드 및 링크

### 3.1 의존성 설치 및 빌드

```bash
bun install
bun run build
```

### 3.2 로컬 링크 등록

```bash
bun link && bun link oh-my-opencode --cwd ~/.config/opencode
```

### 3.3 링크 확인

```bash
ls -la ~/.config/opencode/node_modules/oh-my-opencode
```

---

## Phase 4: 완료 보고

다음 항목을 포함한 최종 리포트:

1. ✅ 동기화 완료 (적용된 커밋 수)
2. ✅ 빌드 성공 여부
3. ✅ 링크 적용 확인
4. 📝 주요 변경사항 요약 (Phase 1에서 분석한 내용 재참조)
