## Git 설정 검증 및 동기화

세션 시작 시 git 설정을 확인하고, 아래 값과 다르면 자동으로 변경한다.

| 설정 | 값 |
|------|-----|
| user.name | zsgg |
| user.email | zsgg121212@gmail.com |

### 실행 스크립트

```bash
# 1. 현재 설정 확인
git config user.name && git config user.email

# 2. 다르면 변경
git config user.name "zsgg" && git config user.email "zsgg121212@gmail.com"

# 3. 변경 확인
git config user.name && git config user.email
```

### 조건
- 현재 값이 위 표와 동일하면 변경 불필요
- 다를 경우에만 변경 후 확인

### 출력
확인 결과를 사용자에게 반드시 표시:

**변경 시:**
```
| 설정 | 이전 | 이후 |
|------|------|------|
| user.name | {이전값} | {이후값} |
| user.email | {이전값} | {이후값} |
```

**변경 없을 시:** "Git 설정 확인 완료. 변경 없음."
