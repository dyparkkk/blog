---
layout: default
title: Exception
parent: Java
nav_order: 1
---

# Exception
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## 예외 계층

<img src="../imgs/exception00.png" width="500" height="250">

- `Throwable` : 최상위 예외. Excetption과 Error
- `Error` : 메모리 부족이나 시스템 오류같은 애플리케이션에서 복구 불가능한 시스템 예외. 개발자가 잡으라고 만든 예외가 아님. 
  - 상위 예외를 잡으면 하위 예외도 잡힘 -> Throwable을 잡지 말아야 함
  - Unchecked Exception
- `Exception` : 체크 예외. 
  - 애플리케이션에서 사용할 수 있는 실직적인 최상위 예외
  - 하위의 RuntimeException을 제외한 모든 예외는 Checked Exception임
- `RuntimeException` : 언체크 예외. 런타임 에외라고도 불림
  - 컴파일러가 체크하지 않는 예외


## 기본 예외 규칙

1. 예외는 잡아서 처리하거나 던져야 함
2. 예외를 잡아서 처리하거나 던질 때 그 자식들도 함께 처리된다
    - catch, throw 둘 다 그 하위 예외도 처리해줌


**예외를 끝까지 처리하지 못했을 경우**
- java `main()` 쓰레드의 경우에는 예외 로그를 출력하면서 종료됨
- 웹 애플리케이션의 경우 WAS가 받아서 처리함, 보통 개발자가 지정한 오류페이지를 보여준다


## 체크 예외

- 장점 : 개발자가 실수로 예외를 누락하지 않도록 컴파일 오류로 잡아줌. 
- 단점 : 모든 체크 예외는 잡거나 던져야 함 -> 번거로움. 추가로 의존관계 문제가 발생하기도 함

### 무분별한 체크 예외의 문제점

1. 복구 불가능한 예외  
대부분의 예외는 복구 불가능하다.  
예를 들어 SQLException을 보면 DB문제, SQL문법 문제 등 다양한 문제가 있으며, 대부분은 복구 불가능하다. 따라서 이런 문제들은 로그를 남기고 개발자가 오류를 빠르게 인지하는 것이 필요하다. 서블릿 필터, 스프링 인터셉터, 스프링의
`ControllerAdvice`를 사용하면 이런 부분을 깔끔하게 공통으로 해결할 수 있다.

2. 의존 관계에 대한 문제
컨트롤러나 서비스는 처리할 수 없는 예외를 throw를 통해 밖으로 던지게 된다. 이때 특정 Exception을 코드로 선언해서 사용한다.  
하지만 Exception이 특정 라이브러리의 포함되어있을 때 컨트롤러나 서비스는 특정 라이브러리에 의존하게 되는 문제가 발생한다.  
예) `SQLException`의 경우 `java.sql.SQLException`을 의존하므로 JDBC가 아닌 다른 구현체로 변경할 경우 문제가 된다. 

## 예외 포함과 스택 트레이스

예외를 전환할 때 꼭 기존의 예외를 포함해야 함 !!   
그렇지 않으면 스택 트레이스를 확인할 때 심각한 문제가 발생함


- 로그를 출력할 때 마지막 파라미터에 예외를 넣어주면 스택 트레이스를 출력할 수 있음
  - 예) `log.info("message={}", message, ex)`

```java
public void call() {
  try {
    runSQL();
  } catch (SQLException e) {
    throw new RuntimeSQLException(e); //기존 예외(e) 포함
  }
}
```
- 예외를 포함시키지 않는다면 예외가 어디서, 어떻게 터졌는지 확인할 수 없는 문제 발생...

**잊지말고 전 예외를 넣어주자**