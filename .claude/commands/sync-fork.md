---
description: Fork 저장소를 upstream과 동기화 (fetch, rebase, force push, build, link)
---

# Fork 저장소 Upstream 동기화

원본 저장소(upstream)의 변경사항을 fork된 저장소에 동기화.

## 순서

1. **Changelog 분석 및 리포트 생성**
   - upstream fetch 후 현재 HEAD와 upstream/dev 사이의 diff 분석
   ```bash
   git fetch upstream
   git log HEAD..upstream/dev --oneline --no-merges
   ```
   - 각 커밋 메시지를 분석하여 changelog 생성
   - 결과를 `./doc/fork.md`에 출력 (아래 포맷 참고)

   **fork.md 포맷:**
   ```markdown
   ## Upstream Changelog

   **동기화 일시**: YYYY-MM-DD HH:MM
   **비교 범위**: HEAD..upstream/dev

   ### 변경사항 요약

   #### Features
   - [commit_hash] 커밋 메시지 요약

   #### Fixes
   - [commit_hash] 커밋 메시지 요약

   #### Refactor
   - [commit_hash] 커밋 메시지 요약

   #### Others
   - [commit_hash] 커밋 메시지 요약

   ### 전체 커밋 목록
   - commit_hash: 전체 커밋 메시지
   ```

2. **fork-summary.md 생성**
   - `fork.md`의 내용을 분석하여 사용자가 이해하기 쉬운 요약본 작성
   - 주요 변경사항을 카테고리별로 정리
   - 한 줄 요약 포함
   - 파일 위치: `./doc/fork-summary.md`

3. **Upstream remote 확인**
   ```bash
   git remote -v
   ```
   - `upstream`이 없으면 추가:
   ```bash
   git remote add upstream https://github.com/code-yeongyu/oh-my-opencode.git
   ```

4. **현재 상태 태그 생성 (rebase 전 백업)**
   ```bash
   git tag pre-sync/$(date +%Y%m%d-%H%M)
   ```

5. **Upstream 브랜치에 rebase**
   ```bash
   git rebase upstream/dev
   ```

6. **충돌 발생 시 해결**
   - 충돌 파일 확인: `git status`
   - 해결 후: `git add <파일>` → `git rebase --continue`

7. **main-zsgg 브랜치에 force push (태그 포함)**
   ```bash
   git checkout -B main-zsgg
   git push -u origin main-zsgg --force --tags
   ```

8. **빌드**
   ```bash
   bun install
   bun run build
   ```
