# 🧮 RunningTimeFormatter란?

> 기존 String.format의 정적인 format 방식을 개선한 정적 클래스이다.

# 🧮 사용 방법

### 🔗 class를 생성 -> 복붙

> 자세한 주석과 사용법은 [RunningTimeFormatter.java](https://github.com/ghdcksgml1/RunningTimeFormatter/blob/main/RunningTimeFormatter.java) 파일 참고

```java
import java.util.HashMap;
import java.util.Map;
import java.util.concurrent.TimeUnit;

public class RunningTimeFormatter {

    private static final Map<String, Integer> TIME_UNIT_BINARY_VALUE = new HashMap<>() {
        {
            put("MILLISECONDS", 0B00001);
            put("SECONDS", 0B00010);
            put("MINUTES", 0B00100);
            put("HOURS", 0B01000);
            put("DAYS", 0B10000);
        }
    };
    private static final String[] TIME_FORMAT = {"(ms)", "(s)", "(m)", "(h)", "(d)"};
    private static final Long[] TIME_VALUE    = { 1000L,   60L,   60L,   24L, 100000L};

    /**
     * @param runningTime : Long 타입의 실행시간 (형식을 변환시킬 대상)
     * @param timeUnit    : 변경할 시간 단위 (MilliSeconds ~ Days)
     * @return
     */
    public static String format(Long runningTime, TimeUnit timeUnit) {
        int bitmask = getBitmask(timeUnit);

        return generateStringFromBitMask(bitmask, runningTime);
    }

    /**
     * @param runningTime (, TimeUnit.MILLISECONDS)
     * @return
     */
    public static String format(Long runningTime) {
        int bitmask = TIME_UNIT_BINARY_VALUE.get("MILLISECONDS");

        return generateStringFromBitMask(bitmask, runningTime);
    }

    /**
     * @param runningTime : Long 타입의 실행시간 (형식을 변환시킬 대상)
     * @param timeUnit    : 변경할 시간 단위 (MilliSeconds ~ Days)
     * @param startIndex  : 시간 단위로 부터 잘라내고 싶은 단위의 개수
     * @return
     */
    public static String format(Long runningTime, TimeUnit timeUnit, int startIndex) {
        Long cpyRunningTime = runningTime;

        if (!(startIndex >= 0 && startIndex < TIME_VALUE.length)) {
            throw new IllegalArgumentException("startIndex는 0~4 사이의 값 입니다.");
        }

        int bitmask = getBitmask(timeUnit);
        for (int i = 0; bitmask != 0; i++) {
            if (bitmask % 2 == 1) {
                if (i + startIndex >= TIME_VALUE.length) {
                    throw new IllegalArgumentException("잘못된 startIndex 값 입니다.");
                }

                int index = i + startIndex;
                bitmask = (int) Math.pow(2, index);
                for ( ; i < index; i++) {
                    cpyRunningTime /= TIME_VALUE[i];
                }

                break;
            }
            bitmask /= 2;
        }

        return generateStringFromBitMask(bitmask, cpyRunningTime);
    }

    // TimeUnit을 Bitmask로 바꿔주는 메소드
    private static int getBitmask(TimeUnit timeUnit) {
        int bitmask; 

        switch (timeUnit) {
            case MILLISECONDS -> bitmask = TIME_UNIT_BINARY_VALUE.get("MILLISECONDS"); // (d), (h), (m), (s), (ms)
            case SECONDS      -> bitmask = TIME_UNIT_BINARY_VALUE.get("SECONDS");      // (d), (h), (m), (s)
            case MINUTES      -> bitmask = TIME_UNIT_BINARY_VALUE.get("MINUTES");      // (d), (h), (m)
            case HOURS        -> bitmask = TIME_UNIT_BINARY_VALUE.get("HOURS");        // (d), (h)
            case DAYS         -> bitmask = TIME_UNIT_BINARY_VALUE.get("DAYS");         // (d)
            default           -> bitmask = 0B00000; //
        }

        return bitmask;
    }

    private static String generateStringFromBitMask(int bitmask, Long runningTime) {
        StringBuffer sb = new StringBuffer("");
        Long cpyRunningTime = runningTime;

        for (int i = 0; bitmask != 0; i++) {
            if (bitmask % 2 == 1) {
                for (; cpyRunningTime != 0;i++) {
                    sb.insert(0, " " + (cpyRunningTime % TIME_VALUE[i]) + TIME_FORMAT[i]);
                    cpyRunningTime /= TIME_VALUE[i];
                }
            }
            bitmask /= 2;
        }

        return sb.toString();
    }
}
```

### 🔗 기본 방법  

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

### 🔗 TimeUnit 옵션 넣기

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

### 🔗 format에서 잘라내고 싶은 부분을 설정

> format에서 잘라내고 싶은 부분은 표시하는 것을 감추는 것을 뜻한다.

```java
// 기존 방식
Long milliSeconds = 1234L;
System.out.println(RunningTimeFormatter.format(milliSeconds, TimeUnit.MILLISECONDS));

>> 실행결과 : 1(s) 234(ms)

// 잘라내는 방식
Long milliSeconds = 1234L;
System.out.println(RunningTimeFormatter.format(milliSeconds, TimeUnit.MILLISECONDS, 1));

>> 실행결과 : 1(s)
```

다음과 같이  startIndex를 1로 설정했으므로 MilliSeconds 기준으로 1개의 단위가 제거 되었다.

<br/><br/>

### 🔗 붙이는 기호를 바꾸고 싶을때

> (ms) , (s) 같은 기호가 아니라 밀리초, 초 등으로 바꾸고 싶다면 아래와 같이 간편하게 바꿀 수 있다.

먼저, 코드의 TIME_FORMAT을 찾는다.

<img width="709" alt="스크린샷 2023-07-22 오전 11 10 14" src="https://github.com/ghdcksgml1/RunningTimeFormatter/assets/79779676/0acb3e7c-caac-421c-9d33-7872f53e5e8c">

각각 바꿔준다.

```java
    // 변경 전
    private static final String[] TIME_FORMAT = {"(ms)", "(s)", "(m)", "(h)", "(d)"};
    private static final Long[] TIME_VALUE    = { 1000L,   60L,   60L,   24L, 100000L};

    // 변경 후
    private static final String[] TIME_FORMAT = {"밀리초", "초", "분", "시", "일"};
    private static final Long[] TIME_VALUE    = { 1000L,   60L,   60L,   24L, 100000L};


Long milliSeconds = 1234L;
System.out.println(RunningTimeFormatter.format(milliSeconds));

>> 실행결과 : 1초 234밀리초
```
