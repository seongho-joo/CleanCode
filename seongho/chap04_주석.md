## 목차
- [주석은 나쁜 코드를 보완하지 못한다](#1)


> "나쁜 코드에 주석을 달지 마라. 새로 짜라." - 브라이언 W. 커니핸, P.J. 플라우거   
> 코드를 깔끔하게 정리하고 표현력을 강화하는 방향으로, 그래서 애초에 주석이 필요 없는 방향으로 에너지를 쏟겠다.   
> 코드만이 자기가 하는 일을 진실되게 말한다. 코드만이 정확한 정보를 제공하는 유일한 출처다.   
> 우리는 주석을 가능한 줄이도록 꾸준히 노력해야 한다.

<a name="1"></a>

## 주석은 나쁜 코드를 보완하지 못한다
코드에 주석을 추가하는 일반적인 이유는 코드 품질이 나쁘기 때문이다. 지저분한 모듈이라는 사실을 자각했을때 "주석을 달아야겠다" 가 아니라 "코드를 정리해야 한다" 다.   
자신이 저지른 난장판을 주석으로 설명하려 애쓰는 대신에 그 난장판을 깨끗이 치우는데 시간을 보내라 !

<a name="2"></a>

## 코드로 의도를 표현하라!
많은 경우 주석으로 달려는 설명을 함수로 만들어 표현해도 충분하다.
```java
// Bad
// 직원에게 복지 혜택을 받을 자격이 있는지 검사한다.
if ((employee.flags & HOURLY_FLAG) && (employee.age > 65))
```
```java
// Good
if (employee.isEligibleForFullBenefits())
```

<a name="3"></a>

## 좋은 주석
어떤 주석은 필요하거나 유익하다.

<a name="3-1"></a>

### *법적인 주석*
회사가 정립한 구현 포준에 맞춰 법적인 이유로 특정 주석을 넣으라고 명시한 경우
- 각 소스 파일 첫머리에 주석으로 들어가는 저작권 정보와 소유권 정보
```
// Copyright (C) 2003, 2004, 2005 by Object Mentor, Inc. All rights reserved.
// GNU Genral Public License 버전 2 이상을 따르는 조건으로 배포한다.
```

<a name="3-2"></a>

### *정보를 제공하는 주석*
때로는 기본적인 정보를 주석으로 제공하면 편리하다.
```java
// 테스트 중인 Responder 인스턴스를 반환한다.
protected abstract Responder responderInstance();

// kk:mm:ss EEE, MMM dd, yyyy
Pattern timeMatcher = Pattern.compile("\\d*:\\d*:\\d*: \\w*, \\w* \\d*, \\d*");
```

<a name="3-3"></a>

### *의도를 설명하는 주석*
때때로 주석은 구현을 이해하게 도와주는 선을 넘어 결정에 깔린 의도까지 설명한다.

<a name="3-4"></a>

### *의미를 명료하게 밝히는 주석*
모호한 인수나 반환값은 그 의미를 읽기 좋게 표현하며 이해하기 쉬워진다. 인수나 반환값이 표준 라이브러리나 변경하지 못하는 코드에 속한다면 의미를 명료하게 밝히는 주석이 유용하다.   
주석이 올바른지 검증하기 쉽지 않다. 그러므로 주석을 달 때는 더 나은 방법이 없는지 고민하고 정확히 달도록 각별히 주의한다.
```java
public void testCompareTo() throws Exection {
  WikiPagePath a = PathParser.parse("PageA");
  WikiPagePath ab = PathParser.parse("PageA.PageB");
  WikiPagePath b = PathParser.parse("PageB");
  WikiPagePath aa = PathParser.parse("PageA.PageA");
  WikiPagePath bb = PathParser.parse("PageB.PageB");
  WikiPagePath ba = PathParser.parse("PageB.PageA");

  assertTrue(a.compareTo(a) == 0); // a == a
  assertTrue(a.compareTo(b) != 0); // a != b
  assertTrue(ab.compareTo(ab) == 0); // ab == ab
  assertTrue(a.compareTo(b) == -1); // a < b
  assertTrue(aa.compareTo(ab) == =1); // aa < ab
  assertTrue(ba.compareTo(bb) == -1); // ba < bb
  assertTrue(b.compareTo(a) == 1); // b > a
  assertTrue(ab.compareTo(aa) == 1); // ab > aa
  assertTrue(bb.compareTo(ba) == 1); // bb > ba
}
```

<a name="3-5"></a>

### *결과를 경고하는 주석*
다른 프로그래머에게 결과를 경고할 목적으로 주석을 사용한다.
```java
public static SimpleDateFormat makeStandardHttpDateFormat() {
  // SimpleDateFormat은 스레드에 안전하지 못하다.
  // 따라서 각 인스턴스를 독립적으로 생성해야 한다.
  SimpleDateFormat df = new SimpleDateFormat("EEE, dd MMM yyyy HH:mm:ss z");
  df.setTimeZone(TimeZone.getTimeZone("GMT"));
  return df;
}
```

<a name="3-6"></a>

### *TODO 주석*
'앞으로 할 일', 프로그래머가 필요하다 여기지만 당장 구현하기 어려운 업무를 기술한다.
```java
// TODO-MdM 현재 필요하지 않다.
// 체크아웃 모델을 도입하면 함수가 필요 없다.
protected VersionInfo makeVersion() throws Exception {
  return null;
}
```

<a name="3-7"></a>

### *중요성을 강조하는 주석*
자칫 대수롭지 않다고 여겨질 뭔가의 중요성을 강조하기 위해서도 주석을 사용한다.
```java
String listItemContent = match.group(3).trim();
// 여기서 trim은 정말 중요하다. trim 함수는 문자열에서 시작 공백을 제거한다.
// 문자열에 시작 공백이 있으면 다른 문자열로 인식되기 때문이다.
new ListItemWidget(this, listItemContent, this.level + 1);
return buildList(text.substring(match.end()));
```

<a name="3-8"></a>

### *공개 API에서 Javadocs*
공개 API를 구현한다면 반드시 훌륭한 javadocs를 작성한다.

<a name="4"></a>

## 나쁜 주석
일반적으로 대다수 주석은 허술한 코드를 지탱하거나, 엉성한 코드를 변명하거나, 미숙한 결정을 합리화하는 등 프로그래머가 주절거리는 독백에서 크게 벗어나지 못한다.

<a name="4-1"></a>

### *주절거리는 주석*
주석을 달기로 결정했다면 충분한 시간을 들여 최고의 주석을 달도록 노력한다. 이해가 안되어 다른 모듈까지 뒤져야 하는 주석은 독자와 제대로 소통하지 못하는 주석이다.    
그런 주석은 바이트만 낭비할 뿐이다.
```java
public void loadProperties() {
  try {
    String propertiesPath = propertiesLocation + "/" + PROPERTIES_FILE;
    FileInputStream propertiesStream = new FileInputStream(propertiesPath);
    loadedProperties.load(propertiesStream);
  } catch (IOException e) {
    // 속성 파일이 없다면 기본값을 모두 메모리로 읽어 들였다는 의미다.
  }
}
```

<a name="4-2"></a>

### *같은 이야기를 중복하는 주석*
헤더에 달린 주석이 같은 내용을 그대로 중복하는 주석은 코드보다 주석을 읽는 시간이 더 오래 걸린다.

<a name="4-3"></a>

### *오해할 여지가 있는 주석*
주석에 담긴 '살짝 잘못된 정보'가 달린 주석
```java
// this.closed가 true일 때 반환되는 유틸리티 메서드다.
// 타임아웃에 도달하면 예외를 던진다.
public synchronized void waitForClose(final long timeoutMillis) throws Exception {
  if(!closed) {
    wait(timeoutMillis);
    if(!closed) 
      throw new Exception("MockResponseSender cloud not be closed!");
  }
}
```
`this.closed`가 `true`로 변하는 순간에 메서드는 반환되지 않는다. `this.closed`가 `true`여야 메서드는 반환된다.

<a name="4-4"></a>

### *의무적으로 다는 주석*
모든 함수에 Javadocs를 달거나 모든 변수에 주석을 달아야 한다는 규칙은 코드를 복잡하게 만들며, 거짓말을 퍼뜨리고, 혼동과 무질서를 초래한다.
```java
/*
@param title CD 제목
@param author CD 저자
@param tracks CD 트랙 숫자
@param durationInMinutes CD 길이(단위: 분)
*/

public void addCD(String title, String author, int tracks, int durationInMinutes) {
  CD cd = new CD();
  cd.title = title;
  cd.author = author;
  cd.tracks = tracks;
  cd.duration = durationInMinutes;
  cdList.add(cd)
}
```

<a name="4-5"></a>

### *이력을 기록하는 주석*
모듈을 편집할 때마다 모듈 첫머리에 추가하는 주석이다.   
소스 코드 관리 시스템이 없는 시절에는 변경 이력을 기록하고 관리하는 관례가 바람직했으나 현재는 혼란만 가중되므로 완전히 제거하는 편이 좋다.

<a name="4-6"></a>

### *있으나 마나 한 주석*
너무나 당연한 사실을 언급하며 새로운 정보를 제공하는 못하는 주석이다.
있으나 마나 한 주석을 달려는 유혹에서 벗어나 코드를 정리하는게 더 낫고, 행복한 프로그래머가 되는 지름길이다.
```java
// 기본 생성자
protected AnnualDateRule() {
  
}
// 월 중 일자
private int dayOfMonth;
// 월 중 일자를 반환한다.
// @return 월 중 일자
public int getDayOfMonth() {
  return dayOfMonth;
}
```

<a name="4-7"></a>

### *무서운 잡음*
어떠한 목적도 수행하지 않는 주석
```java
// The name.
private String name;
// The version.
private String version;
```

<a name="4-8"></a>

### *함수나 변수로 표현할 수 있다면 주석을 달지 마라*
주석이 필요하지 않도록 코드를 개선하는 편이 더 좋다.
```java
// Bad
// 전역 목록 <smodule>에 속하는 모듈이 우리가 속한 하위 시스템에 의존하는가?
if (smodule.getDependSubsystems().contains(subSysMod.getSubSystem()))

// Good
ArrayList moduleDependees = smodule.getDependSubsystems();
String ourSubSystem = subSysMod.getSubSystem();
if (moduleDependees.contain(ourSubSystem))
```

<a name="4-9"></a>

### *위치를 표시하는 주석*
소스 파일에서 특정 위치를 표시하기 위해 사용하는 주석이다.   
극히 드물지만 배너 아래 특정 기능을 모아놓으면 유용한 경우도 있지만 가독성을 낮출 수 있어 제거해야 마땅하다.
배너를 남용하면 독자가 흔한 잡음으로 여겨 무시한다.
```
// Actions //////////////////
```

<a name="4-10"></a>

### *닫는 괄호에 다는 주석*
중첩이 심하고 장황한 함수라면 의미가 있을지도 모르지만 작고 캡슐화된 함수에는 잡음일 뿐이다.
```java
public class wc {
  public static void main(String[] args) {
    BufferedReader in = new BufferedReader(new InputStreamReader(System.in));
    String line;
    int lineCount = 0;
    int charCount = 0;
    int wordCount = 0;
    try {
      while ((line == in.readLine()) != null) {
        lineCount += 1;
        charCount += line.length();
        String words[] = line.split("\\W");
        wordCount += words.length;
      } // while
      System.out.println("wordCount = " + wordCount);
    } // try
    catch (IOException e) {
      System.err.println("Error : " + e.getMessages());
    } // catch
  } // main
}
```

<a name="4-11"></a>

### *공로를 돌리거나 저자를 표시하는 주석*
`/* 릭이 추가함 */`
소스 코드 관리 시스템은 누가 언제 무엇을 추가했는지 기억하기 때문에 코드에 주석으로 표시하는것은 불필요한 정보이다.

<a name="4-12"></a>

### *주석으로 처리한 코드*
주석으로 처리된 코드는 이유가 있어 남겨 놓았으리라고, 중요하니깐 지우기를 주저한다.   
이 또한 소스 코드 관리 시스템이 코드를 기억하기 때문에 주석으로 처리할 필요가 없다.
```java
InputSreamResponse response = new InputStreamResponse();
response.setBody(formatter.getResultStream(), formatter.getByteCount());
// InputStream resultsStream = formatter.getResultStream();
// StreamReader reader = new StreamReader(resultsStream);
// response.setContent(reader.read(formatter.getByteCount()));
```

<a name="4-13"></a>

### *HTML 주석*
소스 코드에서 HTML 주석은 혐오 그 자체다.   
주석을 뽑아 웹 페이지에 올릴 거라면 주석에 HTML 태그를 삽입해야 하는 책임은 프로그래머가 아니라 도구가 져야한다.

<a name="4-14"></a>

### *전역 정보*
주석을 달아야 한다면 근처에 있는 코드만 기술하라. 코드 일부에 주석을 달면서 시스템의 전반적인 정보를 기술하지 마라.
```java
/**
* 적합성 테스트가 동작하는 포트: 기본값은 <b>8082</b>
*
* @param fitnessePort
*/
public void setFitnessePort(int fitnessePort) {
  this.fitnessPort = fitnessePort
}
```

<a name="4-15"></a>

### *너무 많은 정보*
주석에다 흥미로운 역사나 관련 없는 정보를 장황하게 늘어놓지 마라.

<a name="4-16"></a>

### *모호한 관계*
주석과 주석이 설명하는 코드는 둘 사이 관계가 명백해야 한다.
```java
// 모든 픽셀을 담을 만큼 충분한 배열로 시작한다(여기에 필터 바이트를 더한다).
// 그리고 헤더 정보를 위해 200바이트를 더한다.
this.pngBytes = new byte[((this.width + 1) * this.height * 3) + 200];
```

<a name="4-17"></a>

### *함수 헤더*
짧은 함수는 긴 설명이 필요 없다. 짧고 한 가지만 수행하며 이름을 잘 붙인 함수가 주석으로 헤더를 추가한 함수보다 훨씬 좋다.

<a name="4-18"></a>

### *비공개 코드에서 Javadocs*
공개 API는 Javadocs가 유용하지만 공개하지 않을 코드라면 Javadocs는 쓸모가 없다.
