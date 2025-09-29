---
tags:
  - 구현
  - 자료구조
  - 문자열
  - 집합과맵
  - 해시를쓴집합과맵
  - 정규표현식
created: 2025-09-13
reviewed: 2025-09-29
URL: https://www.acmicpc.net/problem/30034
반복수: "2"
복습: true
중요성:
레이팅: 실버5
메모:
---
## 25.09.26
하... 실버인데 바로못맞춤

```
import java.io.*;  
import java.util.*;  
class Main{  
    public static void main(String[] args) throws IOException {  
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));  
        Set<String> splitter = new HashSet<>();  
        int N = Integer.parseInt(br.readLine()); //문자 구분자 수  
        StringTokenizer st = new StringTokenizer(br.readLine());  
        for(int i=0 ; i<N ; i++){  
            splitter.add(st.nextToken());  
        }  
        int M = Integer.parseInt(br.readLine()); //숫자 구분자 수  
        st = new StringTokenizer(br.readLine());  
        for(int i=0 ; i<M ; i++){  
            splitter.add(st.nextToken());  
        }  
        int K = Integer.parseInt(br.readLine()); //병합지 수  
        st = new StringTokenizer(br.readLine());  
        for(int i=0 ; i<K ; i++){  
            splitter.remove(st.nextToken());  
        }  
        int S = Integer.parseInt(br.readLine()); //문자열 길이  
        String s = br.readLine();  
        for(String delimeter : splitter){  
            s = s.replaceAll(delimeter," ");  
        }  
        String[] results = s.split(" ");  
        StringBuilder sb2 = new StringBuilder();  
        for(String r : results){  
            if(r.equals("")) continue;  
            sb2.append(r+"\n");  
        }  
        System.out.print(sb2.toString());  
    }  
}
```

### r.equals("")로 비교해야 하는 이유

이 질문이 코드의 핵심 동작을 이해하는 데 매우 중요합니다.

s.split(" ")는 문자열을 공백(" ") 하나를 기준으로 자릅니다. 만약 원래 문자열에 구분자가 연속으로 나타나면 어떤 일이 벌어질까요?

예를 들어,

- 구분자(splitter)에 , 와 . 가 있다고 가정해봅시다.
    
- 입력 문자열 s가 "Hello,,world.123" 이었다고 해봅시다.
    

1. **replaceAll 실행 후:**
    
    - s = s.replaceAll(",", " ") -> s는 "Hello world.123" 이 됩니다. (쉼표 두 개가 공백 두 개로 바뀜)
        
    - s = s.replaceAll(".", " ") -> s는 "Hello world 123" 이 됩니다.
        
2. **split(" ") 실행:**
    
    - "Hello  world 123" 을 공백(" ") 하나를 기준으로 자르면 결과는 String[] 배열이 됩니다.
        
    - ["Hello", "", "world", "123"]
        
    - 여기서 "Hello"와 "world" 사이의 **연속된 두 개의 공백** 때문에, 그 사이에는 아무 내용이 없는 빈 문자열 ""이 생성됩니다. split은 구분자 '사이'의 값을 반환하기 때문입니다.

## 정규표현식 쓰는 방법
- 모든 구분자를 | (OR 연산자)로 연결하여 하나의 정규식 패턴을 만듭니다. 예: (,|;|.)
- 이 패턴을 사용하여 replaceAll을 한 번만 호출합니다.
- split 할 때, 공백 하나(" ")가 아닌 하나 이상의 공백("\\s+")을 기준으로 자르면 빈 문자열("")이 생기는 문제를 해결할 수 있습니다.

```
import java.io.*;
import java.util.*;
import java.util.regex.Pattern;
import java.util.stream.Collectors;

class MainImproved {
    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        Set<String> splitter = new HashSet<>();

        // 1. 구분자 입력받기 (이 부분은 동일)
        int N = Integer.parseInt(br.readLine());
        StringTokenizer st = new StringTokenizer(br.readLine());
        for (int i = 0; i < N; i++) {
            splitter.add(st.nextToken());
        }
        int M = Integer.parseInt(br.readLine());
        st = new StringTokenizer(br.readLine());
        for (int i = 0; i < M; i++) {
            splitter.add(st.nextToken());
        }
        int K = Integer.parseInt(br.readLine());
        st = new StringTokenizer(br.readLine());
        for (int i = 0; i < K; i++) {
            splitter.remove(st.nextToken());
        }

        int S = Integer.parseInt(br.readLine());
        String s = br.readLine();

        // 2. [개선점 1] 모든 구분자를 하나의 정규식으로 만들기
        // Pattern.quote()는 '.', '*', '+' 같은 정규식 특수문자를 일반 문자로 처리해줌
        String regex = splitter.stream()
                               .map(Pattern::quote) // 특수문자 이스케이프 처리
                               .collect(Collectors.joining("|")); // "|"로 구분자들을 연결

        // 3. [개선점 2] replaceAll을 단 한 번만 호출
        // 구분자가 없는 경우 regex가 비어있을 수 있으므로 확인
        if (!splitter.isEmpty()) {
            s = s.replaceAll(regex, " ");
        }

        // 4. [개선점 3] "\\s+"를 사용하여 하나 이상의 공백으로 분리
        // 이렇게 하면 연속된 공백이 하나의 구분자처럼 취급되어 빈 문자열이 생기지 않음
        String[] results = s.trim().split("\\s+");

        // 5. 결과 출력 (이제 빈 문자열 체크가 필요 없음)
        StringBuilder sb = new StringBuilder();
        for (String r : results) {
            // s가 공백으로만 이루어진 경우 split 결과로 [""]가 나올 수 있으므로 한 번 더 체크
            if (!r.isEmpty()) { 
                sb.append(r).append("\n");
            }
        }
        System.out.print(sb.toString());
    }
}
```