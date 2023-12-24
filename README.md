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

