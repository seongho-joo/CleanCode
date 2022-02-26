# Clean Code 11장 시스템
> "복잡성은 죽음이다. 개발자에게서 생기를 앗아가며, 제품을 계획하고 제작하고 테스트하기 어렵게 만든다." - 레이 오지(Ray Ozzie), 마이크로소프트 CTO

- [Clean Code 11장 시스템](#clean-code-11장-시스템)
  - [도시를 세운다면?](#도시를-세운다면)
  - [시스템 제작과 시스템 사용을 분리하라](#시스템-제작과-시스템-사용을-분리하라)
    - [Main 분리 🔗](#main-분리-)
    - [팩토리 🔗](#팩토리-)
    - [의존성 주입 🔗](#의존성-주입-)
  - [확장](#확장)
    - [횡단(cross-cutting) 관심사 🔗](#횡단cross-cutting-관심사-)
  - [자바 프록시](#자바-프록시)
  - [순수 자바 AOP 프레임워크](#순수-자바-aop-프레임워크)
  - [AspectJ 관점](#aspectj-관점)
  - [테스트 주도 시스템 아키텍처 구축](#테스트-주도-시스템-아키텍처-구축)
  - [의사 결정을 최적화하라](#의사-결정을-최적화하라)
  - [명백한 가치가 있을 때 표준을 현명하게 사용하라](#명백한-가치가-있을-때-표준을-현명하게-사용하라)
  - [시스템은 도메인 특화 언어가 필요하다](#시스템은-도메인-특화-언어가-필요하다)
  - [결론](#결론)
***

## 도시를 세운다면?
도시는 수도 관리 팀, 전력 관리 팀, 교통 관리 팀, 치안 관리 팀, 건축물 관리 팀 등 각 분야를 관리하는 팀이 있고, 적절한 추상화와 모듈화 때문에 잘 돌아 간다. 소프트웨어 팀도 도시처럼 구성한다.

깨끗한 코드를 구현하면 낮은 추상화 수준에서 관심사를 분리하기 쉬워진다. 이 장에서는 높은 추상화 수준, 즉 **시스템** 수준에서도 깨끗함을 유지하는 방법을 살펴본다.

## 시스템 제작과 시스템 사용을 분리하라
**제작**<sup>construction</sup>은 **사용**<sup>use</sup> 아주 다르다.

> 소프트웨어 시스템은(애플리케이션 객체를 제작하고 의존성을 서로 '연결'하는) 준비과정과 (준비 과정 이후에 이어지는) 런타임 로직을 분리해야 한다.

시작 단계는 모든 애플리케이션이 풀어야 할 **관심사**<sup>concern</sup>다. 관심사 분리는 이 분야에서 가장 오래되고 가장 중요한 설계 기법 중 하나다.

체계적이고 탄탄한 시스템을 만들고 싶다면 흔히 쓰는 좀스럽고 손쉬운 기법으로 모듈성을 깨서는 절대로 안 된다. 설정 논리는 일반 실행 논리와 분리해야 모듈성이 높아진다.   
또한 주요 의존성을 해소하기 위한 방식, 즉 전반적이며 일관적인 방식도 필요하다.

### Main 분리 🔗
시스템 생성과 시스템 사용을 분리하는 한 가지 방법으로, 생성과 관련한 코드는 모두 main이나 main이 호출하는 모듈로 옮기고, 나머지 시스템은 모든 객체가 생성되었고 모든 의존성이 연결되었다고 가정한다.

![그림 11-1 main()에서 생성 분리](https://user-images.githubusercontent.com/37948906/125300462-03ce8400-e365-11eb-8354-3f8bbbf3563a.png)

모든 화살표가 main쪽에서 애플리케이션 쪽을 향한다. 즉, 애플리케이션은 main이나 객체가 생성되는 과정을 전혀 모른다는 뜻이다. 단지 모든 객체가 적절히 생성되었다고 가정한다.

### 팩토리 🔗
때로는 객체가 생성되는 시점을 애플리케이션이 결정할 필요도 생긴다. 예를 들어, 주문처리 시스템에서 애플리케이션은 LineItem 인스턴스를 생성해 Order에 추가한다. 이때는 ABSTRACT FACTORY 패던을 사용한다.   
그러면 LineItem을 생성하는 시점은 애플리케이션이 결정하지만 LineItem을 생성하는 코드는 애플리케이션이 모른다. 

![그림 11-2 팩토리로 생성분리](https://user-images.githubusercontent.com/37948906/125300462-03ce8400-e365-11eb-8354-3f8bbbf3563a.png)

여기서도 마찬가지로 모든 의존성이 main에서 OrderProcessing 애플리케이션으로 향한다. 즉, OrderProcessing 애플리케이션은 LineItem이 생성되는 구체적인 방법을 모른다.    
그럼에도 OrderProcessing 애플리케이션은 LineItem 인스턴스가 생성되는 시점을 완벽하게 통제하며, 필요하다면 OrderProcessing 애플리케이션에서만 사용하는 생성자 인수도 넘길 수 있다.

### 의존성 주입 🔗
사용과 제작을 분리하는 강력한 메커니즘 하나가 **의존성 주입**<sup>Dependency Injection, DI</sup>이다.

의존성 주입 ❓
- 제어 역전<sup>Inversion of Control, IoC</sup> 기법을 의존성 관리에 적용한 메커니즘
  - 한 객체가 맡은 보조 책임을 새로운 객체에게 전적으로 떠넘긴다.
  - 새로운 객체가 넘겨받은 책임만 맡으므로 단일 객체 원칙(SRP)을 지키게 된다.
- DI 컨테이너가 필요한 객체의 인스턴스를 만든 후 생성자 인수나 설정자 메서드를 사용해 의존성을 설정한다.
- 스프링 프레임워크는 가장 널리 알려진 자바 DI 컨테이너를 제공한다.

## 확장
- '처음부터 올바르게' 시스템을 만들 수 있다는 믿음은 미신이다. 대신에 우리는 오늘 주어진 사용자 스토리에 맞춰 시스템을 구현해야 한다. 내일은 새로운 스토리에 맞춰 시스템을 조정하고 확장하면 된다.   
⟹ 반복적이고 점진적인 애자일 방식의 햄심이다. 테스트 주도 개발(TDD), 리팩터링, 깨끗한 코드는 코드 수준에서 시스템을 조정하고 확장하기 쉽게 만든다.

> 소프트웨어 시스템은 물리적인 시스템과 다르다. 관심사를 적절히 분리해 관리 한다면 소프트웨어 아키텍처는 점진적으로 발전할 수 있다.

- 비즈니스 논리가 덩치 큰 컨테이너와 밀접하게 결합되면 독자적인 단위 테스트가 어렵다.

### 횡단(cross-cutting) 관심사 🔗
- 원론적으로는 모듈화되고 캡슐화된 방식으로 영속성 방식을 구상할 수 있다. 하지만 현실적으로는 연속성 방식을 구현한 코드가 온갖 객체로 흩어진다.
- AOP에서 **관점**<sup>aspect</sup>이라는 모듈 구성 개념은 "특정 관심사를 지원하려면 시스템에서 특정 지점들이 동작하는 방식을 일관성 있게 바꿔야 한다"라고 명시한다.
  - 영속성을 예를 들면, 프로그래머는 영속적으로 저장할 객체와 속성을 선언한 후 영속성 책임을 영속성 프레임워크에 위임한다. 그러면 AOP 프레임워크는 대상 코드에 영향을 미치지 않는 상태로 동작 방식을 변경한다.

## 자바 프록시
- 단순한 상황에 적합하다. JDK에서 제공하는 동적 프록시는 인터페이스만 지원하므로 클래스 프록시를 사용하려면, CGLIB, ASM, Javassist 등과 같은 바이트 코드 처리 라이브러리가 필요하다.

> 자바 브록시 예제   
> Bank 애플리케이션에서 JDK 프록시를 사용해 영속성을 지원한다.
```java
// Bank.java
import java.util.*;
// Bank 추상화
public interface Bank {
  Collection<Account> getAccounts();
  void setAccount(Collection<Account> accounts);
}

// BankImpl.java
import java.util.*;

// 추상화를 위한 POJO("Plain Old Java Object") 구현
public class BankImpl implements Bank {
  private List<Account> accounts;

  public Collection<Account> get Accounts() {
    return accounts;
  }
  public void setAccounts(Collection<Account> accounts) {
    this.accounts = new ArrayList<Account>();
    for (Account account : accounts) {
      this.accounts.add(account);
    }
  }
}

// BankProxyHandler.java
import java.land.reflect.*;
import java.util.*;

// 프록시 API가 필요한 "InvocationHandler"
public class BankProxyHandler implements InvocationHandler {
  private Bank bank;

  public BankProxyHandler (Bank bank) {
    this.bank = bank;
  }
  // InvocationHandler에 정의된 메서드
  public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
    String methodName = method.getName();
    if (methodName.equals("getAccounts")) {
      bank.setAccounts(getAccountsFromDatabase());
      return bank.getAccounts();
    } else if (methodName.equals("setAccounts")) {
      bank.setAccounts((Collection<Account>) args[0]);
      setAccountsToDatabase(bank.getAccounts());
      return null;
    } else {
      ...
    }
  } 
  // 세부사항은 여기에 이어진다.
  protected Collection<Account> getAccountsFromDatabase() { ... }
  protected void setAccountsToDatabase(Collection<Account> accounts) { ... }
}
// 다른 곳에 위치하는 코드
Bank bank = (Bank) Proxy.newProxyInstance(
  Bank.class.getClassLoader(),
  new Class[] { Bank.class },
  new BankProxyHandler(new BankImpl())
);
```
- 단순한 예제지만 코드가 상당히 많으며 복잡하다.
- 프록시를 사용하면 깨끗한 코드를 작성하기 여럽고, 프록시는 시스템 단위로 실행 '지점'을 명시하는 메커니즘을 제공하지 않는다.

## 순수 자바 AOP 프레임워크
- 대부분의 프록시 코드는 도구로 자동화 가능하다. 스프링 AOP, JBoss AOP 등과 같은 여러 자바 프레임워크는 내부적으로 프록시를 사용한다.
- Spring은 비즈니스 논리를 POJO로 구현한다
- POJO는 순수하게 도메인에 초점을 맞추어 엔터프라이즈 프레임워크에 의존하지 않아 테스트가 개념적으로 더 쉽고 간단하다.
- BJB3은 XML 설정 파일과 자바 5 애너테이션 기능을 사용해 횡단 관심사를 선언적으로 지원하는 스프링 모델을 따른다.

> EJB3로 Bank 객체를 다시 작성한 코드
```java
import javax.persistence.*;
import java.util.ArrayList;
import java.util.Collection;

@Entity
@Table(name = "BANKS")
public class Bank implements java.io.Serializable {
  @Id @GeneratedValue(strategy=GenerationType.AUTO)
  private int id;

  @Embeddable
  public class Address {
    protected String streetAddr1;
    protected String streetAddr2;
    protected String city;
    protected String state;
    protected String zipCode;
    } 

  @Embedded 
  private Address address; 

  @OneToMany(cascade = CascadeType.ALL, fetch = FetchType.EAGER, mappedBy="bank")
  private Collection<Account> accounts = new ArrayList<Account>();
  
  public int getId() { 
    return id;
  }
  
  public void setId(int id){
    this.id = id;
  }
  
  public void addAccount(Account account) {
    account.setBank(this);
    accounts.add(account);
  }
  
  public Collection<Account> getAccounts() {
    return accounts;
  }
  
  public void setAccounts(Collection<Account> accounts) {
    this.accounts = accounts;
  }
}
```
- 애너테이션에 들어있는 영속성 정보는 일부든 전부든, 필요하다면, XML 배치 기술자로 옮겨도 괜찮다. 그러면 순수한 POJO만 남는다.

## AspectJ 관점
- 관심사를 관점으로 분리하는 가장 강력한 도구
- 언어 차원에서 관점을 모듈화 구성으로 지원하는 자바 언어 확장이다. 
- 관점을 분리하는 강력하고 풍부한 도구 집합을 제공하긴 하지만, 새 도구를 사용하고 새 언어 문법과 사용법을 익혀야 한다는 단점이 있다.
- 최근에 나온 AspectJ '애너테이션 폼'은 새로운 도구와 새로운 언어라는 부담을 어느 정도 완화한다.

📍 '애너테이션 폼'
- 순수한 자바 코드에서 자바 5 애너테이션을 사용해 관점을 정의
- 스프링 프레임워크는 AspectJ에 미숙한 팀들이 애너테이션 기반 관점을 쉽게 사용하도록 다양한 기능을 제공

## 테스트 주도 시스템 아키텍처 구축
- 애플리케이션 도메인 논리를 POJO로 작성할 수 있다면, 즉 코드 수준에서 아키텍처 관심사를 분리할 수 있따면, 진정한 **테스트 주도** 아키텍처 구축이 가능하다.
- '아주 단순하면서도' 멋지게 분리된 아키텍처로 소프트웨어 프로젝트를 진행해 결과물을 재빨리 출시한 후, 기반 구조를 추가하며 조금씩 확장해 나가도 괜찮다.
- 프로젝트를 시작할 때는 일반적인 범위, 목표, 일정은 물론이고 결과로 내놓을 시스템의 일반적인 구조도 생각해야 한다. 변화는 환경에 대처에 진로를 변경할 능력도 반드시 유지해야 한다.

> 최선의 시스템 구조는 각기 POJO객체로 구현되는 모듈화된 관심사 영역으로 구성된다. 이렇게 서로 다른 영역은 해당 영역 코드에 최소한의 영향을 미치는 관점이나 유사한 도구를 사용해 통합한다. 이런 구조 역시 코드와 마찬가지로 테스트 주도 기법을 적용할 수 있다.

## 의사 결정을 최적화하라
- 모듈을 나누고 관심사를 분리하면 지엽적인 관리와 결정이 가능해진다.
- 아주 큰 시스템에선 한 사람이 모든 결정이 내리기 어렵기 때문에 가장 적합한 사람에게 책임을 맡기면 가장 좋다.
- 최대한 정보를 모아 최선의 결정을 내리기 위해 가능한 마지막 순간까지 결정을 미루는 것은 때때로 최선일 수도 있다.

> 관심사를 모듈로 분리한 POJO 시스템은 기민함을 제공한다. 이런 기민함 덕택에 최신 정보에 기반해 최선의 시점에 최적의 결정을 내리기가 쉬워진다. 또한 결정의 복잡성도 줄어든다.

## 명백한 가치가 있을 때 표준을 현명하게 사용하라
> 표준을 사용하면 아이디어와 컴포넌트를 재사용하기 쉽고, 적절한 경험을 가진 사람을 구하기 쉬우며, 좋은 아이디어를 캡슐화하가 쉽고, 컴포넌트를 엮기 쉽다. 하지만 때로는 표준을 만드는 시간이 너무 오래 걸려 업계가 기다리지 못한다. 어떤 표준은 원래 표준을 제정한 목적을 잊어버리기도 한다.

## 시스템은 도메인 특화 언어가 필요하다
- DSL은 간단한 스크립트 언어나 표준 언어로 구현한 API를 가르킨다. DSL로 짠 코드는 도메인 전문가가 작성한 구조적인 산문처럼 읽힌다.
- 애자일 기법이 팀과 프로젝트 이해관계자 사이에 의사소통 간극을 줄여 주듯이 좋은 DSL은 도메인 개념과 그 개념을 구현한 코드 사이에 존재하는 '의사소통 간극'을 줄여준다.
- 효과적으로 사용한다면 DSL은 추상화 수준을 코드 관용구나 디자인 패턴 이상으로 끌어올린다. 그래서 개발자가 적절한 추상화 수준에서 코드 의도를 표현할 수 있다.

> 도메인 특화 언어(Domain-Specific language, DSL)를 사용하면 고차원 정책에서 저차원 세부사항에 이르기까지 모든 추상화 수준과 모든 도메인을 POJO로 표현할 수 있다.

## 결론
- 깨끗하지 못한 아키텍처는 도메인 논리를 흐리며 기민성을 떨어뜨린다. 도메인 논리가 흐려지면 버그가 숨어들기 쉬워지고, 스토리를 구현하기 어려지워지는 탓에 제품 품질이 떨어진다. 기민성이 떨어지면 생산성이 낮아져 TDD가 제공하는 장점이 사라진다.
- 모든 추상화 단계에서 의도는 명확히 표현해야 한다.
- 시스템을 설계하든 개별 모듈을 설계하든, 실제로 **돌아가는 가장 단순한 수단**을 사용해야 한다는 사실을 명심하자.