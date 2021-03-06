# 입국심사

출처: https://programmers.co.kr/learn/courses/30/lessons/43238

## 지문

n명이 입국심사를 위해 줄을 서서 기다리고 있습니다. 각 입국심사대에 있는 심사관마다 심사하는데 걸리는 시간은 다릅니다.

처음에 모든 심사대는 비어있습니다. 한 심사대에서는 동시에 한 명만 심사를 할 수 있습니다. 가장 앞에 서 있는 사람은 비어 있는 심사대로 가서 심사를 받을 수 있습니다. 하지만 더 빨리 끝나는 심사대가 있으면 기다렸다가 그곳으로 가서 심사를 받을 수도 있습니다.

모든 사람이 심사를 받는데 걸리는 시간을 최소로 하고 싶습니다.

입국심사를 기다리는 사람 수 n, 각 심사관이 한 명을 심사하는데 걸리는 시간이 담긴 배열 times가 매개변수로 주어질 때, 모든 사람이 심사를 받는데 걸리는 시간의 최솟값을 return 하도록 solution 함수를 작성해주세요.

제한사항
입국심사를 기다리는 사람은 1명 이상 1,000,000,000명 이하입니다.
각 심사관이 한 명을 심사하는데 걸리는 시간은 1분 이상 1,000,000,000분 이하입니다.
심사관은 1명 이상 100,000명 이하입니다.

입출력 예
n	| times	| return
--|-------|-------
6	| [7, 10]	| 28

입출력 예 설명
가장 첫 두 사람은 바로 심사를 받으러 갑니다.

7분이 되었을 때, 첫 번째 심사대가 비고 3번째 사람이 심사를 받습니다.

10분이 되었을 때, 두 번째 심사대가 비고 4번째 사람이 심사를 받습니다.

14분이 되었을 때, 첫 번째 심사대가 비고 5번째 사람이 심사를 받습니다.

20분이 되었을 때, 두 번째 심사대가 비지만 6번째 사람이 그곳에서 심사를 받지 않고 1분을 더 기다린 후에 첫 번째 심사대에서 심사를 받으면 28분에 모든 사람의 심사가 끝납니다.

## 해설

모든 입국자를 처리하는 데 필요한 최소 시간을 구하는 문제입니다. 입력되는 n(입국심사자)과 times 내의 원소가 구간의 최대값이 10<sup>9</sup>으로 크기 때문에, Integer Overflow를 조심하면서 문제를 풀어야 합니다.

최악의 케이스를 상정할 때, 10<sup>9</sup>명의 인원을 심사관 1명이 10<sup>9</sup>분 / 인원의 속도로 처리할 때 10<sup>18</sup>의 시간이 소요될 수 있습니다. 10<sup>3</sup>이 2<sup>10</sup>과 비슷한 값이기 때문에, 10<sup>18</sup>이라면 2<sup>60</sup>과 유사한 값을 가집니다.

Long은 -2<sup>63</sup> ~ 2<sup>63</sup>-1 까지의 값을 처리할 수 있기 때문에, 처리시간과 관련된 변수를 모두 long으로 선언해주면 Integer Overflow를 피할 수 있습니다.

심사관과 심사관 별로 1명 처리에 필요한 시간이 times로 주어진 상태에서, 우선 최선의 상황(모든 심사관이 1분에 1명을 처리)을 가정하여 처리시간의 lower bound를 설정해줍니다.

~~~java
long from = n / times.length;
~~~

최악의 상황인 경우 위와 같이 모든 심사관이 10<sup>9</sup>분에 한명을 처리합니다. 이 경우 처리시간의 upper bound는 다음과 같이 구할 수 있습니다. 10<sup>9</sup>분이 얼마나 긴 시간인지 궁금해서 찾아봤는데, 약 1901년 정도네요. 엄청난 심사관입니다.

~~~java
long to = 1000000000L * n / times.length;
~~~

이제 우리가 구한 lower bound과 upper bound 사이에 답이 존재한다는 것을 알 수 있습니다. 이분 탐색을 통해서 범위를 좁혀나가 봅시다.

문제의 요구사항은 인원과 처리속도가 주어졌을때 시간을 구하는 것이지만, 반대로 이분탐색시에는 처리속도와 시간이 주어졌을 때 인원을 구해서 이분탐색을 수행합니다.

mid = (from + to) / 2 와 같이 구간의 중간에 점을 잡은 이후, 이 시간이 주어졌을때 처리가능한 인원이,
- n 보다 같거나 높다면:
  - 더 적은 시간 안에 해결이 가능
  - from ~ mid까지의 구간을 다시 이분탐색
- n 보다 작다면:
  - 더 많은 시간이 있어야 해결 가능
  - (mid+1) ~ to까지의 구간을 다시 이분탐색

답에 근접한 이후, 최종적으로 from이 1이 증가하면서 to와 같아지는 지점에서 while문을 탈출하고 from == to == answer가 성립하게 됩니다.

위와 같이 탐색을 진행하면 탐색구간을 좁혀가서 O(log<sub>2</sub>n)의 시간복잡도로 최적의 해를 찾을 수 있습니다.

## Solution.java
~~~java
class Solution {
    public long solution(int n, int[] times) {
        long from = n / times.length;
        long to = 1000000000L * n / times.length;

        while(from < to) {
            long mid = (from + to) / 2;
            if(numProcessed(times, mid) < n) {
                from = mid + 1;
            }
            else {
                to = mid;
            }
        }

        return from;
    }

    public long numProcessed(int[] times, long time) {
        long output = 0;
        for(int i = 0; i < times.length; i++) {
            output += (time / times[i]);
        }
        return output;
    }
}
~~~
