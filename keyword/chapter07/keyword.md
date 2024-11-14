## 1. RestControllerAdvice

**RestController 어노테이션이 붙은 컨트롤러에서 발생하는 예외(@ExceptionHandler)를 AOP를 적용해 예외를 전역적으로 처리할 수 있는 어노테이션**이다.<br>
Spring은 전역적으로 예외를 처리할 수 있는 방법 2가지를 제공한다.<br>
- ControllerAdvice
- RestControllerAdvice
<br>

ControllerAdvice 어노테이션 역시 Controller 어노테이션이 붙은 컨트롤러에서 발생하는 예외를 전역적으로 처리하는 어노테이션이다.
<br> 그럼 두 개의 차이점은 뭘까?

### ControllerAdvice vs RestControllerAdvice

- @Controller와 @RestController
- @RestControllerAdvice는 @ResponseBody가 붙어 있어 응답을 Json으로 내려준다.
- @ControllerAdvice는 @Component가 붙어 있어 ControllerAdvice가 선언된 클래스는 스프링 빈으로 등록된다.

아래 코드를 보자.

```java
@Slf4j
@RestControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(ArithmeticException.class)
    public ResponseEntity<String> handleArithmeticException(ArithmeticException e) {
        return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR).body(e.getMessage());
    }
    
    @ExceptionHandler(IllegalArgumentException.class)
    public ResponseEntity<String> handleIllegalArgument(IllegalArgumentException e) {
        return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR).body(e.getMessage());
    }
    
    @ExceptionHandler(IllegalArgumentException.class)
    public ResponseEntity<String> handleIllegalArgument(IllegalArgumentException e) {
        return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR).body(e.getMessage());
    }
    
    @ExceptionHandler(Exception.class)
    public ResponseEntity<Object> handleException(Exception e) {
        return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR).body(e.getMessage());
    }
    ...
}
```

- 예외들을 @RestControllerAdvice 어노테이션이 붙은 클래스에서 처리하여 예외처리 전용 클래스를 만들어 관리한다.

그렇다면, 본격적으로 **@RestControllerAdvice를 이용해 Spring 예외를 처리하는 방법**에 대해 알아보자.

### 1. Error Code 정의하기.

```java
public interface ErrorCode {
    String name();
    HttpStatus getHttpStatus();
    String getMessage();
}
```
(1)<br>
Error Code는 기본적으로 에러 이름, HTTP 상태 그리고 메세지를 가진다.<br>
인터페이스로 만들어 추상화하고, enum 클래스로 Error Code를 구현한다.

```java
@Getter
@RequiredArgsConstructor
public enum CommonErrorCode implements ErrorCode {

    INVALID_PARAMETER(HttpStatus.BAD_REQUEST, "Invalid parameter included"),
    RESOURCE_NOT_FOUND(HttpStatus.NOT_FOUND, "Resource not exists"),
    INTERNAL_SERVER_ERROR(HttpStatus.INTERNAL_SERVER_ERROR, "Internal server error"),
    ;

    private final HttpStatus httpStatus;
    private final String message;
}

@Getter
@RequiredArgsConstructor
public enum UserErrorCode implements ErrorCode {

    INACTIVE_USER(HttpStatus.FORBIDDEN, "User is inactive"),
    ;

    private final HttpStatus httpStatus;
    private final String message;
}
```

보통 Error Code는 전역적으로 사용되는 CommonErrorCode와 특정 도메인에 대해 구체적으로 내려가는 UserErrorCode로 나뉜다.

(2)
발생한 예외를 처리해줄 예외 클래스를 추가해주어야 한다.<br>
아래 코드를 보면 이해될 것이다.

```java
@Getter
@RequiredArgsConstructor
public class CommonException extends RuntimeException {

    private final ErrorCode errorCode;

}
```

이 경우는 런타임 예외를 상속받는 예외 클래스이다.

### 2. 에러 응답 클래스 생성하기.

이제 클라이언트에 응답으로 던져줄 Error 포맷을 만들기 위해 DTO 클래스를 만들어야 한다.

```java
@Getter
@Builder
@RequiredArgsConstructor
public class ErrorResponse {

    private final String code;
    private final String message;
}
```

### 3. @RestControllerAdvice 구현하기.

```java
@RestControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(CommonException.class) // ①
    public ResponseEntity<Object> handleCustomException(CustomException e) {
        ErrorCode errorCode = e.getErrorCode();
        return handleExceptionInternal(errorCode);
    }

    @ExceptionHandler({Exception.class}) // ②
    public ResponseEntity<Object> handleIllegalArgument(Exception e) {
        ErrorCode errorCode = CommonErrorCode.INTERNAL_SERVER_ERROR;
        return handleExceptionInternal(errorCode);
    }

    private ResponseEntity<Object> handleExceptionInternal(ErrorCode errorCode) {
        return ResponseEntity.status(errorCode.getHttpStatus())
                .body(makeErrorResponse(errorCode));
    }

    private ErrorResponse makeErrorResponse(ErrorCode errorCode) {
        return ErrorResponse.builder()
                .code(errorCode.name())
                .message(errorCode.getMessage())
                .build();
    }


    @ExceptionHandler(ArithmeticException.class) // ③
    public ResponseEntity<Object> handleArithmeticException(ArithmeticException e) {
        ErrorCode errorCode = CommonErrorCode.INTERNAL_SERVER_ERROR;
        return handleExceptionInternal(errorCode, e.getMessage());
    }


    private ResponseEntity<Object> handleExceptionInternal(ErrorCode errorCode, String message) {
        return ResponseEntity.status(errorCode.getHttpStatus())
                .body(makeErrorResponse(errorCode, message));
    }

    private ErrorResponse makeErrorResponse(ErrorCode errorCode, String message) {
        return ErrorResponse.builder()
                .code(errorCode.name())
                .message(message)
                .build();
    }
}
```

