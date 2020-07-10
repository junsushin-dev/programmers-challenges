# 징검다리

출처: https://programmers.co.kr/learn/courses/30/lessons/43236

## 지문

출발지점부터 distance만큼 떨어진 곳에 도착지점이 있습니다. 그리고 그사이에는 바위들이 놓여있습니다. 바위 중 몇 개를 제거하려고 합니다.

예를 들어, 도착지점이 25만큼 떨어져 있고, 바위가 [2, 14, 11, 21, 17] 지점에 놓여있을 때 바위 2개를 제거하면 출발지점, 도착지점, 바위 간의 거리가 아래와 같습니다.

제거한 바위의 위치|	각 바위 사이의 거리|	거리의 최솟값
-|-|-
[21, 17]|	[2, 9, 3, 11]|	2
[2, 21]|	[11, 3, 3, 8]|	3
[2, 11]|	[14, 3, 4, 4]|	3
[11, 21]|	[2, 12, 3, 8]|	2
[2, 14]|	[11, 6, 4, 4]|	4
위에서 구한 거리의 최솟값 중에 가장 큰 값은 4입니다.

출발지점부터 도착지점까지의 거리 distance, 바위들이 있는 위치를 담은 배열 rocks, 제거할 바위의 수 n이 매개변수로 주어질 때, 바위를 n개 제거한 뒤 각 지점 사이의 거리의 최솟값 중에 가장 큰 값을 return 하도록 solution 함수를 작성해주세요.

제한사항
- 도착지점까지의 거리 distance는 1 이상 1,000,000,000 이하입니다.
- 바위는 1개 이상 50,000개 이하가 있습니다.
- n 은 1 이상 바위의 개수 이하입니다.

입출력 예
distance|	rocks|	n|	return
-|-|-|-
25|	[2, 14, 11, 21, 17]|	2|	4

## 해설

제거할 수 있는 바위의 수가 주어졌을 때, 징검다리 사이의 최소거리를 최대화시키는 문제입니다.

휴리스틱을 이용하여, 바위를 치우면서 가장 짧은 거리를 없애는 방식으로 시도할 경우 최적의 해에 도달하지 못할 수 있습니다.

예시)

출발점/바위/도착점: 0, 7, 12, 15, 21, 33
바위 간 거리: 7, 5, 3, 6, 12
치울 수 있는 바위: 2개

<짧은 거리부터 지워나가기>
1. 3이 가장 짧은 거리이고, 왼쪽과 오른쪽 중 5가 더 짧은 거리이기 때문에 5와 3 사이의 바위를 치워서 5와 3을 병합한다.

바위 간 거리: 7, 8, 6, 12

2. 6이 가장 짧은 거리이고, 왼쪽의 8이 더 짧은 거리이기 때문에 6과 8 사이의 바위를 치워서 6와 8을 병합한다.

바위 간 거리: 7, 14, 12 -> 최소거리 7

하지만 위 경우의 답은 최초 상황에서 7, 5사이 그리고 3,6사이의 바위 2개를 치우는 방법입니다.

바위 간 거리: 12, 9, 12 -> 최소거리 9

따라서 위의 휴리스틱으로는 풀이가 불가능하여, 다른 방법을 사용해야 합니다.

치울 수 있는 돌의 개수에서 최소거리를 뽑는 것과 반대방향으로 특정 최소거리를 얻기 위해서 치워야 할 돌의 개수는 최적의 숫자를 구할 수 있습니다.

예시)

바위 간 거리: 7, 5, 3, 6, 12
목표 최소거리: 9

1. 7이 9보다 작으므로 5와 병합
2. 합쳐진 (7+5)는 9보다 크거나 같으므로 통과
3. 3이 9보다 작으므로 6과 병합
4. 합쳐진 (3+6)은 9보다 크거나 같으므로 통과
5. 12는 9보다 크거나 같으므로 통과

위와 같이 최소거리 9를 만족하기 위해서는 필수적으로 돌 2개를 치우는 것이 필요한 것을 알 수 있습니다.

이를 이용하여 답이 될 수 있는 최소거리의 범위를 정하고, 이 범위를 이분탐색을 통해서 줄여 나가면 문제를 해결할 수 있습니다.

## 팁

이분탐색의 경우는 탈출조건을 적절히 설정하지 않으면 from과 to가 한칸 차이나는 최종 단계에서 무한 루프에 걸릴 수 있습니다.

예시)

답 = 3
from = 3
to = 4

mid = (3 + 4) / 2 = 3
답이 3이므로 mid를 대입했을 경우 만족,
따라서 mid 보다 크거나 같은 범위에 답 존재
from = mid
from = 3

위와 같이 from이 3에서 출발하여 다시 3으로 돌아온 것을 볼 수 있습니다.

이를 방지하기 위해서는 우선 while 문을 (from + 1 < to) 로 정의하거나, (from + 1 == to) 일 경우 탈출하도록 while 문을 구성해야 합니다.

이 경우 4를 대입한다면 조건을 만족하지 못하기 때문에, 이를 체크하고 만족할 경우 to를, 만족하지 못할 경우 from을 답으로 선택하면 이분탐색을 마무리할 수 있습니다.

~~~
while(from < to) {
    int mid = (from + to) / 2;
    if( numRocks(dists, mid, rocks.length) > n) {
        to = mid - 1;
    }
    else {
        if(from + 1 == to) break;
        from = mid;
    }
}

return (numRocks(dists, to, rocks.length) <= n) ? to : from;
~~~

## Solution.java
~~~
import java.util.*;

class Solution {
    public int solution(int distance, int[] rocks, int n) {
        List<Integer> dists = new ArrayList<>();
        Arrays.sort(rocks);
        int currPos = 0;
        for(int i = 0; i < rocks.length; i++) {
            dists.add(rocks[i] - currPos);
            currPos = rocks[i];
        }
        dists.add(distance - currPos);

        int from = Collections.min(dists);
        int to = 0;
        for(int dist: dists) {
            to += dist;
        }

        while(from < to) {
            int mid = (from + to) / 2;
            if( numRocks(dists, mid, rocks.length) > n) {
                to = mid - 1;
            }
            else {
                if(from + 1 == to) break;
                from = mid;
            }
        }

        return (numRocks(dists, to, rocks.length) <= n) ? to : from;
    }

    public int numRocks(List<Integer> dists, int minLen, int initRocks) {
        int numRock = 0;
        int currLen = 0;
        for(int dist: dists) {
            if( (currLen + dist) < minLen ){
                currLen += dist;
            }
            else {
                numRock += 1;
                currLen = 0;
            }
        }
        numRock -= 1; // last rock unnecessary
        return initRocks - numRock;
    }
}
~~~
