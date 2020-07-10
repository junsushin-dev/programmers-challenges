# 단어 변환

출처: https://programmers.co.kr/learn/courses/30/lessons/43163

## 지문

두 개의 단어 begin, target과 단어의 집합 words가 있습니다. 아래와 같은 규칙을 이용하여 begin에서 target으로 변환하는 가장 짧은 변환 과정을 찾으려고 합니다.
~~~
1. 한 번에 한 개의 알파벳만 바꿀 수 있습니다.
2. words에 있는 단어로만 변환할 수 있습니다.
~~~
예를 들어 begin이 hit, target가 cog, words가 [hot,dot,dog,lot,log,cog]라면 hit -> hot -> dot -> dog -> cog와 같이 4단계를 거쳐 변환할 수 있습니다.

두 개의 단어 begin, target과 단어의 집합 words가 매개변수로 주어질 때, 최소 몇 단계의 과정을 거쳐 begin을 target으로 변환할 수 있는지 return 하도록 solution 함수를 작성해주세요.

제한사항
- 각 단어는 알파벳 소문자로만 이루어져 있습니다.
- 각 단어의 길이는 3 이상 10 이하이며 모든 단어의 길이는 같습니다.
- words에는 3개 이상 50개 이하의 단어가 있으며 중복되는 단어는 없습니다.
- begin과 target은 같지 않습니다.
- 변환할 수 없는 경우에는 0를 return 합니다.

입출력 예
begin	| target | words | return
------|--------|-------|-------
hit	| cog	| [hot, dot, dog, lot, log, cog] | 4
hit	| cog	| [hot, dot, dog, lot, log]	| 0

입출력 예 설명

예제 1

문제에 나온 예와 같습니다.

예제 2

target인 cog는 words 안에 없기 때문에 변환할 수 없습니다.

## 해설

단어과 단어들 간의 변환관계를 그래프의 정점과 간선으로 표현하여 풀 수 있는 문제입니다. 서로 하나의 알파벳이 차이나는 단어들을 graph 상에서 연결된 것으로 표현하고, 완성된 graph에서 begin 단어에서 출발하여 breadth-first-search(BFS)로 target 단어까지의 최소거리를 순회하면 문제를 해결할 수 있습니다.

단어 간의 연결관계를 체크하기 위해서 차이나는 알파벳의 갯수를 체크하는 connection 함수를 정의하였습니다.
~~~
public boolean connection(String s1, String s2) {
    int diff = 0;
    for(int i = 0; i < s1.length(); i++) {
        if(s1.charAt(i) != s2.charAt(i)) diff += 1;
    }
    if(diff == 1) return true;
    else return false;
}
~~~

위 connection 함수를 이용해서 begin 단어와 words에 포함된 단어들에 대해서 그래프를 구축합니다. 그래프는 이 경우 인접 리스트 방식으로 표현하였습니다. 인접 리스트는 각 정점(문제의 경우에는 단어)에 대해서 자신과 인접한 정점을 리스트로 저장합니다.

~~~
HashMap<String, List<String>> graph = new HashMap<>();

graph.put(begin, new ArrayList<String>());
for(String word: words) {
    if(connection(begin, word)) {
        graph.get(begin).add(word);
    }
}

for(String word1: words) {
    graph.put(word1, new ArrayList<String>());
    for(String word2: words) {
        if(word1.equals(word2)) continue;
        else {
            if(connection(word1, word2)) {
                graph.get(word1).add(word2);
            }
        }
    }
}
~~~

예시)

지문의 예시 사례의 경우 아래와 같이 인접 리스트로 그래프를 표현할 수 있습니다.
~~~
hit - [hot]
hot - [dot, lot]
dot - [hot, dog, lot]
dog - [dot, log, cog]
lot - [hot, dot, log]
log - [dog, lot, cog]
cog - [dog, log]
~~~

위와 같이 구축된 그래프를 BFS를 통해 순회하면서 begin에서 출발하여 각 정점에 도달하는 거리를 체크합니다. BFS의 경우 현재 정점에 인접하였고, 방문하지 않은 정점을 queue에 넣어가면서 처리하는 방식으로 구현할 수 있습니다.

~~~
public int bfs(String begin, String target, HashMap<String, List<String>> graph) {
    HashMap<String, Integer> distance = new HashMap<>();
    for(String key: graph.keySet()) {
        distance.put(key, -1); // -1 means unvisited
    }

    LinkedList<String> queue = new LinkedList<>();
    queue.add(begin);
    distance.put(begin, 0);

    while(!queue.isEmpty()) {
        String currStr = queue.poll();
        Integer currDist = distance.get(currStr);
        for(String child: graph.get(currStr)) {
            if(distance.get(child) == -1) {
                queue.add(child);
                distance.put(child, currDist + 1);
            }
        }
    }

    return distance.getOrDefault(target, 0);
}
~~~

## 팁

HashMap.getOrDefault 함수를 사용하면 키가 존재하지 않는 경우에 기본값을 반환하도록 할 수 있습니다. 이 문제의 경우 words 안에 target 단어가 존재하지 않아서 graph 또는 distance 상에서 target 단어가 key로 존재하지 않는 경우를 처리하기에 좋습니다. 

## Solution.java
~~~
import java.util.HashMap;
import java.util.ArrayList;
import java.util.LinkedList;
import java.util.List;
import java.util.Map;

class Solution {
    public int solution(String begin, String target, String[] words) {
        HashMap<String, List<String>> graph = new HashMap<>();

        graph.put(begin, new ArrayList<String>());
        for(String word: words) {
            if(connection(begin, word)) {
                graph.get(begin).add(word);
            }
        }

        for(String word1: words) {
            graph.put(word1, new ArrayList<String>());
            for(String word2: words) {
                if(word1.equals(word2)) continue;
                else {
                    if(connection(word1, word2)) {
                        graph.get(word1).add(word2);
                    }
                }
            }
        }

        int answer = bfs(begin, target, graph);
        return answer;
    }

    public boolean connection(String s1, String s2) {
        int diff = 0;
        for(int i = 0; i < s1.length(); i++) {
            if(s1.charAt(i) != s2.charAt(i)) diff += 1;
        }
        if(diff == 1) return true;
        else return false;
    }

    public int bfs(String begin, String target, HashMap<String, List<String>> graph) {
        HashMap<String, Integer> distance = new HashMap<>();
        for(String key: graph.keySet()) {
            distance.put(key, -1); // -1 means unvisitied
        }

        LinkedList<String> queue = new LinkedList<>();
        queue.add(begin);
        distance.put(begin, 0);

        while(!queue.isEmpty()) {
            String currStr = queue.poll();
            Integer currDist = distance.get(currStr);
            for(String child: graph.get(currStr)) {
                if(distance.get(child) == -1) {
                    queue.add(child);
                    distance.put(child, currDist + 1);
                }
            }
        }

        return distance.getOrDefault(target, 0);
    }
}
~~~
