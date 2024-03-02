# /23-12-21
spring.io -> projects -> spring boot -> learn -> reference.doc
thymeleaf.org

## spring boot는 Tomcat을 내장하고 있고
웹 브라우저에서 내장 톰켓 서버에 요청을 하고 스프링컨테이너에 있는 컨트롤러에서 해당 요청을 받아 
해당 컨트롤러의 로직을 진행한 후 컨트롤러가 리턴값으로 문자를 반환하면 뷰 리졸버(vieResolcer)가 화면을 찾아서 처리한다.

- 스트링 부트 템플릿엔진 기본 viewName 매핑
- resources:templates/ +(ViewName) + .html

### 참고. 
spring-boot-devtools 라이브러리를 추가하면, html 파일을 컴파일만 해주면 서버 재시작 없이 View파일 변경이 가능하다.

# /23-12-22
./gradlew build 오류 발생 
해결 하기 위한 방법은?

# /23-12-23 
./gradlew build에서 오류가 난 원인은 PC에서 사용중인 JAVA_HOME의 jdk버전은 1.8이었지만 구성한 project의 java version은 17이었기 때문에
JAVA_HOME 경로를 해당 17버전의 jdk로 변경해 주었다.

- 스프링 웹개발 기초
1. 정적 컨텐츠
2. MVC와 템플릿 엔진
3. API

1. 정적 컨텐츠
EX) /static 폴더 안에 존재하는 html
정적 컨텐츠는 웹브라우저 요청 (/Hello-static.html) 을 내장 톰켓서버에서 받으면 스프링부트에서는 스프링 컨테이너에서 요청에 관한 컨트롤러가 있는지 확인한다.
허나 없으면, 내부 리소시스 안에있는 해당 요청파일이 존재하는지 찾아서 반환해준다.


# /23-12-24
MVC와 템플릿 엔진
MVC:Model, VIew, Controller
View : 화면을 그리는데 집중
Controller, Model : 비즈니스 로직, 내부적인 처리하는데 집중

thymeleaf 사용 시 ${}는 model안에 있는 값을 치환해서 나타낸다

웹브라우저에서 localhost:8080/hello-mvc 요청을 보내면 내장 톰켓서버에서 받고 
톰켓서버는 이를 스프링한테 보낸다 스프링은 helloController의 메서드에 맵핑이 돼있네 해서 해당 메서드를 호출하고
해당 메서드에서 리턴을 hello-template라고 하고 모델안에 name이라는 이름과 값을 넣어 스프링한테 주면 
스프링의 viewResolver(화면과 관련된 해결자:뷰를 찾아주고 템플릿을 연결)가 동작해 return의
스트링네임과 똑같은걸 찾아 thymeleaf 템플릿 엔진에서 처리해달라고 넘긴다 
템플릿 엔진이 렌더링을 해서 변환을 한 HTML을 웹브라우저에 반환을 해서 화면을 보여주게 된다.

API
Controller에 @ResponseBody를 쓰게 되면, 응답 바디부에 이 내용을 직접 넣어주겠다는 의미
이전 템플릿 엔진과의 차이는 View가 없이 문자가 그대로 내려간다.
템플릿엔진은 템플릿에 있는 뷰를 조작하지만 위 방식은 리턴한 데이터를 그대로 화면에 나타낸다

@ResponseBody를 사용하고 return을 객체를 해주게 되면 JSON형태의 데이터자체를 화면에 반환한다.

웹브라우저에서 /hello-api 요청을 보내면 내장 톰켓에서 해당 요청을 스프링에 알리고 
스프링컨테이너에서 해당 컨트롤러를 찾아서 @ResponseBody가 있으면 이전에 ViewResolver에 던졌던 것과는 다르게 
HttpMessageConverter가 동작을 하게 된다.
단순 문자면 StringConverter, 객체면 JsonConverter가 동작해 반환한다.

@ResponseBody를 사용하면
- HTTP의 BODY에 문자 내용을 직접 반환
- viewResolver 대신에 HttpMessageConberter가 동작
- 기본 문자처리 : StringHttpMessageConverter
- 기본 객체처리 : MappingJackson2HttpMessageConverter
- Byte 처리 등등 기타 여러 HttpMessageConverter가 기본으로 등록되어 있음

Json 변환라이브러리는 Gson(구글), Jackson 2개가 있다

-참고 : 클라이언트의 HTTP Accept 헤더와 서버의 컨트롤러 반환 타입 정보 둘의 조합해서 HttpMessageConverter가 선택된다. 

