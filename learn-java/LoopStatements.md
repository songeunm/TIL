# [Java] 반복문

[Velog](https://velog.io/@semoon/Java-%EB%B0%98%EB%B3%B5%EB%AC%B8)<br>
[inflearn] 김영한의 자바 입문 - 코드로 시작하는 자바 첫걸음

---

특정 코드를 반복해서 실행

Java에서는 아래의 세가지 반복문 제공

- `while`
- `do-while`
- `for`

## while

조건에 따라 코드를 반복해서 실행

- 조건문에 True를 넣어 무한반복 가능
- 장점
    - 루프의 조건이 루프 내부에서 변경되는 경우 관리 쉬움
    - 복잡한 조건 및 시나리오에 적합
    - 종료 시점을 명확하게 알 수 없는 경우 용이
- 단점
    - 초기화, 조건 체크, 반복 후의 작업이 분산되어 있어 코드 이해 및 작성이 어려움
    - 루프 변수가 while 블록 바깥에서도 접근 가능 → 실수 가능성 증가

```java
package operator;

public class While_1 {
    public static void main(String[] args) {
        int count = 0;
        
        while (count < 5){
            System.out.println("현재 숫자: " + count);
            count ++;
        }
    }
}
```

```java
// 결과
현재 숫자: 0
현재 숫자: 1
현재 숫자: 2
현재 숫자: 3
현재 숫자: 4
```

💡 변경사항이 발생했을 때 변경해야 하는 부분이 적을수록 좋은 코드이다.

## do-while

while문과 비슷하지만 조건에 상관 없이 최초 한 번은 무조건 실행

```java
package loop;

public class DoWhile1 {
    public static void main(String[] args) {
        int i = 10;

        do {
            System.out.println("현재 숫자: " + i);
            i++;
        } while (i < 3);
    }
}
```

```java
// 결과
현재 숫자: 10
```

## break, continue

반복문에서 사용 가능한 키워드

- `break`

반복문을 즉시 종료 후 반복문을 빠져나감

- `continue`

반복문의 나머지 부분을 건너뛰고 다음 반복으로 진행

```java
package loop;

public class Break1 {
    public static void main(String[] args) {
        int sum = 0;
        int i = 1;

        while(true){
            sum += i;
            if (sum > 10){
                System.out.println("합이 10보다 크면 종료: i=" + i + " sum=" + sum);
                break;
            }
            i++;
        }
    }
}
```

```java
package loop;

public class Continue1 {
    public static void main(String[] args) {
        int i = 1;

        while (i <= 5){
            if (i == 3){
                i++;
                continue;
            }
            System.out.println(i);
            i++;
        }
    }
}

```

## for

주로 반복 횟수가 정해져 있는 경우 사용하는 반복문

- 초기식, 조건식, 증감식 생략 가능하나 세미콜론은 유지해야 함
- 초기식, 조건식, 증감식에 컴마를 사용하여 여러 개를 넣을 수 있음
- 장점
    - 초기화, 조건 검사, 반복 후 작업 등이 규칙적으로 한줄에 모두 명시되어 이해가 쉬움, 편리
    - 반복을 위한 카운터 변수를 다른 부분과 명확히 구분 가능
    - 루프 변수의 범위가 for 루프 블록에 제한됨
- 단점
    - 루프의 조건이 루프 내부에서 변경되는 경우 관리 어려움
    - 복잡한 조건을 가진 경우 어려움

```java
package loop;

public class For1 {
    public static void main(String[] args) {
        int sum = 0;
        int endNum = 3;

        for (int i = 1; i < endNum + 1; i++) {
            sum += i;
            System.out.println("i=" + i + " sum=" + sum);
        }
    }
}
```

```java
// 결과
i=1 sum=1
i=2 sum=3
i=3 sum=6
```

# 추가 정보

`System.out.println()` 을 하면 출력 후 줄바꿈을 함

`System.out.print()` 을 하면 출력 후 줄바꿈을 하지 않음!
