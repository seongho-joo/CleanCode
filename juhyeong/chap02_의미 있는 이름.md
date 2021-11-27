## 목차 ##
- [들어가면서](#1)
- [의도를 분명히 밝혀라](#2)
- [그릇된 정보를 피하라](#3)
- [의미 있게 구분해라](#4)
- [발음하기 쉬운 이름을 사용하라](#5)
- [검색하기 쉬운 이름을 사용하라](#6)
- [클래스 이름](#7)
- [메서드 이름](#8)
- [기발한 이름은 피하라](#9)
- [한 개념에 한 단어를 사용하라](#10)
- [말장난을 하지마라](#11)
- [의미 있는 맥락을 추가하라](#12)
- [결론](#13)



<a name="1"></a>
## 들어가면서 ##
- 소프트웨어에서 이름은 어디나 쓰인다.
  - 우리는 변수에도 이름을 붙이고, 함수에도 이름을 붙이고, 인수와 클래스와 패키지에도 이름을 붙인다.
  - 이렇듯 많이 사용하므로 이름을 잘 지으면 여러모로 편하다.

<a name="2"></a>
## 의도를 분명히 밝혀라 ##
- "의도가 분명하게 이름을 지으라"고 말하기는 쉽다. 여기서는 의도가 분명한 이름이 **정말로** 중요하다는 사실을 거듭 강조한다.
- 좋은 이름을 지으려면 시간이 걸리지만 좋은 이름으로 절약하는 시간이 훨씬 더 많다.
- 주석이 필요하다면 의도를 분명히 드러내지 못했다는 말이다.
```cpp
int d; // 경과 시간(단위: 날짜)
```

이름 d는 아무 의미도 드러나지 않는다. 경과 시간이나 날짜라는 느낌이 안든다. 측정하려는 값과 단위를 표현하는 이름이 필요하다.
```cpp
int elapsedTimeInDats;
int daysSinceCreation;
int daysSinceModification;
int fileAgeInDays;
```
의도가 드러나는 이름을 사용하면 코드 이해와 변경이 쉬워진다. 다음 코드는 무엇을 할까?

```java
public List<int[]> getThem() {
  List<int[]> list1 = new ArrayList<int[]>();
  for (int[] x : theList)
    if (x[0] == 4)
      list1.add(x);
  return list1;
}
```

코드가 하는 일을 짐작하기 어렵다. 복잡한 문장도 없고, 공백과 들여쓰기도 적당한데 왜일까?
문제는 코드의 단순성이 아니라 코드의 **함축성** 이다. 다시 말해, 코드 맥락이 코드 자체에 명시적으로 드러나지 않는다.

```
1. theList에 무엇이 들었는가?
2. theList에서 0번째 값이 어째서 중요한가?
3. 값 4는 무슨 의미인가?
4. 함수가 반환하는 리스트 list1을 어떻게 사용하는가?
```
위 코드 샘플엔 이와 같은 정보가 드러나지 않는다. 지뢰찾기 게임을 만든다고 가정하자. 그러면 theList가 게임판이라는 사실을 안다. theList를 gameBoard로 바꿔보자.

```java
public List<int[]> getFlaggedCells() {
  List<int[]> flaggedCells = new ArrayList<int[]>();
  for (int[] cell : gameBoard)
    if(cell[STATUS_VALUE] == FLAGGED) 
      flaggedCells.add(cell);
  return flaggedCells;
}
```
각 개념에 이름만 붙여도 코드가 상당히 나아진다. 위에서 보듯, 코드의 단순성을 변하지 않았다. 그런데도 코드는 더욱 명확해졌다. 

단순히 이름만 고쳤는데도 함수가 하는 일을 이해하기 쉬워졌다. 바로 이것이 **좋은 이름이 주는 위력**이다.

<a name="3"></a>
## 그릇된 정보를 피하라 ##
- 그릇된 단서는 코드 의미를 흐린다.
  - 직각삼각형의 빗변(hypotenuse)을 구현할 때는 hp가 훌륭한 약어로 보일지라도 hp라는 변수는 독자에게 그릇된 정보를 제공한다.
- 여러 계정을 그룹으로 묶을 때, 실제 List가 아니라면, accountList라 명명하지 않는다.
  - 프로그래머에게 List라는 단어는 특수한의미다.
  - 계정을 담는 컨테이너가 실제 List가 아니라면 프로그래머에게 그릇된 정보를 제공하는 셈이다.
- 서로 흡사한 이름을 사용하지 않도록 주의한다.
  - 한 모듈에서 ```XYZControllerForEfficientHandlingOfStrings```라는 이름을 사용하고, 조금 떨어진 모듈에서 ```XYZControllerForEfficientStorageOfStrings```라는 이름을 사용한다면?
  - 두 단어는 겁나게 비슷하다.
- 이름으로 그릇된 정보를 제공하는 진짜 끔찍한 예가 소문자 L이나 대문자 O변수다. 두 변수를 함께 사용하면 더욱 끔직해진다.

```java
int a = 1;
if (O == 1)
a = O1;
else
l = O1;
```


<a name="4"></a>
## 의미 있게 구분해라 ##

- 컴파일러나 인터프리터만 통과하려는 생각으로 코드를 구현하는 프로그래머는 스스로 문제를 일으킨다.
- 컴파일러를 통과할지라도 연속된 숫자를 덧붙이거나 불용어를 추가하는 방식은 적절하지 못하다. 이름이 달라야 한다면 의미도 달라져야 한다.
  - 연속적인 숫자를 덧붙인 이름(a1, a2, ..., aN)은 의도적인 이름과 정반대다.
  - 이런 이름은 그릇된 정보를 제공하는 이름도 아니며, 아무런 정보를 제공하지 못하는 이름이다. 저자 의도가 전혀 드러나지 않는다.
  
```java
public static void copyChars(char a1[], char a2[]) {
  for (int i = 0; i < a1.length; i++) {
    a2[i] = a1[i];
  }
}
```

함수 인수 이름으로 source와 destination을 사용한다면 코드 읽기가 훨씬 더 쉬워진다.

- 불용어를 추가한 이름 역시 아무런 정보도 제공하지 못한다.
  - Product라는 클래스가 있다고 가정하자.
  - 다른 클래스를 ProductInfo 혹은 ProductData라 부른다면 개념을 구분하지 않은 채 이름만 달리한 경우다.
```java
getActiveAccount();
getActiveAccounts();
getActiveAccountInfo();
```
이 프로젝트에 참여한 프로그래머는 어느 함수를 호출할지 어떻게 알까?
명학환 관례가 없다면 moneyAmount는 money와 구분이 안된다. **읽는 사람이 차이를 알도록 이름을 지어라.**


<a name="5"></a>
## 발음하기 쉬운 이름을 사용하라 ##

- 프로그래밍은 사회 활동이기 때문에 발음하기 쉬운 이름은 중요하다.

```java
class DtaRcd102 {
  private Date genymdhms;
  private Date modymdhms;
  private final String pszqint = "102";
  /* ... */
};
```

와

```java
class Customer {
  private Date generationTimestamp;
  private Date modificationTimestamp;
  private final String recordId = "102";
  /* ... */
}
```
둘째 코드는 지적인 대화가 가능해진다. "주형씨, 이 레코드 좀 보세요. 'Genera-tion Timestamp' 값이 내일 날짜입니다! 어떻게 이렇죠?"



<a name="6"></a>
## 검색하기 쉬운 이름을 사용하라 ##

- 문자 하나를 사용하는 이름과 상수는 텍스트 코드에서 쉽게 눈에 띄지 않는다는 문제점이 있다.
- MAX_CLASSES_PER_STUDENT는 grep로 찾기가 쉽지만, 숫자 7은 은근히 까다롭다.
  - 7이 들어가는 파일 이름이나 수식이 모두 검색되기 때문이다.
  - 상수가 여러 자리 숫자이고 누군가 상수 내 숫자 위치를 바꿨다면 문제는 더 심각해진다. 상수에 버그가 있으나 검색으로 찾아내지 못한다.

<a name="7"></a>
## 클래스 이름 ##

- 클래스 이름과 객체 이름은 명사나 명사구가 적합하다.
  - Customer, WikiPage, Account, AddressParser 등이 좋은 예다. 
  - Manager, Processor, Data, Info 등과 같은 단어는 피하고, 동사는 사용하지 않는다.

<a name="8"></a>
## 메서드 이름 ##

- 메서드 이름은 동사나 동사구가 적합하다.
  - postPayment, deletepage, save 등이 좋은 예다.
- 생성자(Constructor)를 중복정의(overload)할 때는 정적 팩토리 메서드를 사용한다.
- 메서드는 인수를 설명하는 이름을 사용한다.

```java
Complex fulcrumPoint = new Complex(23.0);
```
위 코드가 아래 코드보다 좋다.
```java
Complex fulcrumPoint =  Complex.FromRealNumber(23.0); 
```
- 생성자를 제한하려면 해당 생성자를 private로 선언한다.

<a name="9"></a>
## 기발한 이름은 피하라 ##

- 이름이 너무 기발하면 저자와 유머 감각이 비슷한 사람만, 그리고 농담을 기억 하는 동안만, 이름을 기억한다.
  - HolyHandGrenade라는 함수가 무엇을 하는지 알겠는가? 기발한 이름이지만 DeleteItems가 더 좋다.
  - 재미난 이름보다 명료한 이름을 선택하라.
  

<a name="10"></a>
## 한 개념에 한 단어를 사용하라 ##

- 추상적인 개념 하나에 단어 하나를 선택해 이를 고수한다.
  - 예를 들어, 똑같은 메서드를 클래스마다 fetch, retrieve, get으로 제각각 부르면 혼란스럽다.
  - 어느 클래스에서 어느 이름을 썼는지 기억하기가 어렵다.
- 일관성 있는 어휘는 코드를 사용할 프로그래머가 반갑게 여길 선물이다.

<a name="11"></a>
## 말장난을 하지마라 ##

- 한 단어를 두 가지 목적으로 사용하지 마라. 다른 개념에 같은 단어를 사용한다면 그것은 말장난에 불과하다.
  - "한 개념에 한 단어를 사용하라"는 규칙을 따랐더니 예를 들어, 여러 클래스에 add라는 메서드가 생겼다.
  - 모든 add 메서드의 매개변수와 반환값이 의미적으로 똑같다면 문제가 없다.
  - 하지만 때로는 프로그래머가 같은 맥락이 아닌데도 '일관성'을 고려해 add라는 단어를 선택한다. 
  - 예를 들어, 지금까지 구현한 add 메서드는 모두가 기존 값 두개를 더하거나 이어서 새로운 값을 만든다고 가정하자.
  - 새로 작성하는 메서드는 집합에 값 하나를 추가한다.
  - 이 메서드를 add라고 불러도 괜찮을까?
  - 새 메서드는 기존 add메서드와 맥락이 다르다.
  - 그러므로 insert나 append라는 이름이 적당하다. 새 메서드를 add라 부른다면 이는 말장난이다.


<a name="12"></a>
## 의미 있는 맥락을 추가하라 ##

- 스스로 의미가 분명한 이름이 없지 않다. 하지만 대다수 이름은 그렇지 못하다.
  - 클래스, 함수, 이름 공간에 넣어 맥락을 부여한다.
  - 모든 방법이 실패하면 마지막 수단으로 접두어를 붙인다.
  - 예를 들어, firstName, lastName, street, houseNumber, city, state, zipcode라는 변수가 있다.
  - 변수를 훑어보면 주소라는 사실을 금방 알아챈다.
  - 하지만 어느 메서드가 state라는 변수 하나만 사용한다면? 변수 state가 주소 일부라는 사실을 금방 알아챌까?
  - addr라는 접두어를 추가해 addrFirstName, addrLastName, addrState라 쓰면 맥락이 좀 더 분명해진다.

맥락이 불분명한 변수
```java
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
```

맥락이 분명한 변수
```java
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

<a name="13"></a>
## 결론 ##

좋은 이름을 선택하려면 설명 능력이 뛰어나야 하고 문화적인 배경이 같아야 한다. 이것이 제일 어렵다. 좋은 이름을 선택하는 능력은 기술, 비즈니스, 관리 문제가 아니라 교육 문제이다. 우리 분야 사람들이 이름 짓는 방법을 제대로 익히지 못하는 이유가 여기에 있다.

사람들이 이름을 바꾸지 않으려는 이유 하나는 다른 개발자가 반대할까 두려워서다. 
그러나 우리들 생각은 다르다. 오히려(좋은 이름으로 바꿔주면) 반갑고 고맙다. 코드를 개선하려는 노력을 중단해서는 안 된다.
