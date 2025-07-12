Thread Class의 Subclass를 구현하면 된다. 그런데 이 방식은 실무에서 안쓰인다함. 자바에서는 다중 상속이 불가하기 때문에 유연하지 않아서 그렇다.

`run()`을 오버라이드 해서 원하는 동작을 수행 시킴. 이 `run()`은 Runnable Interface의 메서드.

```java
class MyThread extends Thread {
    public void run() {
        System.out.println("Thread is running");
    }
}

MyThread thread = new MyThread();
thread.start();
```


