# [Java] Scanner

[Velog](https://velog.io/@semoon/Java-Scanner)<br>
[inflearn] 김영한의 자바 입문 - 코드로 시작하는 자바 첫걸음

---

# Scanner

사용자의 입력을 받는 클래스

- 출력 `System.out` 과 같이 `System.in` 을 통해 입력을 받을 수 있으나 복잡
- Scanner를 사용하면 편리하게 입력을 받을 수 있음
- **input type을 맞추어 입력받아야함**

```java
package scanner;

import java.util.Scanner; // java의 util 에서 Scanner를 import

public class Scanner1 {

    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);

        System.out.print("문자열을 입력하세요: ");  // print -> 출력 후 자동 줄바꿈X
        String str = scanner.nextLine(); // 엔터를 입력할 때 까지 문자를 입력 받음
        System.out.println("입력한 문자열: " + str);

        System.out.print("정수를 입력하세요: ");
        int intValue = scanner.nextInt(); // 정수를 입력 받음
        System.out.println("입력한 정수: " + intValue);

        System.out.print("실수를 입력하세요: ");
        double doubleValue = scanner.nextDouble(); // 실수를 입력 받음
        System.out.println("입력한 실수: " + doubleValue);
    }
}
```

```java
// 실행 결과

문자열을 입력하세요: hello
입력한 문자열: hello
정수를 입력하세요: 100
입력한 정수: 100
실수를 입력하세요: 100.5
입력한 실수: 100.5
```

# Scanner 사용 예제

- Scanner 예제
    - 예제 1: 정수합 계산기
        
        ```java
        package scanner;
        
        import java.util.Scanner;
        
        public class Scanner2 {
        
            public static void main(String[] args) {
                Scanner scanner = new Scanner(System.in);
        
                System.out.print("첫번째 숫자 입력: ");
                int num1 = scanner.nextInt();
                System.out.print("두번째 숫자 입력: ");
                int num2 = scanner.nextInt();
        
                int sum = num1 + num2;
                System.out.println("두 숫자의 합: " + sum);
            }
        }
        ```
        
    - 예제 2: 더 큰 숫자 구하기
        
        ```java
        package scanner;
        
        import java.util.Scanner;
        
        public class Scanner3 {
        
            public static void main(String[] args) {
                Scanner scanner = new Scanner(System.in);
        
                System.out.print("첫번째 숫자 입력: ");
                int num1 = scanner.nextInt();
                System.out.print("두번째 숫자 입력: ");
                int num2 = scanner.nextInt();
        
                if (num1 > num2) {
                    System.out.println("더 큰 숫자: " + num1);
                } else if (num1 < num2) {
                    System.out.println("더 큰 숫자: " + num2);
                } else {
                    System.out.println("두 숫자가 같습니다.");
                }
        
            }
        }
        ```
        
- Scanner + 반복문
    - 예제 1: “exit”이 입력될 때까지 입력을 그대로 출력하기
        
        ```java
        package scanner;
        
        import java.util.Scanner;
        
        public class ScannerEx1 {
        
            public static void main(String[] args) {
                Scanner scanner = new Scanner(System.in);
        
                while (true) {
                    System.out.print("입력: ");
                    String inp = scanner.nextLine();
        
                    if (inp.equals("exit")){
                        System.out.println("종료합니다.");
                        break;
                    } else {
                        System.out.println(inp);
                    }
        
                }
            }
        }
        ```
        
    - 예제 2: 두 숫자가 모두 0 일 때까지 두 숫자의 합 구하기
        
        ```java
        package scanner;
        
        import java.util.Scanner;
        
        public class ScannerEx2 {
        
            public static void main(String[] args) {
                Scanner scanner = new Scanner(System.in);
        
                while (true) {
                    System.out.print("첫번째 숫자 입력: ");
                    int num1 = scanner.nextInt();
                    System.out.print("두번째 숫자 입력: ");
                    int num2 = scanner.nextInt();
        
                    if (num1 == 0 && num2 == 0) {
                        System.out.println("종료합니다.");
                        break;
                    } else {
                        int res = num1 + num2;
                        System.out.println("두 숫자의 합: " + res);
                    }
        
                }
            }
        }
        ```
        
    - 예제 3: 0이 입력될 때까지 누적합 구하기
        
        ```java
        package scanner;
        
        import java.util.Scanner;
        
        public class ScannerEx3 {
        
            public static void main(String[] args) {
                Scanner scanner = new Scanner(System.in);
        
                int res = 0;
                while (true) {
                    System.out.print("숫자 입력: ");
                    int num = scanner.nextInt();
        
                    if (num == 0) {
                        break;
                    }
                    res += num;
                }
                System.out.println("누계: " + res);
            }
        }
        ```
        
- 문제와 풀이
    - 문제 1: 이름 나이 입력받고 출력하기
        
        사용자로부터 입력받은 이름과 나이를 출력하세요. 출력 형태는 “당신의 이름은 [이름]이고, 나이는 [나이]살입니다.” 이어야 합니다.
        
        ```java
        package scanner;
        
        import java.util.Scanner;
        
        public class ScannerEx4 {
        
            public static void main(String[] args) {
                Scanner scanner = new Scanner(System.in);
        
                System.out.print("이름: ");
                String name = scanner.nextLine();
                System.out.print("나이: ");
                int age = scanner.nextInt();
        
                System.out.println("당신의 이름은 " + name + "이고, 당신의 나이는 " + age + "살 입니다.");
            }
        }
        ```
        
    - 문제 2: 홀수 짝수
        
        사용자로부터 하나의 정수를 입력받고, 이 정수가 홀수인지 짝수인지 판별하는 프로그램을 작성하세요.
        
        ```java
        package scanner;
        
        import java.util.Scanner;
        
        public class ScannerEx5 {
        
            public static void main(String[] args) {
                Scanner scanner = new Scanner(System.in);
        
                System.out.print("정수 입력: ");
                int num = scanner.nextInt();
        
                if (num % 2 == 0) {
                    System.out.println("짝수");
                } else {
                    System.out.println("홀수");
                }
            }
        }
        ```
        
    - 문제 3: 음식점 주문
        
        사용자로부터 음식의 이름(`foodName`), 가격(`foodPrice`), 그리고 수량(`foodQuantity`)을 입력받아, 주문한 음식의 총 가격을 계산하고 출력하는 프로그램을 작성하세요
        
        음식의 총 가격은 `totalPrie`라는 이름의 변수에 저장하세요.
        
        주문 정보와 총 가격을 출력하세요. 출력 형태는 “[음식 이름] [수량]개를 주문하셨습니다. 총 가격은 [총 가격]원 입니다.” 이어야 합니다.
        
        ```java
        package scanner;
        
        import java.util.Scanner;
        
        public class ScannerEx6 {
        
            public static void main(String[] args) {
                Scanner scanner = new Scanner(System.in);
        
                System.out.print("음식 이름: ");
                String foodName = scanner.nextLine();
                System.out.print("음식 가격: ");
                int foodPrice = scanner.nextInt();
                System.out.print("음식 수량: ");
                int foodQuantity = scanner.nextInt();
        
                int totalPrice = foodPrice * foodQuantity;
                System.out.println(foodName + " " + foodQuantity + "개를 주문하셨습니다. 총 가격은 " + totalPrice + "원 입니다.");
            }
        }
        ```
        
    - 문제 4: 구구단 출력
        
        사용자로부터 하나의 정수 `n`을 입력받고, 입력받은 정수 `n`의 구구단을 출력하는 프로그램을 작성하세요.
        
        ```java
        package scanner;
        
        import java.util.Scanner;
        
        public class ScannerEx7 {
        
            public static void main(String[] args) {
                Scanner scanner = new Scanner(System.in);
        
                System.out.print("1~9 사이의 정수 입력: ");
                int num = scanner.nextInt();
        
                for (int i = 1; i<10; i++){
                    int res = num * i;
                    System.out.println(num + " x " + i + " = " + res);
                }
            }
        }
        ```
