---
tags: 
created: 2025-08-28
---
```
import java.util.*;

// 그래프의 노드를 표현하는 클래스
// 이 노드는 특정 노드(targetNode)까지의 거리(distance) 정보를 담고 있음
// 우선순위 큐에서 거리가 짧은 순으로 정렬하기 위해 Comparable 인터페이스를 구현
class Node implements Comparable<Node> {
    int targetNode;
    int distance;

    public Node(int targetNode, int distance) {
        this.targetNode = targetNode;
        this.distance = distance;
    }

    // 거리가 짧은 것이 높은 우선순위를 가지도록 compareTo 메서드를 오버라이드
    @Override
    public int compareTo(Node other) {
        return this.distance - other.distance;
    }
}

public class DijkstraTemplate {
    
    // 무한대를 의미하는 값 (문제의 최대 간선 가중치 합보다 큰 값으로 설정)
    private static final int INF = Integer.MAX_VALUE;
    
    // 그래프 정보를 담을 인접 리스트
    // 각 ArrayList는 특정 노드와 연결된 다른 노드들의 정보를 담고 있음
    private static ArrayList<ArrayList<Node>> graph;
    
    // 최단 거리 테이블
    private static int[] shortestPath;

    public static void main(String[] args) {
        // --- 입력 예시 (문제에 맞게 수정) ---
        int numNodes = 6; // 노드 개수
        int numEdges = 11; // 간선 개수
        int startNode = 1; // 시작 노드 번호

        // 그래프 및 최단 거리 테이블 초기화
        graph = new ArrayList<>();
        for (int i = 0; i <= numNodes; i++) {
            graph.add(new ArrayList<>());
        }
        
        shortestPath = new int[numNodes + 1];
        Arrays.fill(shortestPath, INF); // 모든 거리를 무한대로 초기화

        // 간선 정보 입력 (시작 노드, 끝 노드, 가중치)
        // 예시: 1번 노드에서 2번 노드로 가는 비용이 2
        // graph.get(1).add(new Node(2, 2));
        int[][] edges = {
            {1, 2, 2}, {1, 3, 5}, {1, 4, 1},
            {2, 3, 3}, {2, 4, 2},
            {3, 2, 3}, {3, 6, 5},
            {4, 3, 3}, {4, 5, 1},
            {5, 3, 1}, {5, 6, 2}
        };

        for (int[] edge : edges) {
            graph.get(edge[0]).add(new Node(edge[1], edge[2]));
        }
        // --- 입력 끝 ---

        // 다익스트라 알고리즘 실행
        dijkstra(startNode);

        // 결과 출력
        System.out.println("--- 최단 거리 결과 ---");
        for (int i = 1; i <= numNodes; i++) {
            if (shortestPath[i] == INF) {
                System.out.println("노드 " + i + ": 도달할 수 없음");
            } else {
                System.out.println("노드 " + i + ": " + shortestPath[i]);
            }
        }
    }

    /**
     * 다익스트라 알고리즘을 수행하는 함수
     * @param start 시작 노드
     */
    private static void dijkstra(int start) {
        // 우선순위 큐 생성 (거리가 짧은 노드 순으로 자동 정렬)
        PriorityQueue<Node> pq = new PriorityQueue<>();

        // 1. 시작 노드 초기화
        shortestPath[start] = 0;
        pq.offer(new Node(start, 0)); // 큐에 시작 노드 추가 (자기 자신까지의 거리는 0)

        // 2. 큐가 빌 때까지 반복
        while (!pq.isEmpty()) {
            // 현재 가장 거리가 짧은 노드 정보를 꺼냄
            Node currentNode = pq.poll();
            int dist = currentNode.distance;
            int now = currentNode.targetNode;

            // 이미 처리된 노드라면 무시
            // (큐에 남아있는, 더 긴 경로로 도착한 경우를 건너뛰기 위함)
            if (shortestPath[now] < dist) {
                continue;
            }

            // 3. 현재 노드와 연결된 다른 인접 노드들을 확인
            for (Node nextNode : graph.get(now)) {
                int cost = shortestPath[now] + nextNode.distance; // 현재 노드를 거쳐 가는 비용

                // 현재 노드를 거쳐 가는 것이 기존 최단 거리보다 더 짧다면
                if (cost < shortestPath[nextNode.targetNode]) {
                    shortestPath[nextNode.targetNode] = cost; // 최단 거리 갱신
                    pq.offer(new Node(nextNode.targetNode, cost)); // 갱신된 정보를 큐에 추가
                }
            }
        }
    }
}
```
#### **알고리즘 진행 순서**

**초기 설정**

1. `shortestPath` 배열을 `INF`로 채웁니다.
2. 시작 노드의 `shortestPath` 값을 **0**으로 설정합니다.
3. 우선순위 큐에 시작 노드 `new Node(start, 0)`을 넣습니다.

**반복문 (`while !pq.isEmpty()`)**

1. 우선순위 큐에서 **가장 거리가 짧은 노드**(`currentNode`)를 꺼냅니다.
2. 꺼낸 노드의 거리가 `shortestPath`에 기록된 값보다 크다면, 이미 더 빠른 경로가 발견된 것이므로 **무시하고 건너뜁니다(`continue`)**. (이 부분이 효율성을 높이는 핵심!)
3. 꺼낸 노드와 **연결된 모든 인접 노드**들을 순회합니다.
4. 인접 노드 각각에 대해, 현재 노드를 거쳐 가는 것이 기존의 최단 거리보다 짧은지 확인합니다.
    - **"현재 노드까지의 최단 거리 + 인접 노드까지의 간선 가중치"** < **"기존에 알려진 인접 노드까지의 최단 거리"**
5. 만약 더 짧다면, 인접 노드의 최단 거리를 **갱신**하고, 갱신된 정보를 우선순위 큐에 **새로 추가**합니다.