# /23-12-25
회원관리 예제- 백엔드 개발 
1. 비즈니스 요구사항 정리
2. 회원 도메인과 리포지토리 만들기
3. 회원 리포지토리 테스트 케이스 작성
4. 회원 서비스 개발
5. 회원 서비스 테스트

1. 비즈니스 요구사항 정리
- 데이터 : 회원 ID, 이름
- 기능 : 회원 등록, 조회
- 아직 데이터 저장소가 선정되지 않음

일반적인 웹 애플리케이션 계층 구조
- 컨트롤러 : 웹 MVC의 컨트롤러 역할
- 서비스 : 핵심 비즈니스 로직 구현
- 리포지토리 : 데이터베이스에 접근, 도메인 객체를 DB에 저장하고 관리
- 도메인 : 비즈니스 도메인 객체, 예) 회원, 주문, 쿠폰 등등 주로 데이터베이스에 저장하고 관리됨

클래스 의존관계
MemberService -> MemberRepository(Interface) <--- Memory MemberRepository
- 아직 데이터 저장소가 선정되지 않아서, 우선 인터페이스로 구현 클래스를 변경할 수 있도록 설계
- 데이터 저장소는 RDB, NoSQL 등등 다양한 저장소를 고민중인 상황으로 가정
- 개발을 진행하기 위해서 초기 개발 단계에서는 구현체로 가벼운 메모리 기반의 데이터 저장소 사용

회원 도메인과 리포지토리 만들기 

# /24-01-24
순서를 잘못 시작한것 같다.
이제부터는 JPA실전

# JPA와 DB설정, 동작 확인

### 참고 
스프링 부트를 통해 복잡한 설정이 다 자동화 되었다. 
스프링 부트를 통한 추가 설정은 스프링 부트 매뉴얼을 참고하자.

## 쿼리 파라미터 로그 남기기
- org.hibernate.type : SQL 실행 파라미터를 로그로 남긴다.
- 외부 라이브러리 사용 https://github.com/gavlyukovskiy/spring-boot-data-source-decorator?tab=readme-ov-file

### 참고
쿼리 파라미터를 로그로 남기는 외부 라이브러리는 시스템 자원을 사용하므로, 개발 단계에서는 편하게 사용해도 된다. 
하지만 운영시스템에 적용하려면 꼭 성능테스트를 하고 사용하는 것이 좋다.


# /24-01-26
## 도메인 분석 설계
- 요구사항 분석
- 도메인 모델과 테이블 설계
- 엔티티 클래스 계발
- 엔티티 설계 시 주의점

### 요구사항 분석
회원 가입, 회원 목록
상품 등록, 상품 입력
상품 주문, 주문 내역

기능목록
- 회원 기능
  - 회원 등록
  - 회원 조회
- 상품 기능
  - 상품 등록
  - 상품 수정
  - 상품 조회
- 주문 기능
  - 상품 주문
  - 주문 내역 조회
  - 주문 취소
- 기타 요구사항
  - 상품은 제고 관리가 필요하다
  - 상품의 존류는 도서, 음반, 영화가 있다.
  - 상품을 카테고리로 구분할 수 있다.
  - 상품 주문 시 배송 정보를 입력할 수 있다.

# /24-01-27
## 도메인 모델과 테이블 설계
### 회원, 주문, 상품의 관계
회원은 여러 상품을 주문할 수 있다. 그리고 한 번 주문할 때 여러 상품을 선택할 수 있으므로 주문과 상품은 다대다 관계이다.
하지만 이런 다대다 관계는 관계형 데이터베이스는 물론이고 엔티티에서도 거의 사용하지 않는다.

### 회원 엔티티 분석
- 회원(Member) : 이름과 임베디드 타입인 주소(address), 그리고 주문(orders) 리스트를 가진다.
- 주문(Order) : 한 번 주문 시 여러 상품을 주문할 수 있으므로 주문과 주문상품(OrderItem)은 일대다 관계이다.
주문은 상품을 주문한 회원과 배송 정보, 주문 날짜, 주문상태(status)를 가지고 있다. 주문 상태는 열거형을 사용했는데 주문(ORDER), 취소(CANCEL)을 표현할 수 있다.
- 주문상품(OrderItem) : 주문한 상품 정보와 주문 금액(orderPrice), 주문 수량(count)정보를 가지고 있다.
- 상품(Item) : 이름, 가격, 재고수량(stockQuantity)을 가지고 있다. 상품을 주문하면 재고수량이 줄어든다. 상품의 종류로는 도서, 음반, 영화가 있는데 각각은 사용하는 속성이 조금씩 다르다.
- 배송(Delivery) : 주문 시 하나의 배송 정보를 생성한다. 주문과 배송은 일대일 관계이다.
- 카테고리(Category) : 상품과 다대다 관계를 맺는다. parent, child로 부모, 자식 카테고리를 연결한다.
- 주소(Address) : 값 타입(임베디드 타입)이다. 회원과 배송(Delivery)에서 사용한다.

