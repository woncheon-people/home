# SPRING MVC 2 - ThymeLeaf 기본기능

1. 타임리프란?
- Thymeleaf는 HTML, XML, JavaScript, CSS 및 일반 텍스트를 처리할 수 있는 웹 및 독립 실행형 환경 모두를 위한 최신 서버 Java 템플릿 엔진이다.

1. 타임리프의 특징
- **서버 사이드 HTML 렌더링 (SSR)**
    
    타임리프는 백엔드 서버에서 HTML을 동적으로 렌더링 하는 용도로 사용된다.
    
- **네츄럴 템플릿**
    
    타임리프는 순수 HTML을 최대한 유지하는 특징이 있다.
    타임리프로 작성한 파일은 HTML을 유지하기 때문에 웹 브라우저에서 파일을 직접 열어도 내용을 확인할
    수 있고, 서버를 통해 뷰 템플릿을 거치면 동적으로 변경된 결과를 확인할 수 있다.
    JSP를 포함한 다른 뷰 템플릿들은 해당 파일을 열면, 예를 들어서 JSP 파일 자체를 그대로 웹 브라우저에서
    열어보면 JSP 소스코드와 HTML이 뒤죽박죽 섞여서 웹 브라우저에서 정상적인 HTML 결과를 확인할 수
    없다. 오직 서버를 통해서 JSP가 렌더링 되고 HTML 응답 결과를 받아야 화면을 확인할 수 있다.
    반면에 타임리프로 작성된 파일은 해당 파일을 그대로 웹 브라우저에서 열어도 정상적인 HTML 결과를
    확인할 수 있다. 물론 이 경우 동적으로 결과가 렌더링 되지는 않는다. 하지만 HTML 마크업 결과가 어떻게
    되는지 파일만 열어도 바로 확인할 수 있다.
    이렇게 **순수 HTML을 그대로 유지하면서 뷰 템플릿도 사용할 수 있는 타임리프의 특징을 네츄럴 템플릿**
    (natural templates)이라 한다.
    
- **스프링 통합 지원**
    
    타임리프는 스프링과 자연스럽게 통합되고, 스프링의 다양한 기능을 편리하게 사용할 수 있게 지원한다. 
    

타임리프를 사용하려면 다음과 같은 선언을 html 태그에서 작성해야 한다.

```html
<html xmlns:th="http://www.thymeleaf.org">
```

## 텍스트 기능 - utext, text

 

타임리프는 기본적으로 HTML 테그의 속성에 기능을 정의해서 동작한다. HTML의 콘텐츠(content)에
데이터를 출력할 때는 다음과 같이 th:text 를 사용하면 된다.

```html
<span **th:text="${data}**">안녕</span>
<span>[[${item}]]</span> 
```

- 위와 같은 코드에서 타임리프가 동작하면 안녕이 아닌 data 변수의 값이 출력된다.

HTML 테그의 속성이 아니라 HTML 콘텐츠 영역안에서 직접 데이터를 출력하고 싶으면 다음과 같이
[[...]] 를 사용하면 된다.

<aside>
📌 컨텐츠 안에서 직접 출력하기 = **[[${data}]]**

</aside>

```java
@Controller
  @RequestMapping("/basic")
  public class BasicController {
      @GetMapping("/text-basic")
      public String textBasic(Model model) {
          model.addAttribute("data", "Hello Spring!");
          return "basic/text-basic";
      }
}
```

```html
<!DOCTYPE html>
  <html xmlns:th="http://www.thymeleaf.org">
  <head>
      <meta charset="UTF-8">
      <title>Title</title>
  </head>
<body>
<h1>컨텐츠에 데이터 출력하기</h1>
<ul>
<li>th:text 사용 <span th:text="${data}"></span></li> 
<li>컨텐츠 안에서 직접 출력하기 = [[${data}]]</li>
  </ul>
  </body>
</html>
```

### 📍 Escape, HTML 엔티티

HTML 문서는 < , > 같은 특수 문자를 기반으로 정의된다. 따라서 뷰 템플릿으로 HTML 화면을 생성할 때는 출력하는 데이터에 이러한 특수 문자가 있는 것을 주의해서 사용해야 한다. 

```java
public String basic(Model model){
	model.addAttribute("item","Hello <b>Spring</b>");
    return "basic";
}
```

모델에 태그를 포함한 문자열을 담았고, 우리는 <b> 태그를 이용해 Spring!이라는 단어를 진하게 나오게 만들고 싶다.

웹 브라우저에서 출력을 해보면 출력은 다음과 같다.

![스크린샷 2023-05-31 오후 7.11.59.png](SPRING%20MVC%202%20-%20ThymeLeaf%20%E1%84%80%E1%85%B5%E1%84%87%E1%85%A9%E1%86%AB%E1%84%80%E1%85%B5%E1%84%82%E1%85%B3%E1%86%BC%201aaacd1960ac4e6697769358dc4792d1/%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2023-05-31_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE_7.11.59.png)

해당 부분을 강조하는 것이 목적이었다.

그런데 태그가 그대로 나온다. 소스보기를 하면 < 부분이 &lt; 로 변경된 것을 확인할 수 있다**.**

