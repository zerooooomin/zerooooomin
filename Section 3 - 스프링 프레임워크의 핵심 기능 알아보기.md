
# 3-1 스프링 프레임워크의 핵심 기능
### 1. 의존성 주입
의존성 주입 (**DI**, Dependency Injection)은 '의존하는 부분을 외부에서 주입하는 것'을 말한다.

### 2. 관점 지향 프로그래밍
관점 지향 프로그래밍 (**AOS**, Aspect Oriented Programming) 에서는 프로그램이 두 가지 요소로 구성된다고 생각한다.
- **중심적 관심사** (Primary Concern) : 실현해야 할 기능을 나타내는 프로그램
- **횡단적 관심사** (Crossutting-Concern) : 본질적인 기능은 아니지만 품질이나 유지보수 등의 관점에서 반드시 필요한 기능을 나타내는 프로그램

간단히 설명하면, 공통 처리 등의 '횡단적 관심사'를 추출하고 프로그맹의 여러 곳에서 호출할 수 있게 설정함으로써 개발자는 실현해야할 기능인 '중심적 관심사'에만 집중해서 작성하면 되는 구조이다.
( 비유하자면 **회식**. '먹고 마시는 것' = '중심적 관심사' / '가게선정, 정산 ... ' = '횡단적 관심사' )

# 3-2 DI 컨테이너 알아보기

### 의존성
의존성이란 **파라미터나 리턴값 또는 지역변수 등으로 다른 객체를 참조하는 것**을 의미한다.
- 클래스 의존 (구현 의존)
- 인터페이스 의존

#### 클래스 의존
---
B 클래스
```
methodX() {  }
```

A 클래스
```
xxx() {
	B b = new B();
	b.methodX();
}
```


>설계가 변경되어 C 클래스로 변경하여 methodY 메서드를 호출하도록 변경해야하는 상황

C 클래스
```
methodY() {  }
```

A 클래스
```
xxx() {
	C c = new C();
	c.methodY();
}
```
A 클래스의 세 군데를 수정. 수정할게 많아지면 실수할 확률 증가

#### 인터페이스 의존
---
A 클래스
```
xxx() {
	I i = new B();
	i.methodX();
}
```

I 인터페이스
```
methodX();
```

B 클래스
```
methodX() {  }
```

>C 클래스를 호출해 methodX 메서드를 호출하도록 변경

A 클래스
```
xxx() {
	I i = new C();
	i.methodX();
}
```

I 인터페이스
```
methodX();
```

C 클래스
```
methodX() {  }
```

A 클래스에서 한 군데 수정.

인터페이스 의존 이점
- 인터페이스는 참조를 받는 유형으로 사용할 수 있으므로 변수의 이름을 변경하지 않아도 된다.
- 인터페이스가 선언된 메서드를 이용하면 클래스가 바뀌어도 메서드명을 변경하지 않아도 된다.

이러한 이점들 때문에 클래스 의존보다 인터페이스 의존을 사용한다.


### DI 컨테이너
의존성 주입은 '의존하는 부분을 외부에서 주입하는 것'이다.
-> 의존하는 부분이란 '**사용한 객체**'클래스에 '**사용되는 객체**' 클래스가 작성된 상태이다.
-> 외부로부터 주입이란 '**사용하는 객체**' 클래스의 밖에서 '**사용되는 객체**' [[인스턴스]]를 주입하는 것이다.

### DI 컨테이너의 다섯 가지 규칙
1. 인터페이스를 이용하여 의존성을 만든다.
2. 인스턴스를 명시적으로 생성하지 않는다.
3. 어노테이션을 클래스에 부여한다.
4. 스프링 프레임워크에서 인스턴스를 생성한다.
5. 인스턴스를이용하고 싶은 곳에 어노테이션을 부여한다.

**규칙 1**
의존하는 부분에 ==**인터페이스를 이용**==한다는 것.

**규칙 2**
인스턴스 생성에 ==**new 키워드**==를 사용하지 않는다는 것.

**규칙 3, 규칙 4**
인스턴스를 생성하려는 클래스에 ==**인스턴스 생성 어노테이션**==을 부여한다.

스프링 프레임워크는 시작할 때 컴포넌트 스캔(Component Scan)을 실행한다.
스캔 이후 스프링 프레임워크는 ==**인스턴스 생성 어노테이션**==이 부여된 클래스의 인스턴스를 생성한다.

| 어노테이션         | 개요                                      |
| ------------- | --------------------------------------- |
| `@Controller` | 인스턴스 생성 지시. 스프링 MVC를 이용할 때 컨트롤러에 부여     |
| `@Service`    | 인스턴스 생성 지시. 트랜잭션 경계가 되는 도메인(서비스) 기능에 부여 |
| `@Repository` | 인스턴스 생성 지시. 데이터베이스 액세스(리포지토리) 기능에 부여    |
| `@Component`  | 위 용도 이외의 클래스에 부여                        |


**규칙 5**
==**스프링 프레임워크에 의해 생성된 인스턴스**==를 이용하는 클래스에 참조를 받는 필드를 선언하고 필드에 ==**`@Autowired` 어노테이션**==을 부여한다.



