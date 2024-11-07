# [Java] 조건문
[inflearn] 김영한의 자바 입문 - 코드로 시작하는 자바 첫걸음

---
# 조건문
특정 조건에 따라서 다른 코드를 실행하는 것

## if, else, else if

- if문
    - 조건을 만족할 경우 블록을 실행
- else문
    - if문의 조건을 만족하지 않은 경우 블록을 실행
    - if문의 뒤에 쓰임
- else if문
    - if문의 조건이 거짓일 때 **다음 조건을 순서대로 확인** 후 조건을 만족할 경우 블록을 실행
- if문 vs. else if문
    - if → 서로 연관된 조건인 경우
    - else if → 독립적인 조건인 경우

```java
package cond;

public class If2 {
    public static void main(String[] args) {
        int age = 14;

        if (age <= 7) {
            System.out.println("미취학");
        } else if (age <= 13) {
            System.out.println("초등학생");
        } else if (age <= 16) {
            System.out.println("중학생");
        } else if (age <= 19) {
            System.out.println("고등학생");
        } else {
            System.out.println("성인");
        }
    }
}
```

```java
// 실행 결과
중학생
```

## switch

- if문을 좀더 편리하게 사용할 수 있는 기능 → if문보다 직관적
- if와 달리 비교연산자 사용이 불가능하고, **값이 같은지 확인만 가능**
- `break`에 주의! ⇒ break 없으면 다음 케이스도 실행하게 됨

```java
package cond;

public class Switch1 {
    public static void main(String[] args) {
        int grade = 2;

        int coupon;
        switch (grade) {
            case 1:
                coupon = 1000;
                break;
            case 2:
                coupon = 2000;
                break;
            case 3:
                coupon = 3000;
                break;
            default:
                coupon = 500;
        }
        System.out.println("발급받은 쿠폰: " + coupon);
    }
}
```

```java
// 실행 결과
발급 받은 쿠폰: 2000
```

## 새로운 switch문 (자바 14)

- 기존 switch문과의 차이
    - `->`를 사용함
    - 선택된 데이터를 반환할 수 있음
    - 더 깔끔해짐

```java
package cond;

public class Switch2 {

    public static void main(String[] args) {
        int grade = 2;

        int coupon = switch (grade) {
            case 1 -> 1000;
            case 2 -> 2000;
            case 3 -> 3000;
            default -> 500;
        };
        System.out.println("발급받은 쿠폰: " + coupon);
    }
}
```

## 삼항 연산자 (조건 연산자)

- `(조건) ? 참_표현식 : 거짓_표현식` 단순하게 참과 거짓에 따라 값을 구하는 경우 사용 가능
- 자바에서 유일하게 항이 3개인 연산자
- 표현식에 블럭은 넣을 수 없고 단순 표현식만 넣을 수 있음
- if문보다 간결하게 표현 가능

```java
package cond;

public class CondOp2 {

    public static void main(String[] args) {
        int age = 18;
        String status = (age >= 20) ? "성인" : "미성년자";
        System.out.println(status);
    }
}
```

```java
//실행 결과
미성년자
```