웹 브라우저는 <를 HTML 태그의 시작으로 인식한다. 따라서 < 를 태그의 시작이 아니라 문자로 표현할 수 있는 방법이 필요한데, 이것을 ***HTML 엔티티*** 라 한다. 그리고 이렇게 HTML에서 사용하는 특수 문자를 HTML 엔티티로 변경하는 것을 이스케이프(escape)라 한다. 그리고 타임리프가 제공하는 th:text , [[...]] 는 기본적으로 이스케이스(escape)를 제공한다.

<aside>
📌 그러면 이스케이프 기능을 사용하지 않으려면 어떻게 해야 할까? 타임리프는 두 가지 기능을 제공한다.

- th:text -> th:utext
- [[...]] -> [(...)]
</aside>

이를 이용하여 아래와 같이 코드를 변경

```html
<span th:utext="${item}">hello</span>
<span>[(${item})]</span>
```

```html
<!DOCTYPE html>
    <html xmlns:th="http://www.thymeleaf.org">
    <head>
        <meta charset="UTF-8">
        <title>Title</title>
    </head>
<body>
    <h1>text vs utext</h1>
    <ul>
        <li>th:text = <span th:text="${data}"></span></li>
        <li>th:utext = <span th:utext="${data}"></span></li>
    </ul>
    <h1><span th:inline="none">[[...]] vs [(...)]</span></h1>
    <ul>
        <li><span th:inline="none">[[...]] = </span>[[${data}]]</li>
        <li><span th:inline="none">[(...)] = </span>[(${data})]</li>
    </ul>
    </body>
    </html>
```

이렇게 수정하고 실행해보면 <b> 태그를 사용한 곳이 글씨 굵기가 굵어지는 것을 확인하며 정상 수행되는 것을 확인할 수 있다. 

**th:inline="none"** : 타임리프는 [[...]] 를 해석하기 때문에, 화면에 [[...]] 글자를 보여줄 수 없다. 

이 테그 안에서는 타임리프가 해석하지 말라는 옵션이다.

⚠️ 주의!

실제 서비스를 개발하다 보면 escape를 사용하지 않아서 HTML이 정상 렌더링 되지 않는 수많은 문제가 발생한다. escape를 기본으로 하고, 꼭 필요한 때만 unescape를 사용하자.

## **변수 -SpringEL**

타임리프에서 변수를 사용할 때는 변수 표현식을 사용한다.

<aside>
📌 변수 표현식 : **${.**..**}**

</aside>

그리고 이 변수 표현식에는 스프링 EL이라는 스프링이 제공하는 표현식을 사용할 수 있다.

가정을 하나 하고 시작하겠다. 우리는 User라는 클래스가 존재한다. 

```java
@GetMapping("/variable")
  public String variable(Model model) {
      User userA = new User("userA", 10);
      User userB = new User("userB", 20);
      List<User> list = new ArrayList<>();
      list.add(userA);
      list.add(userB);
      Map<String, User> map = new HashMap<>();
      map.put("userA", userA);
      map.put("userB", userB);
      model.addAttribute("user", userA);
      model.addAttribute("users", list);
      model.addAttribute("userMap", map);
      return "basic/variable";
  }

  @Data // Setter, Getter, ToString, Constructor
  static class User {
      private String username;
      private int age;
  
       public User(String username, int age) {
          this.username = username;
          this.age = age;
} }
```

이에 대한 인스턴스를 여러 개 만들어서 List에 넣는다. 이후  모델에 담고 타임리프를 통해 렌더링 시킬 것이다. 

이 User라는 클래스는 username 프로퍼티를 가지고 있다.

그럼 이제 어떻게 변수를 사용할 수 있는지 아래 코드를 통해 확인해보자.

```html
<ul>Object
 <li>${user.username} = <span th:text="${user.username}"></span></li>
 <li>${user['username']} = <span th:text="${user['username']}"></span></li>
 <li>${user.getUsername()} = <span th:text="${user.getUsername()}"></span></li>
</ul>

<ul>List
 <li>${users[0].username} = <span th:text="${users[0].username}"></span></li>
 <li>${users[0]['username']} = <span th:text="${users[0]['username']}"></span></li>
 <li>${users[0].getUsername()} = <span th:text="${users[0].getUsername()}"></span></li>
</ul>

<ul>Map
 <li>${userMap['userA'].username} = <span th:text="${userMap['userA'].username}"></span></li>
 <li>${userMap['userA']['username']} = <span th:text="${userMap['userA']['username']}"></span></li>
 <li>${userMap['userA'].getUsername()} = <span th:text="${userMap['userA'].getUsername()}"></span></li>
</ul>
```

- Object
    - user.username : user의 username을 프로퍼티 접근 -> user.getUsername()
    - user ['username'] : 위와 같음 -> user.getUsername()
    - user.getUsername() : user의 getUsername()을 직접 호출
- List
    - users [0]. username List에서 첫 번째 회원을 찾고 username 프로퍼티 접근 -> list.get(0). getUsername()
    - users [0]['username']: 위와 같음
    - users [0]. getUsername() : List에서 첫 번째 회원을 찾고 메서드 직접 호출
