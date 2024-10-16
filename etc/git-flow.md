# Git Flow
[Velog](https://velog.io/@semoon/Git-Flow)

## Git Flow

Git을 사용하는 프로젝트에서 협업을 쉽게 하기 위해 만들어진 브랜칭 모델

### `main` 또는 `master` 브랜치

프로덕션에 배포되는 코드

- 작업 X

### `develop` 브랜치

다음 출시 버전 개발 브랜치

- 작업
    - `main` 브랜치로 배포 전 테스트
    - 상시로 버그를 수정한 커밋들이 추가됨
    - 기능 추가의 경우 `feature` 브랜치
- pull
    - `main`
- merge
    - `main`

### `feature` 브랜치

기능 개발 브랜치

- 작업
    - 보통 맡은 작업은 여기서 작업
    - 머지 후 삭제
- pull
    - `develop`
- merge
    - `develop`

### `release` 브랜치

배포 준비 브랜치

- 작업
    - 버그 수정, 문서 업데이트 등
    - 배포 이전 심사 요청 단계
    - 이상이 있을 경우 `release` 브랜치 내에서 수정
- pull
    - `develop`
- merge
    - `develop`, `main`

### `hotfix` 브랜치

프로덕션의 긴급 버그 수정 브랜치

- 작업
    - 배포된 코드의 버그를 수정
    - 배포 파일 만든 후 이상이 있을 경우 `release` 브랜치 내에서 수정
- pull
    - `main`
- merge
    - `develop`, `main`

## Github Flow

GitHub에서 제안한 단순하고 직관적인 브랜칭 모델

- 브랜치의 수가 적어 관리하기 쉽다.
- 단일 브랜치로 배포를 관리하기 때문에 복잡도가 낮다.
- `main`에 병합되면 곧바로 배포가 가능 → CD 환경에서 빠르게 변경사항을 반영

### `main` 브랜치

프로덕션에 배포되는 코드

- 작업 X

### `feature` 브랜치

기능 개발, 버그 수정 브랜치

## Git Flow vs Github Flow

![](https://velog.velcdn.com/images/semoon/post/d068d8a9-02d0-4e72-b00e-dca9f0756227/image.png)
