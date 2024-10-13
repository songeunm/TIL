# [Java] Variable

[Velog](https://velog.io/@semoon/Java-Variable)

김영한의 자바 입문 - 코드로 시작하는 자바 첫걸음

---

![](https://velog.velcdn.com/images/semoon/post/7405fd0c-1ae0-4a66-a3f8-e0c8b18406ab/image.png) 변수 미사용 | ![](https://velog.velcdn.com/images/semoon/post/90e3a506-6e4a-4763-90cd-9c5cd28fcc9e/image.png) 변수 사용
--- | --- | 

1. `variable` 패키지 생성
2. `variable` 패키지 아래에 `Var1` , `Var2` 자바 클래스 생성
    - 클래스는 관습적으로 **첫글자가 대문자**

### 패키지(package)

- 자바 파일을 구분하기 위한 폴더
- 패키지에 들어가는 자바 파일의 첫 줄에는 `package [패키지명]` 을 넣어주어야함
- 자바 파일이 위치하는 패키지와 패키지 선언의 위치가 같아야 한다.

### 변수(variable)

- 데이터를 담는 그릇
- Var1과 달리 Var2와 같이 변수를 사용하는 경우 수정 및 관리가 용이
- 변수 선언: 변수를 만드는 것
- 변수 초기화: 변수에 처음으로 값을 대입하는 것
- `[변수형] [변수명];` 과 같이 생성

### 변수 선언과 초기화

```java
package variable;

public class Var5 {
    public static void main(String[] args) {
		    // 1. 변수 여러개 선언
		    int a, b, c;
		    
        // 2. 변수 선언, 초기화 따로
        int d;
        d = 1;
        System.out.println(d);

        // 3. 변수 선언, 초기화 동시에
        int e = 2;
        System.out.println(e);
        
        // 4. 여러 변수 선언, 초기화 동시에
        int f = 3, g = 4, h = 5;
        System.out.println(f);
        System.out.println(g);
        System.out.println(h);
    }
}

```

- 변수 선언
    - 컴퓨터의 메모리 공간을 확보하고 메모리 공간에 접근할 수 있는 이름을 부여
    - 하나씩 선언할 수도 있고, 한번에 여러 개의 변수를 선언할 수도 있음
- 초기화
    - 변수 선언 후 처음으로 값을 저장하는 것
    - `int b = 2;` 와 같이 변수 선언과 초기화를 동시에 할 수 있음
- 초기화 하지 않은 변수 사용
    - 컴파일 에러 발생
        
        ```java
        java: variable a might not have been initialized
        ```
        
    - 메모리는 여러 시스템이 함께 사용하는 공간 → 어떤 값들이 계속 저장되고있음
    - 변수를 처음 선언했을 때 기존에 어떤 값이 있는지는 모름 → 자바는 오류를 방지하기 위해 초기화를 강제
    - 지역 변수(Local Variable)은 개발자가 직접 초기화
    - 클래스 변수, 인스턴스 변수는 자바가 자동으로 초기화
    - 컴파일 에러가 발생하면 에러가 발생한 라인을 주석처리

### 변수 타입

```java
package variable;

public class Var7 {
    public static void main(String[] args) {
        int a = 100; // 정수
        double b = 10.5; // 실수
        boolean c = true; // 불리안
        char d = 'A'; // 문자 하나
        String e = "Hello Java"; // 문자열

        System.out.println(a);
        System.out.println(b);
        System.out.println(c);
        System.out.println(d);
        System.out.println(e);
    }
}

```

- `String` 은 첫 글자가 대문자로 시작하는 특별한 타입
- 리터럴(literal)
    - 코드에서 개발자가 직접 적은 고정된 값을 부르는 프로그래밍 용어
    - 변수의 값은 변할 수 있지만 리터럴은 변하지 않음

### 숫자 타입

```java
package variable;

public class Var8 {
    public static void main(String[] args) {
        // 정수형
        byte b = 127; // -128 ~ 127 (1byte, 2^8)
        short s = 32767; // -32768 ~ 32767 (2byte, 2^16)
        int i = 2147483647; // -2147483648 ~ 2147483647 (약 20억) (4byte, 2^32)
        long l = 9223372036854775807L; // 정수 중 제일 큼 (8byte, 2^64)

        // 실수형
        float f = 10.0f; // 뒤에 f 붙여줘야 함 (4byte, 2^32)
        double d = 10.0; // float 보다 정밀도가 높음 (8byte, s^64)

        // 기타
        boolean bl = true; // true, false (1byte)
        char c = 'c'; // 문자 하나 (1byte)
        String st = "string"; // 문자열 (문자열 길이에 따라 메모리가 동적으로 변경)
    }
}
```

- 큰 숫자를 표현할 수 있는 변수형일수록 큰 메모리 공간을 차지
- 리터럴 타입 지정
    - 정수는 `int`, 실수는 `double` 이 기본
    - 정수 리터럴이 `int` 의 범위인 약 20억을 넘어가면 뒤에 `L` 을 붙여서 `long` 으로 변경해야 함 (소문자 `l` 도 가능하나 `1` 과 헷갈리기 때문에 관례적으로 대문자를 사용함)
    - 실수 리터럴을 `float` 형으로 사용하고 싶다면 뒤에 `f` 를 붙여 `float` 으로 변경 ( `float` 은 오차가 많이 발생해서 실무에서는 보통 `double` 을 사용함)

### 변수 타입 정리

- 실무에서 거의 사용하지 않는 타입
    - `byte`, `short`
        - 표현 길이가 너무 작다.
        - 자바는 기본으로 4byte(`int`)를 효율적으로 계산하도록 설계되어 있다.
        - 대신 파일을 바이트 단위로 다루기 때문에 `byte`는 파일 전송/파일 복사 등에 주로 사용된다.
    - `float`
        - 표현 길이가 너무 작다.
        - 정밀도가 낮다.
    - `char`
        - 표현 길이가 너무 작다.
        - 문자 하나를 표현할 때에도 `String` 을 사용할 수 있다.

⇒ 메모리 용량은 매우 저렴! → 메모리 용량 약간 절약 <<< 개발 속도나 효율에 초점

- 실무에서 자주 사용하는 타입
    - 정수: `int`, `long`
        - 자바는 정수에 기본으로 `int`를 사용, 20억이 넘을 경우 `long` 사용
        - 파일을 다룰 때는 `byte` 사용
    - 실수: `double`
    - 불린 - `boolean`
        - 조건문에서 자주 사용
    - 문자열: `String`

### 변수 명명 규칙

- **규칙**: 지키지 않으면 컴파일 에러 발생
    - 숫자로 시작할 수 없다.
    - 공백이 들어갈 수 없다.
    - 자바 예약어를 변수 이름으로 사용할 수 없다. (`int`, `class`, `public`)
    - 영문자, 숫자, 달러 기호, 언더바만 사용할 수 있다.
- **관례**: 필수는 아니지만 보통은 이를 따름
    - 소문자로 시작하는 낙타 표기법(Carmel case)을 사용한다. (`orderDetail`, `myAccount`)
- 자바 언어의 관례
    - 클래스: 대문자로 시작 + 낙타 표기법
    - 상수: 모두 대문자 + 언더바
    - 패키지: 모두 소문자
    - 나머지: 소문자로 시작 + 낙타 표기법

❗️변수 이름은 용도를 명확하게 설명해야한다.
