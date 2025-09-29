---
tags:
  - 문자열
  - C
created: 2025-09-12
reviewed: 2025-09-29
URL: https://www.acmicpc.net/problem/5525
반복수: "1"
복습: true
중요성:
레이팅: 실버1
메모:
---
```
import java.io.BufferedReader;  
import java.io.IOException;  
import java.io.InputStreamReader;  
import java.util.*;  
  
class Main {  
    public static void main(String[] args) throws IOException {  
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));  
        //1. 0이 띄엄띄엄 N개 만큼 있는지 확인한다.  
        //2. 1번을 통과했다면 좌우가 I 인지 본다  
        int N = Integer.parseInt(br.readLine());  
        int M = Integer.parseInt(br.readLine());  
        String S = br.readLine();  
        int ans = 0;  
        int cnt = 0;  
        for(int i=1; i<=M-2;i++){  
            if (S.charAt(i - 1) == 'I' && S.charAt(i) == 'O' && S.charAt(i + 1) == 'I') {  
                ++cnt;  
                if(cnt == N){  
                    --cnt;  
                    ++ans;  
                }  
                ++i;   // 이게 특이하다.@@@@@@
            }else{  
                cnt = 0;  
            }  
        }  
        System.out.println(ans);  
    }  
}
```