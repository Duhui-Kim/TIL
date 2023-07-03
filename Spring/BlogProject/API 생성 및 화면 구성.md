## Spring Data JPA

### @EnableJpaAuditing

- @CreatedDate, @LastModifiedDate : 생성일자, 최종 수정 일자 저장 기능 제공
- @CreatedBy, @LastModifiedBy : 생성한 사람, 수정한 사람을 자동으로 저장해줌 (User 정보 필요)



## Entity의 기본생성자(public, protected) 

### 기본생성자가 필요한 이유

```
스프링의 @RequestBody 바인딩 방식은 기본 생성자를 통해 객체를 생성한 후 Java Reflection을 이용해 필드 값을 집어넣어 주는 방식이다. Reflection은 클래스 이름만 알면 생성자, 필드, 메서드 등 클래스의 모든 정보에 접근이 가능하다. 하지만 생성자의 매개변수 정보는 가져올 수 없다. 따라서 Reflection으로 생성할 객체에 모든 필드를 받는 생성자가 있더라도 Reflection은 해당 생성자를 호출할 수가 없다. 그래서 Reflection은 기본 생성자로 객체를 생성하고 필드 값을 강제로 매핑해주는 방식을 사용한다.

JPA 역시 데이터를 DB에서 조회해 온 뒤 객체를 생성할 때 Reflection을 사용한다. 때문에 기본 생성자로 객체를 생성한다. 결론적으로 기본 생성자가 존재하지 않는다면 데이터베이스에서 조회해 온 값을 엔티티로 만들 때 객체 생성 자체에 실패하게 되기 때문에, JPA에서는 기본 스펙으로 기본 생성자를 반드시 생성해 줄 것을 정해놓고 있다.

그렇다면 왜 Reflection을 사용할까? 이는 우리가 엔티티로 어떤 타입을 생성할 지 JPA는 알 수 없기 때문이다. Reflection을 사용하지 않고 객체를 생성하려면 미리 객체의 타입을 알고 있어야 합니다. 하지만 프레임워크나 라이브러리는 사용자가 정의할 구체 클래스 정보를 알 수 없다. 때문에 어떤 타입으로 엔티티를 만들더라도 해당 엔티티를 생성하기 위해 Reflection을 사용하여 엔티티 인스턴스를 만들어 주는 것이다.
```



### 기본생성자가 private이면 안되는 이유

```
 JPA의 엔티티를 매핑하는 방식으로 조회 시 연관된 엔티티 정보를 바로 가져오는 즉시 로딩(Eager Loading) 방식과 연관된 엔티티에 프록시, 즉 가짜 객체를 넣어준 뒤 해당 객체의 정보가 실제로 필요한 타이밍에 연관된 엔티티를 조회해오는 지연 로딩(Lazy Loading) 이라는 방식이 있다. 여기서 지연 로딩과 프록시 객체를 사용하기 위해서는 생성자가 private이어서는 안 된다.
 
 지연 로딩으로 인해 프록시 객체를 넣어줘야 하면 원본 엔티티를 상속한 프록시 엔티티를 만들고, 실제 필요한 타이밍에 엔티티를 조회해 온 뒤 프록시 엔티티가 원본 엔티티를 참조하도록 하여 사용하게 된다. 때문에 연관된 엔티티 자리에 지연 로딩으로 인해 프록시가 들어가든 즉시 로딩으로 실제 엔티티가 들어가든 상관 없이 항상 정상적으로 기능해야 하니 프록시는 당연히 원래 들어가야 할 엔티티의 하위 타입일 수 밖에 없다.

 만약 기본 생성자가 private으로 선언되어 있다면 해당 엔티티를 상속한 프록시를 만들 수 없을 것이다. 상속한 객체의 생성자는 반드시 부모 객체의 생성자 super를 호출해야 하는데, private이면 상속받은 클래스에서 호출할 수 없기 때문이다.
```



## SETTER 사용을 지양

```java
@Getter
public class ArticleResponse {
    private final String title;
    private final String content;

    public ArticleResponse(Article article) {
        this.title = article.getTitle();
        this.content = article.getContent();
    }
}
```

### 1. 사용한 의도를 쉽게 파악하기 어렵다.

- Setter를 사용하게 되면 값을 생성하는 것인지 변경하는 것인지 정확한 의도를 파악하기 어렵다.

- 객체의 내부 값이 복잡한 경우 더욱 한 눈에 파악하기 힘들 것이다.

  

### 2. 객체의 일관성을 유지하기 힘들다.

- 객체의 내용을 Update하는 method 밖에서도 Setter를 활용하면 객체의 내용을 바꿀 수 있다.
- 따라서 POST 객체의 일관성이 무너지게 된다.



## LOMBOK

### ArgsConstructor

- NoArgsConstructor : 기본 생성자 추가
- AllArgsConstructor : 모든 필드값을 파라미터로 받는 생성자 추가
- RequiredArgsConstructor : final이 붙거나 @NotNull이 붙은 필드의 생성자 추가



## 오라클 SQL ERROR

```
org.springframework.beans.factory.BeanCreationException: Error creating bean with name 'dataSourceScriptDatabaseInitializer' defined in class path resource [org/springframework/boot/autoconfigure/sql/init/DataSourceInitializationConfiguration.class]: Failed to execute SQL script statement #3 of URL [file:/C:/Users/dadad/OneDrive/desktop/BlogProject/BlogProject/build/resources/main/data.sql]: INSERT INTO article (title, content) VALUES ("제목 3", "내용 3")
```

- 오라클에서는 **INSERT INTO 테이블명 (컬럼1, 컬럼2) VALUES (*"Value1", "Value2"*)** => 에러 발생
- 쌍따옴표는 컬럼명을 감싸주는 기호이고, 홑따옴표는 문자열을 감싸주는 기호라고 한다.



## 타임리프

| 표현식 | 설명                                                   |
| :----- | ------------------------------------------------------ |
| ${...} | 변수의 값 표현식                                       |
| #{...} | 속성 파일 값 표현식                                    |
| @{...} | URL 표현식                                             |
| *{...} | 선택한 변수의 표현식. th:object에서 선택한 객체에 접근 |



## 인텔리제이 한글 깨짐 문제

https://www.lesstif.com/java/intellij-file-console-encoding-121012310.html