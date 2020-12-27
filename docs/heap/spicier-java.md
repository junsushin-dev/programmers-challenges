# 더 맵게

출처: https://programmers.co.kr/learn/courses/30/lessons/42626

## 지문
매운 것을 좋아하는 Leo는 모든 음식의 스코빌 지수를 K 이상으로 만들고 싶습니다. 모든 음식의 스코빌 지수를 K 이상으로 만들기 위해 Leo는 스코빌 지수가 가장 낮은 두 개의 음식을 아래와 같이 특별한 방법으로 섞어 새로운 음식을 만듭니다.
~~~
섞은 음식의 스코빌 지수 = 가장 맵지 않은 음식의 스코빌 지수 + (두 번째로 맵지 않은 음식의 스코빌 지수 * 2)
~~~
Leo는 모든 음식의 스코빌 지수가 K 이상이 될 때까지 반복하여 섞습니다.
Leo가 가진 음식의 스코빌 지수를 담은 배열 scoville과 원하는 스코빌 지수 K가 주어질 때, 모든 음식의 스코빌 지수를 K 이상으로 만들기 위해 섞어야 하는 최소 횟수를 return 하도록 solution 함수를 작성해주세요.

제한 사항
- scoville의 길이는 2 이상 1,000,000 이하입니다.
- K는 0 이상 1,000,000,000 이하입니다.
- scoville의 원소는 각각 0 이상 1,000,000 이하입니다.
- 모든 음식의 스코빌 지수를 K 이상으로 만들 수 없는 경우에는 -1을 return 합니다.

입출력 예

scoville	|K	|return
[1, 2, 3, 9, 10, 12]	|7	|2

입출력 예 설명

스코빌 지수가 1인 음식과 2인 음식을 섞으면 음식의 스코빌 지수가 아래와 같이 됩니다.

새로운 음식의 스코빌 지수 = 1 + (2 * 2) = 5

가진 음식의 스코빌 지수 = [5, 3, 9, 10, 12]

스코빌 지수가 3인 음식과 5인 음식을 섞으면 음식의 스코빌 지수가 아래와 같이 됩니다.

새로운 음식의 스코빌 지수 = 3 + (5 * 2) = 13

가진 음식의 스코빌 지수 = [13, 9, 10, 12]

모든 음식의 스코빌 지수가 7 이상이 되었고 이때 섞은 횟수는 2회입니다.

## 해설
Heap(또는 PriorityQueue)를 이용해서 받은 원소들을 오름차순으로 사용하는 문제입니다. 모든 원소의 값이 특정 기준치를 넘을 때까지 가장 덜 매운, 그리고 두번째로 덜 매운 음식을 섞어 주어야 합니다.

배열을 단순히 정렬하여도 처음에 받은 원소를 오름차순으로 정렬하여 사용할 수 있지만, 이 문제의 경우 섞은 음식이 새로운 원소로서 추가되어야 하기 때문에 Heap이 더욱 적합한 자료구조라고 할 수 있습니다. 단순한 배열 + 원소 추가 시에 정렬을 매번 새로 해줄 경우 Heap을 사용하는 것보다 더 연산속도가 비효율적이 될 것입니다.

Java의 경우 PriorityQueue 자료구조 클래스가 Java.util에 내장되어 있어서 간편하게 호출하여 사용할 수 있습니다. 단순히 PriorityQueue를 import 및 선언해주고, 모든 원소를 집어넣은 후 차례차례 poll()을 통해서 가장 덜 매운 원소를 추출하여 섞고 다시 PriorityQueue에 넣어주는 과정을 반복하면 됩니다.
~~~java
while(pq.peek() < K && pq.size() > 1) {
    Integer last = pq.poll();
    Integer secondLast = pq.poll();
    pq.add(last + secondLast * 2);
    answer++;
}
~~~

## 팁

for을 이용한 반복문 없이 입력값으로 받은 배열을 PriorityQueue에 넣기 위해서 Stream을 사용할 수 있습니다. Stream을 사용하여 받은 배열을 Collection 으로 바꾸어 PriorityQueue를 최초 선언 시에 넣어줄 경우 해당 원소들이 그대로 새로 생긴 PriorityQueue에 들어가게 됩니다.
~~~java
PriorityQueue<Integer> pq = new PriorityQueue<>(Arrays.stream(scoville).boxed().collect(Collectors.toList()));
~~~

## Solution.java
~~~java
import java.util.*;
import java.util.stream.Collectors;

class Solution {
    public int solution(int[] scoville, int K) {
        PriorityQueue<Integer> pq = new PriorityQueue<>(Arrays.stream(scoville).boxed().collect(Collectors.toList()));

        int answer = 0;

        while(pq.peek() < K && pq.size() > 1) {
            Integer last = pq.poll();
            Integer secondLast = pq.poll();
            pq.add(last + secondLast * 2);
            answer++;
        }

        if(pq.peek() < K) {
            answer = -1;
        }

        return answer;
    }
}
~~~