- Map
    - userMap ['userA']. username : Map에서 userA를 찾고, username 프로퍼티 접근 -> map.get("userA"). getUsername()
    - userMap ['userA']['username'] : 위와 같음
    - userMap ['userA']. getUsername() : Map에서 userA를 찾고 메서드 직접 호출

<aside>
📌 지역변수 선언

th:with를 사용하면 지역 변수를 선언해서 사용할 수 있다. 지역 변수는 선언한 태그 안에서만 사용 가능.

```java
<div th:with="first=${users[0]}">
 <p>처음 사람의 이름은 <span th:text="${first.username}"></span></p>
</div>
```

</aside>

### 기본 객체들

타임리프는 기본 객체들을 제공한다.
${#request} - 스프링 부트 3.0부터 제공하지 않는다.
${#response} - 스프링 부트 3.0부터 제공하지 않는다.
${#session} - 스프링 부트 3.0부터 제공하지 않는다.
${#servletContext} - 스프링 부트 3.0부터 제공하지 않는다.
${#locale}

⚠️주의

**스프링 부트 3.0**스프링 부트 3.0 부터는 ${#request} , ${#response} , ${#session} , ${#servletContext} 를 지원하지 않는다. 만약 사용하게 되면 다음과 같은 오류가 발생한다.

스프링 부트 3.0이라면 직접 model 에 해당 객체를 추가해서 사용해야 한다.

그런데 #request 는 HttpServletRequest 객체가 그대로 제공되기 때문에 데이터를 조회하려면
request.getParameter("data") 처럼 불편하게 접근해야 한다.

이런 점을 해결하기 위해 편의 객체도 제공한다.

HTTP 요청 파라미터 접근: param

예) ${param.paramData}
HTTP 세션 접근: session

예) ${session.sessionData}

스프링 빈 접근: @
예) ${@helloBean.hello('Spring!')}

```java
// 스프링부트 3.0 미만
@GetMapping("/basic-objects")
  public String basicObjects(HttpSession session) {
      session.setAttribute("sessionData", "Hello Session");
      return "basic/basic-objects";
  }
  @Component("helloBean")
  static class HelloBean {
      public String hello(String data) {
          return "Hello " + data;
} }
```

```java
// 스프링 부트 3.0 이상 : 스프링 부트 3.0이라면 직접 model 에 해당 객체를 추가해서 사용해야 한다.
@GetMapping("/basic-objects")
  public String basicObjects(Model model, HttpServletRequest request,
  HttpServletResponse response, HttpSession session) {
      session.setAttribute("sessionData", "Hello Session");
      model.addAttribute("request", request);
      model.addAttribute("response", response);
      model.addAttribute("servletContext", request.getServletContext());
      return "basic/basic-objects";
}
  @Component("helloBean")
  static class HelloBean {
      public String hello(String data) {
          return "Hello " + data;
} }
```

```html

<h1>식 기본 객체 (Expression Basic Objects)</h1> <ul>
      <li>request = <span th:text="${#request}"></span></li>
      <li>response = <span th:text="${#response}"></span></li>
      <li>session = <span th:text="${#session}"></span></li>
      <li>servletContext = <span th:text="${#servletContext}"></span></li>
			<li>locale = <span th:text="${#locale}"></span></li>
  </ul>
<h1>편의 객체</h1> <ul>
      <li>Request Parameter = <span th:text="${param.paramData}"></span></li>
      <li>session = <span th:text="${session.sessionData}"></span></li>
      <li>spring bean = <span th:text="${@helloBean.hello('Spring!')}"></span>
</li> </ul>
```

## 유틸리티 객체와 날짜

타임리프는 문자, 숫자, 날짜, URI 등을 편리하게 다루는 다양한 유틸리티 객체들을 제공한다.

타임리프 유틸리티 객체들

- #message : 메시지, 국제화 처리
- #uris : URI 이스케이프 지원
- #dates : java.util.Date 서식 지원
- #calendars : java.util.Calendar 서식 지원
- #temporals : 자바 8 날짜 서식 지원
- #numbers : 숫자 서식 지원
- #strings : 문자 관련 편의 기능
- #objects : 객체 관련 기능 제공
- #bools : boolean 관련 기능 제공
- #arrays : 배열 관련 기능 제공
- #lists , #sets , #maps : 컬렉션 관련 기능 제공
- #ids : 아이디 처리 관련 기능 제공, 뒤에서 설명

타임리프 유틸리티 객체 [https://www.thymeleaf.org/doc/tutorials/3.0/usingthymeleaf.html#expression-utilityobjects](https://www.thymeleaf.org/doc/tutorials/3.0/usingthymeleaf.html#expression-utilityobjects)

유틸리티 객체 예시 [https://www.thymeleaf.org/doc/tutorials/3.0/usingthymeleaf.html#appendix-b-expressionutility-objects](https://www.thymeleaf.org/doc/tutorials/3.0/usingthymeleaf.html#appendix-b-expressionutility-objects)

자바 8 날짜

타임리프에서 자바 8 날짜인 LocalDate , LocalDateTime , Instant를 사용하려면 추가 라이브러리가 필요하다. 스프링 부트 타임리프를 사용하면 해당 라이브러리가 자동으로 추가되고 통합된다.

타임리프 자바 8 날짜 지원 라이브러리 - thymeleaf-extras-java8 time

자바 8 날짜용 유틸리티 객체 -  #temporals

사용 예시는 아래와 같다.

```html
<span th:text="${#temporals.format(localDateTime, 'yyyy-MM-dd HH:mm:ss')}">
</span>

<!--model.addAttribute("localDateTime", LocalDateTime.now()); 
매핑된 컨트롤러에서 이를 실행 -->
```

![스크린샷 2023-06-01 오전 12.45.38.png](SPRING%20MVC%202%20-%20ThymeLeaf%20%E1%84%80%E1%85%B5%E1%84%87%E1%85%A9%E1%86%AB%E1%84%80%E1%85%B5%E1%84%82%E1%85%B3%E1%86%BC%201aaacd1960ac4e6697769358dc4792d1/%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2023-06-01_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%258C%25E1%2585%25A5%25E1%2586%25AB_12.45.38.png)

## URL 링크

타임리프에서 URL을 생성할 때는 @{...} 문법을 사용하면 된다.

```java
@GetMapping("/link")
public String link(Model model) {
 model.addAttribute("param1", "data1");
 model.addAttribute("param2", "data2");
 return "basic/link";
}
```

타임리프는 아래와 같은 html이다. 짧게 URL 링크를 사용하는 태그만 작성하겠다.

```xml
<li><a th:href="@{/hello}">basic url</a></li>
        <li><a th:href="@{/hello(param1=${param1}, param2=${param2})}">hello query param</a></li>
        <li><a th:href="@{/hello/{param1}/{param2}(param1=${param1}, param2=${param2})}">path variable</a></li>
        <li><a th:href="@{/hello/{param1}(param1=${param1}, param2=${param2})}">path variable + query parameter</a></li>
```

<aside>
📌 **단순한 URL**
@{/hello} -> /hello

</aside>

<aside>
📌 **쿼리 파라미터**

@{/hello(param1=${param1}, param2=${param2})}

/hello? param1=data1¶m2=data2

()에 있는 부분은 쿼리 파라미터로 처리된다.

</aside>

<aside>
📌 **경로 변수**

@{/hello/{param1}/{param2}(param1=${param1}, param2=${param2})}

/hello/data1/data2 URL 경로상에 변수가 있으면 () 부분은 경로 변수로 처리된다

</aside>

<aside>
📌 **경로 변수 + 파라미터**

@{/hello/{param1}(param1=${param1}, param2=${param2})}

/hello/data1? param2=data2

경로 변수와 쿼리 파라미터를 함께 사용할 수 있다.

</aside>

## **리터럴 Literals**

리터럴은 소스 코드상에 고정된 값을 말하는 용어다.

예를 들어서 다음 코드에서 "Hello"는 문자 리터럴, 10, 20는 숫자 리터럴이다.

```java
String a = "Hello"
int a = 10 * 20
```

타임리프는 다음과 같은 리터럴이 있다.

- 문자 : 'hello'
- 숫자 : 10
- 불린 : true, false
- null : null

**타임리프에서 문자 리터럴은 항상 ' (작은따옴표)로 감싸야한다.**

```html
<span th:text="'hello'">
```

그런데 문자를 항상 ' 로 감싸는 것은 너무 귀찮은 일이다.

<aside>
📌 공백 없이 쭉 이어진다면 하나의 의미 있는 토큰으로 인지해서 다음과 같이 작은따옴표를 생략가능

룰: A-Z , a-z , 0-9 , [] ,. , - , _

</aside>

```html
<span th:text="hello">
```

📍 오류

```xml
<span th:text="hello world!"></span>
```

문자 리터럴은 원칙상 ' 로 감싸야한다. 중간에 공백이 있어서 하나의 의미 있는 토큰으로도 인식되지 않는다.

📍수정

```xml
<span th:text="'hello world!'"></span>
```

이렇게 ' 로 감싸면 정상 동작한다. 하지만 이는 너무 불편하다 이를 대체하기 위한 문법이 존재한다.

<aside>
📌 **리터럴 대체
마지막의 리터럴 대체 문법을 사용하면 마치 템플릿을 사용하는 것 처럼 편리하다.**

</aside>

```xml
 	<span th:text="'hello'+' world!'"></span>
        <span th:text="'hello world!'"></span>
        <span th:text="'hello '+${data}"></span>
        <span th:text="|hello ${data}|"></span>
```

아래와 같은 코드는 정상적으로 작동한다.

```xml
<span th:text="|hello ${data}|">
```

## 연산

타임리프 연산은 자바와 크게 다르지 않다. 
HTML 안에서 사용하기 때문에 HTML 엔티티를 사용하는 부분만 주의하자.

코드는 아래와 같다. 이번에는 경우가 많아 전체 HTML을 가져왔다.

```xml
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<ul>
    <li>산술 연산
        <ul>
            <li>10 + 2 = <span th:text="10 + 2"></span></li>
            <li>10 % 2 == 0 = <span th:text="10 % 2 == 0"></span></li>
        </ul>
    </li>
    <li>비교 연산
        <ul>
            <li>1 > 10 = <span th:text="1 &gt; 10"></span></li>
            <li>1 gt 10 = <span th:text="1 gt 10"></span></li>
            <li>1 >= 10 = <span th:text="1 >= 10"></span></li>
            <li>1 ge 10 = <span th:text="1 ge 10"></span></li>
            <li>1 == 10 = <span th:text="1 == 10"></span></li>
            <li>1 != 10 = <span th:text="1 != 10"></span></li>
        </ul>
    </li>
    <li>조건식
        <ul>
            <li>(10 % 2 == 0)? '짝수':'홀수' = <span th:text="(10 % 2 == 0)?
'짝수':'홀수'"></span></li>
        </ul>
    </li>
    <li>Elvis 연산자<!--조건식 축약 버전 --><ul>
            <li>${data}?: '데이터가 없습니다.' = <span th:text="${data}?: '데이터가없습니다.'"></span></li>
            <li>${nullData}?: '데이터가 없습니다.' = <span th:text="${nullData}?:'데이터가 없습니다.'"></span></li>
        </ul>
    </li>
    <li>No-Operation
        <ul>
            <li>${data}?: _ = <span th:text="${data}?: _">데이터가 없습니다.</span></li>
            <li>${nullData}?: _ = <span th:text="${nullData}?: _">데이터가없습니다.</span></li>
        </ul>
    </li>
</ul>
</body>
</html>
```

<aside>
📌 **비교 연산**: HTML 엔티티를 사용해야 하는 부분을 주의하자, 
> (gt), < (lt), >= (ge), <= (le),! (not), == (eq),!= (neq, ne)

</aside>

- 조건식: 자바의 조건식과 유사하다.
- Elvis 연산자: 조건식의 편의 버전

<aside>
📌 **No-Operation**: _ 인 경우 마치 타임리프가 실행되지 않는 것처럼 동작한다. 이것을 잘 사용하면 HTML의 내용 그대로 활용할 수 있다. 마지막 예를 보면 데이터가 없습니다. 부분이 그대로 출력된다.

</aside>

## 속성 값 설정

**타임리프 태그 속성(Attribute)**

타임리프는 주로 HTML 태그에 th:* 속성을 지정하는 방식으로 동작한다. th:* 로 속성을 적용하면 기존 속성을 대체한다. 기존 속성이 없으면 새로 만든다.

```xml
<input type="text" name="mock" th:name="userA" />
<!-- name="userA"로 바뀌어 있다.--><h1>속성 추가</h1>
- th:attrappend = <input type="text" class="text" th:attrappend="class='large'" /><br/>
- th:attrprepend = <input type="text" class="text" th:attrprepend="class='large'" /><br/>
- th:classappend = <input type="text" class="text" th:classappend="large" /><br/><!--이거 자주 쓴다고 한다 --><h1>checked 처리</h1>
- checked o <input type="checkbox" name="active" th:checked="true" /><br/>
- checked x <input type="checkbox" name="active" th:checked="false" /><br/>
- checked=false <input type="checkbox" name="active" checked="false" /><br/>
```

### 속성설정

th:* 속성을 지정하면 타임리프는 기존 속성을 th:* 로 지정한 속성으로 대체한다. 

기존 속성이 없다면 새로 만든다.

<aside>
📌 **속성 추가**

th:attrappend : 속성 값의 뒤에 값을 추가한다.

th:attrprepend : 속성 값의 앞에 값을 추가한다.

th:classappend : class 속성에 자연스럽게 추가한다.

</aside>

<aside>
📌 **checked 처리**

HTML에서는 <input type="checkbox" name="active" checked="false"/>

이 경우에도 checked 속성이 있기 때문에 checked 처리가 되어버린다.

HTML에서 checked 속성은 checked 속성의 값과 상관없이 checked라는 속성만 있어도 체크가 된다. 이런 부분이 true , false 값을 주로 사용하는 개발자 입장에서는 불편하다. 
타임리프의 th:checked는 값이 false 인 경우 checked 속성 자체를 제거한다.

</aside>

```html
<input type="checkbox" name="activ" th:checked="false"> 

<!--타임리프 렌더링 후-->

<input type="checkbox" name="active"/>
```

## 반복

타임리프에서 반복은 th:each를 사용한다. 추가로 반복에서 사용할 수 있는 여러 상태 값을 지원한다.

아래와 같은 코드를 작성하고 모델을 통해 List를 꺼낸 후, th:each로 반복을 사용해보자.

```java
@GetMapping("/each")
public String each(Model model) {
     addUsers(model);
     return "basic/each";
}
private void addUsers(Model model) {
     List<User> list = new ArrayList<>();
     list.add(new User("userA", 10));
     list.add(new User("userB", 20));
     list.add(new User("userC", 30));
     model.addAttribute("users", list);
}
```

이제 타임리프를 확인해보자.

```xml
 <tr th:each="user : ${users}">
 <td th:text="${user.username}">username</td>
 <td th:text="${user.age}">0</td>
 </tr>

<!-- 다른 코드는 생략--><tr th:each="user, userStat : ${users}">
 <td th:text="${userStat.count}">username</td>
 <td th:text="${user.username}">username</td>
 <td th:text="${user.age}">0</td>
 <td>
 index = <span th:text="${userStat.index}"></span>
 count = <span th:text="${userStat.count}"></span>
 size = <span th:text="${userStat.size}"></span>
 even? = <span th:text="${userStat.even}"></span>
 odd? = <span th:text="${userStat.odd}"></span>
 first? = <span th:text="${userStat.first}"></span>
 last? = <span th:text="${userStat.last}"></span>
 current = <span th:text="${userStat.current}"></span>
 </td>
 </tr>
```

<aside>
📌 **반복 기능**

- **<tr th:each="user : ${user}">**
- 반복 시 오른쪽 컬렉션(${user})의 값을 하나씩 꺼내서 왼쪽 변수(user)에 담아서 태그를 반복 실행한다.
- th:each는 List 뿐만 아니라 배열 java.util.Iterable, java.util.Enumeration을 구현한 모든 객체를 반복에 사용할 수 있다. Map도 사용할 수 있는데 이 경우 변수에 담기는 값은 Map.Entry이다.
</aside>

<aside>
📌 **반복 상태 유지**

- <tr th:each= "user, userStat : ${users}">
- 반복의 두 번째 파라미터를 설정해서 반복의 상태를 확인할 수 있습니다.
- 두 번째 파라미터는 생략 가능한데, 생략하면 지정한 변수명( user ) + Stat 가 됩니다. 
여기서는 user + Stat = userStat 이므로 생략 가능합니다.
</aside>

### 반복 상태 유지 기능

- index : 0부터 시작하는 값
- count : 1부터 시작하는 값
- size : 전체 사이즈
- even , odd : 홀수, 짝수 여부( boolean )
- first , last :처음, 마지막 여부( boolean )
- current : 현재 객체

## **조건부 평가**

<aside>
📌 **타임리프의 조건식**

if, unless (if의 반대)

</aside>

아까와 동일하게 유저를 모델에 담고 타임리프에서 렌더링 한다. 짧게 코드만 살펴보면 아래와 같다.

```html
<span th:text="'미성년자'" th:if="${user.age lt 20}"></span>
<span th:text="'미성년자'" th:unless="${user.age ge 20}"></span>

<tr th:each="user, userStat : ${users}">
 <td th:switch="${user.age}">
 <span th:case="10">10살</span>
 <span th:case="20">20살</span>
 <span th:case="*">기타</span>
 </td>
 </tr>
```

<aside>
📌 if, unless
⚠️ **타임리프는 해당 조건이 맞지 않으면 태그 자체를 렌더링 하지 않는다.**

만약 다음 조건이 false 인 경우 <span>... </span>

</aside>

switch

- * 은 만족하는 조건이 없을 때 사용하는 디폴트이다.

## 주석

타임리프에서 크게 주석은 3가지가 있다. 바로 살펴보겠다.

```html
<h1>1. 표준 HTML 주석</h1>
<!--
<span th:text="${data}">html data</span> -->

<h1>2. 타임리프 파서 주석</h1> <!--/* [[${data}]] */-->
<!--/*-->
  <span th:text="${data}">html data</span>
  <!--*/-->

<h1>3. 타임리프 프로토타입 주석</h1>
<!--/*/
<span th:text="${data}">html data</span>
/*/-->
```

**1. 표준 HTML 주석**

자바스크립트의 표준 HTML 주석은 타임리프가 렌더링 하지 않고, 그대로 남겨둔다.

**2. 타임리프 파서 주석**

타임리프 파서 주석은 타임리프의 진짜 주석이다. 렌더링에서 주석 부분을 제거한다.

**3. 타임리프 프로토타입 주석**

타임리프 프로토타입은 약간 특이한데, HTML 주석에 약간의 구문을 더했다. HTML 파일을 웹 브라우저에서 그대로 열어보면 HTML 주석이기 때문에 이 부분이 웹 브라우저가 렌더링 하지 않는다. 타임리프 렌더링을 거치면 이 부분이 정상 렌더링 된다. 쉽게 이야기해서 HTML 파일을 그대로 열어보면 주석처리가 되지만, 타임리프를 렌더링 한 경우에만 보이는 기능이다.

## 블록

<th:block>은 HTML 태그가 아닌 타임리프의 유일한 태그다.

```xml
<th:block th:each="user : ${users}">
 <div>
 사용자 이름1 <span th:text="${user.username}"></span>
 사용자 나이1 <span th:text="${user.age}"></span>
 </div>
 <div>
 요약 <span th:text="${user.username} + ' / ' + ${user.age}"></span>
 </div>
</th:block>
```

타임리프의 특성상 HTML 태그 안에 속성으로 기능을 정의해서 사용하는데,
위 예처럼 이렇게 사용하기 애매한 경우에 사용하면 된다. th:block 은 렌더링 시 제거된다.

## **자바스크립트 인라인**

타임리프는 자바스크립트에서 타임리프를 편리하게 사용할 수 있는 자바스크립트 인라인 기능을 제공한다.

자바스크립트 인라인 기능은 다음과 같이 적용하면 된다.

```xml
<script th:inline="javascript">
```

```html
<!DOCTYPE html>
  <html xmlns:th="http://www.thymeleaf.org">
  <head>
      <meta charset="UTF-8">
      <title>Title</title>
  </head>
<body>
<!-- 자바스크립트 인라인 사용 전 --> <script>
      var username = [[${user.username}]];
      var age = [[${user.age}]];
//자바스크립트 내추럴 템플릿
var username2 = /*[[${user.username}]]*/ "test username";
//객체
      var user = [[${user}]];
  </script>
<!-- 자바스크립트 인라인 사용 후 --> <script th:inline="javascript">
      var username = [[${user.username}]];
      var age = [[${user.age}]];
//자바스크립트 내추럴 템플릿
var username2 = /*[[${user.username}]]*/ "test username";
//객체
      var user = [[${user}]];
  </script>
  </body>
  </html>
```

자바스크립트 인라인을 사용하지 않은 경우 어떤 문제들이 있는지 알아보고, 
인라인을 사용하면 해당 문제들이 어떻게 해결되는지 확인해보자.

### **텍스트 렌더링**

- var username = [[${user.username}]];
    - 인라인 사용 전 var username = userA;
    - 인라인 사용 후 var username = **"**userA**"**;
- 인라인 사용 전 렌더링 결과를 보면 userA라는 변수 이름이 그대로 남아있다. 타임리프 입장에서는 정확하게 렌더링 한 것이지만 아마 개발자가 기대한 것은 다음과 같은 "userA"라는 문자일 것이다. 결과적으로 userA가 변수명으로 사용되어서 자바스크립트 오류가 발생한다. 다음으로 나오는 숫자 age의 경우에는 " 가 필요 없기 때문에 정상 렌더링 된다.
- 인라인 사용 후 렌더링 결과를 보면 문자 타입인 경우 "를 포함해준다. 추가로 자바스크립트에서 문제가 될 수 있는 문자가 포함되어 있으면 이스케이프 처리도 해준다. 예) " \"

### **자바스크립트 내추럴 템플릿**

타임리프는 HTML 파일을 직접 열어도 동작하는 내추럴 템플릿 기능을 제공한다. 자바스크립트 인라인 기능을 사용하면 주석을 활용해서 이 기능을 사용할 수 있다.

- var username2 = /*[[${user.username}]]*/ "test username";
- 인라인 사용 전 var username2 = /*userA*/ "test username"; 
인라인 사용 후 var username2 = "userA";
- 인라인 사용 전 결과를 보면 정말 순수하게 그대로 해석을 해버렸다. 
따라서 내추럴 템플릿 기능이 동작하지 않고, 심지어 렌더링 내용이 주석 처리되어 버린다.
- 인라인 사용 후 결과를 보면 주석 부분이 제거되고, 기대한 "userA"가 정확하게 적용된다.

### 객체

- 타임리프의 자바스크립트 인라인 기능을 사용하면 객체를 JSON으로 자동으로 변환해준다.
- var user = [[${user}]];
    - 인라인 사용 전 var user = BasicController.User(username=userA, age=10);
    - 인라인 사용 후 var user = {"username":"userA", "age":10};
- 인라인 사용 전은 객체의 toString()이 호출된 값이다.
- 인라인 사용 후는 객체를 JSON으로 변환해준다.

## **템플릿 조각**

웹 페이지를 개발할 때는 공통 영역이 많이 있다. 예를 들어서 상단 영역이나 하단 영역, 좌측 카테고리 등등 여러 페이지에서 함께 사용하는 영역들이 있다. 이런 부분을 코드를 복사해서 사용한다면 변경 시 여러 페이지를 다 수정해야 하므로 상당히 비효율 적이다. 타임리프는 이런 문제를 해결하기 위해 템플릿 조각과 레이아웃 기능을 지원한다.

대부분의 템플릿 엔진에서 이 같은 개념을 지원한다.

먼저 템플릿 조각인 footer.html의 코드다.

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<body>
<footer th:fragment="copy">
  푸터 자리 입니다.
</footer>
<footer th:fragment="copyParam (param1, param2)">
  <p>파라미터 자리 입니다.</p>
  <p th:text="${param1}"></p>
  <p th:text="${param2}"></p>
</footer>
</body>
</html>
```

th:fragment가 있는 태그는 다른 곳에 포함되는 코드 조각이다.

이제 진짜 렌더링 되는 HTML 페이지 코드다.

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<h1>부분 포함</h1>
<h2>부분 포함 insert</h2>
<div th:insert="~{basic/footer :: copy}"></div>
<h2>부분 포함 replace</h2>
<div th:replace="~{basic/footer :: copy}"></div>
<h2>부분 포함 단순 표현식</h2>
<div th:replace="basic/footer :: copy"></div>
<h1>파라미터 사용</h1>
<div th:replace="~{basic/footer :: copyParam ('데이터1', '데이터
2')}"></div>
</body>
</html>
```

- basic/footer :: copy : basic/footer.html 템플릿에 있는 th:fragment="copy"라는 부분을 템플릿 조각으로 가져와서 사용한다는 의미이다.

<aside>
📌 **부분 포함 insert**

<div th:replace="{basic/footer :: copy}"></div>

th:insert를 사용하면 현재 태그 div 내부에 추가한다.

</aside>

<aside>
📌 **부분 포함 replace**

<div th:replace="~{basic/footer :: copy}"></div>

th:replace를 사용하면 현재 태그 div를 대체한다.

</aside>

<aside>
📌 **부분 포함 단순 표현식**

<div th:replace="basic/footer :: copy"></div>

~{..}를 사용하는 것이 원칙이지만 템플릿 조각을 사용하는 코드가 단순하면 이 부분을 생략 가능.

</aside>

<aside>
📌 **파라미터 사용**

다음과 같이 파라미터를 전달해서 동적으로 조각을 렌더링 할 수도 있다.

<div th:replace="~{basic/footer :: copyParam ('데이터 1', '데이터 2')}"></div>

</aside>

## **템플릿 레이아웃 1**

<aside>
📌 **템플릿 레이아웃**

이전에는 일부 코드 조각을 가지고 와서 사용했다면, 이번에는 개념을 더 확장해서 코드 조각을 레이아웃에 넘겨서 사용하는 방법에 대해서 알아보자.

예를 들어서 <head>에 공통으로 사용하는 css , javascript 같은 정보들이 있는데, 이러한 공통 정보들을 한 곳에 모아 두고, 공통으로 사용하지만, 각 페이지마다 필요한 정보를 더 추가해서 사용하고 싶다면 다음과 같이 사용하면 된다.

</aside>

아래는 베이스가 되는 레이아웃 html이다.

```html
<html xmlns:th="http://www.thymeleaf.org">
<head th:fragment="common_header(title,links)"> <title th:replace="${title}">레이아웃 타이틀</title>
<!-- 공통 -->
      <link rel="stylesheet" type="text/css" media="all" th:href="@{/css/
  awesomeapp.css}">
      <link rel="shortcut icon" th:href="@{/images/favicon.ico}">
      <script type="text/javascript" th:src="@{/sh/scripts/codebase.js}"></
  script>
<!-- 추가 -->
<th:block th:replace="${links}" />
</head>
```

아래는 렌더링 되는 html 페이지다.

```xml
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head th:replace="template/layout/base :: common_header(~{::title},~{::link})">
 <title>메인 타이틀</title>
 <link rel="stylesheet" th:href="@{/css/bootstrap.min.css}">
 <link rel="stylesheet" th:href="@{/themes/smoothness/jquery-ui.css}">
</head>
<body>
메인 컨텐츠
</body>
</html>
```

<결과>

```html
<html>
<head>
<title>메인 타이틀</title> <!-- 공통 -->
  <link rel="stylesheet" type="text/css" media="all" href="/css/awesomeapp.css">
  <link rel="shortcut icon" href="/images/favicon.ico">
  <script type="text/javascript" src="/sh/scripts/codebase.js"></script>
<!-- 추가 -->
<link rel="stylesheet" href="/css/bootstrap.min.css">
<link rel="stylesheet" href="/themes/smoothness/jquery-ui.css">
</head> <body> 메인 컨텐츠 </body> </html>
```

<aside>
📌 **common_header(~{::title},~{::link}) 이 부분이 핵심이다.**
::title 은 현재 페이지의 title 태그들을 전달한다. 
::link는 현재 페이지의 link 태그들을 전달한다.

결과를 보자. 메인타이틀이 전달한 부분으로 교체되었다. 공통부분은 그대로 유지되고, 추가 부분에 전달한 <link> 들이 포함된 것을 확인할 수 있다.

</aside>

이 개념을 더 확장하면 <html> 전체에 적용할 수 있다.

layoutFile.html

```html
<!DOCTYPE html>
<html th:fragment="layout (title, content)" xmlns:th="http://
www.thymeleaf.org">
<head>
 <title th:replace="${title}">레이아웃 타이틀</title>
</head>
<body>
<h1>레이아웃 H1</h1>
<div th:replace="${content}">
 <p>레이아웃 컨텐츠</p>
</div>
<footer>
 레이아웃 푸터
</footer>
</body>
</html>
```

layoutExtendMain.html

```html
<!DOCTYPE html>
<html th:replace="~{template/layoutExtend/layoutFile :: layout(~{::title},
~{::section})}"
 xmlns:th="http://www.thymeleaf.org">
<head>
 <title>메인 페이지 타이틀</title>
</head>
<body>
<section>
 <p>메인 페이지 컨텐츠</p>
 <div>메인 페이지 포함 내용</div>
</section>
</body>
</html>
```

<결과>

```html
<!DOCTYPE html>
<html>
<head>
<title>메인 페이지 타이틀</title> </head>
<body>
<h1>레이아웃 H1</h1>
<section>
<p>메인 페이지 컨텐츠</p> <div>메인 페이지 포함 내용</div> </section>
<footer> 레이아웃 푸터
  </footer>
  </body>
</html>
```

layoutFile.html을 보면 기본 레이아웃을 가지고 있는데, <html>에 th:fragment 속성이 정의되어 있다. 이 레이아웃 파일을 기본으로 하고 여기에 필요한 내용을 전달해서 부분 부분 변경하는 것으로 이해하면 된다.

layoutExtendMain.html 는 현재 페이지인데, <html> 자체를 th:replace를 사용해서 변경하는 것을 확인할 수 있다. 결국 layoutFile.html 에 필요한 내용을 전달하면서 자체를 layoutFile.html로 변경한다.