### 참고
회원이 주문을 하기 때문에 회원이 주문리스트를 가지는 것은 얼핏 보면 잘 설계한것 같지만, 객체 세상은 실제 세계와는 다르다. 
실무에서는 회원이 주문을 참조하지 않고, 주문이 회원을 찾모하는 것으로 충분하다. 여기서는 일대다, 다대일 양방향 연관관계를 설명하기 위해서 추가했다.

### 회원 테이블 분석
- MEMBER : 회원 엔티티의 Address 임베디드 타입 정보가 회원 테이블에 그대로 들어갔다. 이것은 DELIVERY 테이블도 마찬가지다.
- ITEM : 앨범, 도서, 영화 타입을 통합해서 하나의 테이블로 만들었다. DTYPE 컬럼으로 타입을 구분한다.

### 참고
테이블명이 ORDER가 아니라 ORDERS인 것은 데이터베이스가 order by 때문에 예약어로 잡고 있는 경우가 많다. 그래서 관례상 ORDERS를 많이 사용한다.

### 참고
데이터베이스 테이블명, 컬럼명에 대한 관례는 회사마다 다르다. 보통은 대문자 + _(언더스코어)나 소문자 + _(언더스코어) 방식 중에 하나를 지정해서 일관성 있게 사용한다.
강의에서 설명할 때는 객체와 차이를 나타내기 위해 데이터베이스 테이블, 컬럼명은 대문자를 사용했지만 실제 코드에서는 DB에 소문자 + _(언더스코어) 스타일을 사용하겠다

### 연관관계 매핑 분석
- 회원과 주문 : 일대다, 다대일의 양방향 관계다. 따라서 연관관계의 주인을 정해야 하는데, 외래 키가 있는 주문을 연관관계의 주인으로 정하는 것이 좋다.
             그러므로 Order.member를 ORDERS.MEMBER_ID 외래키와 매핑한다.
- 주문상품과 주문 : 다대일 양방향 관계다. 외래 키가 주문상품에 있으므로 주문상품이 연관관계의 주인이다. 그러므로 OrderItem.order를 ORDER_ITEM.ORDER_ID 외래 키와 매핑한다.
- 주문상품과 상품 : 다대일 단방향 관계다. OrderItem.item을 ORDER_ITEM.ITEM_ID 외래 키와 매핑한다.
- 주문과 배송 : 일대일 단방향 관계다. Order.delivery를 ORDERS.DELIVERY_ID 외래 키와 매핑한다.
- 카테고리와 상품 : @ManyToMany를 사용해서 매핑한다. (실무에서 @ManyToMany는 사용하지 말자. 여기서는 다대다 관계를 예제로 보여주기 위해 추가했을 뿐이다)

### 참고
외래키가 있는 곳을 연관관계의 주인으로 정해라
연관관계의 주인은 단순히 외래키를 누가 관리하냐의 문제이지 비즈니스상 우위에 있다고 주인으로 정하면 안된다.
예를 들어서 자동차와 바퀴가 있으면, 일대다 관계에서 항상 다쪽에 외래키가 있으므로 외래키가 있는 바퀴를 연관관계의 주인으로 정하면 된다.
물론 자동차를 연관관계의 주인으로 정하는 것이 불가능 한 것은 아니지만, 자동차를 연관관계의 주인으로 정하면 자동차가 관리하지 않는 바퀴 테이블의 외래키 값이 업데이트 되므로 관리와 유지보수가 어렵고,
추가적으로 별도의 업데이트 쿼리가 발생하는 성능 문제도 있다. 

# /24-01-28
## 엔티티 클래스 개발
- 예제에서는 설명을 쉽게하기 위해 엔티티 클래스에 Getter, Setter를 모두 열고, 최대한 단순하게 설계
- 실무에서는 가급적 Getter는 열어두고, Setter는 꼭 필요한 경우에만 사용하는 것을 추천

