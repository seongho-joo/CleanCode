## 목차
- [Intro](#1)
- [의도를 분명히 밝혀라](#2)
- [그릇된 정보를 피하라](#3)
- [의미 있게 구분하라](#4)
- [발음하기 쉬운 이름을 사용하라](#5)
- [검색하기 쉬운 이름을 사용하라](#6)
- [인코딩을 피하라](#7)
- [자신의 기억력을 자랑하지 마라](#8)
- [클래스 이름](#9)
- [메서드 이름](#10)
- [기발한 이름은 피하라](#11)
- [한 개념에 한 단어를 사용하라](#12)
- [말장난을 하지 마라](#13)
- [해법 영역에서 가져온 이름을 사용하라](#14)
- [문제 역역에서 가져온 이름을 사용하라](#15)
- [의미 있는 맥락을 추가하라](#16)
- [불필요한 맥락을 없애라](#17)
- [정리](#18)
***

<a name="1"></a>

## Intro
소프트웨어에서 이름은 언제나 쓰인다.   
이 장에선 이름을 잘 짓는 간단한 규칙을 몇 가지 소개한다.   

<a name="2"></a>

## 의도를 분명히 밝혀라
좋은 이름을 지으려면 시간이 걸리지만 좋은 이름으로 절약하는 시간이 훨씬 많다.   
변수(함수, 클래스)의 이름은 따로 주석이 필요하지 않을 정도로 변수의 존재 이유, 기능, 사용 방법이 드러나야한다.   
의도가 드러나는 이름을 사용하면 코드 이해와 변경이 쉬워진다.   
의미를 함축하거나 독자가 사전지식을 가지고 있다고 가정하면 안된다.   
- 예시 1
```java
// Bad - d는 아무 의미도 드러나지 않음
int d; // 경과 시간(단위: 날짜)
// Good
int elapsedTimeDays;
int daysSinceCreation;
int daysSincedModification;
int fileAgeInDays;
```
- 예시 2
```java
// Bad - 코드 맥락이 코드 자체에 명시적으로 드러나지 않음
public List<int[]> getThem() {
  List<int[]> list1 = new ArrayList<int[]>();
  for (int[] x : theList)
    if (x[0] == 4)
      list1.add(x);
  return list1;
}
// Good
public List<int[]> getFlaggedCells() {
  List<int[]> flaggedCells = new ArrayList<int[]>();
  for (int[] cell : gameBoard)
    if(cell[STATUS_VALUE] == FLAGGED) 
      flaggedCells.add(cell);
  return flaggedCells;
}
```

<a name="3"></a>

## 그릇된 정보를 피하라
프로그래머는 코드에 그릇된 단서를 남겨선 안된다.   
널리 쓰이는 의미가 있는 단어를 다른 의미로 사용하면 안된다.
- hp, aix, sco는 적합하지 않다.

프로그래머에게 특수한 의미를 가지고 있는 단어는 이름에 넣지 않는 편이 바람직하다.
- 여러 계정을 묶을 때, 실제 List가 아니라면, `accountList`라 명명하지 않는다.
- 실제 List가 아니라면 프로그래머에게 그릇된 정보를 제공하는 셈.
- `accountGroup`, `bunchOfAccounts`, 아니면 단순히 `Accounts`라 명명한다.
  
서로 흡사한 이름을 사용하지 않도록 주의한다.
```cpp
int a = 1;
if (O == l)
  a = Ol
else 
  l = 01
// 글꼴을 바꿔 차이를 드러내는 해결책을 제안함
// 하지만 이름만 바꾸면 문제가 깨끗이 풀리는데 괜스레 일거리를 만들 필요가 없다.
```

<a name="4"></a>

## 의미 있게 구분하라
컴파일러나 인터프리터만 통과하려는 생각으로 코드를 구현하는 프로그래머는 스스로 문제를 일으킨다.   
연속된 숫자를 덧붙이거나 불용어를 추가하는 방식은 적절하지 못하다. 이름이 달라야 한다면 의미도 달라져야 한다.   
읽는 사람이 차이를 알도록 이름을 지어라.
- 예시
  - `Name` vs `NameString`
  - `Customer` vs `CustomerObject`
  - `getAcitveAccount()` vs `getAcitveAccounts()` vs `getAcitveAccountInfo()`
  - `money` vs `moneyAmount`
  - `theMessage` vs `message`
```java
// Bad
public static void copyChars(char a1[], char a2[]) {
  for (int i = 0; i < a1.length; i++) {
    a2[i] = a1[i];
  }
}
// Good
public static void copyChars(char source[], char destination[]) {
  for(int i = 0; i < source.length; i++) {
    destination[i] = source[i];
  }
}
```

<a name="5"></a>

## 발음하기 쉬운 이름을 사용하라
프로그래밍은 사회 활동이기 때문에 발음하기 쉬운 이름은 중요하다.
- 예시
```java
// Bad
class DtaRcd102 {
  private Date genymdhms;
  private Date modymdhms;
  private final String pszqint = "102";
  /* ... */
};
// Good
class Customer {
  private Date generationTimestamp;
  private Date modificationTimestamp;
  private final String recordId = "102";
  /* ... */
}
```

<a name="6"></a>

## 검색하기 쉬운 이름을 사용하라
문자 하나를 사용하는 이름과 상수는 텍스트 코드에서 쉽게 눈에 띄지 않는다는 문제점이 있다.   
이름 길이는 범위 크기에 비례해야 한다. 변수나 상수를 코드 여러 곳에서 사용한다면 검색하기 쉬운 이름이 바람직하다.

<a name="7"></a>

## 인코딩을 피하라
굳이 부담을 더하지 않아도 이름에 인코딩할 정보는 아주 많다.
- 헝가리식 표기법
  - 변수 이름에 타입을 인코딩할 필요 없다.
  - 예시) `PhoneNumber phoneString;`
- 멤버 변수 접두어
  - 멤버 변수 접두어를 붙일 필요 없다.
- 인터페이스 클래스와 구현 클래스
  - 인코딩이 필요할 경우도 있다.
  - 인터페이스 인터페이스와 구현 클래스 중 하나를 인코딩해야 한다면 구현 클래스를 택하라.

<a name="8"></a>

## 자신의 기억력을 자랑하지 마라
문자 하나만 사용하는 변수 이름은 문제가 있다. 단, 루프에서 반복 횟수를 세는 변수 i, j, k는 괜찮다(l은 절대 안됨!)   
똑똑한 프로그래머와 전문가 프로그래머 사이의 차이점은 전문가 프로그래머는 명료함이 최고라는 사실을 이해한다.   
전문가 개발자는 자신의 능력을 좋은 방향으로 사용해 남들이 이해하는 코드를 내놓는다.

<a name="9"></a>

## 클래스 이름
클래스 이름과 객체 이름은 명사나 명사구가 적합하다.   
Manager, Precessor, Data, Info 등과 같은 단어는 피하고 동사는 사용하지 않는다.   
예시)
- `Customer`, `WikiPage`, `Account`, `AddressParser`

<a name="10"></a>

## 메서드 이름
메서드 이름은 동사나 동사구가 적합하다.
접근자, 변경자, 조건자는 javabean 표준에 따라 값 앞에 get, set, is를 붙인다.
생성자를 중복정의할 때는 정적 팩토리 메서드를 사용한다.   
생성자 사용을 제한하려면 해당 생성자를 `private`로 선언한다.
- 예시)
```cpp
string name = employee.getName();
customer.setName("mike");
if (paycheck.isPosted())...

// Bad
Complex fulcrumPoint = new Complex(23.0);
// Good
Complex fulcrumPoint =  Complex.FromRealNumber(23.0); 
```

<a name="11"></a>

## 기발한 이름은 피하라
재미난 이름보다 명료한 이름을 선택하는게 바람직하다.   
의도를 분명하고 솔직하게 표현하라
- HolyhandGrenade -> DeleteItems
- whack() -> kill()

<a name="12"></a>

## 한 개념에 한 단어를 사용하라
메서드 이름은 독자적이고 일관적이어야 주석을 뒤져보지 않고도 프로그래머가 올바른 메서드를 선택한다.   
일관성 있는 어휘는 코드를 사용할 프로그래머가 반갑게 여길 선물이다.

<a name="13"></a>

## 말장난을 하지 마라
한 단어를 두 가지 목적으로 사용하지 마라.   
프로그래머는 코드를 최대한 이해하기 쉽게 짜야 한다.

<a name="14"></a>

## 해법 영역에서 가져온 이름을 사용하라
- 코드를 읽을 사람도 프로그래머라는 사실을 명심한다.
- 전산 용어, 알고리즘 이름, 패턴 이름, 수학 용어 등을 사용 하자.

<a name="15"></a>

## 문제 영역에서 가져온 이름을 사용하라
- 적적한 '프로그래머 용어'가 없다면 문제 영역에서 이름을 가져온다.
- 우수한 프로그래머와 설계자라면 해법 영역과 문제 영역을 구분할 줄 알아야 한다.   

<a name="16"></a>

## 의미 있는 맥락을 추가하라
- 클래스, 함수, namespace에 넣어 맥락을 부여한다.
- 맥락을 부여해도 불분명하다면 접두어를 사용한다.
```java
// Bad
private void printGuessStatistics(char candidate, int count) {
  String number;
  String verb;
  String pluralModifier;
  if (count == 0) {
    number = "no";
    verb = "are";
    pluralModifier = "s";
  } else if (count == 1) {
    number = "1";
    verb = "is";
    pluralModifier = "";
  } else {
    number = Integer.toString(count);
    verb = "are";
    pluralModifier = "s";
  }
  String guessMessage = String.format("There %s %s %s%s", verb, number, candidate, pluralModifier);
  print(gessMessge);
}
// Good
public class GuessStaticsMessage {
  private String number;
  private String verb;
  private pluralModifier;

  public String make(char candidate, int count) {
    createPluralDependentMessageParts(count);
    return String.format(
      "There %s %s %s%s",
       verb, number, cadidate, pluralModifier);
    )
  }

  private void createPluralDependentMessageParts(int count) {
    if (count == 0) {
      thereAreNoLetters();
    } else if (count == 1) {
      tehereIsOneLetter();
    } else {
      thereAreManyLetters(count);
    }
  }

  private void thereAreManyLetters(int count) {
    number = Integer.toString(count);
    verb = "are";
    pluralModifier = "s";
  }

  private void thereIsOneLetters() {
    number = "1";
    verb = "is";
    pluralModifier = "";
  }

  private void thereAreNoLetters() {
    number = "no";
    verb = "are";
    pluralModifier "s";
  }
}
```

<a name="17"></a>

## 불필요한 맥락을 없애라
'Gas Station Deluxe'라는 애플리케이션을 짠다고 가정하자. 모든 클래스 이름을 GSD로 시작하는것은 전혀 바람직하지 않다.   
IDE에서 G를 입력하고 자동 완성 키를 누르면 IDE는 모든 클래스를 열거한다.
- 일반적으로는 짧은 이름이 긴 이름보다 좋다. 단 의미가 분명한 경우에 한해서.
- 이름에 불필요한 맥락을 추가하지 않도록 주의한다.

<a name="18"></a>

## 정리
- 좋은 이름을 선택하려면 설명 능력이 뛰어나야 하고 문화적인 배경이 같아야 한다.
- 이름을 바꾸는 것에 두려워 하지마라.