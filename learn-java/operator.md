# [Java] Operator
[Velog](https://velog.io/@semoon/Java-Operator)<br>
[inflearn] 김영한의 자바 입문 - 코드로 시작하는 자바 첫걸음

---

## 연산자

- 연산자(operator): 연산 기호
- 피연산자(operand): 연산 대상
- 종류
    - 산술 연산자: `+`, `-`, `*`, `/`, `%`
    - 증감 연산자: `++`, `--`
    - 비교 연산자: `==`, `!=`, `>`, `<`, `>=`, `<=`
    - 논리 연산자: `&&`, `||`, `!`
    - 대입 연산자: `=`, `+=`, `-=`, `*=`, `/=`, `%=`
    - 삼항 연산자: `? :`

## 산술 연산자

수학 연산을 수행하는 연산자

- `+` : 더하기
- `-` : 빼기
- `*` : 곱하기
- `/` : 나누기
    - 자바는 `int` 끼리 계산하면 결과도 `int` 를 사용
    - `int` 는 정수이기 때문에 소수점 이하를 표함 불가능
    - 0으로 나눌 경우 `/ by zero` 에러 발생
- `%` : 나머지

```java
package operator;

public class Operator1 {

    public static void main(String[] args) {
        int a = 5;
        int b = 2;

        // 덧셈
        int sum = a + b;
        System.out.println("a + b = " + sum); 
        // 뺄셈
        int diff = a - b;
        System.out.println("a - b = " + diff);
        // 곱셈
        int multi = a * b;
        System.out.println("a * b = " + multi);
        // 나눗셈 -> int 끼리의 계산은 소수점 표현이 없음
        int div = a / b;
        System.out.println("a / b = " + div);
        // 나머지
        int mod = a % b;
        System.out.println("a % b = " + mod);
    }
}
```

```java
// 실행 결과
a + b = 7
a - b = 3
a * b = 10
a / b = 2
a % b = 1
```

## 문자열 더하기

- 문자열 + 문자열: 문자열을 이어붙인 문자열
- 문자열 + 숫자: 숫자를 문자열으로 변환하여 이어붙인 문자열

```java
package operator;

public class Operator2 {

    public static void main(String[] args) {
        // 문자열과 문자열 더하기 1
        String result1 = "hello " + "world";
        System.out.println(result1);
        // 문자열과 문자열 더하기 2
        String s1 = "string1";
        String s2 = "string2";
        String result2 = s1 + s2;
        System.out.println(result2);

        // 문자열과 숫자 더하기 1
        String result3 = "a + b = " + 10;
        System.out.println(result3);
        // 문자열과 숫자 더하기 2
        int num = 20;
        String str = "a + b = ";
        String result4 = str + num;
        System.out.println(result4);

    }
}

```

```java
// 실행 결과
hello world
string1string2
a + b = 10
a + b = 20
```

## 연산자 우선순위

- 우선순위
    1. 괄호 (`()`)
    2. 단항 연산자 (`++`, `--`, `!`, `~`, `new`, `(type)` 등)
    3. 산술 연산자 (`*`, `/`, `%` 다음 `+`, `-`)
    4. Shift 연산자 (`<<`, `>>`, `>>>`)
    5. 비교 연산자 (`<`, `<=`, `>`, `>=`, `instanceof`)
    6. 등식 연산자 (`==`, `!=`)
    7. 비트 연산자 (`&`, `^`, `|`)
    8. 논리 연산자 (`&&`, `||`)
    9. 삼항 연산자 (`? :`)
    10. 대입 연산자 (`=`, `+=`, `-=`, `*=`, `/=`, `%=` 등)
- 수학과 마찬가지의 연산자 우선순위를 적용
- 우선순위가 헷갈린다면 괄호 `()` 를 통해 명시하는 것이 유지보수 용이

```java
package operator;

public class Operator3 {

    public static void main(String[] args) {
        int sum1 = 1 + 2 * 3;
        int sum2 = (1 + 2) * 3;
        System.out.println("sum1 = " + sum1);
        System.out.println("sum2 = " + sum2);
    }
}
```

```java
// 실행 결과
sum1 = 7
sum2 = 9
```

## 증감 연산자

값을 1씩 증가시키거나 감소시키는 연산자

- 증감 연산자
    - `++`: 값을 1 증가
    - `--`: 값을 1 감소
- 전위 증감 연산자
    - 증감 연산자 피연산자 앞에 옴
    - 코드 실행 후 변수 값 증감
- 후위 증감 연산자
    - 증감 연산자 피연산자 뒤에 옴
    - 변수 값 증감 후 코드 실행

```java
package operator;

public class Operator6 {

    public static void main(String[] args) {
        // 전위 증감 연산자
        int a = 1;
        int b = 0;
        b = ++a; // a 값 증감 후 b 대입
        System.out.println("a = " + a);
        System.out.println("b = " + b);
        // 후위 증감 연산자
        a = 1;
        b = 0;
        b = a++; // b에 a 값 대입 후 a 값 증감
        System.out.println("a = " + a);
        System.out.println("b = " + b);
    }
}

```

```java
// 실행 결과
a = 2
b = 2
a = 2
b = 1
```

## 비교 연산자

- 비교 연산자
    - `==`: 같다
    - `!=`: 같지 않다
    - `<`: 크다
    - `<=`: 크거나 같다
    - `>`: 작다
    - `>=`: 작거나 같다
- 결과로 boolean을 반환
- 문자열 비교
    - 문자열이 같은지 비교할 때는 `==`이 아니라 `.equals()` 메서드를 사용

```java
package operator;

public class Operator7 {

    public static void main(String[] args) {
        int a = 2;
        int b = 3;

        // 비교 연산자
        System.out.println(a == b);
        System.out.println(a != b);
        System.out.println(a > b);
        System.out.println(a < b);
        System.out.println(a >= b);
        System.out.println(a <= b);

        boolean result = a == b;
        System.out.println(result);

        System.out.println("");
        // 문자열 비교
        String str1 = "string1";
        String str2 = "string2";
        boolean result1 = "hello".equals("hello"); // 리터럴 비교
        boolean result2 = str1.equals("string1"); // 문자열 변수, 리터럴 비교
        boolean result3 = str1.equals(str2); // 문자열 변수 비교
        System.out.println(result1);
        System.out.println(result2);
        System.out.println(result3);
    }
}
```

```java
// 실행 결과
false
true
false
true
false
true
false

true
true
false
```

## 논리 연산자

`boolean` 형을 비교하는데 사용하는 연산자

- 논리 연산자
    - `&&`: AND
    - `||`: OR
    - `!`: NOT

```java
package operator;

public class Logical1 {

    public static void main(String[] args) {
        System.out.println("&&: AND 연산");
        System.out.println(true && true);
        System.out.println(true && false);
        System.out.println(false && false);

        System.out.println("||: OR 연산");
        System.out.println(true || true);
        System.out.println(true || false);
        System.out.println(false || false);

        System.out.println("! 연산");
        System.out.println(!true);
        System.out.println(!false);

        System.out.println("변수 활용");
        boolean a = true;
        boolean b = false;
        System.out.println(a && b);
        System.out.println(a || b);
        System.out.println(!a);
        System.out.println(!b);
    }
}
```

```java
// 실행 결과
&&: AND 연산
true
false
false
||: OR 연산
true
true
false
! 연산
false
true
변수 활용
false
true
false
true
```

## 대입 연산자

값을 변수에 할당하는 연산자

- 대입 연산자
    - `=` : 오른쪽 값을 왼쪽 변수에 할당
- 축약(복합) 대입 연산자
    
    산술 연산자와 대입 연산자를 축약하여 사용
    
    - `+=`
    - `-=`
    - `*=`
    - `/=`
    - `%=`
