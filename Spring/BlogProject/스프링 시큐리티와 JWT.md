## 스프링 시큐리티

### 정의

- 스프링 기반 애플리케이션의 보안을 담당하는 스프링의 하위 프레임워크
- CSRF 공격 방어, Session Fixation 공격 방어, 요청 헤더 보안 처리도 해준다.

#### * 인증, 인가

- 인증 (Authentication) : 사용자의 신원을 입증하는 과정
- 인가 (Authorization) : 사이트의 특정 부분에 접근할 수 있는지 권한을 확인 



### 스프링 시큐리티 필터

#### 구조도

![image](https://github.com/Duhui-Kim/CodingTest_JAVA/assets/118238663/3d0d3183-8baa-450a-bf0e-e734ff8ab969)



#### 필터 상세 기능

<img src="https://github.com/Duhui-Kim/BlogProject/assets/118238663/dabc27fb-106d-4d91-9c7c-c3a068f906a2" alt="그림1" style="zoom:20%;" />



#### 인증 관리자와 접근결정 관리자

<img src="https://github.com/Duhui-Kim/CodingTest_JAVA/assets/118238663/1cb08135-8be4-4354-87da-c272048b3501" alt="그림2" style="zoom:25%;" />



### UserDetails

- SpringSecurity에서 사용자의 정보를 담는 인터페이스
- 기본 오버라이드 메서드

![그림12](https://github.com/Duhui-Kim/CodingTest_JAVA/assets/118238663/009a42f4-1656-4ae6-98e5-b95c8f9b6b68)



### UserDetailsService

- SpringSecurity에서 유저의 정보를 가져오는 인터페이스

- 기본 오버라이드 메서드

  ![image](https://github.com/Duhui-Kim/BlogProject/assets/118238663/39091d10-bd25-4153-8e12-9225775bbe6a)



## Java8 함수형 인터페이스

![image](https://github.com/Duhui-Kim/BlogProject/assets/118238663/68f8c72f-68a9-4d9b-9e5d-32669fb3fc8f)



## Optional<T\>

### 개념

- Java8에서 새롭게 도입된 Class로, null이 올 수 있는 값을 감싸는 Wrapper Class이다. NullPointException을 방지한다.
- long 대신 Long, int 대신 Integer를 쓰는 것과 비슷하다.

- 공식문서 내용

  ![캡처11](https://github.com/Duhui-Kim/CodingTest_JAVA/assets/118238663/51878988-d2bc-498b-8c23-e982631130cf)

### Method

<img src="https://github.com/Duhui-Kim/BlogProject/assets/118238663/adbc268a-a7f8-4986-a590-b79e8fd3b2d9" alt="그림3" style="zoom:20%;" />

#### orElse()와 orElseGet() & orElseThrow()의 차이점

- orElse()는 파라미터로 값을 받고, orElseGet()와 orElseThrow()는 파라미터로 함수형 인터페이스를 받는다.

- 두 method의 차이는 아래와 같이 나타난다.

  ```java
  // null값이 아닌 testObj 생성
  Optional<String> testObj = Optional.ofNullable("테스트");
  
  // 이런 method가 있다고 가정하면
  public String testMethod() {
  	System.out.println("이거 실행되나?");
      return "테스트";
  }
  
  // orElse
  System.out.println("1. " + testObj.orElse(testMethod()));
  
  // orElseGet
  System.out.println("2. " + testObj.orElseGet(testMethod()));
  
  /*
   <result>
   이거 실행되나?
   1. 테스트
   
   2. 테스트
  */
  ```

- orElse()에서는 값을 저장하기 때문에 null 여부에 상관 없이 method를 실행시켜 값을 저장해둔다. Optional 객체가 비어있지 않으므로 가지고 있는 값을 출력한다.

- orElseGet()에서는 함수형 인터페이스를 저장하기 때문에 null이 아닌 경우 실행되지 않는다.





## JWT

### 정의

- Java Web Token의 약자
- Header, Payload, Signature로 구성

### 특징

- 무상태성(Stateless) : 서버에서 클라이언트의 인증 정보를 유지하지 않아도 됨.
- 확장성 : 토큰의 주체가 클라이언트이기 때문에 하나의 토큰으로 여러 서버에 접근 가능.
- 무결성 : HMAC(Hash-based Message Authenication) 기법으로 토큰을 발급한 이후에 토큰 변경 불가능.

### 리프레시 토큰

- 토큰을 주고 받는 환경이 보안에 취약해서 토큰이 노출되었을 경우, 악용될 수 있음.
- 액세스 토큰의 유효기간을 짧게 설정하고, 리프레시 토큰의 유효기간은 길게 설정