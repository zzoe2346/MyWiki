https://hbase.tistory.com/295

```java
public class Test {  
    public static void main(String[] args) {  
        for (Thread t : Thread.getAllStackTraces().keySet()) {  
            System.out.println(t.getName() + ":" + t.getThreadGroup().getName());  
        }  
    
        Thread thread = new Thread(()->{  
            System.out.println("hh");  
        });  
        
        System.out.println(thread.getThreadGroup().getName());  
  
        notMain();  
    }  
  
    private static void notMain() {  
        Thread thread = new Thread(()->{  
            System.out.println("hh");  
        });  
        
        System.out.println(thread.getThreadGroup().getName());
    }  
}
```
