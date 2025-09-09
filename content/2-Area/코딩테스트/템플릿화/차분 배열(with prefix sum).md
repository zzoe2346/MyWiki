---
tags: 
created: 2025-09-08
---
https://school.programmers.co.kr/learn/courses/30/lessons/92344#qna

[2022 카카오 신입 공채 1차 온라인 코딩테스트 for Tech developers 문제해설 - tech.kakao.com](https://tech.kakao.com/posts/488#%EB%AC%B8%EC%A0%9C-6-%ED%8C%8C%EA%B4%B4%EB%90%98%EC%A7%80-%EC%95%8A%EC%9D%80-%EA%B1%B4%EB%AC%BC)

`Time Complexcity: O(N * M + K)`

```java
class Solution {
    public int solution(int[][] board, int[][] skill) {
        /*
        N <= 1000, M <= 1000
        skill.length <= 100
        O(N*M*100)
        */
        
        int[][] memo = new int[board.length+1][board[0].length+1];
        
        //K 아 이게 O(K)가 아니라 O(K*N)이네
        //개선: O(K*1). 즉, O(K)
        for(int[] skillElement : skill){
            int type = skillElement[0]; //1은 공격, 2는 회복
            int r1 = skillElement[1];
            int c1 = skillElement[2];
            int r2 = skillElement[3];
            int c2 = skillElement[4];
            int degree = skillElement[5]; //공격이나 회복 수치
            int value = 0;
            value = (type == 1) ? -1 * degree : degree;
        
            memo[r1][c1] += value;
            memo[r1][c2 + 1] -= value;
            memo[r2 + 1][c1] -= value;
            memo[r2 + 1][c2 + 1] += value;

            //O(N)
            // for(int i = r1 ; i <= r2 ; i++){
            //     memo[i][c1] += value;
            // }
            // for(int i = r1 ; i <= r2 ; i++){
            //     memo[i][c2+1] += (-1)*value;
            // }
        }
        //N*M
        for(int i=0 ; i<board.length ; i++){
            for(int j=1 ; j<board[0].length ; j++){
                memo[i][j] += memo[i][j-1];
            } 
        }
        //N*M
        for(int j=0 ; j<board[0].length ; j++){
            for(int i=1 ; i<board.length ; i++){
                memo[i][j] += memo[i-1][j];
            } 
        }
        int answer = 0;
        //N*M
        for(int i=0 ; i<board.length ; i++){
            for(int j=0 ; j<board[0].length ; j++){
                board[i][j] += memo[i][j];
                if(board[i][j] > 0) ++answer;
            }
        }
        
        return answer;
    }
}
```