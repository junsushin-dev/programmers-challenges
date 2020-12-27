# 섬 연결하기

출처: https://programmers.co.kr/learn/courses/30/lessons/42861

## 지문

n개의 섬 사이에 다리를 건설하는 비용(costs)이 주어질 때, 최소의 비용으로 모든 섬이 서로 통행 가능하도록 만들 때 필요한 최소 비용을 return 하도록 solution을 완성하세요.

다리를 여러 번 건너더라도, 도달할 수만 있으면 통행 가능하다고 봅니다. 예를 들어 A 섬과 B 섬 사이에 다리가 있고, B 섬과 C 섬 사이에 다리가 있으면 A 섬과 C 섬은 서로 통행 가능합니다.

제한사항

- 섬의 개수 n은 1 이상 100 이하입니다.
- costs의 길이는 ((n-1) * n) / 2이하입니다.
- 임의의 i에 대해, costs[i][0] 와 costs[i][1]에는 다리가 연결되는 두 섬의 번호가 들어있고, costs[i][2]에는 이 두 섬을 연결하는 다리를 건설할 때 드는 비용입니다.
- 같은 연결은 두 번 주어지지 않습니다. 또한 순서가 바뀌더라도 같은 연결로 봅니다. 즉 0과 1 사이를 연결하는 비용이 주어졌을 때, 1과 0의 비용이 주어지지 않습니다.
- 모든 섬 사이의 다리 건설 비용이 주어지지 않습니다. 이 경우, 두 섬 사이의 건설이 불가능한 것으로 봅니다.
- 연결할 수 없는 섬은 주어지지 않습니다.

입출력 예

n|	costs|	return
-|-|-
4|	[[0,1,1],[0,2,2],[1,2,5],[1,3,1],[2,3,8]]|	4

입출력 예 설명

costs를 그림으로 표현하면 다음과 같으며, 이때 초록색 경로로 연결하는 것이 가장 적은 비용으로 모두를 통행할 수 있도록 만드는 방법입니다.

![예시1](https://user-images.githubusercontent.com/32405358/103172839-b5aad500-4899-11eb-837d-28db293c0f26.png)

## 해설

Greedy Algorithm을 이용해서 섬들을 연결하는 경우의 수를 줄이는 문제입니다. 

Brute Force 방식으로 해결할 경우에는 n개의 섬을 연결하기 위해 n-1개의 경로가 연결되어야 하고, 모든 경우의 수를 따져본다면 주어진 경로의 개수를 m이라고 할 때, <sub>m</sub>C<sub>n-1</sub> 의 경우의 수가 나오는 게 됩니다. 

경로의 개수는 문제에서 명시적으로 주어져 있지 않지만, n개의 섬이 서로 모두 연결되어있다고 할 때, n * (n - 1) / 2 의 개수까지 존재할 수 있습니다. 
(자신이 아닌 모든 node와 연결하기 위해 n-1개의 경로가 있고, 이것에 n개의 node에 대해서 있으며, 경로가 양쪽으로 중복되기 때문에 위의 개수가 나옵니다)

위와 같은 최악의 상황에서는 경우의 수가 <sub>n<sup>2</sup></sub>C<sub>n-1</sub> 정도에 수렴하게 되는데, 이미 너무 과도한 연산이기 때문에 이 방법을 택할 수는 없다는 것을 알 수 있습니다. 

위 문제를 효율적으로 해결하기 위해서, Disjoint-set(=union-find) 자료구조과 sorting을 이용하도록 하겠습니다. 

[Disjoint-set(서로소 집합)](https://en.wikipedia.org/wiki/Disjoint-set_data_structure) 자료구조는 서로 겹치지 않는 집합들의 합집합 연산과, 각 원소가 같은 집합에 속하는 지 판별할 수 있는 연산을 지원합니다. 연산속도는 합집합 연산을 할수록 개선되는데, 최악의 경우에도 O(n) 정도입니다. 

문제에서의 '섬'을 위 자료구조의 '원소'로 보고, '연결된 섬의 그룹'을 '집합'으로 보아서 섬들의 연결 관계를 파악하는데 위 자료구조를 적용할 수 있습니다. 

최초 그룹의 갯수를 섬의 갯수인 n개로 보았을 때, 경로를 하나 연결할 때마다 그룹 두개가 하나로 합쳐지면서 그룹의 갯수가 1씩 줄어들게 됩니다. 따라서 n-1번 경로를 연결하면 무조건적으로 그룹이 1개가 남고, 모든 섬이 연결되게 됩니다. 
(단, 이미 연결된 그룹 내에서는 경로를 추가하지 않습니다.)

문제의 목표가 cost를 최소화하는 것이기 때문에, 두개의 다른 그룹을 연결시켜주는 경로 중 <strong>가장 cost가 낮은</strong> 경로를 연결하는 것이 유리합니다. 이때 이 경로가 서로 이미 연결된 섬들을 연결하지만 않는다면, cost가 낮은 순서로 경로를 연결해 나감으로서 cost를 최소화하며 모든 섬들을 연결할 수 있습니다.

따라서 위 Logic을 실현하기 위해서 일단 주어진 costs 안에서 경로들을 cost가 낮은 순으로 정렬합니다. 정렬된 경로들을 차례차례 검사하면서, Disjoint-set을 이용해서 쓸모가 없는(이미 연결된 섬들을 연결하는) 경로들을 생략해주면 모든 섬을 최소한의 cost로 연결할 수 있습니다. 

## Solution.java
~~~java
import java.util.Arrays;
import java.util.Comparator;

class Solution {
    public int solution(int n, int[][] costs) {
        Arrays.sort(costs, Comparator.comparingInt(bridge -> bridge[2]));
        
        Graph graph = new Graph(n);
        
        int answer = 0;
        
        for(int[] bridge: costs) {
            if(graph.isAllConnected()) break;
            int a = bridge[0];
            int b = bridge[1];
            int cost = bridge[2];
            if(graph.isConnected(a, b)) continue;
            graph.connect(a, b);
            answer += cost;
        }

        return answer;
    }
    
    class Graph {
        int[] parent;
        int groupCount;
        
        public Graph(int nodeCount) {
            parent = new int[nodeCount];
            groupCount = nodeCount;
            // initialize parent as self
            for(int i = 0; i < nodeCount; i++) {
                parent[i] = i;
            }
        }
        
        public void connect(int a, int b) {
            int aRoot = this.find(a);
            int bRoot = this.find(b);
            parent[aRoot] = bRoot;
            groupCount--;
        }
        
        private int find(int index) {
            if(parent[index] == index) return index;
            parent[index] = this.find(parent[index]);
            return parent[index];
        }
        
        public boolean isConnected(int a, int b) {
            return find(a) == find(b);
        }
        
        public boolean isAllConnected() {
            return this.groupCount == 1;
        }
    }
}
~~~
