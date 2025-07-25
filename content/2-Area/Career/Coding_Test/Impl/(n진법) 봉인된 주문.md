---
created: 2025-07-13
---

https://school.programmers.co.kr/learn/courses/30/lessons/389481
- 오답 원인
	- 진법 변환 메서드 로직이 틀린게 주요 원인
- 보완
	- 진법 변환 로직은 코드로 바꿀때 잘 안됨. 연습해놓으면 앞으로 틀릴 가능성이 낮아질것으로 보임
## 틀린거

```java
import java.util.*;

class Solution {
    public String solution(long n, String[] bans) {
        /*
        주문서모음집의 주문은 11자 이하로 쓰여있다. 소문자.
        n이 엄청 크다 long n번째 주문을 찾는게 관건
        브루트포스하면 시초 발생 뻔함.
        금지된 주문을 딱 넣으면 원래 몇번째인지 알 수 있으면 좋을거같은데.
        알파벳은 26자 a(1) b c d e, f g h i j, k l m n o, p q r s t, u v w x y, z(26)
        2자리수: aa(27) ab(28/26+2) ac ... az(52/26+26) , ba(53), bb ....
        ae(26+5. = 31)
        3자리수: aaa(일단 이까지오는데...)
        */
        //n보다 크면 무시
        //n보다 작으면 ++하고 이후에 n-x해주고 이 n-x가 어떤주문인지 파악하면된다.
        //1. bans들의 순서 찾는다.
        //System.out.println((int)'a');//97이니까 -96계혹해주면됨
        List<Long> ints = new ArrayList<>();
        for(String ban : bans){
            ints.add(getNumber(ban));
        }
        ints.sort(Comparator.naturalOrder());
        
        long myN = n;
        Arrays.sort(bans);
        for(long i : ints){
            if(i < myN){
                myN++;
            }
        }
        return getString(myN+1);
    }
    
    public long getNumber(String str){
        long number = 0;
        for(int i = 0 ; i < str.length() ; i++ ){
            number +=  (int)Math.pow(26, i) * ((int)(str.charAt(i)) - 96);
        }
        return number;
    }
    
    public String getString(long number){
        System.out.println(number);
        //21을 어케 2진수로 1101
        /*
          2|5
          2|2--1
          2|1--0
          2|6
          2|3--0
          2|1--1
        */
        StringBuilder sb = new StringBuilder();
        while(26 <= number){
            sb.append((char)(number%26+96));
            //System.out.println("!"+(char)(number%26+96));
            number = number/26;
        }
        sb.append((char)(number + 96));
        sb.reverse();
        return sb.toString();
    }
}
```
## 정답 코드
```java
import java.util.*;

class Solution {
    public String solution(long n, String[] bans) {
        /*
        주문서모음집의 주문은 11자 이하로 쓰여있다. 소문자.
        n이 엄청 크다 long n번째 주문을 찾는게 관건
        브루트포스하면 시초 발생 뻔함.
        금지된 주문을 딱 넣으면 원래 몇번째인지 알 수 있으면 좋을거같은데.
        알파벳은 26자 a(1) b c d e, f g h i j, k l m n o, p q r s t, u v w x y, z(26)
        2자리수: aa(27) ab(28/26+2) ac ... az(52/26+26) , ba(53), bb ....
        ae(26+5. = 31)
        3자리수: aaa(일단 이까지오는데...)
        */
        //n보다 크면 무시
        //n보다 작으면 ++하고 이후에 n-x해주고 이 n-x가 어떤주문인지 파악하면된다.
        //1. bans들의 순서 찾는다.
        //System.out.println((int)'a');//97이니까 -96계혹해주면됨
        List<Long> ints = new ArrayList<>();
        for(String ban : bans){
            ints.add(getNumber(ban));
        }
        ints.sort(Comparator.naturalOrder());
        
        long myN = n;
        for(long i : ints){
            if(i <= myN){
                myN++;
            }else{
                break;
            }
        }
        return getString(myN);
    }
    
    
    /**
     * 문자열을 1-26 기반의 26진법 숫자로 변환합니다.
     * 예: "a" -> 1, "z" -> 26, "aa" -> 27, "ab" -> 28
     */
    public long getNumber(String str) {
        long number = 0;
        long powerOf26 = 1; // 26^0, 26^1, 26^2, ...
        // 문자열의 오른쪽부터 왼쪽으로 순회하며 계산해야 합니다.
        for (int i = str.length() - 1; i >= 0; i--) {
            // 'a'는 1, 'b'는 2, ..., 'z'는 26
            long charValue = str.charAt(i) - 'a' + 1;
            number += charValue * powerOf26;
            powerOf26 *= 26;
        }
        return number;
    }
	/*
     * 1-26 기반의 26진법 숫자를 문자열로 변환합니다.
     * 예: 1 -> "a", 26 -> "z", 27 -> "aa", 28 -> "ab"
     */
    public String getString(long number) {
        StringBuilder sb = new StringBuilder();
        // number가 0보다 클 때까지 반복합니다.
        while (number > 0) {
            // 0-25 범위의 나머지를 얻기 위해 (number - 1)을 합니다.
            // 이렇게 하면 1~26이 0~25에 매핑됩니다. (1->0, 26->25)
            long remainder = (number - 1) % 26;
            sb.append((char) ('a' + remainder));

            // 다음 자릿수 계산을 위해 (number - 1)을 26으로 나눕니다.
            number = (number - 1) / 26;
        }
        // 결과는 역순으로 만들어지므로 뒤집어서 반환합니다.
        return sb.reverse().toString();
    }
}
```