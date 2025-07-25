---
created: 2025-04-27
---
## Thread.sleep() 내부 동작 (한글 & 영어 요약)

**Thread.sleep()** 메서드는 자바에서 현재 실행 중인 쓰레드를 지정한 시간 동안 일시 정지시키는 기능을 합니다. 아래에 한글과 영어로 각각 핵심 내용을 정리합니다.

## **한글 요약**

- **현재 실행 중인 쓰레드 일시정지:**  
    Thread.sleep()은 호출된 쓰레드를 지정한 시간(밀리초/나노초) 동안 일시정지(TIMED_WAITING 상태) 시킵니다[3](https://jonghoonpark.com/2024/06/26/java-thread-sleep)[6](https://codegym.cc/ko/groups/posts/ko.659.javaui-thread-sleep-mesodeu).
    
- **CPU 자원 양도:**  
    정지된 동안 CPU를 점유하지 않으므로, OS 스케줄러가 다른 쓰레드나 프로세스에 CPU를 할당할 수 있습니다[1](https://stackoverflow.com/questions/24358989/how-thread-sleep-works-internally)[4](https://www.digitalocean.com/community/tutorials/thread-sleep-java).
    
- **정확한 시간 보장 불가:**  
    실제 정지 시간은 시스템 타이머와 스케줄러에 따라 달라질 수 있으며, 지정한 시간보다 더 오래 걸릴 수 있습니다[1](https://stackoverflow.com/questions/24358989/how-thread-sleep-works-internally)[6](https://codegym.cc/ko/groups/posts/ko.659.javaui-thread-sleep-mesodeu).
    
- **예외 처리:**  
    sleep() 도중 다른 쓰레드가 interrupt()를 호출하면 InterruptedException이 발생합니다[2](https://www.lambdatest.com/blog/sleep-java-method/)[5](https://codegym.cc/groups/posts/threadsleep-method-in-java).
    
- **동기화 락 유지:**  
    sleep()은 wait()과 달리 동기화 락(모니터)을 해제하지 않습니다. 즉, synchronized 블록 안에서 sleep()을 호출해도 락을 계속 보유합니다[1](https://stackoverflow.com/questions/24358989/how-thread-sleep-works-internally).
    
- **static 메서드:**  
    sleep()은 static 메서드이므로, 항상 "현재 실행 중인" 쓰레드만 일시정지합니다. 객체를 통해 호출해도 동작은 동일합니다[3](https://jonghoonpark.com/2024/06/26/java-thread-sleep).
    

## **영어 요약 (How Thread.sleep() works internally)**

- **Pauses the current thread:**  
    `Thread.sleep()` pauses the currently executing thread for the specified duration (milliseconds and optionally nanoseconds)[4](https://www.digitalocean.com/community/tutorials/thread-sleep-java)[5](https://codegym.cc/groups/posts/threadsleep-method-in-java).
    
- **Releases CPU, not locks:**  
    While sleeping, the thread is not scheduled for execution and does not consume CPU time, but it retains any synchronization locks it holds (unlike `wait()`, which releases the monitor)[1](https://stackoverflow.com/questions/24358989/how-thread-sleep-works-internally)[4](https://www.digitalocean.com/community/tutorials/thread-sleep-java).
    
- **State transition:**  
    The thread moves to a TIMED_WAITING state, and after the specified time (or if interrupted), it transitions back to RUNNABLE, waiting for CPU scheduling[2](https://www.lambdatest.com/blog/sleep-java-method/)[4](https://www.digitalocean.com/community/tutorials/thread-sleep-java).
    
- **Interrupts:**  
    If another thread interrupts the sleeping thread, an `InterruptedException` is thrown immediately[1](https://stackoverflow.com/questions/24358989/how-thread-sleep-works-internally)[5](https://codegym.cc/groups/posts/threadsleep-method-in-java).
    
- **Timing precision:**  
    The actual sleep duration depends on the system timer and OS scheduler, so the thread may sleep longer than requested[1](https://stackoverflow.com/questions/24358989/how-thread-sleep-works-internally)[6](https://codegym.cc/ko/groups/posts/ko.659.javaui-thread-sleep-mesodeu).
    
- **Static method:**  
    `Thread.sleep()` is a static method, always affecting the current thread, regardless of how it is called[3](https://jonghoonpark.com/2024/06/26/java-thread-sleep).
    
- **Native implementation:**  
    Internally, `Thread.sleep()` typically calls a native method (e.g., `sleep0`), which interacts with the operating system's timers and thread scheduler[3](https://jonghoonpark.com/2024/06/26/java-thread-sleep).
    

## **추가 코드 예시 (Java)**

java

`public class SleepDemo {     public static void main(String[] args) throws InterruptedException {        long start = System.currentTimeMillis();        Thread.sleep(2000); // 2초 일시정지        System.out.println("Sleep time in ms = " + (System.currentTimeMillis() - start));    } }`

이 코드는 main 쓰레드를 약 2초 동안 일시정지시키고, 실제로 걸린 시간을 출력합니다[4](https://www.digitalocean.com/community/tutorials/thread-sleep-java)[5](https://codegym.cc/groups/posts/threadsleep-method-in-java)[6](https://codegym.cc/ko/groups/posts/ko.659.javaui-thread-sleep-mesodeu).

## **핵심 요약**

|특징|설명 (한글)|Description (English)|
|---|---|---|
|일시정지 대상|현재 실행 중인 쓰레드|Current executing thread|
|락 해제 여부|락(모니터) 해제 안 함|Does not release locks|
|예외 처리|interrupt() 시 InterruptedException 발생|Throws InterruptedException if interrupted|
|실제 정지 시간|OS 스케줄러/타이머에 따라 달라짐|Depends on OS scheduler/timer|
|메서드 타입|static (정적 메서드)|static (affects current thread only)|
|내부 구현|네이티브 메서드 통해 OS와 상호작용|Uses native methods to interact with OS|

**참고:**

- Thread.sleep()은 항상 현재 실행 중인 쓰레드만 정지시키며, OS 및 JVM의 스케줄러에 따라 실제 동작 시간이 달라질 수 있습니다[1](https://stackoverflow.com/questions/24358989/how-thread-sleep-works-internally)[3](https://jonghoonpark.com/2024/06/26/java-thread-sleep)[4](https://www.digitalocean.com/community/tutorials/thread-sleep-java)[6](https://codegym.cc/ko/groups/posts/ko.659.javaui-thread-sleep-mesodeu).
    

### Citations:

1. [https://stackoverflow.com/questions/24358989/how-thread-sleep-works-internally](https://stackoverflow.com/questions/24358989/how-thread-sleep-works-internally)
2. [https://www.lambdatest.com/blog/sleep-java-method/](https://www.lambdatest.com/blog/sleep-java-method/)
3. [https://jonghoonpark.com/2024/06/26/java-thread-sleep](https://jonghoonpark.com/2024/06/26/java-thread-sleep)
4. [https://www.digitalocean.com/community/tutorials/thread-sleep-java](https://www.digitalocean.com/community/tutorials/thread-sleep-java)
5. [https://codegym.cc/groups/posts/threadsleep-method-in-java](https://codegym.cc/groups/posts/threadsleep-method-in-java)
6. [https://codegym.cc/ko/groups/posts/ko.659.javaui-thread-sleep-mesodeu](https://codegym.cc/ko/groups/posts/ko.659.javaui-thread-sleep-mesodeu)
7. [https://velog.io/@codingskynet/sleep-%ED%95%A8%EC%88%98%EB%8A%94-%EC%96%B4%EB%96%BB%EA%B2%8C-%EB%8F%99%EC%9E%91%ED%95%98%EB%8A%94-%EA%B2%83%EC%9D%BC%EA%B9%8C](https://velog.io/@codingskynet/sleep-%ED%95%A8%EC%88%98%EB%8A%94-%EC%96%B4%EB%96%BB%EA%B2%8C-%EB%8F%99%EC%9E%91%ED%95%98%EB%8A%94-%EA%B2%83%EC%9D%BC%EA%B9%8C)
8. [https://learn.microsoft.com/en-us/dotnet/api/system.threading.thread.sleep?view=net-9.0](https://learn.microsoft.com/en-us/dotnet/api/system.threading.thread.sleep?view=net-9.0)
9. [https://seunghyunson.tistory.com/27](https://seunghyunson.tistory.com/27)
10. [https://docs.oracle.com/javase/7/docs/api/java/lang/Thread.html](https://docs.oracle.com/javase/7/docs/api/java/lang/Thread.html)
11. [https://softwareengineering.stackexchange.com/questions/328586/how-does-sleeping-a-thread-work](https://softwareengineering.stackexchange.com/questions/328586/how-does-sleeping-a-thread-work)
12. [https://kindbear.tistory.com/67](https://kindbear.tistory.com/67)
13. [https://coderanch.com/t/411155/java/Thread-sleep-working-expected](https://coderanch.com/t/411155/java/Thread-sleep-working-expected)
14. [https://www.tutorialspoint.com/what-is-the-use-of-thread-sleep-method-in-java](https://www.tutorialspoint.com/what-is-the-use-of-thread-sleep-method-in-java)
15. [https://www.daleseo.com/sleep-interrupt-join/](https://www.daleseo.com/sleep-interrupt-join/)
16. [https://steady-coding.tistory.com/543](https://steady-coding.tistory.com/543)
17. [https://harrydony.tistory.com/421](https://harrydony.tistory.com/421)
18. [https://happyzodiac.tistory.com/15](https://happyzodiac.tistory.com/15)
19. [https://pvs-studio.com/en/docs/warnings/v6095/](https://pvs-studio.com/en/docs/warnings/v6095/)
20. [https://www.freecodecamp.org/news/non-blocking-thread-sleep-on-jvm/](https://www.freecodecamp.org/news/non-blocking-thread-sleep-on-jvm/)

---

Perplexity로부터의 답변: [pplx.ai/share](https://www.perplexity.ai/search/pplx.ai/share)