# 여행경로

출처: https://programmers.co.kr/learn/courses/30/lessons/43164

## 지문

주어진 항공권을 모두 이용하여 여행경로를 짜려고 합니다. 항상 ICN 공항에서 출발합니다.

항공권 정보가 담긴 2차원 배열 tickets가 매개변수로 주어질 때, 방문하는 공항 경로를 배열에 담아 return 하도록 solution 함수를 작성해주세요.

### 제한사항
- 모든 공항은 알파벳 대문자 3글자로 이루어집니다.
- 주어진 공항 수는 3개 이상 10,000개 이하입니다.
- tickets의 각 행 [a, b]는 a 공항에서 b 공항으로 가는 항공권이 있다는 의미입니다.
- 주어진 항공권은 모두 사용해야 합니다.
- 만일 가능한 경로가 2개 이상일 경우 알파벳 순서가 앞서는 경로를 return 합니다.
- 모든 도시를 방문할 수 없는 경우는 주어지지 않습니다.

### 입출력 예

| tickets	| return |
| ------- | ------ |
| [[ICN, JFK], [HND, IAD], [JFK, HND]] | [ICN, JFK, HND, IAD] |
| [[ICN, SFO], [ICN, ATL], [SFO, ATL], [ATL, ICN], [ATL,SFO]]	| [ICN, ATL, ICN, SFO, ATL, SFO] |

### 입출력 예 설명

예제 #1

[ICN, JFK, HND, IAD] 순으로 방문할 수 있습니다.

예제 #2

[ICN, SFO, ATL, ICN, ATL, SFO] 순으로 방문할 수도 있지만 [ICN, ATL, ICN, SFO, ATL, SFO] 가 알파벳 순으로 앞섭니다.

## 해설

DFS를 이용해서 모든 정점이 아닌 '경로'를 순회하는 문제입니다. 보통 DFS 문제들은 모든 정점을 순회하면 끝이 나는데, 이 문제는 모든 경로를 한번씩 지나가야 해서 살짝 차이가 있습니다. 

우선 주어진 경로들을 그래프로 표현해야 합니다. 경로 리스트를 그래프의 표현방식으로 사용할 수도 있지만, 편의성과 시간복잡도를 위해서 인접 리스트로 바꾸어주었습니다. 

예제 #2를 인접 리스트로 바꾸면 다음과 같습니다. 

<인접 리스트>
- ATL -> ICN, SFO
- ICN -> ATL, SFO
- SFO -> ATL

그래프를 구축한 이후, ICN에서 출발해서 모든 경로를 순회해 보아야 합니다. 문제에 알파벳 순서가 앞서는 경로가 더 우선한다는 조건이 있기 때문에, DFS를 사용하지 않고 단순히 알파벳 순서로 빠른 도착지를 택하는 방법을 사용하면 될 것이라고 생각할 수도 있습니다. 

그러나 위 로직을 사용할 경우, 아래와 같은 테스트케이스가 실패하게 됩니다. 

| tickets	| return |
| ------- | ------ |
| [["ICN", "AAA"], ["ICN", "BBB"], ["BBB", "ICN"]] | ["ICN", "BBB", "ICN", "AAA"] |

![예시](https://i.imgur.com/CQ4Cq7W.png)

위와 같은 사례의 경우, AAA는 도착하는 항공편만이 있기 때문에 경로를 완성하기 위해서는 무조건 AAA를 마지막으로 끝내야 합니다. 단순히 알파벳 순으로 경로를 처리한다면 AAA로 간 이후에 다음 경로가 없어서 에러가 발생할 것입니다. 

이를 해결하기 위해서, AAA에서 더 이상 진행할 경로가 없으면 다시 출발점인 ICN으로 돌아와서 다른 경로로 진행하는 로직이 필요합니다. Depth-First Search를 이용하면 재귀적으로 위와 같은 순회 로직을 짤 수 있습니다. 

DFS로 경로를 순회하면서 사용한 항공편은 그래프 자체에서 지워주거나, 별도의 방법으로 visited 처리를 해줍니다. 만약 특정 경로의 DFS가 실패했다면, 다시 해당 경로를 살려주고 다른 경로로 DFS 순회를 시도해야 합니다. 

## 팁
인접 리스트에 PriorityQueue를 이용하면 별도의 Sort 명령어 없이도 방문 가능한 공항들을 알파벳 순서를 유지할 수 있습니다. 

## 소요시간

2시간

## Solution.java
~~~java
import java.util.*;

class Solution {
    static Stack<String> stack = new Stack();
    static HashMap<String, PriorityQueue<String>> graph = new HashMap();
    String[][] tickets;
    
    public String[] solution(String[][] tickets) {
        this.tickets = tickets;
        
        for(String[] ticket: tickets) {
            String from = ticket[0];
            String to = ticket[1];
            if(graph.containsKey(from)) {
                PriorityQueue<String> toList = graph.get(from);
                toList.add(to);
            } else {
                PriorityQueue<String> toList = new PriorityQueue();
                toList.add(to);
                graph.put(from, toList);
            }
        }
        
        String position = "ICN";
        stack.push(position);
        dfs(position);
        
        int idx = 0;
        String[] answer = new String[tickets.length + 1];
        for(String item: stack) {
            answer[idx] = item;
            idx++;
        }

        return answer;
    }
    
    private boolean dfs(String position) {
        if(stack.size() == tickets.length + 1) {
            return true;
        }
        
        PriorityQueue<String> toList = graph.get(position);
        if(toList == null) return false;
        
        LinkedList<String> queue = new LinkedList();
        queue.addAll(toList);
        
        while(!queue.isEmpty()) {
            String next = queue.poll();
            stack.push(next);
            toList.remove(next);
            if(dfs(next)) return true;
            toList.add(next);
            stack.pop();
        }
        
        return false;
    }
}
~~~