### 참고
이론적으로 Getter, Setter 모두 제공하지 않고, 꼭 필요한 메서드를 제공하는게 가장 이상적이다.
하지만 실무에서 엔티티의 데이터는 조회할 일이 너무 많으므로, Getter의 겨우 모두 열어두는 것이 편리하다.
Getter는 아무리 호출해도 호출하는 것 만으로 어떤 일이 발생하지는 않는다. 
하지만 Setter는 문제가 다르다. 
Setter를 호출하면 데이터가 변한다.
Setter를 막아두면 가까운 미래에 엔티티가 도대체 왜 변경되는지 추적하기 점점 힘들어진다.
그래서 엔티티를 변경할 때는 Setter 대신에 변경 지점이 명확하도록 변경을 위한 비즈니스 메서드를 별도로 제공해야한다.

### 참고
값 타입은 변경 불가능하게 설계해야 한다.
@Setter를 제거하고, 생성자에서 값을 모두 초기화해서 변경 불가능한 클래스를 만들자. 
JPA 스펙상 엔티티나 임베디드 타입(@Embeddable)은 자바 기본 생성자(default constructor)를 public 또는 protected로 설정해야 한다.
public으로 두는 것 보다는 protected로 설정하는 것이 그나마 더 안전하다.
-> JPA가 이런 제약을 두는 이유는 JPA구현 라이브러리가 객체를 생성할 때 리플랙션 같은 기술을 사용할 수 있도록 지원해야 하기 때문이다.

# /24-01-29
## 엔티티 설계 시 주의점
### 엔티티에는 가급적 Setter를 사용하지 말자
Setter가 모두 열려있다면 변경 포인트가 너무 많아서, 유지보수가 어렵다.

### 모든 연관관계는 지연로딩으로 설정!
- 즉시로딩(EAGER)은 예측이 어렵고, 어떤 SQL이 실행될지 추적하기 어렵다. 특히 JPQL을 실행할 때 N+1 문제가 자주 발생한다.
- 실무에서 모든 연관관계는 지연로딩(LAZY)으로 설정해야 한다.
- 연관된 엔티티를 함께 DB에서 조회해야 하면, fetch join 또는 엔티티 그래프 기능을 사용한다.
- @XToOne(OneToOne, ManyToOne)관계는 기본이 즉시로딩이므로 직접 지연로딩으로 설정해야 한다.

### 컬렉션은 필드에서 초기화 하자
컬렉션은 필드에서 바로 초기화 하는 것이 안전하다.
- null 문제에서 안전하다.
- 하이버네이트는 엔티티를 영속화 할 때, 컬렉션을 감싸서 하이버네이트가 제공하는 내장 컬렉션으로 변경한다.
  만약 getOrders()처럼 임의의 메서드에서 컬렉션을 잘못 생성하면 하이버네이트 내부 메커니즘에 문제가 발생할 수 있다.
  따라서 필드레벨에서 생성하는 것이 가장 안전하고, 코드도 간결하다.
- 컬렉션 객체를 변경하지 않는 것이 좋다.

### 테이블, 컬럼명 생성 전략
스프링 부트에서 하이버네이트 기본 매핑 전략을 변경해서 실제 테이블 필드명은 다름
하이버네이트 기존 구현 : 엔티티의 필드명을 그대로 테이블명으로 사용
(SpringPhysicalNamingStrategy)

스프링 부트 신규설정(엔티티(필드) -> 테이블(컬럼))
1. 카멜케이스 -> 언더스코어 (memberPoint -> member_point)
2. .(점) -> _(언더스코어)
3. 대문자 -> 소문자

적용 2단계
1. 논리명 생성 : 명시적으로 컬럼, 테이블명을 직접 적지 않으면 ImplicitNamingStragegy 사용
  spring.jap.hibernate.naming.implicit-strategy : 테이블이나, 컬럼명을 명시하지 않을 때 논리명 적용
2. 물리명 적용
  spring.jpa.hibernate.naming.physical-strategy : 모든 논리명에 적용됨, 실제 테이블에 적용
(username -> usernm 등으로 회사 룰로 바꿀 수 있음)


### cascade
모든 엔티티는 저장 하고 싶을 때 각각 저장을 해야하는데 cascade를 활용하면
order와 연결된 orderItem을 order가 등록 되면 자동으로 업데이트 되도록 할 수 있다.

### 연관관게 편의 메서드
order와 member가 있을 때 
member가 주문을 하면 두 테이블에 값을 세팅해 주는게 

# /24-01-30
## 구현 요구사항
### 예제를 단순화 하기위해 다음 기능은 구현X
- 로그인과 권한 관리
- 파라미터 검증과 예외 처리 단순화
- 상품은 도서만 사용
- 카테고리 사용 X
- 배송 정보 사용 X

