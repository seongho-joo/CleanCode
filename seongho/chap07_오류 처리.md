## 목차
- [오류 코드보다 예외를 사용하라](#1)
- [Try-Catch-Finally 문부터 작성하라](#2)
- [미확인 예외를 사용하라](#3)
- [예외에 의미를 제공하라](#4)
- [호출자를 고려해 예외 클래스를 정의하라](#5)
- [정상 흐름을 정의하라](#6)
- [null을 반환하지 마라](#7)
- [null을 전달하지 마라](#8)
- [결론](#9)
***

> &nbsp;오류 처리는 프로그램에 반드시 필요한 요소 중 하나일 뿐이다.
> 간단히 말해, 뭔가 잘못될 가능성은 늘 존재한다.
> 깨끗한 코드와 오류 처리는 확실히 연관성이 있다. 
> 상당수 코드 기반은 전적으로 오류 처리 코드에 좌우된다.   
> &nbsp;이 장에서는 깨끗하고 튼튼한 코드에 한 걸음 더 다가가는 단계로 우아하고 고상하게 오류를 처리하는 기법과 고려 사항 몇 가지를 소개한다.

<a name="1"></a>

## 오류 코드보다 예외를 사용하라
오류 코드를 사용하면 함수를 호출한 즉시 오류를 확인해야하기 때문에 호출자 코드가 복잡해진다. 그렇기 때문에 오류가 발생하면 예외를 던지는 것이 좋다.
```java
public void sendShutDown() {
  try {
    tryToShutDown();
  }catch (DeviceShutDownError e) {
    logger.log(e);
  }
}

private void TryToShutDown() throws DeviceShutDownError {
  DeviceHandle handle = getHandle(DEV1);
  DeviceRecord record = retrieveDeviceRecord(handle);

  pauseDevice(handle);
  clearDeviceWorkQueue(handle);
  closeDevice(handle);
}

private DeviceHandle getHandle(DeviceId id) {
  ...
  throw noew DeviceShutDownError("Invalid handle for: " + id.toString());
  ...
}
```
프로그램 논리를 처리하는 부분과 오류를 처리하는 부분이 분리가 되어 코드가 깔끔해진다. 각 개념을 독립적으로 살펴보고 이해할 수 있다.

<a name="2"></a>

## Try-Catch-Finally 문부터 작성하라
try 블록에서 무슨 일이 생기든지 catch 블록은 프로그램 상태를 일관성 있게 유지해야 한다. 그러므로 예외가 발생할 코드를 짤 때는 try-catch-finally 문으로 시작하는 편이 낫다.

먼저 강제로 예외를 일으키는 테스트 케이스를 작성한 후 테스트를 통과하게 코드를 작성하는 방법을 권장한다. 그러면 자연스럽게 try 블록의 트랜잭션 범위부터 구현하게 되므로 범위 내에서 트랜잭션 본질을 유지하기 쉬워진다.

<a name="3"></a>

## 미확인<sup>unchecked</sup> 예외를 사용하라
확인된 예외는 몇 가지 장점을 제공하지만 안정적인 소프트웨어를 제작하는 요소로 확인된 예외가 반드시 필요하지 않다는 사실이 분명해졌다. C#, C++, Python, Ruby는 확인된 예외를 지원하지 않지만 안정적인 소프트웨어를 구현하기에 무리가 없다. 
그러므로 확인된 오류가 치르는 비용에 상승하는 이익을 제공하지는 철저히 따져봐야 한다.

확인된 예외는 OCP를 위반한다. 메서드에서 확인된 예외를 던졌는데 catch 블록이 세 단계 위에 있다면 그 사이 메서드 모두가 선언부에 해당 예외를 정의해야 한다. 즉, 하위 단계에서 코드를 변경하면 상위 단계 메서드 선언부를 전부 고쳐야 한다.
학인된 오류를 던진다면 함수는 선언부에 throws 절을 추가해야 한다. 그러면 변경한 함수를 호출하는 함수 모두가 catch 블록에서 새로운 예외를 처리하거나, 선언부에 throw 절을 추가해야 한다. 결과적으로 최하위 단계에서 최상위 단계까지 연쇄적인 수정이 일어난다.

때로는 확인된 예외도 유용하다. 아주 중요한 라이브러리를 작성한다면 모든 예외를 잡아야 한다. 하지만 일반적인 애플리케이션은 의존성이라는 비용이 이익보다 크다.

<a name="4"></a>

## 예외에 의미를 제공하라
예외를 던질 때는 전후 상황을 덧붙인다면 오류가 발생한 원인가 위치를 찾기가 쉬워진다. 그리고 오류 메시지에 정보를 담아 예외에 함께 던진다.
> ex) 실패한 연산 이름, 실패 유형

<a name="5"></a>

## 호출자를 고려해 예외 클래스를 정의하라
애플리케이션에서 오류를 정의할 때 프로그래머에게 가장 중요한 관심사는 **오류를 잡아내는 방법**이 되어야 한다.
```java
// Bad
ACMEPort port = new ACMEPort(12);

try {
  port.open();
} catch (DeviceResponseException e) {
  reportPortError(e);
  logger.log("Device response exceptino", e);
} catch (ATM1212UnlockedException e) {
  reportPortError(e);
  logger.log("Unlock exception", e);
} catch (GMXError e) {
  reportPortError(e);
  logger.log("Device response exception");
} finally {
  ...
}
```
```java
// Good
LocalPort port = new LocalPort(12);
try {
  port.open();
} catch (PortDeviceFailure e) {
  reportError(e);
  logger.log(e.getMessage(), e);
} finally {
  ...
}

public class LocalPort {
  private ACMEPort innerPort;

  public LocalPort(int portNumber){
    innerPort = new ACMEPort(portNumber);
  }

  public void open() {
    try {
      innterPort.open();
    } catch (DeviceResoponse e) {
      throw new PortDeviceFailure(e);
    } catch (ATM1212UnlockException e) {
      throw new PortDeviceFailure(e);
    } catch (GMXError e) {
      throw new PortDeviceFailure(e);
    }
  }
  ...
}
```
LocalPort 클래스처럼 ACMEPort를 감싸는 클래스는 매우 유용하다. 

외부 API를 감싸기 기법의 장점
- 외부 라이브러리와 프로그램 사이에서 의존성이 줄어든다.
- 다른 라이브러리로 갈아타도 비용이 적다.
- 감싸기 클래스에서 외부 API를 호출하는 대신 테스트 코드를 넣어주는 방법으로 프로그램을 테스트하기도 쉬워진다.
- 특정 업체가 API를 설계한 방식에 발목 잡히지 않는다.
  
예외 클래스에 포함된 정보로 오류를 구분해도 괜찮은 경우 예외 클래스는 하나로 충분하고, 한 예외는 잡아내고 다른 예외는 무시해도 괜찮은 경우라면 여러 예외 클래스르 사용한다.

<a name="6"></a>

## 정상흐름을 정의하라
특수 사례 패턴<sup>SPECIAL CASE PATTERN</sup>는 클래스를 만들거나 객체를 조작해 특수 사례를 처리하는 방식이다. 그러면 클래스나 객체가 예외적인 상황을 캡슐화해서 처리하므로 클라이언트가 코드가 예외적인 상황을 처리할 필요가 없어진다.
```java
// Bad
try {
  MealExpenses expenses = expenseResponrtDAO.getMeals(employee.getId());
  m_total += expenses.getTotal();
} catch (MealExpensesNotFound e) {
  m_total += getMealPerDiem();
}
```
```java
// Good
MealExpenses expenses = expenseResponrtDAO.getMeals(employee.getId());
m_total += expenses.getTotal();

public class PerDiemMealExpenses implements MealExpenses {
  public int getTotal() {
    // 기본값으로 일일 기본 식비를 반환한다.
  }
}
```

<a name="7"></a>

## null을 반환하지 마라
null을 반환하는 코드는 null 확인을 빼먹는다면 애플리케이션이 통제 불능에 빠질지도 모른다.   
null 확인이 누락된 문제는 null 확인이 너무 많아 문제다. 메서드에서 null을 반환해야 한다면 예외를 던지거나 특수 사례 객체를 반환한다.
```java
// Bad
public void registerItem(Item item) {
  if (item != null) {
    ItemRegistry registry = peristentStore.getItemRegistry();
    if (registry != null) {
      Item existing = registry.getItem(item.getID());
      if (existing.getBillingPeriod().hasRetailOwner()) {
        existing.register(item);
      }
    }
  }
}
// 둘째 행에 null 확인이 빠졌다

// Bad
List<Employee> employees = getEmployees();
if (employees != null) {
  for (Employee e : employees) {
    totalPay += e.getPay();
  }
}

// Good
List<Employee> employees = getEmployees();
for (Employee e : employees) {
  totalPay += e.getPay();
}

public List<Employee> getEmployees() {
  if (..직원이 없다면..) 
    return Collections.emptyList();
}
```

<a name="8"></a>

## null을 전달하지 마라
메서드에서 null을 반환하는 방식도 나쁘지만 메서드로 null을 전달하는 방식은 더 나쁘다. 정상적인 인수로 null을 기대하는 API가 아니라면 메서드로 null을 전달하는 코드는 최대한 피한다.   
대다수 프로그래밍 언어는 호출자가 실수로 넘기는 null을 적절히 처리하는 방법이 없다. 해결 방법은 애초에 null을 넘기지 못하도록 금지하는 정책이 합리적이다.
```java
// Bad
public class MetricsCalculator {
  public double xProjection(Point p1, Point p2) {
    return (p2.x - p1.x) * 1.5;
  }
  ...
}
// calculator.xProjection(null, new Point(12, 13));
// 누군가 위와 같이 호출을 한다면 NullPointerException이 발생한다.

// 위 코드보단 조금 나음
// NullPointerException은 발생하지 않지만 InvalidArgumentException을 잡아내는 처리기가 필요함
public class MetricsCalculator {
  public double xProjection(Point p1, Point p2) {
    if (p1 == null || p2 == null) {
      throw InvalidArgumentException("Invalid argument for MetricsCalculator.xProjection");
    }
    return (p2.x - p1.x) * 1.5;
  }
  ...
}
// assert문 사용
public class MetricsCalculator {
  public double xProjection(Point p1, Point p2) {
    assert p1 != null : "p1 should not be null";
    assert p2 != null : "p2 should not be null";
    return (p2.x - p1.x) * 1.5;
  }
  ...
}
```

<a name="9"></a>

## 결론
깨끗한 코드는 읽기도 좋아야 하지만 안정성도 높아야 한다.
오류 처리를 프로그램 논리와 분리해 독자적인 사안으로 고려하면 튼튼하고 깨끗한 코드를 작성할 수 있다.
오류 처리를 프로그램 논리와 분리하면 독립적인 추론이 가능해지며 코드 유지보수성도 크게 높아진다.