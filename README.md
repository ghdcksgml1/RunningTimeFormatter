# RunningTimeFormatter란?

> 기존 String.format의 정적인 format 방식을 개선한 정적 클래스이다.

# 사용 방법

### 기본 방법  

> 시간 단위는 MilliSeconds이다.

### 예시)

- 기존 String.format( )을 이용하는 방법

```java
Long milliSeconds = 123L;
System.out.println(String.format("%d (ms)", milliSeconds));

>> 실행결과 : 123 (ms)

Long milliSeconds = 1234L;
System.out.println(String.format("%d (ms)", milliSeconds));

>> 실행결과 : 1234 (ms)
```

<br/><br/>

- RunningTimeFormatter를 이용한 방법
```java
Long milliSeconds = 123L;
System.out.println(RunningTimeFormatter.format(milliSeconds));

>> 실행결과 : 123 (ms)

Long milliSeconds = 1234L;
System.out.println(RunningTimeFormatter.format(milliSeconds));

>> 실행결과 : 1(s) 234 (ms)
```

동적으로 초과하는 시간 단위가 있다면 문자를 추가해준다.

<br/><br/>

### TimeUnit 옵션 넣기

> TimeUnit을 통해 원하는 단위로 설정할 수 있다.


- Default (TimeUnit.MilliSeconds)
```java
Long milliSeconds = 1234L;
System.out.println(RunningTimeFormatter.format(milliSeconds));

>> 실행결과 : 1(s) 234 (ms)
```

<br/><br/>

- Seconds (TimeUnit.Seconds)
```java
Long milliSeconds = 1234L;
System.out.println(RunningTimeFormatter.format(milliSeconds, TimeUnit.Seconds));

>> 실행결과 : 20(m) 34(s)
```

TimeUnit.DAYS 까지 지원

<br/><br/>

### format에서 잘라내고 싶은 부분을 설정

> format에서 잘라내고 싶은 부분은 표시하는 것을 감추는 것을 뜻한다.

```java
Long 
```
