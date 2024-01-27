/23-12-21
spring.io -> projects -> spring boot -> learn -> reference.doc
thymeleaf.org

spring boot는 Tomcat을 내장하고 있고
웹 브라우저에서 내장 톰켓 서버에 요청을 하고 스프링컨테이너에 있는 컨트롤러에서 해당 요청을 받아 
해당 컨트롤러의 로직을 진행한 후 컨트롤러가 리턴값으로 문자를 반환하면 뷰 리졸버(vieResolcer)가 화면을 찾아서 처리한다.

- 스트링 부트 템플릿엔진 기본 viewName 매핑
- resources:templates/ +(ViewName) + .html

참고. spring-boot-devtools 라이브러리를 추가하면, html 파일을 컴파일만 해주면 서버 재시작 없이 View파일 변경이 가능하다.

/23-12-22
./gradlew build 오류 발생 
해결 하기 위한 방법은?

/23-12-23 
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


/23-12-24
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

/23-12-25
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
