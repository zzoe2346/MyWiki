---
tags: 
created: 2025-09-13
---

### 1. 0-1 BFS란?

0-1 BFS는 그래프 탐색 알고리즘의 한 종류로, **간선(edge)의 가중치가 0 또는 1인 경우에만** 사용 가능한 최단 경로 알고리즘입니다.

일반적인 ==BFS와 다익스트라(Dijkstra) 알고리즘의 특징을 결합한 형태==로 볼 수 있습니다.

| 알고리즘        | 특징                              | 시간 복잡도       | 사용 사례                       |
| :---------- | :------------------------------ | :----------- | :-------------------------- |
| **BFS**     | 모든 간선의 가중치가 1일 때 최단 경로를 보장      | O(V + E)     | 미로 찾기 (모든 칸 이동 비용이 1)       |
| **다익스트라**   | 모든 간선의 가중치가 양수일 때 최단 경로를 보장     | O(E log V)   | 도로망 최단 경로 (도로마다 소요 시간 다름)   |
| **0-1 BFS** | **간선 가중치가 0 또는 1일 때** 최단 경로를 보장 | **O(V + E)** | 순간이동(비용 0)과 걷기(비용 1)가 있는 문제 |

**핵심:** 0-1 BFS는 다익스트라처럼 가중치를 고려하지만, 가중치가 0과 1로 매우 제한적이기 때문에 우선순위 큐(PriorityQueue) 대신 **덱(Deque)** 을 사용하여 다익스트라보다 훨씬 빠른 `O(V + E)`의 시간 복잡도를 가집니다.

---

### 2. 핵심 아이디어: Deque의 마법

0-1 BFS의 핵심은 `Deque`(Double-Ended Queue)를 사용하는 것입니다. Deque는 큐의 양쪽 끝에서 데이터를 넣고 뺄 수 있는 자료구조입니다.

- **일반 BFS:** `Queue`를 사용하며, 새로 발견한 노드를 항상 **뒤에** 넣습니다. (`queue.add()`)
- **0-1 BFS:** `Deque`를 사용하며, 간선의 가중치에 따라 넣는 위치를 결정합니다.

1.  **가중치가 0인 간선으로 이동할 경우:**
    *   새로운 노드를 Deque의 **앞쪽(front)** 에 넣습니다. (`deque.addFirst()`)
    *   **의미:** 비용이 들지 않았으므로, 사실상 현재 노드와 같은 레벨(같은 거리)에 있는 것과 같습니다. 따라서 다른 노드들보다 **먼저 탐색**해야 합니다.

2.  **가중치가 1인 간선으로 이동할 경우:**
    *   새로운 노드를 Deque의 **뒤쪽(back)** 에 넣습니다. (`deque.addLast()`)
    *   **의미:** 비용이 1 증가했으므로, 다음 레벨(거리+1)로 넘어간 것입니다. 일반적인 BFS처럼 순서대로 탐색하면 됩니다.

이렇게 하면 Deque 내부는 항상 비용이 적은 노드들이 앞쪽에, 비용이 큰 노드들이 뒤쪽에 정렬된 상태를 유지하게 되어 다익스트라의 우선순위 큐와 유사한 역할을 더 효율적으로 수행할 수 있습니다.

---

### 3. Java 구현 예시 (백준 13549번: 숨바꼭질 3)

가장 대표적인 0-1 BFS 문제인 '[[숨바꼭질 3]]'을 예시로 코드를 작성해 보겠습니다.

- **문제:** 수빈이(N)가 동생(K)을 찾는다.
- **이동 방법:**
    1.  걷기: X-1 또는 X+1로 이동 (1초 소요) -> **가중치 1**
    2.  순간이동: 2*X로 이동 (0초 소요) -> **가중치 0**
- **목표:** 동생을 찾는 가장 빠른 시간(최소 비용)은?

``` 
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.ArrayDeque;
import java.util.Arrays;
import java.util.Deque;
import java.util.StringTokenizer;

public class Main {
    static final int MAX = 100001;

    // 위치와 시간을 저장할 클래스
    static class Node {
        int position;
        int time;

        public Node(int position, int time) {
            this.position = position;
            this.time = time;
        }
    }

    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StringTokenizer st = new StringTokenizer(br.readLine());
        int N = Integer.parseInt(st.nextToken()); // 수빈이 위치
        int K = Integer.parseInt(st.nextToken()); // 동생 위치

        // 각 위치까지의 최소 시간을 저장할 배열
        int[] dist = new int[MAX];
        Arrays.fill(dist, Integer.MAX_VALUE);

        // Deque 생성
        Deque<Node> deque = new ArrayDeque<>();

        // 시작점 설정
        dist[N] = 0;
        deque.addFirst(new Node(N, 0));

        while (!deque.isEmpty()) {
            Node current = deque.pollFirst(); // 항상 앞에서 꺼냄

            // 이미 더 빠른 경로를 찾았다면 건너뛰기 (중요: 성능 최적화)
            if (current.time > dist[current.position]) {
                continue;
            }

            // 동생을 찾았다면 종료
            if (current.position == K) {
                System.out.println(current.time);
                return;
            }

            // 1. 순간이동 (가중치 0)
            int nextPosTeleport = current.position * 2;
            if (nextPosTeleport < MAX && dist[nextPosTeleport] > current.time) {
                dist[nextPosTeleport] = current.time; // 시간은 그대로
                // 가중치가 0이므로 Deque의 맨 앞에 추가
                deque.addFirst(new Node(nextPosTeleport, current.time));
            }

            // 2. 걷기 (X-1, 가중치 1)
            int nextPosBack = current.position - 1;
            if (nextPosBack >= 0 && dist[nextPosBack] > current.time + 1) {
                dist[nextPosBack] = current.time + 1;
                // 가중치가 1이므로 Deque의 맨 뒤에 추가
                deque.addLast(new Node(nextPosBack, current.time + 1));
            }

            // 3. 걷기 (X+1, 가중치 1)
            int nextPosFront = current.position + 1;
            if (nextPosFront < MAX && dist[nextPosFront] > current.time + 1) {
                dist[nextPosFront] = current.time + 1;
                // 가중치가 1이므로 Deque의 맨 뒤에 추가
                deque.addLast(new Node(nextPosFront, current.time + 1));
            }
        }
    }
}
```