## 애플리케이션 아키텍처
### 계층형 구조 사용
- controller,web : 웹 계층
- service : 비즈니스 로직, 트랜잭션 처리
- repository : JPA를 직접 사용하는 계층, 엔티티 매니저 사용
- domain : 엔티티가 모여있는 계층, 모든 계층에서 사용

### 패키지 구조
- jpabook.jpashop
  - domain
  - exception
  - repository
  - service
  - web

### 개발 순서 : 서비스, 리포지토리 계층을 개발하고, 테스트 케이스를 작성해서 검증, 마지막에 웹 계층 적용

## 회원 도메인 개발
### 구현 기능
- 회원 등록
- 회원 목록 조회

### 순서
- 회원 엔티티 코드 다시 보기
- 회원 리포지토리 개발
- 회원 서비스 개발
- 회원 기능 테스트

# /24-01-31
## 회원 서비스 개발

### 참고 
회원 가입기능 구현 부분에서 validateDuplicateMember를 만들어 중복 회원 검증을 할 수 있도록 구현 했지만 member의 이름으로만 검증을 하는경우 
여러명이 동시에 같은 이름으로 등록을 하게되면 동시에 로직을 타면서 Exception이 발생하지 않을 수 있으므로, member의 name을 unique 제약조건으로 잡아주는걸 권장한다.

### 참고
memberRepository등 주입이 필요할 때 생성자 주입을 쓰는걸 권장한다. Spring 입문강의에서 들었던걸 기억하자! 기억이 안난다면 다시 복습하자!
생성자가 한개만 있는 경우에는 @Autowired 애노테이션이 없어도 Spring이 인식하고 주입해준다.
그리고 변경할 일이 없기 때문에 선언할 때 final을 붙여주자 ex) private final MemberRepository memberRepository
final은 컴파일 시점에 값이 들어오는지 체크해 줄 수 있다.
@AllArgsConstructor를 쓰면 생성자를 만들어주지 않아도 된다.
@RequiredArgsConstructor를 쓰면 final이 있는 변수의 생성자를 세팅해준다. @PersistenceContext를 붙여서 생성해줬던 EntityManager에도 적용이 가능하다.

# /24-02-03
## 회원 기능 테스트

### 테스트 요구사항
- 회원가입을 성공해야 한다.
- 회원가입 할 때 같은 이름이 있으면 예외가 발생해야 한다.


### 참고
@Transactinal 애노테이션을 테스트에서 사용하면 기본적으로 Rollback을 해준다.
EnitityManager가 persist를 한다고 해서 DB에 데이터가들어가는게 아니다. commit까지 처리를 해줘야 한다.
테스트에서는 자동적으로 rollback을 하기 때문에 데이터가 들어간것을 확인하기 위해서는 @Rollback(value=false)설정을 추가해줘야한다.
insert되는 문장을 확인해 보고 싶다면 EntityManager.flush()를 사용해주면 된다.

### 참고
spring에서 테스트를 할때 resource폴더를 만들고 application.yml을 만들면 main에 있는 application.yml보다 먼저 인식한다.
그리고 테스트를 위해 메모리 db를 사용하기 위한방법도 있으며, 기본적으로 spirng에서는 아무런 설정을 하지 않아도 testdb를 사용하도록 제공한다.

# /24-02-04

## 상품 도메인 개발

### 구현 기능
- 상품 등록
- 상품 목록 조회
- 상품 수정

### 순서
- 상품 엔티티 개발(비즈니스 로직 추가)
- 상품 리포지토리 개발
- 상품 서비스 개발
- 상품 기능 테스트

# /24-02-05
## 상품 리포지토리 개발

## 상품 서비스 개발

# /24-02-06
## 주문 도메인 개발
### 구현 기능
- 상품 주문
- 주문 내역 조회
- 주문 취소

### 순서
- 주문 엔티티, 주문상품 엔티티 개발
- 주문 리포지토리 개발
- 주문 서비스 개발
- 주문 검색 기능 개발
- 주문 기능 테스트

## 주문, 주문상품 엔티티 개발

# /24-02-09
## 주문 리포지토리 개발

## 주문 서비스 개발
### 참고 
CascadeType.ALL 같은 경우 라이프 사이클을 동일하게 관리하고 다른것들이 참조하지않는 경우 사용하는게 좋다.
OrderItem과 Delivery같은 경우 참조하는게 Order하나 뿐이기 때문에 설정을 한 경우다.

