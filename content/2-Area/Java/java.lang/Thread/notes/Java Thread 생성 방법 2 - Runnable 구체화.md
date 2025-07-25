---
created: 2025-04-27
---
러너블 인터페이스 구현하여 스레드를 생성함. 이 방식이 Thread Class를 상속하는 방식보다 유연하다.

```java

class MyRunnable implements Runnable {
    public void run() {
        System.out.println("Thread is running");
    }
}

Thread thread = new Thread(new MyRunnable());
thread.start();

```