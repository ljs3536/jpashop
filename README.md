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