대부분 이렇게 구현하여 에러를 처리한다.

### 4. 에러 응답 확인하기.

다음과 같이 컨트롤러를 구현하고 해당 API를 호출해 에러 응답이 내려오는 것을 확인한다.

```java
@RestController
@RequiredArgsConstructor
public class UserController {

    @GetMapping("/users/{id}")
    public ResponseEntity<User> getUser() {
        throw new CommonException(UserErrorCode.INACTIVE_USER);
    }
}
```

### 장점

- 무분별한 try-catch가 없어 가독성이 좋다.
- 하나의 클래스로 모든 Controller에 대한 전역적인 예외처리가 가능하여 깔끔하다.
- 반복적인 코드를 제거할 수 있다.
- 예외가 발생한 경우 일정한 형식의 응답을 제공하기 좋다.

<br>
<br>

## 2. lombok

어노테이션 기반으로 코드를 자동완성 해주는 라이브러리이다.<br>
lombok을 이용하면 Getter, Setter, Equals, ToString 등과 같은 다양한 방면의 코드를 자동완성할 수 있다.

```java
// lombok 적용 전

public class Store extends Common {
    
    private String companyName;                        
    private String telephone;                                  
                              

    public String getCompanyName() {
        return companyName;
    }

    public void setCompanyName(String companyName) {
        this.companyName = companyName;
    }

    public String getTelephone() {
        return telephone;
    }
    
    public void setTelephone(String telephone) {
        this.telephone = telephone;
    }
    
}

// lombok 적용 후
@Getter
@Setter
public class Store extends Common {

    private String companyName;
    private String telephone;
    
}
```

### 장점

- 어노테이션 기반의 코드 자동 생성을 통한 생산성 향상
- 반복되는 코드를 제거하여 가독성 및 유지보수성 향상
- Getter, Setter 외에 빌더 패턴이나 로그 생성 등 다양한 방면으로 활용 가능

### lombok 기능 및 사용

#### 1) @Getter, @Setter

이를 클래스 이름 위에 적용하면 모든 변수들에 적용이 가능하고, 변수 이름 위에 적용하면 해당 변수에만 적용 가능하다.<br>
사용 예제는 위 코드를 참고하자.

#### 2) @AllArgsConstructor

모든 변수를 사용하는 생성자를 자동완성 해주는 어노테이션이다.

```java
@Getter
@AllArgsConstructor
public class Store extends Common {

    private String companyName;
    private String telephone;
    
    // 생성자를 자동 생성
    /*
    public Store(String companyName, String telephone) {
        this.companyName = companyName;
        this.telephone = telephone;
    }
     */
}
```

#### 3) @NoArgsConstructor

어떠한 변수도 사용하지 않는 기본 생성자를 자동완성 해주는 어노테이션이다.

```java
@Getter
@NoArgsConstructor
public class Store extends Common {

    private String companyName;
    private String telephone;
    
    // 생성자를 자동 생성
    /*
    public Store() {
       
    }
     */
}
```

#### 4) RequiredArgsConstructor

특정 변수만을 활용하는 생성자를 자동완성 해주는 어노테이션이다.

```java
@Getter
@RequiredArgsConstructor
public class Store extends Common {

    private String companyName;
    private String telephone;
    
    // 생성자를 자동 생성
    /*
    public Store(String companyName) {
       this.companyName = companyName;
    }
     */
}
```

#### 5) @Builder

해당 클래스의 객체의 생성에 Builder 패턴을 적용시킨다.

```java
@Getter
@NoArgsConstructor
public class Store extends Common {

    private String companyName;
    private String telephone;
    
    @Builder
    public Store(String companyName) {
        this.companyName = companyName;
    }
}
```

#### 6) @ToString

클래스의 변수들을 기반으로 ToString 메소드를 자동으로 완성해준다.<br>
출력을 원하지 않는 변수에는 @ToString.Exclude 어노테이션을 붙여주면 출력을 제외할 수 있다.<br>
만약 상위 클래스에 대해 적용하고자 한다면 상위 클래스에 @ToString(callSuper = true)를 적용하면 된다.

```java
@ToString
@AllArgsConstructor
public class Store extends Common {
    
    private String companyName;
    private String telephone;
    
}
```

#### 7) @Log4j, @Slf4J

Log4J(Slf4J) 설정을 이용하여 로그 기능 사용할 수 있다.