### 참고
미리 만들어준 생성자를 제외하고 new Object를 생성해 setter를 사용한 객체를 채우는 방식을 사용하는것을 방지하고 싶다면
protected 생성자를 만들어주면 new 방식으로 객체를 생성하는 것을 막을 수 있다.
위와 같은 방식으로 @NoArgsConstructor(access = AccessLevel.PROTECTED) 애노테이션을 추가해줄 수도 있다.

### 참고
주문 서비스의 주문과 주문 취소 메서드를 보면 비즈니스 로직 대부분이 엔티티에 있다.
서비스 계층은 단순히 엔티티에 필요한 요청을 위임하는 역할을 한다. 
이처럼 엔티티가 비즈니스 로직을 가지고 객체 지향의 특성을 적극 활용하는 것을 *도메인 모델 패턴*이라 한다.
반대로 엔티티에는 비즈니스 로직이 거의 없고 서비스 계층에서 대부분의 비즈니스 로직을 처리하는 것을 *트랜잭션 스크립트 패턴*이라고 한다.

# /24-02-10
## 주문 기능 테스트

### 테스트 요구사항
- 상품 주문이 성공해야 한다.
- 상품을 주문할 때 재고 수량을 초과하면 안된다.
- 주문 취소가 성공해야 한다.

### 참고
Address가 Embeddable로 되어있는데 이때 사용하려면 인자가 없는 생성자가 필요하다.

# /24-02-11
## 주문 검색 기능 개발
### JPA에서 동적 쿼리를 어떻게 해야 하는가?

### 참고
최종적으로 동적 쿼리는 Querydsl을 사용하여 처리할 것이다. 

# /24-02-12
## 홈 화면과 레이아웃

# /24-02-13
## 회원 등록
### 참고 
@NotEmpty를 사용하려는데 import가 안되는 상황
@NotEmpty는 javax.validation.constraints 패키지에 존재한다.
스프링 부트 2.2 이하는 포함하고 있지만, 스프링 부트 2.3 이상은 따로 의존성을 추가해주어야 한다.

### 참고
BindingResult를 사용하면 오류가 발생했을 때 해당 오류를 팅겨내지 않고 해당 오류를 이용한 처리를 할 수 있다.

# /24-02-14
## 회원 목록 조회
### 참고
엔티티를 최대한 순수하게 유지해야한다. 핵심 로직에만 디펜던시가 있도록 유지해야한다.
엔티티는 화면을 위한 로직은 없어야한다. 
엔티티를 그대로 쓰는것보다 화면에서 사용하는 폼을 DTO로 생성해서 사용하는것이 좋다.
API를 만들때는 절대 엔티티를 외부로 반환하면 안된다. 

# /24-02-17
## 상품 등록

# /24-02-18
## 상품 목록

## 상품 수정

### 참고
JPA에서는 병합 방식보다는 변경감지 방식을 쓰는게 맞다

### 참고
수정 같은 부분에서 ID를 이용해서 변경을 하기 떄문에 취약점 부분에 문제가 발생할 수 있다.
그러므로 해당 유저의 권한에 대한 체크 부분이 서비스 부분에서 필요하다.

# /24-02-19
## 변경감지와 병합(merge)
### 준영속 엔티티?
영속성 컨텍스트가 더는 관리하지 않는 엔티티를 말한다.
여기서는 itemService.saveItem(book)에서 수정을 시도하는 Book객체다. Book객체는 이미 DB에 한번 저장되어서 식별자가 존재한다. 
이렇게 임의로 만들어낸 엔티티도 기존 식별자를 가지고 있으면 준영속 엔티티로 볼 수 있다.

### 준영속 엔티티를 수정하는 2가지 방법
- 변경 감지 기능 사용
- 병합(merge) 사용

### 변경감지 == dirty checking
주문 취소같은 경우 따로 order의 상태를 변경하기만 했지 JPA에게 값을 저장하라고 하지 않았는데 트랜잭션 커밋 시점에서 dirty checking이 이루어지면서 JPA가 알아서 변경을 한다.

### 준영속 엔티티에 대하여
데이터 베이스에 식별자가 있는 경우 준영속 상태의 객체(준영속 엔티티)라고 한다. 
ex) UpdateItem같은 경우 new로 Book객체를 생성하긴 하지만 Id값을 세팅해준다. 
이 Id는 이미 DB에 값이 존재해 식별자가 있어 수정을 하는것이므로 준영속 엔티티라 할 수 있다.

### 변경감지 기능 사용
영속성 컨텍스트에서 엔티티를 다시 조회한 후에 데이터를 수정하는 방법
트랙잭션 안에서 엔티티를 다시 조회, 변경할 값 선택 -> 트랜잭션 커밋 시점에 변경감지(Dirty Checking)이 동작해서 데이터베이스에 UPDATE SQL 실행

