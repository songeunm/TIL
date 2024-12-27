# [Git] git-flow feature 브랜치의 흐름

[Velog](https://velog.io/@semoon/Git-git-flow-feature-브랜치의-흐름)

# git-flow에서 브랜치의 흐름 (feature)
feature 생성 및 이동 > 작업 > 커밋 및 푸쉬 > develop 이동 > 머지 > feature 삭제
### 1. feature 브랜치 생성

- 브랜치 생성
```
git branch feature/new_feature
```

- 브랜치 이동
```
git checkout feature/new_feature
```

- 브랜치 생성 및 이동 (`git 2.23` 이상)
```
git switch -c feature/new_feature
```
`-c`는 `create`를 의미

### 2. feature 브랜치에서 작업

### 3. 작업 내용 **커밋**
```
git commit -m "processing feature"
```

### 4. 작업 내용 **푸쉬**
```
git push origin feature/new_feature
```

### 5. develop 브랜치로 이동
충돌 발생시 충돌 해결 후 머지 내용 커밋
- 브랜치 이동
```
git checkout feature/develop
```
- 브랜치 최신 상태 확인
```
git pull origin develop
```
### 6. feature > develop **머지**
git은 머지 후 브랜치가 유지됨.
git-flow에서는 머지 후 브랜치 삭제 권장.
- 머지된 브랜치 목록 확인
```
git branch --merged
```
- 머지되지 않은 브랜치 목록 확인
```
git branch --no-merged
```
- 브랜치 머지
```
git merge feature/new_feature
```

### 7. develop **푸쉬**
```
git push origin develop
```

### 8. **로컬** feature 브랜치 삭제
- 병합된 로컬 브랜치 삭제
```
git branch -d feature/new_feature
```

- 병합되지 않은 로컬 브랜치 삭제 **(forced)**
```
git branch -D feature/new_feature
```

### 9. **원격** feature 브랜치 삭제
- 원격 브랜치 삭제
```
git push origin --delete feature/new_feature
```
