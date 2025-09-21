---
tags:
  - 취이코전
  - 정렬
created: 2025-09-18
reviewed:
URL: https://school.programmers.co.kr/learn/courses/30/lessons/42747
반복수: "0"
복습:
중요성:
레이팅: Lv. 2
메모:
---
```
class Solution {
    public int solution(int[] citations) {
        int max = -1;
        for(int c : citations) max = Math.max(max, c);
        
        int answer = 0;
        int std = 1;
        for(int i = 1 ; i<=max ; i++){//i는 인용기준 수// i = h 후보
            int cnt = 0;
            for(int c : citations){
                if(c >= i){
                    ++cnt;
                }
            }
            if(cnt  >= i) answer = i;
        }
        
        return answer;
    }
}
```

```
import java.util.Arrays;

public class Solution {
    private boolean isValid(int[] citations, int h) {
        int index = citations.length - h;
        return citations[index] >= h;
    }

    public int solution(int[] citations) {
        Arrays.sort(citations);
        for (int h = citations.length; h >= 1; h--) {
            if (isValid(citations, h)) return h;
        }
        return 0;
    }
}
```