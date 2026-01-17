---
description: Fork 저장소를 upstream과 동기화 (fetch, rebase, force push, build, link)
---

# Fork 저장소 Upstream 동기화

원본 저장소(upstream)의 변경사항을 fork된 저장소에 동기화.

## 순서

1. **Upstream remote 확인**
   ```bash
   git remote -v
   ```
   - `upstream`이 없으면 추가:
   ```bash
   git remote add upstream https://github.com/code-yeongyu/oh-my-opencode.git
   ```

2. **Upstream 변경사항 가져오기**
   ```bash
   git fetch upstream
   ```

3. **현재 상태 태그 생성 (rebase 전 백업)**
   ```bash
   git tag pre-sync/$(date +%Y%m%d-%H%M)
   ```

4. **Upstream 브랜치에 rebase**
   ```bash
   git rebase upstream/dev
   ```

5. **충돌 발생 시 해결**
   - 충돌 파일 확인: `git status`
   - 해결 후: `git add <파일>` → `git rebase --continue`

6. **main-zsgg 브랜치에 force push (태그 포함)**
   ```bash
   git checkout -B main-zsgg
   git push -u origin main-zsgg --force --tags
   ```

7. **빌드**
   ```bash
   bun install
   bun run build
   ```