### 병합 사용
병합은 준영속 상태의 엔티티를 영속 상태로 변경할 때 사용하는 기능이다.

### 병합 동작방식
1. merge()를 실행한다.
2. 파라미터로 넘어온 준영속 엔티티의 식별자 값으로 1차 캐시에서 엔티티를 조회한다.
   2-1. 만약 1차 캐시에 엔티티가 없으면 데이터베이스에서 엔티티를 조회하고, 1차 캐시에 저장한다.
3. 조회한 영속 엔티티(mergeMember)에 member엔티티의 값을 채워 넣는다. (member 엔티티의 모든 값을 mergeMember에 밀어넣는다. 이때 mergeMember의 "회원1"이라는 이름이 "회원명변경"으로 바뀐다.)
4. 영속 상태인 mergeMember를 반환한다.

### 병합 시 동작 방식을 간단히 정리
1. 준영속 엔티티의 식별자 값으로 영속 엔티티를 조회한다.
2. 영속 엔티티의 값을 준영속 엔티티의 값으로 모두 교체한다.(병합한다.)
3. 트랜잭션 커밋 시점에 변경 감지 기능이 동작해서 데이터베이스에 UPDATE SQL이 실행

### 주의 
변경 감지 기능을 사용하면 원하는 속성만 선택해서 변경할 수 있지만, 병합을 사용하면 모든 속성이 변경된다.
병합 시 값이 없으면 null로 업데이트 할 위험도 있다. (병합은 모든 필드를 교체한다.)

### 참고
save() 메서드는 식별자를 자동 생성해야 정상 동작한다. 여기서 사용한 Item 엔티티의 식별자는 자동 새엇ㅇ되도록 @GeneratedValue를 선언했다.
따라서 식별자 없이 save() 메서드를 호출하면 persist()가 호출되면서 식별자 값이 자동으로 할당된다. 
반면에 식별자를 직접 할당하도록 @Id만 선언했다고 가정하자. 이 경우 식별자를 직접 할당하지 않고, save() 메서드를 호출하면 식별자가 없는 상태로 persist()를 호출한다.
그러면 식별자가 없다는 예외가 발생한다.

### 참고
실무에서는 보통 업데이트 기능이 매우 제한적이다. 그런데 병합은 모든 필드를 변경해버리고, 데이터가 없으면 null로 업데이트 해버린다.
병합을 사용하면서 이 문제를 해결하려면, 변경 폼 화면에서 모든 데이터를 항상 유지해야 한다.
실무에서는 보통 변경가능한 데이터만 노출하기 때문에, 병합을 사용하는 것이 오히려 번거롭다.

### 가장좋은 해결방법
### 엔티티를 변경할 때는 항상 변경감지를 사용하세요
- 컨트롤러에서 어설프게 엔티티를 생성하지 마세요
- 트랜잭션이 있는 서비스 계층에 식별자(id)와 변경할 데이터를 명확하게 전달하세요 (파라미터 or dto)
- 트랜잭션이 있는 서비스 계층에서 영속 상태의 엔티티를 조회하고, 엔티티의 데이터를 직접 변경하세요.
- 트랜잭션 커밋 시점에서 변경 감지가 실행됩니다.

# /24-02-20
## 상품 주문

# /24-02-21
## 주문 목록 검색, 취소

# /24-02-22
## 회원 등록 API
### 참고
api와 화면단에서는 공통으로 처리할 부분들이 다르기 때문에 패키지단에서 분리를 하는게 좋다.

### 참고
API에서의 name이 공백으로 들어오는 것을 막기 위해 @NotEmpty를 사용하게 되면 프레젠테이션(화면에서 컨트롤러까지) 계층을 위한 검증로직이 엔티티에 들어있게 된다.
어떤 부분에서는 검증이 필요할 수 있지만 어떤 로직에서는 검증이 필요하지 않을 수 있기 때문에 문제가 발생할 수 있다.

엔티티를 손대서 api스펙 자체가 변하는게 문제다!!!
그러므로 api를 위한 DTO를 새로 만들어야 한다.

apu를 개발할때는 절대 Entity를 파라미터로 받지 말자!!!

dto를 사용하면 컨트롤러단에서 파라미터와 엔티티를 연결해주는 과정이 생기므로 
혹시나 엔티티를 변화시킨다면 오류가 발생한다. 
그리고 api 스펙이 어떻게 되는지 dto를 확인하므로 바로 알 수 있다.

# /24-02-24
## 회원 수정 API

