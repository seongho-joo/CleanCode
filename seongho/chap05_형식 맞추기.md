## 목차
- [형식을 맞추는 목적](#1)
- [적절한 행 길이를 유지하라](#2)
  - [신문 기사처럼 작성하라](#2-1)
  - [개념은 빈 행으로 분리하라](#2-2)
  - [세로 밀집도](#2-3)
  - [수직 거리](#2-4)
  - [세로 순서](#2-5)
- [가로 형식 맞추기](#3)
  - [가로 공백과 밀집도](#3-1)
  - [가로 정렬](#3-2)
  - [들여쓰기](#3-3)
  - [가짜 범위](#3-4)
- [팀 규칙](#4)
- [밥 아저씨의 형식 규칙](#5)
***

> 프로그래머라면 형식을 깔끔하게 맞춰 코드를 짜야 한다. 코드 형식을 맞추기 위한 간단한 규칙을 정하고 그 규칙을 착실히 따라야 한다.   
> 팀으로 일한다면 팀이 합의해 규칙을 정하고 모두가 그 규칙을 따라야 한다. 필요하다면 규칙을 자동으로 적용하는 도구를 활용한다.

<a name="1"></a>

## 형식을 맞추는 목적
코드 형식은 의사소통의 일환이다. 의사 소통은 개발자의 일차적인 의무다.   
처음 잡아놓은 구현 스타일과 가독성 수준은 유지보수 용이성과 확정성에 계속 영향성을 미친다.

<a name="2"></a>

## 적절한 행 길이를 유지하라
200줄 정도인 파일로도 큰 프로젝트들을 구축할 수 있다.   
일반적으로 큰 파일보다 작은 파일이 이해하기 쉽다. 

<a name="2-1"></a>

### *신문 기사처럼 작성하라*
- 이름은 간단하면서도 설명이 가능하게 짓는다.
- 이름만 보고도 올바른 모듈을 살펴보고 있는지 아닌지를 판단할 정도로 신경 써서 짓는다.
- 소스 파일 첫 부분은 고차원 개념과 알고리즘을 설명한다.
- 아래로 내려갈수록 의도를 세세하게 묘사한다.
- 마지막에는 가장 저차원 함수와 세부 내역이 나온다. 

<a name="2-2"></a>

### *개념은 빈 행으로 분리하라*
빈 행은 너무나도 간단한 규칙이지만 코드의 세로 레이아웃에 심오한 영향을 미친다. 빈 행은 새로운 개념을 시작한다는 단서다.   
빈 행을 빼버리면 코드 가독성이 현저하게 떨어진다.

<a name="2-3"></a>

### *세로 밀집도*
줄바꿈이 개념을 분리한다면 세로 밀집도는 연관성을 의미한다. 즉, 서로 밀접한 코드 행은 세로로 가까이 놓여야 한다는 뜻이다.

<a name="2-4"></a>

### *수직 거리*
서로 밀접한 개념은 세로로 가까이 둬야 한다.   
같은 파일에 속할 정도로 밀접한 두 개념은 세로 거리로 연광성을 표현한다. 여기서 연관성이란 한 개념을 이해하는 데 다른 개념이 중요한 정도다.   
- **변수 선언**
  - 변수는 사용하는 위치에 최대한 가까이 선언한다.
- **인스턴스 변수**
  - 인스턴스 변수는 클래스 맨 처음에 선언한다. 변수 간에 세로로 거리를 두지 않는다.
- **종족 함수**
  - 한 함수가 다른 함수를 호출한다면 두 함수는 세로로 가까이 배치한다.
  - 가능하다면 호출하는 함수를 호출되는 함수보다 먼저 배치한다.
  ```java
  public class WikiPageResponder implements SecureResponder {
    protected WikiPage page;
    protected PageData pageData;
    protected String pageTitle;
    protected Request request;
    protected PageCrawler crawler;

    public Response makeResponse(FitNesseContext context, Request request) throws Exception {
      String pageName = getPageNameOrDefault(request, "FrontPage");
      loadPage(pageName, context);
      if (page == null) 
        return notFoundResponse(context, request);
      else 
        return makePageResponse(context);
    }

    private String getPageNameOrDefault(Request request, String defaultPageName) {
      String pageName = request.getResourse();
      if (StringUtil.isBlank(pageName))
        pageName = defaultPageName;

      return pageName;
    }

    protected void loadPage(String resource, FitNesseContext context) throws Exception {
      ...
    }

    private Response notFoundResponse(FitNesseContext context, Request request) throws Exception {
      ...
    }

    private SimpleResponse makePageResponse(FitNesseContexts context) throws Exception {
      ...
    }
  }
  ```
- **개념적 유사성**
  - 친화도가 높을수록 코드를 가까이 배치한다.
  - 친화도가 높은 요인
    - 한 함수가 다른 함수를 호출해 생기는 직접적인 종속성
    - 변수와 그 변수를 사용하는 함수
    - 비슷한 동작을 수행하는 일군의 함수
  ```java
  public class Assert {
    static public void assertTrue(String message, boolean condition) {
      if (!condition) 
        fail(message);
    }

    static public void assertTrue(boolean condition) {
      assertTrue(null, condition);
    }

    static public void assertFalse(String message, boolean condition) {
      assertTrue(message, !condition);
    }

    static public void assertFalse(boolean condition) {
      assertFalse(null, condition);
    }
  }
  ```

<a name="2-5"></a>

### *세로 순서*
일반적으로 함수 호출 종속성은 아래 방향으로 유지한다. 다시 말해, 호출되는 함수를 호출하는 함수보다 나중에 배치한다.[<sup>[1]</sup>](#fn1)   
신문 기사와 마찬가지로 가장 중요한 개념을 가장 먼저 표현한다.세세한 사항은 마지막에 표현한다.


<a name="3"></a>

## 가로 형식 맞추기
한 행은 가로로 20자에서 60자가 적당하다. 100자나 120자에 달해도 나쁘지 않지만 그 이상은 솔직히 주의부족이다.

<a name="3-1"></a>

### *가로 공백과 밀집도*
가로는 공백을 사용해 밀접한 개념과 느슨한 개념을 표현한다.   
할당문은 왼쪽 요소와 오른쪽 요소가 나뉜다. 공백을 넣으면 두 가지 주요 요소가 확실히 나뉜다는 사실이 더욱 분명해진다.   
연산자 우선순위를 강조하기 위해서도 공백을 사용한다. 하지만 코드 형식을 자동으로 맞춰주는 도구는 대다수가 연산자 우선순위를 고려하지 못하므로, 수식에 똑같은 간격을 적용한다.
```java
// 할당 연산자 공백
private void maesureLine(String line) {
  lineCount++;
  int lineSize = line.length();
  totalChars += lineSize;
  lineWidthHistogram.addLine(lineSize, lineCount);
  recordWidestLine(lineSize);
}
// 연산자 우선순위 공백
public class Quadratic {
  public static double root1(double a, double b, double c) {
    double determinant = determinant(a, b, c);
    return (-b + Math.sqrt(determinant)) / (2*a);
  }

  public static double root2(int a, int b, int c) {
    double determinant = determinant(a, b, c);
    return (-b - Math.sqrt(determinant)) / (2*a);
  }

  public static double dterminant(double a, double b, double c) {
    return b*b - 4*a*c;
  }
}
```
<a name="3-2"></a>

### *가로 정렬*
가로 정렬은 코드가 엉뚱한 부분을 강조해 진짜 의도가 가려진다. 그리고 코드 형식을 자동으로 맞춰주는 도구는 대다수가 위와 같은 정렬을 무시한다.

<a name="3-3"></a>

### *들여쓰기*
소스 파일은 윤곽도와 계층이 비슷하다.   
범위<sup>scope</sup>로 이뤄진 계층을 표현하기 위해 우리는 코드를 들여쓴다. 프로그래머는 들여쓰기 체계에 크게 의존한다. 왼쪽으로 코드를 맞춰 코드가 속하는 범위를 시각적으로 표현한다.   
그러면 이 범위에서 저범위로 재빨리 이동하기 쉬워진다.

<a name="3-4"></a>

### *가짜 범위*
빈 while문이나 for 문을 접할때는 세미콜론을 새 행에다 제대로 들여써서 넣어준다.
```java
while(dis.read(buf, 0, readBuffersSize) != -1)
;
```

<a name="4"></a>

## 팀 규칙
프로그래머는 각자 선소하는 규칙이 있지만 팀에 속한다면 자신이 선호해야 할 규칙은 바로 팀 규칙이다.   
팀은 한 가지 규칙에 합의해야 하고 모든 팀원은 그 규칙을 따라야 한다. 그래야 소프트웨어가 일관적인 스타일을 보인다.   
좋은 소프트웨어 시스템은 읽기 쉬운 문서로 이뤄진다는 사실을 기억하기 바란다. 스타일은 일관적이고 매끄러워야 한다. 한 소스 파일에서 봤던 형식이 다른 소스 파일에도 쓰이리라는 신뢰감을 독자에게 줘야 한다.

<a name="5"></a>

## 밥 아저씨의 형식 규칙
```java
 public class CodeAnalyzer implements JavaFileAnalysis {
  private int lineCount;
  private int maxLineWidth;
  private int widestLineNumber;
  private LineWidhtHistogram lineWidthHistogram;
  private int totalChars;

  public CodeAnalyzer() {
    lineWidhtHistogram = new LineWidthHistogram();
  }

  public static List<File> findJavaFiles(File parentDirectory) {
    List<File> files = new ArrayList<File>();
    findJavaFiles(parentDirectory, files);
    return files;
  }

  private static void findJavaFiles(File parentDirectory, List<File> files) {
    for (File file : parentDirectory.listFiles()) {
      if (file.getName().endsWidth(".java"))
        files.add(file);
      else if (file.isDirectory())
        findJavaFies(file, files);
    }
  }

  public void analyzeFile(File javaFile) throws Exception {
    BufferedReader br = new BufferedReade(enw FileReader(javaFile));
    String line;
    while ((line = br.readLine()) != null)
      measureLine(line);
  }

  private void measureLine(String line) {
    lineCount++;
    int lineSize = line.length();
    totalChars += lineSize;
    lineWidthHistogram.addLine(lineSize, lineCount);
    recordWidestLine(lineSize);
  }

  private void recordWidestLine(int lineSize) {
    if (lineSize > maxLineWidth) {
      maxLineWidth = lineSize;
      widestLineNumber = lineCount;
    }
  }

  public int getLineCount() {
    return lineCount;
  }

  public int getMaxLineWidth() {
    return maxLineWidth;
  }

  public int getWidestLineNumber() {
    return widestLineNumber;
  }

  public LineWidthHistogram getLineWidthHistogram() {
    return lineWidthHistogram;
  }

  public double getMeanLineWidth() {
    return (double)totalChars/lineCount;
  }

  public int getMedianLineWidth() {
    Integer[] sortedWidths = getSortedWidths();
    int cumulativeLineCount = 0;
    for (int width : sortedWidths) {
      cumulativeLineCount += lineCountForWidth(width);
      if (cumulativeLineCount > lineCount/2)
        return width;
    }
    throw new Error("Cannot get here");
  }

  private int lineCountForWidth(int width) {
    return lineWidthHistogram.getLinesforWidth(width).size();
  }

  private Integer[] getSortedWidths() {
    Set<Integer> widths = lineWidthHistoram.getWidths();
    Integer[] sortedWidths = (widths.toArray(new Integer[0]));
    Arrays.sort(sortedWidths);
    return sortedWidths;
  }
 }
```

#### 참조
<a name="fn1"></a>

##### 1 파스칼, C, C++와 정확히 반대다. 파스칼, C, C++에서 함수를 호출하려면 먼저 정의하거나 미리 선언 해놓아야 한다.