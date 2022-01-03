## 목차 ##
- [들어가면서](#1)
- [작게 만들어라!](#2)
  - [블록과 들여쓰기](#2-1)  
- [한 가지만 해라!](#3)
- [함수 당 추상화 수준은 하나로!](#4)
  - [위에서 아래로 코드 읽기: 내려가기 규칙](#4-1)  
- [Switch 문](#5)
- [서술적인 이름을 사용하라!](#6)
- [함수 인수](#7)
  - [많이 쓰는 단항 형식](#7-1)
  - [플래그 인수](#7-2)  
  - [이항 함수](#7-3)
  - [삼항 함수](#7-4)
  - [인수 객체](#7-5)
  - [인수 목록](#7-6)
  - [동사와 키워드](#7-7)
- [부수 효과를 일으키지 마라!](#8)
- [명령과 조회를 분리하라!](#9)
- [구조적 프로그래밍](#10)
- [결론](#11)



<a name="1"></a>
## 들어가면서 ##
- 어떤 프로그램이든 가장 기본적인 단위가 함수다.
  - 프로그래밍 초창기에는 시스템을 루틴과 하위 루틴으로 나눴다.
  - 포트란과 PL/1 시절에는 시스템을 프로그램, 하위 프로그램, 함수로 나눴고, 지금은 함수만 살아남았다.

<a name="2"></a>
## 작게 만들어라! ##
- 함수를 만드는 첫째 규칙은 '작게!' 다.
- 함수를 만드는 둘째 규칙은 '더 작게!' 다.
  - 80년대에는 함수가 한 화면을 넘어가면 안된다고 말했으나, 지금은 100줄을 넘어서는 안된다. 아니 20줄도 길다.
- 함수가 얼마나 짧아야 하냐면 일반적으로 1번 코드보다 짧아야 하며, 1번 코드는 2번 코드로 줄여야 마땅하다.

1번 코드
```java
public static String renderPageWithSetupsAndTeardowns(PageData pageData, boolean isSuite) throws Exception {
  boolean isTestPage = pageData.hasAttribute("Test");
  if (isTestPage) {
    WikiPage testPage = pageData.getWikiPage();
    StringBuffer newPageContent = new StringBuffer();
    includeSuiteSetup(testPage, newPageContent, isSuite);
    newPageContent.append(pageData.getContent);
    includeTeardownPages(testPage, newPageContent, isSuite);
    pageData.setContent(newPageContent.toString());
  }
  return pageData.getHtml();
}
```

2번코드
```java
public static String renderPageWithSetupsAndTeardowns(PageData pageData, boolean isSuite) throws Exception {
  if (isTestPage(pageData))
    includeSetupAndTeardownPages(pageData, isSuite);
  return pageData.getHtml();
}
```

<a name="2-1"></a>
#### 블록과 들여쓰기 ####

- if문/else문/while문 등에 들어가는 블록은 한 줄이어야 한다는 의미이다.
  - 대게 여기서 함수를 호출한다.
  - 그러면 바깥을 감싸는 함수(enclosing function)가 작아질 뿐 아니라, 블록 안에서 호출하는 함수의 이름을 적절히 짓는다면, 코드를 이해하기도 쉽다.
- 함수에서 들여쓰기 수준은 1단이나 2단을 넘어서면 안된다.


<a name="3"></a>
## 한 가지만 해라! ##

- 함수는 한 가지를 해야한다. 그 한가지를 잘해야한다. 그 한 가지만을 해야한다.

<a name="4"></a>
## 함수 당 추상화 수준은 하나로! ##

- 한 함수 내에 추상화 수준을 섞으면 코드를 읽는 사람이 헷갈린다.
- 특정 표현이 근복 개념인지 아니면 세부사항인지 구분하기 어려운 탓이다.
- 하지만 문제는 이 정도로 그치지 않는다.
- 근본 개념과 세부사항을 뒤섞기 시작하면, 깨어진 창문처럼 사람들이 함수에 세부사항을 점점 더 추가한다.

<a name="4-1"></a>
#### 위에서 아래로 코드 읽기: 내려가기 규칙 ####

- 코드는 위에서 아래로 이야기처럼 읽혀야 좋다.
  - 한 함수 다음에는 추상화 수준이 한 단계 낮은 함수가 온다.
  - 즉, 위에서 아래로 프로그램을 읽으면 함수 추상화 수준이 한 번에 한 단계씩 낮아진다.
  - 나는 이것을 **내려가기** 규칙이라 부른다.
  - 하지만 추상화 수준이 하나인 함수를 구현하기란 쉽지 않고, 많은 프로그래머가 곤란을 겪지만 매우 중요한 규칙이다.

```
TO 설정 페이지와 해제 페이지를 포함하려면, 설정 페이지를 포함하고, 테스트 페이지 내용을 포함하고, 해제 페이지를 포함한다.
    TO 설정 페이지를 포함하려면, 슈트이면 슈트 설정 페이지를 포함한 후 일반 설정 페이지를 포함한다.
    TO 슈트 설정 페이지를 포함하려면, 부모 계층에서 "SuiteSetUp" 페이지를 찾아 include 문과 페이지 경로를 추가한다.
    TO 부모 계층을 검색하려면....
```

<a name="5"></a>
## Switch 문 ##

- swictch문은 작게 만들기 어렵다.
  - case 분기가 단 두 개인 switch 문도 내 취향에는 너무 길며, 단일 블록이나 함수를 선호한다.
  - 또한 '한 가지' 작업만 하는 switch 문도 만들기 어렵다.

<a name="6"></a>
## 서술적인 이름을 사용하라! ##

- 좋은 이름이 주는 가치는 아무리 강조해도 지나치지 않는다.
  - 워드가 말했던 원칙을 기억하는가? "코드를 읽으면서 짐작했던 기능을 각 루틴이 그대로 수행한다면 깨끗한 코드라 불러도 되겠다."
  - 한 가지만 하는 작은 함수에 좋은 이름을 붙인다면 이런 원칙을 달성함에 있어 이미 절반은 성공했다.
  - 함수가 작고 단순할수록 서술적인 이름을 고르기도 쉬워진다.
- 이름을 붙일 때는 일관성이 있어야 한다.
  - 모듈 내에서 함수 이름은 같은 문구, 명사, 동사를 사용한다.
  - ```includeSetupAndTeardownPages```, ```includeSetupPages```, ```includeSuiteSetupPage```, ```includeSetupPage``` 등이 좋은 예 이다.
  
<a name="7"></a>
## 함수 인수 ##
  
- 함수에서 이상적인 인수 개수는 0개(무항)이고, 다음은 1개(단항), 다음은 2개(이항)이며, 3개(삼항)는 가능한 피하는 편이 좋다.
  - 4개 이상(다항)은 특별한 이유가 필요하며, 특별한 이유가 있어도 사용하면 안 된다.

<a name="7-1"></a>
#### 많이 쓰는 단항 형식 ####
 
- 함수에 인수 1개를 넘기는 이유로 가장 흔한 경우는 두 가지다.
  - 하나는 인수에 질문을 던지는경우다.
  - ```boolean fileExists("MyFile")이 좋은 예다.```
  - 다른 하나는 인수를 뭔가로 변환해 결과를 반환하는 경우다.
  - ```InputStream fileOpen("MyFile")```은 String 형의 파일 이름을 ```InputStream```으로 변환한다. 
  - 이들 두 경우는 독자가 당연하게 받아들이며, 함수 이름을 지을 때는 두 경우를 분명히 구분한다.
  - 지금까지 설명한 경우가 아니라면 단항 함수는 가급적 피한다.
  
  
<a name="7-2"></a>
#### 플래그 인수 ####

- 플래그 인수는 추하다.
  - 함수로 부울 값을 넘기는 관례는 정말로 끔찍하다.
  - 왜냐면 함수가 한꺼번에 여러가지를 처리한다고 대놓고 공표하는 셈이니까. (플래그가 참이면 이걸 하고 거짓이면 저걸 한다는 말이니까!)
  
<a name="7-3"></a>
#### 이항 함수 ####

- 인수가 2개인 함수는 인수가 1개인 함수보다 이해하기 어렵다.
  - 예를 들어, ```writeField(name)``` 는 ```writefield(outputStream, name)``` 보다 이해하기 쉽다.
  - 둘 다 의미는 명백하지만 전자가 더 쉽게 읽히고 더 빨리 이해된다.
  - 후자는 잠시 주춤하며 첫 인수를 무시해야 한다는 사실을 깨닫는 시간이 필요하며, 그 사실이 결국은 문제를 일으킨다.
  - 왜냐고? 어떤 코드든 절대로 무시하면 안되는 오류가 숨어드니까.
- 가능하면 단항 함수로 바꾸도록 애써야 한다.
  - 예를들어, ```writeField``` 메서드를 ```outputStream``` 클래스 구성원으로 만들어 ```outputStream.writeField(name)``` 으로 호출한다.
  - 아니면 ```FieldWriter```라는 새 클래스를 만들어 구성자에서 ```outputStream```을 받고 ```write```메서드를 구현한다.



<a name="7-4"></a>
#### 삼항 함수 ####

- 인수가 3개인 함수는 인수가 2개인 함수보다 훨씬 더 이해하기 어렵다.
  - 순서, 주춤, 무시로 야기되는 문제가 두 배 이상 늘어난다.
  - 그래서 삼항 함수를 만들 때는 신중히 고려하라 권고한다.
  - 예를들어 ```assertEquals(message, expected, actual)``` 이라는 함수를 살펴보자.
  - 첫 인수가 ```expected```라고 예상하지 않았는가? 매번 함수를 볼 때마다 주춤했다가 ```message```를 무시해야 한다는 사실을 상기했다.
  
<a name="7-5"></a>
#### 인수 객체 ####

- 2-3개 필요하다면 일부를 독자적인 클래스 변수로 선언할 가능성을 짚어본다.
  - 예를 들어, 다음 두 함수를 살펴보자.
```java
Circle makeCircle(double x, double y, double radius);
Circle makeCircle(Point center, double radius);
```
  - 객체를 생성해 인수를 줄이는 방법이 눈속임이라 여겨질지 모르지만 그렇지 않다.
  - 위 예제에서 x와 y를 묶었듯이 변수를 묶어 넘기려면 이름을 붙여야 하므로 결국은 개념을 표현하게 된다.

  
<a name="7-6"></a>
#### 인수 목록####

- 때로는 인수 개수가 가변적인 함수도 필요하다. ```String.format``` 메서드가 좋은 예다.
  - ```String.format("%s worked %.2f hours.", name, hours);```
  - 위 예제처럼 가변 인수 전부를 동등하게 취급하면 List형 인수 하나로 취급할 수 있다. 

<a name="7-7"></a>
#### 동사와 키워드 ####

- 함수의 의도나 인수의 순서와 의도를 제대로 표현하려면 좋은 함수 이름이 필수다.
  - 단항 함수는 함수와 인수가 동사/명사 쌍을 이뤄야 한다.
  - 예를 들어, ```write(name)```은 누구나 곧바로 이해한다.
  - '이름(name)이 무엇이든 쓴다(write)는 뜻이다.
  - 좀 더 나은 이름은 ```writeField(name)```이다.
  - 그러면 이름(name)이 필드(field)라는 사실이 분명히 드러난다.
  

<a name="8"></a>
## 부수 효과를 일으키지 마라! ##

- 부수 효과는 거짓말이다.
  - 함수에서 한 가지를 하겠다고 약속하고선 남모래 다른 것도 한다.
  - 때로는 예상치 못하게 클래스 변수를 수정한다. 때로는 함수로 넘어온 인수나 시스템 전역 변수를 수정한다.
  - 어느 쪽이든 교활하고 해로운 거짓말이다. 많은 경우 시간적힌 결합(temporal coupling)이나 순서 종속성(order dependency)을 초래한다.
```java
public class UserValidator {
    private Cryptographer cryptographer;
    
    public boolean checkPassword(String userName, String password) {
        User user = UserGateway.findByName(userName);
        if (user != User.NULL) {
            String codedPhrase = user.getPhraseEncodedByPassword();
            String phrase = cryptographer.decrypt(codedPhrase, password);
            if ("Valid Password".equals(phrase)) {
                Session.initialize();
                return true;
            }
        }
        return false;
    }
}
```
  - 위 코드를 살펴보자. 아주 무해하게 보이는 함수다.
  - 함수는 표준 알고리즘을 사용해 ```userName```과 ```password```를 확인한다.
  - 두 인수가 올바르면 ```true```를 반환하고 아니면 ```false```를 반환한다.
  - 하지만 함수는 부수 효과를 일으킨다. 보이는가?
  - 여기서 함수가 일으키는 부수 효과는 ```Session.initialize()```호출이다. 
  - ```checkPassword```함수는 이름 그대로 암호를 확인한다.
  - 이름만 봐서는 세션을 초기화 한다는 사실이 드러나지 않는다.
  - 그래서 함수 이름만 보고 함수를 호출하는 사용자는 사용자를 인증하면서 기존 세션 정보를 지워버릴 위험에 처한다.
  - 위 코드는 ```checkPasswordAndInitializeSession```이라는 이름이 훨씬 좋다.
 


<a name="9"></a>
## 명령과 조회를 분리하라! ##

- 함수는 뭔가를 수행하거나 뭔가에 답하거나 둘 중 하나만 해야한다. 둘 다 하면 안된다.
- 객체 상태를 변경하거나 아니면 객체 정보를 반환하거나 둘 중 하나다. 둘 다 하면 혼라을 초래한다.
  - 예를 들어, 다음 함수를 살펴보자.
```java 
public boolean set(String attribute, String value);
```
  - 이 함수는 이름이 ```attriute``` 인 속성을 찾아 값을 ```value```로 설정한 후 성공하면 ```true```를 반환하고 실패하면 ```false```를 반환한다.
  - 그래서 다음과 같이 괴상한 코드가 나온다.
```java
if(set("username", "unclebob"))...```
```
  - 독자 입장에서 코드를 읽어보자.
  - 무슨 뜻일까? ```username```이 ```unclebob```으로 설정되어 있는지 확인하는 코드인가?
  - 아니면 ```username```을 ```unclebob```으로 설정하는 코드인가?
  - ```set```이라는 단어가 동사인지 형용사인지 분간하기 어려운 탓에 함수를 호출하는 코드만 봐서는 의미가 모호하다.

<a name="10"></a>
## 구조적 프로그래밍 ##

- 데이스트라는 모든 함수와 함수 내 모든 블록에 입구(entry)와 출구(exit)가 하나만 존재해야 한다고 말했다. 
- 즉 함수는 return 문이 하나여야 한다.
- 루프 안에서 break나 continue를 사용해선 안 되며 goto는 절대로, 절대로 안 된다.

<a name="11"></a>
## 결론 ##

- 이 장은 함수를 잘 만드는 기교를 소개했다.
- 여기서 설명한 규칙을 따른다면 길이가 짧고, 이름이 좋고, 체계가 잡힌 함수가 나오리라.
- 하지만 진짜 목표는 시스템이라는 이야기를 풀어가는 데 있다는 사실을 명심하기 바란다.

 
