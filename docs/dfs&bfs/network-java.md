# 타일 장식물

출처: https://programmers.co.kr/learn/courses/30/lessons/43162

## 지문
네트워크란 컴퓨터 상호 간에 정보를 교환할 수 있도록 연결된 형태를 의미합니다. 예를 들어, 컴퓨터 A와 컴퓨터 B가 직접적으로 연결되어있고, 컴퓨터 B와 컴퓨터 C가 직접적으로 연결되어 있을 때 컴퓨터 A와 컴퓨터 C도 간접적으로 연결되어 정보를 교환할 수 있습니다. 따라서 컴퓨터 A, B, C는 모두 같은 네트워크 상에 있다고 할 수 있습니다.

컴퓨터의 개수 n, 연결에 대한 정보가 담긴 2차원 배열 computers가 매개변수로 주어질 때, 네트워크의 개수를 return 하도록 solution 함수를 작성하시오.

제한사항
컴퓨터의 개수 n은 1 이상 200 이하인 자연수입니다.
각 컴퓨터는 0부터 n-1인 정수로 표현합니다.
i번 컴퓨터와 j번 컴퓨터가 연결되어 있으면 computers[i][j]를 1로 표현합니다.
computer[i][i]는 항상 1입니다.

입출력 예
n | computers	| return
--|-----------|-------
3	| [[1, 1, 0], [1, 1, 0], [0, 0, 1]]	| 2
3	| [[1, 1, 0], [1, 1, 1], [0, 1, 1]]	| 1

## 해설

그래프 상에서 이어진 노드의 집합을 하나의 네트워크로 보고, 서로 독립적인 네트워크의 개수를 세는 문제입니다.

이 문제는
1. DFS를 통해 이어진 노드를 순회하기
2. union - find를 통해서 집합을 만들기

두 방법으로 모두 해결할 수 있습니다.

1번 방법의 경우, 한번 출발할때 answer의 숫자를 1 증가시키고 DFS를 통해서 방문한 노드를 모두 visited로 체크합니다. 모든 노드를 순회한 이후에는 이어진 노드들은 모두 visited로 체크되어 있으므로, visited로 체크되어 있지 않는 노드를 새로 선정하여 다시 DFS로 순회를 시작합니다. 모든 노드가 visited로 체크되었을때, DFS를 출발한 횟수가 답이 됩니다.

2번 방법의 경우, 노드 간의 연결을 체크하면서 특정 집합으로 분류하는 방법입니다. 프로그래머스 홈페이지 상 문제의 분류는 dfs&bfs지만, 이 방법이 먼저 떠올라서 풀이에 사용해 보았습니다. 아래의 풀이에서는 일관적으로 집합 번호를 부여하기 위해서, 숫자가 낮은 쪽으로 무조건 parent를 세팅합니다. 각 노드의 parent는 스스로입니다.

예시)
- 1 - 2 - 3 || 4 와 같이 네트워크 연결 구성
  - parent 초기값: [1,2,3,4]
  - 1과 2의 연결을 체크하고, 값이 더 작은 1을 2의 부모로 설정
  - parent 상태: [1,1,3,4]
  - 2와 3의 연결을 체크하고, 값이 더 작은 2를 3의 부모로 설정
  - 2의 부모가 스스로가 아닌 1로 설정되어 있기 때문에, 이 사실을 체크하고 한번 더 거슬러올라가 최종 부모인 1로 3의 부모를 설정
  - parent 상태: [1,1,1,4]
  - 최종적으로 모든 노드가 Group1 또는 Group4로 분류됨

## Solution.java
~~~java
import java.util.HashSet;

class Solution {
    public int solution(int n, int[][] computers) {
        int[] parent = new int[n];
        for(int i = 0; i < n; i++) {
            parent[i] = i;
        }

        for(int r = 0; r < n; r++) {
            for(int c = 0; c < n; c++) {
                if(r != c && computers[r][c] == 1) {
                    union(parent, r, c);
                }
            }
        }

        for(int i = 0; i < n; i++) {
            find(parent, i);
        }

        HashSet<Integer> mySet = new HashSet<>();
        for(int i = 0; i < n; i++) {
            mySet.add(parent[i]);
        }

        return mySet.size();
    }

    public void union(int[] parent, int p1, int p2) {
        int q1 = find(parent, p1);
        int q2 = find(parent, p2);
        int big = Math.max(q1, q2);
        int small = Math.min(q1, q2);
        parent[big] = small;
    }

    public int find(int[] parent, int p) {
        if(parent[p] == p) return p;
        else {
            int temp = parent[p];
            parent[p] = find(parent, temp);
            return parent[p];
        }
    }
}
~~~