# /24-02-25
## 회원 조회 API

조회 V1 : 응답 값으로 엔티티를 직접 외부에 노출한
- 문제점
  - 엔티티에 프레젠테이션 계층을 위한 로직이 추가된다.
  - 기본적으로 엔티티의 모든 값이 노출된다.
  - 응답 스펙을 맞추기 위해 로직이 추가된다. (@JsonIgnore, 별도의 뷰 로직 등등)
  - 실무에서는 같은 엔티티에 대해 API가 용도에 따라 다양하게 만들어지는데, 한 엔티티에 각각의 API를 위한 프레젠테이션 응답 로직을 담기는 어렵다.
  - 엔티티가 변경되면 API 스펙이 변한다.
  - 추가로 컬렉션을 직접 반환하면 향후 API 스펙을 변경하기 어렵다. (별도의 Result 클래스 생성으로 해결)
- 결론
  - API 응답 스펙에 맞추어 별도의 DTO를 반환한다.

### 참고 : 엔티티를 외부에 노출하지 마세요!
실무에서는 member 엔티티의 데이터가 필요한 API가 계속 증가하게 된다. 어떤 API는 name 필드가 필요하지만, 어떤 API는 name 필드가 필요없을 수 있다.
결론적으로 엔티티 대신에 API 스펙에 맞는 별도의 DTO를 노출해야한다.

# /24-02-26
## API 개발 고급 소개
## 조회용 샘플 데이터 입력

# /24-02-27
## 지연로딩과 조회 성능 최적화
주문 + 배송정보 + 회원을 조회하는 API를 만들자
지연로딩 때문에 발생하는 성능 문제를 단계적으로 해결해보자

### 참고
지금부터 설명하는 내용은 실무에서 JPA를 사용하려면 100%이해해야하는 부분입니다.

## 간단한 주문 조회 V1 : 엔티티를 직접 노출 

# /24-02-28
### 첫번째 문제
양방향 연관관계에서 Order에서 Member를 뿌리는데 또 Order가 있고 서로 계속 무한루프에 빠지는 문제가 발생하는데 이 때는 한 쪽에 @JsonIgnore를 붙여줘야한다.

### 두번째 문제
Order의 member가 fetch = Lazy설정이 되어있는데 이때 proxyMember를 생성하는데 jackson이 판단하기에는 순수한 자바객체가 아니기 때문에 오류가 발생한다.
이럴때는 hibernate5 모듈을 설치해야한다.

### 주의
엔티티를 직접 노출할 때는 양방향 연관관계가 걸린 곳은 꼭! 한곳을 @JsonIgnore 처리해야 한다.
안그러면 양쪽을 서로 호출하면서 무한 루프가 걸린다.

### 참고
앞에서 강조했듯이 정말 간단한 애플리케이션이 아니면 엔티티를 API로 응답으로 외부로 노출하는것은 좋지 않다.
따라서 Hibernate5Module을 사용하기 보다는 DTO로 변환해서 반환하는 것이 더 좋은 방법이다.

### 주의
지연로딩(LAZY)을 피하기 위해 즉시 로딩(EARGR)으로 설정하면 안된다! 
즉시 로딩 때문에 연관관계가 필요 없는 경우에도 데이터를 항상 조회해서 성능 문제가 발생할 수 있다.
즉시 로딩으로 설정하면 성능 튜닝이 매우 어려워진다.
항상 지연로딩을 기본으로 하고, 성능 최적화가 필요한 경우에는 페치 조인(fetch join)을 사용해라

# /24-02-29
## 간단한 주문조회 V2: 엔티티를 DTO로 변환

### v1,v2 둘 다 Lazy 로딩으로 인한 데이터 탐색 쿼리가 너무 많이 호출된다는 문제가 있다.
- 쿼리가 총 1 + N + N 번 실행된다.
  - order 조회 1번 (order조회 결과 수가 N이 된다.)
  - order -> member 지연 로딩 조회 N번
  - order -> delivery 지연 로딩 조회 N번
  - 예)order의 결과가 4개면 최악의 경우 1 + 4 + 4번 실행된다.(최악의경우)
     - 지연로딩은 영속성 컨텍스트에서 조회하므로, 이미 조회된 경우 쿼리를 생략한다.

# /24-03-01

## 간단한 주문 조회 V3; 엔티티를 DTO로 변환 - 페치 조인 최적화
- 엔티티를 페치 조인(fetch join)을 사용해서 쿼리 1번에 조회
- 페치 조인으로 order -> member, order -> delivery는 이미 조회된 상태이므로 지연로딩X
