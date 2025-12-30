# Fork 저장소 최신화 가이드

원본 저장소(upstream)의 변경사항을 fork된 저장소에 동기화하는 방법.

## 사전 조건

- Git 설치
- Fork된 저장소가 로컬에 clone 되어 있음

## 1. Upstream Remote 추가 (최초 1회)

```bash
# upstream 확인
git remote -v

# upstream이 없으면 추가
git remote add upstream https://github.com/code-yeongyu/oh-my-opencode.git
```

## 2. Upstream 변경사항 가져오기

```bash
git fetch upstream
```

## 3. 브랜치 동기화 (Rebase)

깔끔한 선형 히스토리 유지.

```bash
git checkout dev
git rebase upstream/dev
```

## 4. Fork에 Push

Rebase 후 force push 필요:

```bash
git push origin dev --force-with-lease
```

## 충돌 해결

충돌 발생 시:

```bash
# 충돌 파일 확인
git status

# 파일 수정 후
git add <충돌_해결된_파일>

# Rebase 계속
git rebase --continue
```

## 자주 사용하는 명령어 요약

| 작업 | 명령어 |
|------|--------|
| Upstream 추가 | `git remote add upstream <url>` |
| Upstream fetch | `git fetch upstream` |
| 브랜치 rebase | `git rebase upstream/<branch>` |
| Fork에 push | `git push origin <branch> --force-with-lease` |

## 참고

- `--force-with-lease`: 안전한 force push (다른 사람의 커밋 덮어쓰기 방지)
- 정기적으로 동기화하면 충돌 최소화 가능
