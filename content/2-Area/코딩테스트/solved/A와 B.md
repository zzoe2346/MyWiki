---
tags:
  - 구현
  - 그리디
  - 문자열
  - C
created: 2025-09-13
reviewed: 2025-09-29
URL: https://www.acmicpc.net/problem/12904
반복수: "1"
복습: true
중요성:
레이팅: 골드5
메모: 역으로 생각하는 기법
---
```
import java.io.BufferedReader;  
import java.io.IOException;  
import java.io.InputStreamReader;  
import java.util.*;  
  
class Main {  
    public static void main(String[] args) throws IOException {  
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));  
        String S = br.readLine();  
        StringBuilder T = new StringBuilder(br.readLine());  
        // s -> t  
  
        while (true) {  
            if(S.length() == T.length()){  
                if (S.equals(T.toString())) {  
                    System.out.println(1);  
                    return;  
                }else{  
                    System.out.println(0);  
                    return;  
                }  
            }else{  
                if(T.charAt(T.length() - 1) == 'A'){  
                    T.deleteCharAt(T.length() - 1);  
                }else{  
                    T.deleteCharAt(T.length() - 1);  
                    T.reverse();  
                }  
            }  
        }  
    }  
}
```