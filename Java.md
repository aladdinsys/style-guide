# Java 가이드

아래 정의되어 있는 가이드외에는 [Google Java Style Guide](https://google.github.io/styleguide/javaguide.html)를 따른다.


## Naming Convention
- 클래스명은 대문자로 시작하는 의미있는 단수 명사로 작성한다.
- 함수명은 동사로 시작, 필드(변수)명은 명사로 시작한다. 둘 다 카멜케이스를 따른다.
- 컨트롤러는 HTTP Method 에 맞춰서 함수명을 정한다(get, post, patch, put, delete)
- 서비스는 jpa repository 에 맞춰서 함수명을 정한다(find, save, delete)
- 모든 네이밍은 정립된(well-established) 약어를 제외하고는 약어를 사용치 아니한다.

## Entity
- Entity 객체의 테이블명은 PhysicalNamingStrategy 를 활용하여 적용한다.
- Entity 객체는 @Entity, @Getter, @NoArgsConstructor(access = AccessLevel.PROTECTED) 만 사용한다.
- 생성자는 @Builder 어노테이션을 활용하여 Builder 패턴으로 만든다.
- 각 필드에 @Column 어노테이션과 함께 컬럼명을 명시해준다.
    - nullable은 필수, String일 경우 length는 필수이다.
- *ToOne 관계에서는 fetchType은 항상 LAZY로 설정한다.
- Auditing에 @CreatedDate, @LastModifiedDate는 필수이다.
-
```java

@Entity
@Getter
@NoArgsConstructor(access = AccessLevel.PROTECTED)
public class Account {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(nullable = false)
    private Long id;

    @ManyToOne(fetch = FetchType.LAZY, optional = false)
    @JoinColumn(name = "group_id", nullable = false)
    @Setter
    private AccountGroup accountGroup;

    @Column(nullable = false, unique = true, length = 16)
    private String userid;

    @Column(nullable = false, length = 255)
    private String username;

    @Setter
    @Column(nullable = false, length = 255)
    private String password;

    @Column(nullable = false)
    @Enumerated(EnumType.STRING)
    private AccountRole role;

    @Builder
    public Account(Long id,
                   AccountGroup accountGroup,
                   String userid,
                   String username,
                   String password,
                   AccountRole role
    ) {
        this.id = id;
        this.accountGroup = accountGroup;
        this.userid = userid;
        this.username = username;
        this.password = password;
        this.role = role;
    }

```
## RestController
- ResponseEntity는 Http 상태코드, 헤더, body의 내용을 커스텀하여 사용할 수 있다.
- RestController에는 상태코드와 body의 내용이 중요하다.
- Header의 내용을 커스텀 하는 비지니스 로직이 거의 없다.
- 간단한 Response에도 지나치게 많은 추가 코드가 요구된다.
- 따라서 ResponseEnity 대신 상태 코드는 `@ResponseStatus`를 사용한다 (기본값 200)
```java
    @PostMapping("/users")
    @ResponseStatus(HttpStatus.CREATED)
    public UserResponseDto postUser(@RequestBody UserRequestDto dto) {
        return service.save(dto);
    }
```

## DTO
- DTO 객체는 Record Class 를 사용하여 만든다.
- Record Class 는 각 필드에 private final 필드로 정의된다.
- Record Class 는 각 필드에 getter 가 자동 생성된다.
- 데이터의 불변함을 위함이다.

```java
@Builder
public record AccountDto(
        LocalDateTime updDt,
        Long id,
        AccountGroupDto accountGroupDto,
        String userid,
        String username,
        String password,
        AccountRole role
) implements Serializable {

}
```

## Line
- 1줄에 100자를 넘지 않도록 작성한다.
- 코드간의 간격은 2줄이상 간격이 발생하지 않도록 한다.(최대 1줄 줄바꿈)

### Parameter
- Parameter개수가 많아서 줄바꿈이 필요한 경우, **`,`다음부터 줄바꿈한다.**
```java
public Drawing(Long categoryId,
               @NonNull String name,
               @NonNull Strinig code,
               @Nullable String description
        ) {
        ...
        }
```
- 줄바꿈이 필요한 부분부터 줄바꿈 하지 않고 위의 예시처럼 1개 단위로 줄바꿈을 해준다.
- 이런 함수를 호출하는 코드에서도 같은 패턴으로 맞춰서 호출한다.
```java
new Poi(name,
        drawingId,
        x,
        y,
        z,
        description);
```

### Operator
- 많은 operator의 연산으로 줄바꿈이 필요한 경우, **operator 전에 줄바꿈한다.**
```java
int sumAbcd = a + b - c
        + d;
```
- 연산자의 경우는 줄바꿈이 필요한 위치부터 줄바꿈한다.

### Method chain
- Stream/Builder/RxJava 등 여러 함수를 chaining으로 사용하면서 줄바꿈이 필요한 경우, **`.`전에 줄바꿈한다.**
```java
ImageLoader.load(user.getProfileUrl())
        .placeholder(R.drawable.img_user_placeholder)
        .fitCenter()
        .into(binding.ivUser);
```



## Util/Helper/Manager
- 특정기능을 수행하거나 상태를 관리하거나 분리되어 동작을 수행하는 클래스에 대한 사용처별 이름을 정의한다.
### Util
- `public static void AAA`등으로 쓰이는 여러곳에서 사용되는 util성 기능을 모아둔 클래스
- `util` 패키지에 모두 모아둔다.
- 예) `FileUtil`, `ZipUtil`, `StringUtil`등

## if문
- `if (isChecked == false)` 와 같은 코드는 명백한 Lint warning이므로 사용하지 않는다.
```java
if (isUnchecked)
```

- 조건문에 `!`를 넣는것대신 아래와 같은 규칙으로 코드를 작성한다.

1. 조건문에서 체크되는 boolean 변수/함수는 항상 긍정문으로 작성한다.
   `if(isChecked())`
2. 이미 부정문으로 작성되어 있는 함수가 있다면 이를 재사용하여 함수를 작성한다.
   `if(isUnchekced())`
```java
private boolean isUnchekced() {
        return !isChecked();
}
```
3. 조건문 안의 내용은 중괄호(`{}`) 를 사용하여 정의한다.
```java
if(isUnchecked) {
  doSomething();
}
```

## ETC
- 테스트 코드는 하나의 행위를 검사해야 하며, 이 행위 단위로 비지니스 코드의 메서드가 정의 되어야 한다(SRP)
- 불변성을 위해 final 키워드를 지향해야 한다.
- 하드 코딩값 대신 상수 혹은 enum을 사용한다.
- null을 직접 다루지 않고 Optional을 사용한다.
- DRY 원칙을 지키며 중복을 제거한다.
