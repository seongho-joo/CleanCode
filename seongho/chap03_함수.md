# 목차
- [Intro](#1)
- [작게 만들어라](#2)
  - [블록과 들여쓰기](#2-1)
- [한 가지만 해라](#3)
  - [함수 내 섹션](#3-1)
- [함수 당 추상화 수준은 하나로](#4)
  - [위에서 아래로 코드 읽기: 내려가기 규칙](#4-1)
- [Switch 문](#5)
- [서술적인 이름을 사용하라](#6)
- [함수 인수](#7)
  - [많이 쓰는 단항 형식](#7-1)
  - [플래그 인수](#7-2)
  - [이항 함수](#7-3)
  - [삼항 함수](#7-4)
  - [인수 객체](#7-5)
  - [인수 목록](#7-6)
  - [동사와 키워드](#7-7)
- [부수 효과를 일으키지 마라](#8)
  - [출력 인수](#8-1)
- [명령과 조회를 분리하라](#9)
- [오류 코드보다 예외를 사용하라](#10)
  - [Try/Catch 블록 뽑아내기](#10-1)
  - [오류 처리도 한 가지 작업이다](#10-2) 
  - [Error.java 의존성 자석](#10-3)
- [반복하지 마라](#11)
- [구조적 프로그래밍](#12)
- [함수를 어떻게 짜죠?](#13)
- [결론](#14)

<a name="1"></a>

## Intro
어떤 프로그램이든 가장 기본적인 단위는 함수다. 이 장은 함수를 잘 만드는 법을 소개한다.<br>
길이가 길고, 중복된 코드에, 괴상한 문자열에, 낯설고 모호한 자료 유형과 API로 이루어진 함수는 이해하기 어렵다.<br>
그렇다면 읽기 쉽고 이해하기 쉽고 의도를 분명히 표현하는 함수를 어떻게 구현할 수 있을까?<br>
함수에 어떤 속성을 부여해야 처음 읽는 사람이 프로그램 내부를 직관적으로 파악할 수 있는가?

<a name="2"></a>

## 작게 만들어라
함수를 만들 때 최대한 '작게!' 만들어야 한다.   
```java
public static String renderPageWithAndTeardowns(
  PageData pageData, boolean isSuite
) throws Exception {
  boolean isTestPage = pageData.hasAttribute("Test");
  if (isTestPage) {
    WikiPage testPage = new getWikiPage();
    StringBuffer newPageContent = new StringBuffer();
    includeSetupPages(testPage, newPageContent, isSuite);
    newPageContent.append(pageData.getContent());
    includeTeardownPages(testPage, newPageContent, isSuite);
    pageData.setContent(newPageContent.toString());
  }
  return pageData.getHtml();
}
```
위 코드는 아래 코드로 줄여야 마땅하다.
```java
public static String renderPageWithAndTeardowns(
  PageData pageData, boolean isSuite) throws Exceiption {
    if (isTestPage(pageData)) {
      includeSetupAndTeardownPages(pageData, isSuite);
    }
    return page Data.getHtml();
  }
)
```
한 함수는 3-5줄 이내로 작성하는 것을 권장한다.

<a name="2-1"></a>

### *블록과 들여쓰기*
if 문/else 문 /while 문 등에 들어가는 블록은 한 줄이어야 한다. 이 말은 중첩 구조가 생길만큼 함수가 커져서는 안된다는 뜻이다.   
함수에서 들여쓰기 수준은 1단이나 2단을 넘어서지 않고, 블록 안에서 호출하는 함수 이름을 적절히 짓는다면 함수는 읽고 이해하기 쉬워진다.

<a name="3"></a>

## 한 가지만 해라
**함수는 한 가지를 해야 한다. 그 한 가지를 잘 해야 한다. 그 한 가지만을 해야 한다.**   
'한 가지'란 지정된 함수 이름 아래에서 추상화 수준이 하나인 단계만 수행한다면 그 함수는 한 가지 작업만 한다는 것이다.   
함수를 만드는 이유는 큰 개념을 다음 추상화 수준에서 여러 단계로 나눠 수행하기 위해서다.   
함수가 '한 가지'만 하는지 판단하는 방법이 하나 더 있다. 단순히 다른 표현이 아니라 의미 있는 이름으로 다른 함수를 추출할 수 있따면 그 함수는 여러 작업을 하는 셈이다.

<a name="3-1"></a>

### *함수 내 센션*
한수를 여러 섹션으로 나눠진다면 그 함수는 여러 작업을 한다는 증거다.   
한 가지 작업만 하는 함수는 자연스럽게 섹션으로 나누기 어렵다.

<a name="4"></a>

## 함수 당 추상화 수준은 하나로
함수가 확실히 '한 가지' 작업만 하려면 함수 내 모든 문장의 추상화 수준이 동일해야 한다.   
한 함수 내에 추상화 수준을 섞으면 코드를 읽는 사람이 헷갈린다.

<a name="4-1"></a>

### *위에서 아래로 코드 읽기: **내려가기** 규칙*
코드는 위에서 아래로 이야기처럼 읽혀야 좋다.

<a name="5"></a>

## Switch 문
switch 문은 N가지를 처리하기 때문에 작게 만들기 어렵다. 하지만 추상 팩토리<sup>Abstract Factory</sup>에 숨겨 다형적 객체를 생성하는 코드 안에서만 switch 문을 사용하여 반복하지 않는 방법은 있다.   
```java
// Bad
public Money calculatePay(Employee e) throws InvalidEmployeeType {
  swith (e.type) {
    case COMMISSIONED:
      return calculateCommissionedPay(e);
    case HOURLY:
      return calculateHourlyPay(e);
    case SALARIED:
      return calculateSalariedPay(e);
    default:
      throw new InvalidEmployeeType(e.type);
  }
}
/** 
위 코드는 4가지 문제가 있다.
1. 함수가 길다.
   - 새 직원을 유형을 추가하면 더 길어짐.
2. '한 가지' 작업만 수행하지 않는다.
3. SRP(Single Responsibility Principle)을 위반한다.
   - 코드를 변경할 이유가 여러가지가 있기 때문
4. OCP(Open Closed Principle)를 위반한다.
   - 새 직원 유형을 추가할 때마다 코드를 변경하기 때문
5. 위 함수와 구조가 동일한 함수가 무한정 존재한다는 사실이 가장 심각한 문제다.
   - isPayday(Employee e, Date date); 혹은 deliverPay(Employee e, Money pay);
*/
```
```java
// Good
public abstract class Employee {
  public abstract boolean isPayday();
  public abstract Money calculatePay();
  public abstract void deliverPay(Money pay);
}
-----------------
public interface EmployeeFactory {
  public Employee makeEmployee(EmployeeRecord r) throws InvalidEmployeeType;
}
-----------------
public class EmployeeFactoryImpl implements EmployeeFactory {
  public Employee makeEmployee(EmployeeRecord r) throws InvalidEmployeeType {
    switch (r.type) {
      case COMMISSIONED:
        return new CommissionedEmployee(r);
      case HOURLY:
        return new HourlyEmployee(r);
      case SALARIED:
        return new SalariedEmployee(r);
      default:
        throw new InvalidEmployeeType(r.type);
    }
  }
}
```

<a name="6"></a>

## 서술적인 이름을 사용하라
> "코드를 읽으면서 짐작했던 기능을 각 루틴이 그대로 수행한다면 깨끗한 코드라 불러도 되겠다" - 워드   

함수가 작고 단순할수록 서술적인 이름을 고르기도 쉬워진다.   
함수 이름을 정할 때는 여러 단어가 쉽게 읽히는 명명법을 사용한다. 그런 다음, 여러 단어를 사용해 함수 기능을 잘 표현하는 이름을 선택한다.   
이름을 붙일 때는 일관성이 있어야 한다.

<a name="7"></a>

## 함수 인수
인수가 많을수록 개념을 이해하기 어렵기 때문에 함수에서 이상적인 인수 개수는 0개이다. 차선은 입력 인수가 1개뿐인 경우다.

<a name="7-1"></a>

### *많이 쓰는 단항 형식*
함수에 인수 1개를 넘기는 이유로 가장 흔한 이유는 세 가지다.
- 인수에 질문을 던지는 경우    
  `boolean fileExists("MyFile")`
- 인수를 뭔가로 변환해 결과를 반환하는 경우   
  `inputStream fileOpne("MyFile")`
- 이벤트 함수일 경우(이벤트라는 사실이 코드에 명확히 드러나야 함)   
  `passwordAttemptFailedNtimes(int attempts)`   

위 경우가 아니라면 단항 함수는 가급적 피하는 것이 좋다.

<a name="7-2"></a>

### *플래그 인수*
함수로 부울 값을 넘기는 것은 그 함수가 한꺼번에 여러 가지를 처리한다고 대놓고 공표하는 것이므로 지양한다.

<a name="7-3"></a>

### *이항 함수*
인수가 2개인 함수는 인수가 1개인 함수보다 이해하기 어렵다. 가능하면 단항 함수로 바꾸도록 애써야 한다.
이항 함수의 두 요소가 자연적인 순서가 있으면 적절하다.   
자연적인 순서가 없다면 순서를 인위적으로 기억해야 한다.   
```cpp
// 자연적인 순서가 있는 예
Point p = new Point(0, 0);
// 인위적으로 기억해야 하는 예
assertEqueals(expected, actual);
```

<a name="7-4"></a>

### *삼항 함수*
삼항 함수는 이항 함수는 더더욱 이해하기 어렵다.   
매번 함수를 볼 때마다 순서, 주춤, 무시로 야기되는 문제가 많이 때문에 삼항 함수를 만들때는 신중히 고려해야 한다.

<a name="7-5"></a>

### *인수 객체*
인수 2, 3개 필요하다면 일부를 독자적인 클래스 변수로 선언할 가능성을 짚어 본다.
```cpp
// Bad
Circle makeCircle(double x, double y, double radius);
// Good
Circle makeCircle(Point center, double radius);z
```

<a name="7-6"></a>

### *인수 목록*
때로는 인수 개수가 가변적인 함수도 필요하다. 가변 인수를 취하는 함수는 단항, 이항, 삼항 함수로 취급할 수 있다.    
하지만 이를 넘어서는 인수를 사용할 경우에는 문제가 있다.
```java
// 대표적인 예시 String.format 메서드
public String format(String format, Object... args)
```

<a name="7-7"></a>

### *동사와 키워드*
함수의 의도나 인수의 순서와 의도를 제대로 표현하려면 좋은 함수 이름이 필수다.   
단항 함수는 함수와 인수가 동사/명사 쌍을 이뤄야 한다.
- `writeField(name)`

함수 이름에 키워드를 추가하는 형식이다. 이렇게 한다면 인수 순서를 기억할 필요가 없어진다.
- `assertEquals` 보다는 `assertExpectedEqualsActual(exptected, actual)`

<a name="8"></a>

## 부수 효과를 일으키지 마라
함수에서 한 가지를 하겠다고 약속하고 다른 기능을 하는 경우가 있다.    
부수 효과는 시간적인 결합<sup>temporal coupling</sup>이나 순서 종속성<sup>order dependency</sup> 초래한다.
```java
// Bad
public class UserValidator {
  private Cryptographer cryptographer;

  public boolean checkPassword(String nserName, String password) {
    User user = UserGateway.findByName(userName);
    if (user != User.NULL) {
      String codedPhrase = user.getPhraseEncodedByPassword();
      String phrase = crytographer.decrypt(codedPhrase, password);
      if ("Valid Password".equals(phrase)) {
        Session.initialize(); // 부수 효과
        return true;
      }
    }
    return false;
  }
}
// Good - 함수가 '한 가지'만 한다는 규칙은 위반
public class UserValidator {
  private Cryptographer cryptographer;

  public boolean checkPasswordAndInitializeSession(String nserName, String password) {
    User user = UserGateway.findByName(userName);
    if (user != User.NULL) {
      String codedPhrase = user.getPhraseEncodedByPassword();
      String phrase = crytographer.decrypt(codedPhrase, password);
      if ("Valid Password".equals(phrase)) {
        Session.initialize(); 
        return true;
      }
    }
    return false;
  }
}
```

<a name="8-1"></a>

### *출력 인수*
일반적으로는 우리는 인수를 함수 입력으로 해석한다. 객체 지향 언어에서는 출력 인수를 사용할 필요가 거의 없다.   
일반적으로 출력 인수는 피해야 한다. 함수에서 상태를 변경해야 한다면 함수가 속한 객체 상태를 변경하는 방식을 택한다.

<a name="9"></a>

## 명령과 조회를 분리하라
함수는 뭔가를 수행하거나 뭔가에 답하거나 둘 중 하나만 해야 한다.
```java
public boolean set(String attribute, String value);

if (set("username", "unclebob"))...
```
위 코드를 보면 "username"이 "unclebob"으로 설정되어 있는지 확인하는 코드인지, "username"을 "unclebob"으로 설정하는 코드인지 함수를 호출하는 코드만 봐서는 의미가 모호하다.
```java
if (attributeExists("username")) {
  setAttribute("username", "unclebob");
  ...
}
```
위 코드처럼 명령과 조회를 분리해서 코드를 작성한다.

<a name="10"></a>

## 오류 코드보다 예외를 사용하라
명령 함수에서 오류 코드를 반환하면 여러 단계로 중첩되는 코드를 야기한다. 그리고 오류 코드를 곧바로 처리해야 하는 문제에 부딪힌다.
```java
// Bad
if (deletePage(page) == E_OK) {
  if( registry.deleteReference(page.name) == E_OK) {
    if (configKeys.deleteKey(page.name.makeKey()) == E_OK) {
      logger.log("configKey note deleted");
    } else {
      logger.log("deletedReference from registry failed");
    }
  } else {
    logger.log("deleteReference from registry failed");
  }
} else {
  logger.log("delete failed");
  return E_ERROR;
}
// Good
try {
  deletePage(page);
  registry.deleteReference(page.name);
  configKeys.deleteKey(page.name.makeKey());
} catch (Exception e){
  logger.log(e.getMessage());
}
```

<a name="10-1"></a>

### *Try/Catch 블록 뽑아내기*
try/catch 블록은 고드 주고에 혼란을 일으키며, 정상 동작과 오류 처리 동작을 뒤썪는다. 그로므로 try/catch 블록을 별도 함수로 뽑아내는 편이 좋다.
```java
public void delete(Page page) {
  try {
    deletePageAndAllReferences(page);
  } catch (Exception e) {
    logError(e);
  }
}

private void deletePageAndAllReferences(Page page) throws Exceptino {
  deletePage(page);
  registry.deleteReference(page.name);
  configKeys.deleteKey(page.name.makeKey());
}

private void logError(Exception e) {
  logger.log(e.getMessage());
}
```

<a name="10-2"></a>

### *오류 처리도 한 가지 작업이다.*
함수는 '한 가지'작업만 해야 한다. 오류 처리도 '한 가지'작업에 손한다. 그러므로 오류를 처리하는 함수는 오류만 처리해야 마땅하다.

<a name="10-3"></a>

### *Error.java 의존성 자석*
오류 코드를 반환한다는 이야기는, 클래스든 영거형 변수든, 어디선가 오류 코드를 정의한다는 뜻이다.   
오류를 정의한 클래스를 의존성 자석이다. 오류 코드가 변경이된다면 그 클래스를 사용하는 클래스들은 전부 다시 컴파일하고 다시 배치해야하는 문제가 생긴다.

<a name="11"></a>

## 반복하지 마라
중복은 소프트웨어에서 모든 악의 근원이다. 많은 원칙과 기법이 중복을 없애거나 제어할 목적으로 나왔다.

<a name="12"></a>

## 구조적 프로그래밍
다익스트라는 모든 함수와 함수 내 모든 블록에 입구와 출구가 하나만 존재해야 한다. 함수는 return 문이 하나여야 한다.
함수를 작게 만든다면 간혹 `return`, `break`, `continue`를 여러 차례 샤용해도 괜찮다. 반면, goto 문은 큰 함수에서만 의미가 있으므로, 작은 함수에서는 피해야만 한다.

<a name="13"></a>

## 함수를 어떻게 짜죠?
소프트웨어를 짜는 행위는 여느 글짓기와 비슷하다.   
함수를 작성할 때 처음에는 길고 복잡하다. 들여쓰기 단계도 많고 중복된 루프도 많다. 인수 목록도 아주 길다. 이름은 즉흥적이고 코든느 중복된다. 하지만 그 서투른 코드를 빠짐없이 테스트하는 단위 테스트 케이스도 만든다.   
그런 다음 코드를 다듬고, 함수를 만들고, 이름을 바꾸고, 중복을 제거한다. 메서드를 줄이고 순서를 바꾼다. 때로는 전체 클래스를 쪼개기도 한다. 이 와중에도 콛느느 항상 단위 테스트를 통과한다.   
최종적으로는 이 장에서 설명한 규칙을 따르는 함수가 얻어진다. 처음부터 탁 짜내지 않는다. 그게 가능한 사람은 없으리라.

<a name="14"></a>

## 결론
모든 시스템은 특정 응용 분야 시스템을 기술한 목적으로 프로그래머가 설계한 도메인 특화 언어로 만들어진다. 함수는 그 언어에서 동사며, 클래스는 명사다.   
대가 프로그래머는 시스템을 "구현할" 프로그램이 아니라 "풀어갈" 이야기로 여긴다. 프로그래밍 언어라는 수단을 사용해 좀 더 풍부하고 좀 더 표현력이 강한 언어를 만들어 이야기를 풀어간다.   
이 장에서 설명한 규칙을 따른다면 길이가 짧고, 이름이 좋고, 체계가 잡힌 함수가 나오리라. 하지만 진짜 목표는 시스템이라는 이야기를 풀어가는데 있다는 사실을 명심하기 바란다.   
당신이 작성하는 함수가 분명하고 정확한 언어로 깔끔하게 같이 맞아떨어져야 이야기를 풀어가기가 쉬워진다는 사실을 기억하기 바란다.