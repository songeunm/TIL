# [Java] Hello World

[Velog](https://velog.io/@semoon/Java-Hello-World)<br>
[inflearn] 김영한의 자바 입문 - 코드로 시작하는 자바 첫걸음

---

# 개발 환경 설정

- IDE - 인텔리제이(IntelliJ) vs 이클립스(Eclipse)
    - (과거) 이클립스 → (최근) 인텔리제이 (빠른 속도, 편의성)
- OS - 윈도우 vs Mac
    - 자바 개발 대부분의 회사는 Mac 사용, but 윈도우 사용 무방

### 인텔리제이(IntelliJ) 설치

[IntelliJ 설치 링크](https://www.jetbrains.com/ko-kr/idea/download/?section=mac)

- 본인에 맞는 OS 선택
- IntelliJ IDEA Community Edition (무료 버전) 다운로드
- 새로운 프로젝트 생성
    - JDK →Add SDK → Download JDK → Version = 21, Vendor = Oracle OpenJDK
    (mac m1, m2 사용자는 뒤에 aarch64 붙은 vendor 선택)
    - 아래 처럼 바로 추천해주기도 하는듯. 강의 당시 version 21이 최신이랬는데 학습 날짜 기준 version 23이 최신이라 추천해준듯 하다.
    <img src=https://velog.velcdn.com/images/semoon/post/08863e66-2f10-4fa2-9a5f-76ee6100fa53/image.png width=80%>
    
- 화면 구성

	<img src=https://velog.velcdn.com/images/semoon/post/7da00640-0b26-4540-9f5f-be7a5098ac49/image.png width=80%>

- 한글 언어팩 해제
    - IntelliJ IDEA → Settings… → Plugins → Korean 검색 → Korean Language Pack 체크 해제

# 자바 프로그램 실행

- `src`에서 우클릭 → `HelloJava` 이름으로 class 파일 생성
	<img src=https://velog.velcdn.com/images/semoon/post/958f419e-be98-45f0-adc0-74d217037989/image.png width=80%>

### HelloJava 코드

```java
public class HelloJava (
	
		public static void main(String[] args) {
				System.out.println("hello java");
		}
)
```

```java
hello java
```

❗️java는 대소문자를 구분함. 주의!

### 코드 설명

`public class HelloJava`

- HelloJava 클래스
- 중괄호 `{}`를 사용해 블록 표시
- 파일과 클래스 명이 일치해야함

`public static void main(String[] args)`

- main 메서드
- 자바는 main 메서드를 찾아 프로그램을 시작 (프로그램의 시작점)
- 중괄호 `{}`를 사용해 블록 표시
- 자동완성: `psvm`

`System.out.println("hello java");`

- System.out.println() → 값을 콘솔에 출력
- “hello java” → 문자열에 쌍따옴표 `"` 사용
- ; → 문장이 끝나면 세미콜론으로 구분
- 자동완성: `sout`

### 실행 과정

1. HelloJava 프로그램 실행
2. 시작점인 main 메서드 실행
3. System.out.println(”hello java”) 실행 → hello java 출력
4. main 메서드의 블록이 끝나면 프로그램 종료

### 주석(comment)

소스코드에 대한 설명을 첨부하거나 특정 코드의 실행을 지우지 않고 막고싶을 때 사용

- 한 줄 주석 (single line comment) `//`
- 여러 줄 주석 (multi line comment) `/* */`

<img src=https://velog.velcdn.com/images/semoon/post/60e27ed0-9d05-49db-99ef-2d163b0d3a50/image.png width=80%>

# 자바(Java)란?

<img src=https://velog.velcdn.com/images/semoon/post/32ea70a8-9437-4bce-983b-bb064e975e9d/image.png width=80%>

- 자바의 표준적인 설계도, 문서
- 자바 커뮤니티 프로세스(JCP)를 통해 관리됨
- 여러 회사에서 자바 표준 스펙에 맞춰 자바 프로그램 개발
- 자바 구현들은 모두 표준 스펙에 맞도록 개발되어 있어서 변경해도 대부분 문제없이 작동

### 컴파일과 실행
<img src=https://velog.velcdn.com/images/semoon/post/667e15db-7849-45a9-a5ae-57b073846599/image.png width=80%>

- 실행 순서
    1. 개발자가 자바 _소스 코드_ `.java` 작성
    2. 자바 컴파일러 `javac` 를 사용해 소스 코드 _컴파일_
        - `.java` (소스 코드)→ `.class` (바이트 코드) 파일 생성
        - 자바 가상 머신에서 더 빠르게 실행될 수 있게 **최적화**, **문법 오류 검출**
        - 인텔리제이에서 자동으로 처리
            - out 폴더에 `.class` 파일
    3. 자바 프로그램 _실행_
        - 자바에서 제공하는 `java` 라는 프로그램 사용
        - **자바 가상 머신(JVM)**이 실행되면서 프로그램 작동
- IDE와 자바

    <img src=https://velog.velcdn.com/images/semoon/post/0fbc6cf8-e9be-48cb-b8da-b6ef0eb64515/image.png width=50%>

    
    - 인텔리제이는 내부에 자바를 편리하게 설치·관리할 수 있는 기능 제공
    - 자바를 직접 설치하면 환경 설정이 복잡
    - 인텔리제이에서 소스 코드 컴파일 및 실행을 한번에 처리

### 자바와 운영체제 독립성

![](https://velog.velcdn.com/images/semoon/post/9f546746-3ba6-4b80-92d7-be48663f2e01/image.png) 일반 프로그램 |![](https://velog.velcdn.com/images/semoon/post/2cad6f80-08ee-4717-8c2b-d9d6d66677c2/image.png) 자바 프로그램
--- | --- | 

- 일반적인 프로그램 → 다른 OS에서 실행 불가능
- 자바 프로그램 → 자바가 설치된 모든 OS에서 실행 가능

⇒ 자바 개발자는 **OS에 상관 없이 자바에 맞추어 개발**하면 됨

### 자바 개발 환경과 운영 환경

<img src=https://velog.velcdn.com/images/semoon/post/277afa0a-7a33-4188-b72e-70703c02ddb8/image.png width=80%>


- 개발자들은 주로 Windows·Mac OS 사용 ↔ 서버는 주로 Linux 사용
- 자바의 독립성 덕분에 각각의 환경에 맞추어 자바 설치가 가능
- 개발자들이 각자 OS에 맞는 자바를 통해 개발 후 `.class` 파일 서버에 배포
 → (AWS 가정) Amazon Corretto 자바를 AWS 리눅스 서버에 설치해 적용
