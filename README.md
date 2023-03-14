# Spring-DB02

## 2023-03-14
### SQL Mapper vs ORM 기능
- SQL Mapper는 Spring-DB01 에서 잠깐 봤듯이, 개발자가 SQL만 입력을 하고 그 외에 중복되던 기능들(conn, pstmt 등등)은 제거해주고 편리한 기능을 제공해준다.
- ORM은 설명해야 될 부분은 많지만 간단하게 설명하자면 JPA를 사용하면 기본적인 SQL은 JPA가 대신 처리해주는데 JPA는 그저 자바 진영의 ORM(Object Relational Mapping)의 표준이고, JPA를 구현하는 구현체인 hibernate를 주로 사용한다.   
단순히 Spring Data JPA나, queryDSL은 이러한 JPA 사용을 좀 더 편리하게 사용할 수 있게 해주는 도구이다.

### DTO에 대해서
DTO가 데이터 전달 객체 인 건 알고 있었지만, 자세히 알고 사용하진 않았다. 고민해보지 않은 영역인 DTO 에서 기능을 사용해도 되냐에 대한 얘기가 나왔는데, 꼭 필요하다면 어느 정도의 기능이 있어도 된다고 한다. 예를 들어서 들어온 데이터에 대해서 포맷팅 해줘야하는 경우에 대해서 말이다. 또한 해당 객체의 주 목적이 데이터를 전달하기 위함이 목적이라면 해당 객체는 DTO가 될 수 있다.   
여기서 데이터라 하면 꼭 주고 받는 데이터가 아닌, 우리가 동적 검색을 하는 경우에도 데이터를 전달하는 것이기 때문에 DTO라고 할 수 있는 것이다. 추가적으로, DTO를 어느 패키지에 두어야 하는지에 대한 얘기도 나왔다. 프로젝트를 진행하면서 많이 고민했었던 부분인데, 어디에서 호출이 마지막으로 이루어지는지에 따라서 다르게 두면 된다고 한다. 예를 들어서, Controller에서만 쓰고 버린다면 Controller에, Service에서 어떠한 DTO의 변환을 위해서 사용해야 한다면 Service에 동적 검색 등을 위해서 마지막으로 Repository를 참조해야한다면 Repository에 두는 것이 맞다고 한다. 물론 이런 기준을 두지 않고 DTO 란 패키지를 만들어서 사용해도 된다고 하는데 아마 접근 제어자의 범위에서 해당 패키지에서만 사용하는 것을 목적으로 말씀을 해주신 것 같다. 다음 프로젝트 리팩토링 때 해당 사항을 적용해보도록 해야겠다. 

### EventListener와 PostConstruct
기존에 프로젝트에서 사용했던 애노테이션들이다. PostConstruct보다 EventListener를 사용하는 것이 테스트케이스 호출 시 더 좋다는 말을 검색해서 찾아보고 사용했었는데 그것에 대한 이유를 알 수 있었다.
EventListener의 프로퍼티로 ApplicationReadyEvent를 사용하면 해당 애노테이션은 스프링의 애노테이션이라서 스프링 컨테이너가 초기화가 된 이후 즉, Bean 들이 전부 등록 된 이후에 실행되는 애노테이션이다.   
그러나 PostConstruct는 자바 표준의 애노테이션인데 PostConstruct의 수행 시점은 객체가 생성된 이후에 발생한다. 이 말은 즉, 스프링 컨테이너가 전부 초기화 되기 전에 해당 객체가 생성되면서 실행된다는 것인데, 이렇게 되면 @Transactional 같은 AOP가 적용되지 않은 상태로 실행이 될 수 있는 문제가 있다는 것이다. AOP 또한 스프링이 관리를 하고 등록을 해줘야하는 부분인데 그런 영역이 초기화 되기 전에 실행될 수 있다는 문제가 있다는 것이다. 둘의 차이점에 대해서 자세히 알고 사용해야 할 것 같다.
