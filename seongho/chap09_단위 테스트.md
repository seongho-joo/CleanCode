<h2>목차</h2>

- [TDD 법칙 세가지](#tdd-법칙-세가지)
- [깨끗한 테스트 코드 유지하기](#깨끗한-테스트-코드-유지하기)
  - [테스트는 유연성, 유지보수성, 재사용성을 제공한다](#테스트는-유연성-유지보수성-재사용성을-제공한다)
- [깨끗한 테스트 코드](#깨끗한-테스트-코드)
  - [도메인에 특화된 테스트 언어](#도메인에-특화된-테스트-언어)
  - [이중 표준](#이중-표준)
- [테스트 당 assert 하나](#테스트-당-assert-하나)
  - [테스트 당 개념 하나](#테스트-당-개념-하나)
- [F.I.R.S.T.](#first)
  - [빠르게 Fast](#빠르게-fast)
  - [독립적으로 Independent](#독립적으로-independent)
  - [반복가능하게 Repeatable](#반복가능하게-repeatable)
  - [자가검증하는 Self-Validating](#자가검증하는-self-validating)
  - [적시에 Timely](#적시에-timely)
- [결론](#결론)

> 애자일과 TDD 덕택에 단위 테스트를 자동화하는 프로그래머들이 점점 더 늘어나는 추세다.
> 하지만 우리 분야에 테스트를 추가하려고 서두르는 와중에 많은 프로그래머들이 제대로 된 테스트 케이스를 작성해야 한다는 좀 더 미묘한 사실을 놓쳐버렸다.

## TDD 법칙 세가지
- **첫째 법칙** : 실패하는 단위 테스트를 작성할 때까지 실제 코드를 작성하지 않는다.
- **둘째 법칙** : 컴파일은 실패하지 않으면서 실행이 실패하는 정도로만 단위 테스트를 작성한다.
- **셋째 법칙** : 현재 실패하는 테스트를 통과할 정도로만 실제 코드를 작성한다.

## 깨끗한 테스트 코드 유지하기
테스트 코드는 실제 코드와 못지 않게 중요하다. 테스트 코드는 사고와 설계와 주의가 필요하다.   
실제 코드 못지 않게 깨끗하게 짜야 한다.

### 테스트는 유연성, 유지보수성, 재사용성을 제공한다
테스트 코드를 깨끗하게 유지하지 않으면 결국은 잃어버리고 테스트 케이스가 없으면 실제 코드를 유연하게 만드는 버팀목도 사라진다. 코드에 유연성, 유지보수성, 재사용성을 제공하는 버팀목이 바로 단위 테스트다.

테스트 커버리지가 높을 수록 안심하고 아키텍처와 설계를 개선할 수 있다.
테스트는 유연성, 유지보수성, 재사용성을 제공하며 테스트 케이스가 있으면 변경이 쉬워진다.

테스트 코드가 지저분하면 코드를 변경하는 능력이 떨어지며 코드 구조를 개선하는 능력도 떨어진다. 그리고 실제 코드도 지저분해진다.

## 깨끗한 테스트 코드
깨끗한 테스트 코드를 만들려면 필요한 것은 **가독성**이다. 가독성을 높이려면 명료성, 단순성, 풍부한 표현력이 필요하다. 
테스트 코드는 최소의 표현으로 많은 것을 나타내야 한다.
```java
// Bad
public void testGetPageHieratchyAsXml() throws Exception {
  crawler.addPage(root, PathParser.parse("PageOne"));
  crawler.addPage(root, PathParser.parse("PageOne.ChildOne"));
  crawler.addPage(root, PathParser.parse("PageTwo"));

  request.setResource("root");
  request.addInput("type", "pages");
  Responder responder = new SerializedPageResponder();
  SimpleResponse response =
    (SimpleResponse) responder.makeResponse(new FitNesseContext(root), request);
  String xml = response.getContent();

  assertEquals("text/xml", response.getContentType());
  assertSubString("<name>PageOne</name>", xml);
  assertSubString("<name>PageTwo</name>", xml);
  assertSubString("<name>ChildOne</name>", xml);
}

public void testGetPageHieratchyAsXmlDoesntContainSymbolicLinks() throws Exception {
  WikiPage pageOne = crawler.addPage(root, PathParser.parse("PageOne"));
  crawler.addPage(root, PathParser.parse("PageOne.ChildOne"));
  crawler.addPage(root, PathParser.parse("PageTwo"));

  PageData data = pageOne.getData();
  WikiPageProperties properties = data.getProperties();
  WikiPageProperty symLinks = properties.set(SymbolicPage.PROPERTY_NAME);
  symLinks.set("SymPage", "PageTwo");
  pageOne.commit(data);

  request.setResource("root");
  request.addInput("type", "pages");
  Responder responder = new SerializedPageResponder();
  SimpleResponse response =
    (SimpleResponse) responder.makeResponse(new FitNesseContext(root), request);
  String xml = response.getContent();

  assertEquals("text/xml", response.getContentType());
  assertSubString("<name>PageOne</name>", xml);
  assertSubString("<name>PageTwo</name>", xml);
  assertSubString("<name>ChildOne</name>", xml);
  assertNotSubString("SymPage", xml);
}

public void testGetDataAsHtml() throws Exception {
  crawler.addPage(root, PathParser.parse("TestPageOne"), "test page");

  request.setResource("TestPageOne"); request.addInput("type", "data");
  Responder responder = new SerializedPageResponder();
  SimpleResponse response =
    (SimpleResponse) responder.makeResponse(new FitNesseContext(root), request);
  String xml = response.getContent();

  assertEquals("text/xml", response.getContentType());
  assertSubString("test page", xml);
  assertSubString("<Test", xml);
}
```
```java
// Good
public void testGetPageHierarchyAsXml() throws Exception {
  makePages("PageOne", "PageOne.ChildOne", "PageTwo");

  submitRequest("root", "type:pages");

  assertResponseIsXML();
  assertResponseContains(
    "<name>PageOne</name>", "<name>PageTwo</name>", "<name>ChildOne</name>");
}

public void testSymbolicLinksAreNotInXmlPageHierarchy() throws Exception {
  WikiPage page = makePage("PageOne");
  makePages("PageOne.ChildOne", "PageTwo");

  addLinkTo(page, "PageTwo", "SymPage");

  submitRequest("root", "type:pages");

  assertResponseIsXML();
  assertResponseContains(
    "<name>PageOne</name>", "<name>PageTwo</name>", "<name>ChildOne</name>");
  assertResponseDoesNotContain("SymPage");
}

public void testGetDataAsXml() throws Exception {
  makePageWithContent("TestPageOne", "test page");

  submitRequest("TestPageOne", "type:data");

  assertResponseIsXML();
  assertResponseContains("test page", "<Test");
}
```
### 도메인에 특화된 테스트 언어
시스템 조작 API를 사용하는 대신 API 위에다 함수와 유틸리티를 구현한 후 그 함수와 유틸리티를 사용하므로 테스트 코드를 짜기도 읽기도 쉬어진다. 
이렇게 구현한 함수와 유틸리티는 테스트 코드에서 사용하는 특수 API가 된다.

숙련된 개발자라면 자기 코드를 좀 더 간결하고 표현력이 풍부한 코드로 리팩터링해야 마땅하다.

### 이중 표준
테스트 API코드에 적용하는 표준은 실제 코드에 적용하는 표준과 확실히 다르다. 
실제 환경이 아니라 테스트 환경에서 돌아가는 코드이기 때문에 간단하고, 간결하고, 표현력이 풍부해야 하지만 실제 코드만큼 효율적인 필요는 없다.

테스트 환경은 자원이 제한적일 가능성이 낮다. 실제 환경에서는 절대로 안 되지만 테스트 환경에서는 전혀 문제없는 방식이 있다. 대개 메모리나 CPU 효율과 관련 있는 경우다.

## 테스트 당 assert 하나
assert 문이 단 하나인 함수는 결론이 하나라서 코드를 이해하기 쉽고 빠르다.

### 테스트 당 개념 하나
**테스트 함수마다 한 개념만 테스트하라**

이것저것 잡다한 개념을 연속으로 테스트하는 긴 함수는 피한다.
```java
// Bad
public void testAddMonth() {
  SerialDate d1 = SerialDate.createInstance(31, 5, 2004);

  SerialDate d2 = SerialDate.addMonth(1, d1);
  assertEquals(30, d2.getDayOfMonth());
  assertEquals(6, d2.getMonth());
  assertEquals(2004, d2.getYYYY());

  SerialDate d3 = SerialDate.addMonth(2, d1);
  assertEquals(31, d3.getDayOfMonth());
  assertEquals(7, d3.getMonth());
  assertEquals(2004, d3.getYYYY());
  
  SerialDate d4 = SerialDate.addMonth(1, SerialDate.addMonth(1, d1));
  assertEquals(30, d4.getDayOfMonth());
  assertEquals(7, d4.getMonth());
  assertEquals(2004, d4.getYYYY());
}
```
위 코드는 독자적인 개념 세 개를 테스트하므로 독자적인 테스트 세 개로 쪼개야 마땅하다.

가장 좋은 규칙
- 개념 당 assert 문 수를 최소로 줄여라
- 테스트 함수 하나는 개념하나만 테스트하라

## F.I.R.S.T.
깨끗한 테스트는 다음 다섯가지 규칙을 따른다.

### 빠르게 Fast
**테스트 빨라야 한다.** 테스트가 느리면 자주 돌리지 못하고 그렇게 된다면 초반에 문제를 찾아내 고치기 못한다.

### 독립적으로 Independent
**각 테스트는 서로 의존하면 안 된다.** 각 테스트는 독립적으로 그리고 어떤 순서로 실행해도 괜찮아야 한다.   
테스트가 서로에게 의존하면 하나가 실패할 때 나머지도 잇달아 실패하므로 원인을 진단하기 어려워지며 후반 테스트가 찾아내야 할 결함이 숨겨진다.

### 반복가능하게 Repeatable
**테스트는 어떤 환경에서도 반복 가능해야 한다.** 테스트가 돌아가지 않는 환경이 하나라도 있다면 테스트가 실패한 이유를 둘러댈 변명이 생긴다.

### 자가검증하는 Self-Validating
**테스트는 부울값으로 결과를 내야한다.** 테스트가 스스로 성공과 실패를 가늠하지 않는다면 판단은 주관적이 되며 지루한 수작업 평가가 필요하게 된다.

### 적시에 Timely
**테스트는 적시에 작성해야 한다.** 단위 테스트는 테스트하려는 실제 코드를 구현하기 직전에 구현한다.   

## 결론
테스트 코드는 실제 코드만큼이나 프로젝트 건강에 중요하다. 테스트 코드는 실제 코드의 유연성, 유지보수성, 재사용성을 보존하고 강화하기 때문에 실제 코드보다 더 중요할지도 모른다. 그러므로 테스트 코드는 지속적으로 깨끗하게 관리하자. 표현력을 높이고 간결하게 정리하자. 테스트 API를 도메인 특화 언어를 만들자.

테스트 코드가 방치되어 망가지면 실제 코드도 망가진다. 테스트 코드를 깨끗하게 유지하자.