### ==3-2 요약==
- 스프링 프레임워크는 임의로 구현한 클래스를 **인스턴스화하는 기능**을 제공한다. (DI 컨테이너)
- 스프링 프레임워크를 사용하는 애플리케이션은 **인스턴스를 명시적으로 생성하지 않는다.** (new 키워드 사용 X)
- **정해진 어노테이션** (@Component 어노테이션)을 클래스에 부여하는 것으로 **스프링 프레임워크가 인스턴스를 생성**한다.
- 생성된 인스턴스를 사용하고 싶은 부분에서 필드를 준비하고 주석(@Autowired 어노테이션)을 부여하면 스프링 프레임워크가 인스턴스가 필요한 것으로 판단하고 인스턴스를 주입한다.
- 인터페이스를 이용해서 의존성을 만들고 DI를 사용하여 '사용되는 객체'클래스를 변경하는 경우 '사용하는 객체'클래스의 수정 없이 변경할 수 있다.


# 3-3 어노테이션 역할 알아보기
어노테이션을 간단히 세 가지 항목으로 설명하면,
1. 어노테이션(annotation)은 주석의 의미하는 영어 표현이다.
2. '@xxx' 와 같은 형태로 작성한다.
3. 외부 소프트웨어에 필요한 처리 내용을 전달한다.

### 레이어별로 사용할 인스턴스 생성 어노테이션

도메인 주도 설계의 레이어

| 레이어                                | 개요                                                                  |
| ---------------------------------- | ------------------------------------------------------------------- |
| 애플리케이션 레이어 (Application Layer)     | 애플리케이션 레이어는 클라이언트와의 데이터 입출력을 제어하는 레이어이다.                            |
| 도메인 레이어 (Domain Layer)             | 도메인 레이어는 애플리케이션의 중심이 되는 레이어로서 업무 처리를 수행하는 레이어이다.                    |
| 이프라스트럭처 레이어 (Infrastructure Layer) | 인프라스트럭처 레이어는 데이터베이스에 대한 데이터 영속석(Persistence Context) 등을 담당하는 레이어이다. |

---

레이어별 인스턴스 생성 어노테이션 설명(주요 처리)

| 어노테이션       | 개요                         |
| ----------- | -------------------------- |
| @Controller | 애플리케이션 레이어의 컨트롤러에 부여       |
| @Service    | 도메인 레이어의 업무 처리에 부여         |
| @Repository | 인프라 레이어의 데이터베이스 액세스 처리에 부여 |


클라이언트 -> 애플리케이션 레이어 -> 도메인 레이어 -> 인프라 레이어 -> 데이터베이스




# AOP(관점 지향 프로그래밍)의 기초 지식


### AOP의 고유 용어
| 용어                    | 내용                                                                                            |
| --------------------- | --------------------------------------------------------------------------------------------- |
| 어드바이스(Advice)         | 횡단적 관심사의 구현(메서드). 로그 출력 및 [[트랜잭션]] 제어 등이다.                                                    |
| 애스펙트(Aspect)          | 어드바이스를 정리한것(클래스)이다.                                                                           |
| 조인포인트(JoinPoint)      | 어드바이스를 중심적인 관심사에 적용하는 타이밍. 메서드(생성자) 실행 전, 메서드(생성자) 실행 후 등 실행되는 타이밍이다.                         |
| 포인트컷(Pointcut)        | 어드바이스를 삽입할 수 있는 위치. 예를 들어, 메서드 이름이 get으로 시작할 때만 처리하는 조건을 정의할 수 있다.                            |
| [[인터셉터]](Interceptor) | 처리의 제어를 [[인터셉트]]하기 위한 구조 또는 프로그램이다. 스프링 프레임워크에서는 인터셉트라는 매커니즘으로 어드바이스를 중심 관심사에 추가한 것처럼 보이게 한다. |
| 타깃(Target)            | 어드바이스가 도입되는 대상을 말한다.                                                                          |




### 어드바이스의 다섯 가지 종류
| 어드바이스                  | 내용                                                        | 어노테이션           |
| ---------------------- | --------------------------------------------------------- | --------------- |
| Before Advice          | 중심적 관심사가 실행되기 '이전'에 횡단적 관심사를 실행                           | @Before         |
| After Returning Advice | 중심적 관심사가 '정상적으로 종료된 후'에 횡단적 관심사를 실행                       | @AfterReturning |
| After Throwing Advice  | 중심적 관심사로부터 '예외가 던져진 후'로 횡단적 관심사를 실행                       | @AfterThrowing  |
| After Advice           | 중심적 관심사의 '실행 후'에 횡단적 관심사를 실행(정상 종료나 예외 종료 등의 결과와 상관없이 실행) | @After          |
| Around Advice          | 중앙적 관심사 호출 전후에 횡단적 관심사를 실행                                | @Around         |


### 와일드카드
| 와일드카드       | 내용                                                                                      |
| ----------- | --------------------------------------------------------------------------------------- |
| \* (애스터리스크) | 임의의 문자열을 나타내고, 패키지를 나타낼 때는 임의의 패키지 한 계층을 나타낸다. 메서드의 인수에서는 한 개의 인수를 나타내 반환 값으로 이동할 수 있다. |
| .. (점 두 개)  | 패키지를 나타내는 경우 0개 이상의 패키지를 나타낸다. 메서드의 인수를 표현하는 경우에는 0개 이상의 임의의 인수를 나타낸다.                  |
| + (플러스)     | 클래스명 뒤에 기술해 클래스와 그 서브클래스 및 구현 클래스 모두를 나타낸다.                                             |
화일드 카드를 이용해 적용 범위를 유연하게 지정할 수 있다.

e.g.
`execution(* com.example.service.DemoService.*(..))`
내용 : DemoService 클래스의 메서드에 어드바이스를 적용한다.

`excution(String com.example.service.DemoService.*(..))`
내용 : DemoService 클래스의 반환값이 String 타입인 메서드에 어드바이스를 적용한다.




