# 메세지, 국제화
## 메세지
화면에 보이는 문구를 다른 문구로 전체로 변경해달라고 하면 단어 하나를 다 찾아가서 바꿔야하는 상황이 발생한다. 해당 HTML 파일에 메시지가 하드코딩 되어 있기 떄문이다.
이런 다양한 메시지를 한 곳에서 관리하도록 하는 기능을 메시지 기능이라 한다.
Key, value로 설정하고 해당 데이터를 key 값으로 불러서 사용하는 것이다.

**addFrom.html**
```html
<label for="itemName" th:text="#{item.itemName}"></label>
```
하드코딩 된 단어들을 별도의 파일로 만들어서 관리하는 거라고 생각하면 된다.

## 국제화
미국에 있는 유저가 들어오면 또는 웹브라우저 언어를 다른 언어로 설정해놓았으면 그 언어에 맞게(나라별로) 관리하는 것이다.

영어를 사용하는 사람이면 `message_en.propertis` 를 사용하고
한국어를 사용하는 사람이면 `message_ko.propertis` 를 사용하게 개발하면 된다.

한국에서 접근한 것인지 영어에서 접근한 것인지는 인삭하는 방법은 HTTP `accept-laguage` 해더 값을 사용하거나 사용자가 직접 언어를 선택하도록 하고, 쿠키 등을 사용해서 처리하면 된다.

스프링은 기본적인 메시지와 국제화 기능을 모두 제공한다. 웹 개발자면 기본적으로 필요한 기능이기 떄문에 프레임워크로 지원된다.

---

## 스프링 메시지 소스 설정
메시지 관리 기능을 사용하려면 스프링이 제공하는 `MEssageSource`를 스프링 빈으로 등록하면 되는데, `MessageSource`는 인터페이스이다. 따라서 구현체인 `ResourceBundleMessageSource`를 스프링으 빈으로 등록하면 된다.

그런데 파일을 자동으로 읽어서 메시지와 국제화를 자동으로 연결해준다. 하지만 스프링 부트를 사용하면 자동으로 등록되기 때문에 스프링 부트를 이용한다.

### 스프링 부트 메시지 소스 설정
`application.propertis`
```
spring.message.basename=messages,config.i18n.message
```

### 스프링 부트 메시지 소스 기본 값
```
spring.message.basename=messages
```
MessageSource를 스프링 빈으로 등록하지 않고, 스프링 부트와 관련된 별도의 설정을 하지 않으면 messages라는 이름으로 기본 등록된다. 따라서 `message_en_properties`, `message_ko_properties`, `message.properties`(관련된 것을 못찾을 때) 파일만 등록하면 자동으로 인식된다.
> 더 자세한 내용이 궁금하면 spring 메뉴얼을 참조 https://docs.spring.io/spring-boot/docs/2.7.8/reference/html/application-properties.html#appendix.application-properties


### 메시지 파일 만들기
- `messages.properties` : 기본 값으로 사용(한글), 디폴트
- `messages_en.properties` : 영어 국제화 사용

경로 : `/resources/messages.properties`

---

## 스프링 메시지 소스 사용
- messagSource.getMessage("hello", null, null);
    - code : hello
    - args : null
    - locale : null

가장 단순한 테스트는 메시지 코드로 hello를 입력하고 나머지 값은 null을 입력했다. locale 정보가 없으면 basename에서 설정한 기본 이름 메시지 파일을 조호한다. -> message.properties 파일에서 데이터를 조회한다.

### 메시지가 없는 경우, 기본 메시지
- 메시지가 없는 경우에는 NoSuchMessageException 이 발생한다.
- 메시지가 없어도 기본 메시지( defaultMessage )를 사용하면 기본 메시지가 반환된다.

### 매개변수 사용
다음 메시지의 {0} 부분은 매개변수를 전달해서 치환할 수 있다. hello.name=안녕 {0} -> Spring 단어를 매개변수로 전달 -> 안녕 Spring

### 국제화 파일 선택
locale 정보를 기반으로 국제화 파일을 선택한다.
- Locale이 en_US 의 경우 messages_en_US messages_en messages 순서로 찾는다.
- Locale 에 맞추어 구체적인 것이 있으면 구체적인 것을 찾고, 없으면 디폴트를 찾는다고 이해하면 된다.
- ms.getMessage("hello", null, null) : locale 정보가 없으므로 messages 를 사용
- ms.getMessage("hello", null, Locale.KOREA) : locale 정보가 있지만, message_ko 가 없으므로 messages 를 사용

## 웹 애플리케이션에 메시지 적용하기
### 타임리프 메시지 적용
타임리프의 메시지 표현식 `#{...}`를 사용하면 스프링의 메시지를 편리하게 조회할 수 있다.

예를 들어서 방금 등록한 상품이라는 이름을 조회하려면 `#{label.item}`이라고 하면 된다.

### 렌더링 전
```html
<div th:text="#{label.item}"></h2>
```
### 렌더링 후
```html
<div>상품</h2>
```