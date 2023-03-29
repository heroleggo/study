# Day03

## 1.5 스프링의 IoC

스프링은 직접 제어권을 가지고 객체 생성, 관계 부여 등의 역할을 수행한다.
이 과정에서 스프링이 제어권을 가지고 있는 객체를 Bean(빈)이라고 한다.
이러한 빈을 생성하고, 관계설정을 담당하는 IoC 컨테이너를 **애플리케이션 컨텍스트**라고 한다.

- 사용방법
Java의 어노테이션을 활용해 클래스를 생성한 뒤 해당 클래스가 컨텍스트에 해당한다고 표기하면 스프링은 이를 인식하여 해당 컨텍스트를 사용한다.
설정을 담당할 경우 `Configuration` 어노테이션을 부여하고, 객체가 생성되는 역할을 하는 곳에 `Bean` 어노테이션을 부여한다.
설정을 담당하기 때문에, `AnnotationConfigApplicationContext` 클래스를 사용한다.
추가적으로, `ApplicationContext`는 `BeanFactory`의 하위 인터페이스이다.
팩토리의 역할을 하는 `BeanFactory`에 부가적인 기능을 포함하고 있어서 `BeanFactory`보다 `ApplicationContext`를 많이 활용한다.
- Object Factory vs Application Context
Object Factory는 목적에 맞는 객체를 생성하고 다른 객체와 관계를 맺어주는 역할을 한다.
반면, Application Context는 애플리케이션 안에서 IoC를 적용할 모든 객체를 생성하고 관리하는 역할을 한다.
직접 객체를 생성하고 관계를 맺는 코드가 존재하지 않으며, 해당 정보를 별도의 설정정보를 통해 얻는다. (`Configuration` 어노테이션 사용을 통함)
- ApplicationContext의 동작 방식
1. 사용자는 사용하고자 하는 객체를 컨텍스트에 요청
2. 컨텍스트는 설정정보가 포함되어 있는 팩토리에 생성 요청
3. 팩토리는 객체를 생성하고 컨텍스트에 등록
해당 방식의 장점
1. 클라이언트에서 구체적인 팩토리 클래스를 알 필요가 없다.
  - 필요한 객체를 생성해주는 팩토리를 컨텍스트에서 알고 있기 때문에 결합도를 낮출 수 있다.
  - 컨텍스트에 객체를 요청하고 객체를 받아오기 때문에 팩토리 클래스를 직접 활용하지 않아도 된다.
2. 컨텍스트에서 제공하는 부가기능을 활용할 수 있다.
  - 객체 생성, 관계 설정 기능에 더해 생성시점, 전략 등을 다르게 가져갈 수 있다.
  - 자동생성, 후처리 등의 객체를 효과적으로 활용할 수 있는 다양한 기능을 제공한다.
3. 빈을 검색하는 다양한 방법을 지원한다.
  - `getBean` 메소드의 다양한 검색 조건을 활용하여 원하는 객체를 가져올 수 있다.

용어 정리
- 빈 : 스프링이 IoC 방식으로 관리하는 객체
- 빈 팩토리 : 스프링의 IoC를 담당하는 컨테이너이자 빈을 관리하는 기능
- 어플리케이션 컨텍스트 : 빈 팩토리를 확장한 IoC 컨테이너, 부가적인 기능을 제공
- 설정정보/설정 메타정보 : 어플리케이션 컨텍스트, 빈 팩토리의 설정 정보를 담고 있음
- 컨테이너 : IoC 방식으로 빈을 관리한다는 의미에서 컨테이너라는 단어를 사용

## 1.6 싱글톤 레지스트리와 오브젝트 스코프

싱글톤 레지스트리
팩토리를 통해 새로운 객체를 생성할 경우, new 키워드가 사용되기 때문에 호출마다 동일한 기능을 하는 객체가 여러 개 생성된다.
스프링에서 `getBean` 메소드를 활용해 어플리케이션 컨텍스트에서 객체를 요청하고 얻었을 경우, 동일한 객체를 사용한다.
어플리케이션 컨텍스트는 **싱글톤**을 저장하고 관리한다.
서버의 자원은 한정적인 반면, 요청은 무한정하다. 요청이 많을 때 이를 해결하지 못할 경우 서버에 문제가 생길 수 있는데,
객체를 하나 생성해서 재활용하는 형태로 설계된다면 서버의 자원을 아끼면서 안정적으로 요청에 대응할 수 있다.

싱글톤 패턴, 항상 좋은걸까?
싱글톤 패턴 자체는 하나의 인스턴스만을 활용하는 것이기 때문에 지양해야 할 안티패턴이기도 함.
(Java에서 싱글톤은 private 생성자, 별도의 메소드와 static 변수를 필요로 함)
싱글톤 객체를 상속받는 다른 객체를 만들 수 없고, 테스트에 어려움이 있으며 여러 개의 JVM에 분산되어 있다면 결국 싱글톤이 아니게 된다.
static 메소드를 활용하기 때문에 전역 상태로 사용될 수 있다.

싱글톤은 stateless하다.
멀티스레드에서 하나의 객체를 활용하기 때문에 내부에 상태정보를 가지고 있을 경우 스레드별로 달라야 하는 부분에 어긋난다.
상태를 주입하기 위해 메소드 파라미터, 메소드 안의 로컬 변수를 활용할 수 있다. (메소드 실행 시 생성되기 때문)

Spring의 scope
기본적으로 객체는 Singleton 스코프로 작동한다. 이외에 prototype, request, session 등의 스코프가 사용된다.

## 1.7 의존관계 주입(DI)

앞의 설명에서 지속적으로 활용된 IoC(Inversion of Control)이라는 개념은 매우 크다.
그래서 스프링에서는 의존관계 주입 (Dependency Injection, DI) 라는 명칭을 활용할 수 있다.
- 의존관계
의존관계는 방향성을 가지고 있다. A가 B를 의존한다고 할 때, B의 변화에 A가 영향받는다.
인터페이스 모델을 통해 B를 변화에 자유롭게 한다면, 각 클래스 간 결합도를 낮출 수 있다.

- 의존관계 주입
의존관계 주입은 생성자의 파라미터를 활용하여 해결할 수 있다. 원하는 값을 인터페이스에 할당하면 된다.
스프링에서는 `getBean` 메소드를 통해 주입하고자 하는 의존관계를 쉽게 설정할 수 있다.

- 스프링에서의 의존관계 주입의 장점
주입하는 기능 간 전환이 유리하다. DB 커넥션을 변경하고자 할 때, 변경하는 부분을 주입한다면
주입받는 객체는 해당 부분에 대해 신경을 쓰지 않아도 된다.
부가기능을 추가하는 데 유리하다. 의존관계를 주입하는 메소드 안에서
부가적인 기능을 수행할 수 있어서 DB 커넥션 카운트 등의 부가 기능을 작성할 수 있다.