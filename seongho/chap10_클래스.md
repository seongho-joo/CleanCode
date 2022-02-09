# Clean Code 10장 클래스
> 코드의 표현력과 그 코드로 이루어진 함수에 아무리 신경 쓸지라도 좀 더 높은 차원 높은 단계 까지 신경 쓰지 않으면 깨끗한 코드를 얻기는 어렵다.
- [Clean Code 10장 클래스](#clean-code-10장-클래스)
  - [클래스 체계](#클래스-체계)
    - [캡슐화](#캡슐화)
  - [클래스는 작아야 한다!](#클래스는-작아야-한다)
    - [단일 책임 원칙](#단일-책임-원칙)
    - [응집도](#응집도)
    - [응집도를 유지하면 작은 클래스 여럿이 나온다](#응집도를-유지하면-작은-클래스-여럿이-나온다)
  - [변경하기 쉬운 클래스](#변경하기-쉬운-클래스)
    - [변경으로부터 격리](#변경으로부터-격리)

## 클래스 체계
클래스를 정의하는 표준 자바 관례에 따른 순서
1. 정적 공개 상수
2. 정적 비공개 변수
3. 비공개 인스턴스 변수
4. 공개 변수 (필요한 경우는 거의 없음)
5. 공개 함수
6. 비공개 함수 (자신을 호출하는 공개 함수 직후)

추상화 단계가 순차적으로 내려간다.

### 캡슐화
변수와 유틸리티 함수는 가능한 공개하지 않는 편이 낫지만 반드시 숨겨야 한다는 법칙도 없다. 때때로 변수나 유틸리티 함수를 protected로 선언해 테스트 코드에 접근을 허용하기도 한다. 하지만 그전에 비공개 상태를 유지할 온갖 방법을 강구해야 한다.
> 캡슐화를 풀어주는 결정은 언제나 최후의 수단이다.

## 클래스는 작아야 한다!
클래스를 만들 때 첫 번째 규칙도 두 번째 규칙도 크기다. 클래스는 작아야 한다.   
클래스의 크기를 측정하는 척도는 클래스가 맡은 **책임의 수**이다. 클래스 안에 정의된 메서드가 적다고 클래스의 크기가 작은 것은 아니다.
```java
// 메서드 수가 작음에도 불구하고 책임이 많은 예
public class SuperDashboard extends JFrame implments MetaDateUser {
  public Component getLastFocusedComponent();
  public void setLastFocused(Component lastFocused);
  public int getMajorVersionNumber();
  public int getMinorVersionNumber();
  public int getBuildNumber();
}
```
클래스가 맡은 책임을 작게 갖는 법
- 클래스 이름은 해당 클래스 책임을 기술해야 한다.
  - 간결한 이름이 떠오르지 않는다면 클래스가 너무 커서 그렇다.
  - 클래스 이름에 `Processor`, `Manager`, `Supber`등과 같이 모호한 단어가 있다면 클래스가 너무 많은 책임을 떠안겼다는 증거다.
- 클래스 설명은 if, and, or, but을 사용하지 않고서 25단어 내외로 가능해야 한다.
  
### 단일 책임 원칙
단일 책임 원칙(SRP)
- 클래스나 모듈을 변경할 이유가 하나, 단 하나뿐이어야 한다는 원칙
- 클래스는 단 하나의 책임을 가져야 한다.

책임, 즉 변경할 이유를 파악하려 애쓰다 보면 코드를 추상화하기도 쉬워진다.
```java
// 단일 책임 클래스 예
public class Version {
  public int getMajorVersionNumber();
  public int getMinorVersionNumber();
  public int getBuildNumber();
}
```
> SRP는 이해하고 지키기 수월한 개념이지만 클래스 설계자가 가장 무시하는 규칙 중 하나다.   
> 그 이유를 한가지 말하자면 우리들 대다수는 '깨끗하고 체계적인 소프트웨어'보다 '돌아가는 소프트웨어'에 초점을 맞추기 때문이다. 이것이 잘못된건 아니지만 프로그램이 정상으로 돌아간다면 '깨끗하고 체계적인 소프트웨어'라는 다음 관심사로 전환해야 한다.

**큰 클래스 몇 개가 아니라 작은 클래스 여럿으로 이뤄진 시스템이 더 바람직하다.**

### 응집도
클래스는 인스턴스 변수 수가 작아야 한다. 각 클래스 메서드는 클래스 인스턴스 변수를 하나 이상 사용해야 한다.   
일반적으로 메서드가 변수를 더 많이 사용 할수록 메서드와 클래스는 응집도가 더 높다.

'함수를 작게, 매개변수 목록을 짧게'라는 전략을 따르다 보면 때때로 몇몇 메서드만이 사용하는 인스턴스 변수가 아주 많아진다. 이는 새로운 클래스로 쪼개야 한다는 신호다. 응집도가 높아지도록 변수와 메서드를 적절히 분리해 새로운 클래스 두세 개로 쪼개준다.

### 응집도를 유지하면 작은 클래스 여럿이 나온다
큰 함수를 작은 함수러 쪼갤 때 변수들을 인스턴스 변수로 바꾼다면 함수를 쪼개기 쉬워진다. 하지만 몇몇 함수만 사용하는 인스턴스 변수가 늘어나기 때문에 클래스는 응집도는 낮아진다.

**클래스 응집도 낮아진다면 여러 클래스로 쪼갠다!** 그래서 큰 함수를 작은 함수 여럿으로 쪼개다 보면 종종 작은 클래스 여럿으로 쪼갤 기회가 생긴다. 그러면서 프로그램에 점점 더 체계가 잡히고 구조가 투명해진다.

## 변경하기 쉬운 클래스
대다수 시스템은 지속적인 변경이 가해진다. 그리고 변경할 때마다 시스템이 의도대로 동작하지 않을 위험이 따른다. 깨끗한 시스템은 클래스를 체계적으로 정리해 변경에 수반하는 위험을 낮춘다.
```java
// Bad
// 변경이 필요해 손대야하는 클래스
public class Sql {
  public Sql(String table, Colum[] columns)
  public String create()
  public String insert(Object[] fields)
  public String selectAll()
  public String findByKey(String keyColumn, String keyValue)
  public String select(Column column, String pattern)
  public String select(Criteria criteria)
  public String preparedInsert()
  private String columnList(Column[] columns)
  private String valueList(Object[] fields, final Column[] columns)
  private String selectWithCriteria(String criteria)
  private String placeholderList(Column[] columns)
}
```
```java
// Good
// 닫힌 클래스 집합
abstract public class Sql {
  public Sql(String table, Column[] columns)
  abstract public String generate();
}

public class CreateSql extends Sql {
  public CreateSql(String table, Column[] columns)
  @Override public String generate()
}

public class SelectSql extends Sql {
  public SelectSql(String table, Column[] columns)
  @Override public String generate()
}

public class InsertSql extends Sql {
  public InsertSql(String table, Column[] columns, Object[] fields)
  @Override public String generate()
  private String valueList(Object[] fields, final Column[] columns)
}

public class SelectWithCriteriaSql extends Sql {
  public SelectWithCriteriaSql(String table, Column[] columns, Criteria criteria)
  @Override public String generate()
}

public class SelectWithMatchSql extends Sql {
  public SelectWithMatchSql(String table, Column[] columns, String pattern)
  @Override public String generate()
}

public class FindBykeySql extends Sql {
  public FindBykeySql(String table, Column[] columns, String keyColumn, String keyValue)
  @Override public String generate()
}

public class PreparedInsertSql extends Sql {
  public PreparedInsertSql(String table, Column[] columns)
  @Override public String generate()
  private String placeholderList(Column[] columns)
}

public class Where {
  public Where(String criteria)
  public String generate()
}

public class ColumnList {
  public ColmnList(Column[] columns)
  public String generate()
}
```
위 코드처럼 재구성한 Sql 클래스는 SRP와 OCP을 지원한다. 그리고 update 문을 추가할 때 기존 클래스를 변경할 필요가 없다.

📍 OCP(Open-Closed Principle)
- 클래스는 확장에 개방적이고 수정에 폐쇄적이어야 한다는 원칙

새 기능을 수정하거나 기존 기능을 변경할 때 건드릴 코드가 최소인 시스템 구조가 바람직하다. 이상적인 시스템이라면 새 기능을 추가할 때 시스템을 확장 할 뿐 기존 코드를 변경하지 않는다.

### 변경으로부터 격리
상세한 구현에 의존하는 클라이언트 클래스는 구현이 바뀌면 위험에 빠진다. 그래서 우리는 인터페이스와 추상 클래스를 사용해 구현이 미치는 영향을 격리한다.

결합도가 낮다란?
- 각 시스템 요소가 다른 요소로부터 그리고 변경으로 부터 잘 격리되어 있다는 의미

낮은 시스템 결합도의 장점
- 유연성과 재사용성도 높아짐
- 시스템 요소가 서로 잘 격리되어 있으면 각 요소를 이해하기도 더 쉬워짐
- DIP를 따르는 클래스가 나옴

📍 DIP(Dependency Inversion Prinicple)
- 클래스가 상세한 구현이 아니라 추상화에 의존해야 한다는 원칙

```java
public interface StockExchange {
  Money currentPrice(String symbol);
}

public Portfolio {
  private StockExchange exchage;
  public Portfolio(StockExchange exchange) {
    this.exchange = exchange;
  }
  ...
}

public class PortfolioTest {
  private FixedStockExchangeStub exchane;
  private Portfolio portfolio;

  @Before
  protected void setUp() throws Exception {
    exchange = new FixedStockExchangeStub();
    exchange.fix("MSFT", 100);
    portfolio = new Portfolio(exchange);
  }

  @Test
  public void GivenFiveMSTFTotalShouldBe500() throws Exception {
    portfoilo.add(5, "MSFT");
    Assert.assertEquals(500, portfolio.value());
  }
}
```