---

### 4. 0-1 BFS 범용 템플릿 (Java)

위 예시를 일반적인 그래프 문제에 적용할 수 있도록 템플릿으로 만들었습니다. 인접 리스트 방식으로 그래프를 표현하는 경우입니다.

```java
import java.util.*;

public class ZeroOneBFSTemplate {

    // 그래프의 간선을 표현하는 클래스
    static class Edge {
        int to;     // 도착 노드
        int weight; // 가중치 (0 또는 1)

        public Edge(int to, int weight) {
            this.to = to;
            this.weight = weight;
        }
    }
    
    // 탐색 상태를 저장하는 클래스 (노드 번호, 현재까지의 비용)
    static class State {
        int node;
        int cost;

        public State(int node, int cost) {
            this.node = node;
            this.cost = cost;
        }
    }
    
    // --- 템플릿 시작 ---
    public int zeroOneBfs(int startNode, int endNode, int numNodes, List<List<Edge>> graph) {
        // 각 노드까지의 최단 거리를 저장할 배열
        int[] dist = new int[numNodes + 1];
        Arrays.fill(dist, Integer.MAX_VALUE);

        // Deque 생성
        Deque<State> deque = new ArrayDeque<>();

        // 시작점 설정
        dist[startNode] = 0;
        deque.addFirst(new State(startNode, 0));

        while (!deque.isEmpty()) {
            State current = deque.pollFirst();

            // 목표 노드에 도달하면 비용 반환
            if (current.node == endNode) {
                return current.cost;
            }

            // 이미 더 짧은 경로가 발견되었다면 무시
            if (current.cost > dist[current.node]) {
                continue;
            }

            // 현재 노드와 연결된 모든 간선 탐색
            for (Edge edge : graph.get(current.node)) {
                int nextNode = edge.to;
                int weight = edge.weight;
                int newCost = current.cost + weight;

                // 새로운 경로가 기존 경로보다 짧은 경우에만 갱신
                if (newCost < dist[nextNode]) {
                    dist[nextNode] = newCost;
                    State nextState = new State(nextNode, newCost);
                    
                    if (weight == 0) {
                        // 가중치 0: Deque의 맨 앞에 추가
                        deque.addFirst(nextState);
                    } else { // weight == 1
                        // 가중치 1: Deque의 맨 뒤에 추가
                        deque.addLast(nextState);
                    }
                }
            }
        }
        
        // 목표 노드까지의 경로가 없는 경우 (혹은 특정 노드까지의 최단거리를 반환하고 싶을 때)
        return dist[endNode]; // or return -1;
    }
    // --- 템플릿 끝 ---

    // 템플릿 사용 예시
    public static void main(String[] args) {
        // 예시 그래프 구성
        int N = 6; // 노드 개수
        List<List<Edge>> graph = new ArrayList<>();
        for (int i = 0; i <= N; i++) {
            graph.add(new ArrayList<>());
        }
        
        // 1번 노드 -> 2번 노드 (비용 0)
        graph.get(1).add(new Edge(2, 0));
        // 1번 노드 -> 3번 노드 (비용 1)
        graph.get(1).add(new Edge(3, 1));
        // 2번 노드 -> 4번 노드 (비용 1)
        graph.get(2).add(new Edge(4, 1));
        // 3번 노드 -> 5번 노드 (비용 0)
        graph.get(3).add(new Edge(5, 0));
        // 4번 노드 -> 6번 노드 (비용 1)
        graph.get(4).add(new Edge(6, 1));
        // 5번 노드 -> 6번 노드 (비용 1)
        graph.get(5).add(new Edge(6, 1));

        ZeroOneBFSTemplate solver = new ZeroOneBFSTemplate();
        int start = 1;
        int end = 6;
        int shortestPathCost = solver.zeroOneBfs(start, end, N, graph);
        
        if (shortestPathCost == Integer.MAX_VALUE) {
            System.out.printf("%d에서 %d까지 가는 경로가 없습니다.\n", start, end);
        } else {
            System.out.printf("%d에서 %d까지의 최단 비용: %d\n", start, end, shortestPathCost); // 결과: 2
        }
    }
}
```

### 5. 요약: 언제 0-1 BFS를 사용해야 하는가?

==다음 두 가지 조건을 모두 만족할 때 0-1 BFS를 떠올리세요.==

1.  **최단 경로(최소 비용, 최단 시간 등)를 찾는 문제**
2.  그래프의 **모든 간선 가중치가 0 또는 1**로 구성된 문제

이 두 조건이 충족된다면, 다익스트라보다 훨씬 효율적인 0-1 BFS가 정답입니다.