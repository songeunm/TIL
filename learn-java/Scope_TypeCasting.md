# [Java] Scope / Type Casting

[Velog](https://velog.io/@semoon/Java-Scope-Type-Casting)

# Scope

변수에 접근 가능한 범위

- 지역 변수
- 멤버 변수

## 지역 변수 Local Variable

특정 코드 블록에서만 사용할 수 있는 변수

- 선언된 코드 블록을 벗어나면 제거됨
- 코드 블록을 벗어나 접근하면 컴파일 에러 발생

# Type Casting

형이 변경되는 것

- `int` 변수의 앞에 `(double)` 과 같이 적어주면 `int` → `double` 으로 형이 변환됨
- 형변환이 적용된 변수 자체의 형이 바뀌는 것이 아니라, 형을 변환하여 리턴하는 느낌
- 자동 형변환 (묵시적 형변환)
    - 작은 범위에서 큰 범위로의 대입을 하는 것
    - 자바 안에서 자동으로 일어남 (직접 하지 않아도 됨)
- 명시적 형변환
    - 큰 범위에서 작은 범위로의 대입을 하는 것
    - 직접 해줘야 함
    - 오버플로우 발생 가능
        - 해당 타입의 표현 가능 범위를 넘어서 다시 (표현 가능한 가장 작은 값으로) 초기화되는 것
        - 오버플로우가 발생하지 않도록 타입의 사이즈를 늘려줘야함

```java
package casting;

public class Casting3 {

    public static void main(String[] args) {
        long maxIntValue = 2147483647; // int 최대값
        long maxIntOver = 2147483648L; // int 최대값 + 1 (초과)
        int intValue = 0;

        intValue = (int) maxIntValue;
        System.out.println("maxintValue casting = " + intValue);

        intValue = (int) maxIntOver;
        System.out.println("maxIntOver casting = " + intValue); // 오버플로우
    }
}
```

```java
// 결과
maxintValue casting = 2147483647
maxIntOver casting = -2147483648
```

## 계산과 형변환

```java
package casting;

public class Casting4 {
    public static void main(String[] args) {
        int div1 = 3 / 2; // int, int 계산 -> int(1)
        System.out.println("div1 = " + div1);
        
        double div2 = 3 / 2; // int, int 계산 -> int(1) -> 자동 형변환 -> double(1.0)
        System.out.println("di2 = " + div2);
        
        double div3 = 3.0 / 2; // double, int 계산 -> double(1.5)
        System.out.println("div3 = " + div3);
        
        double div4 = (double) 3 / 2; // 명시적 형변환 -> double, int 계산 -> double(1.5) 
        System.out.println("div4 = " + div4);
    }
}
```

```java
// 결과
div1 = 1
di2 = 1.0
div3 = 1.5
div4 = 1.5
```
