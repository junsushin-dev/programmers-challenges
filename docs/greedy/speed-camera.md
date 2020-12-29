# 단속카메라

출처: https://programmers.co.kr/learn/courses/30/lessons/42884

## 지문

고속도로를 이동하는 모든 차량이 고속도로를 이용하면서 단속용 카메라를 한 번은 만나도록 카메라를 설치하려고 합니다.

고속도로를 이동하는 차량의 경로 routes가 매개변수로 주어질 때, 모든 차량이 한 번은 단속용 카메라를 만나도록 하려면 최소 몇 대의 카메라를 설치해야 하는지를 return 하도록 solution 함수를 완성하세요.

제한사항
- 차량의 대수는 1대 이상 10,000대 이하입니다.
- routes에는 차량의 이동 경로가 포함되어 있으며 routes[i][0]에는 - i번째 차량이 고속도로에 진입한 지점, routes[i][1]에는 i번째 차량이 고속도로에서 나간 지점이 적혀 있습니다.
- 차량의 진입/진출 지점에 카메라가 설치되어 있어도 카메라를 만난것으로 간주합니다.
- 차량의 진입 지점, 진출 지점은 -30,000 이상 30,000 이하입니다.

입출력 예

routes|	return
-|-
[[-20,15], [-14,-5], [-18,-13], [-5,-3]]|	2

입출력 예 설명
- -5 지점에 카메라를 설치하면 두 번째, 네 번째 차량이 카메라를 만납니다.
- -15 지점에 카메라를 설치하면 첫 번째, 세 번째 차량이 카메라를 만납니다.

## 해설

Greedy Algorithm을 이용해서 최소값을 구하는 문제입니다.

차들은 특정 구간을 지나게 되는데, 당연히 많은 구간이 겹치는 위치에 카메라를 설치해나가야 최소한의 갯수로 설치가 가능할 것입니다. 

Brute Force 방식으로 다음과 같은 방법을 생각해봅니다. 
1. 모든 구간들을 검사하면서, 가장 많은 차가 지나가는 구간을 찾는다. 
2. 해당 구간에 카메라를 설치한다. 
3. 카메라가 설치되어서 처리된 구간을 리스트에서 제거한다. 
4. 다시 1로 돌아가서 반복한다. 

위와 같은 방식을 택할 경우, 최대 10000개의 구간을 반복적으로 검사해주어야 합니다. 한번 돌릴 때마다 몇개의 구간이 제거될 지는 모르겠지만, 한번에 1개씩만 제거되는 최악의 경우에는 10000번의 검사가 필요할 수도 있습니다. 

벌써 1억번의 연산이 예상되는데, 시간을 초과할 가능성이 높기 때문에 Greedy로 연산횟수를 줄여보도록 하겠습니다. 

생각을 바꾸어서, '가장 많은 구간을 처리하는 위치'가 아닌 '카메라가 하나라도 필수적으로 있어야 하는 위치'를 찾아보도록 합시다. 

1. 모든 구간을 시작하는 위치의 순서로 정렬합니다. 

| 차량 | 시작 | 끝 |
| - | - | - |
| A | -20 | 15 |
| B | -18 | -13 |
| C | -14 | -5 |
| D | -5  | -3 |

2. 가장 빠른 시작지점부터 구간을 하나씩 보면서 겹치는 구간이 존재하는지 체크합니다. 
3. A 차량과 B 차량의 겹치는 구간이 있을 경우, 두 차량을 한번에 처리가 가능합니다. 만약 다음 구간이 또한 겹친다면, 더 많은 차량을 한번에 처리할 수 있으므로 카메라를 아직 설치하지 않습니다.

| 차량 | 시작 | 끝 |
| - | - | - |
| A & B | -18 | -13 |
| C | -14 | -5 |
| D | -5  | -3 |

3. A & B가 겹치는 구간에 C 차량의 구간을 겹쳐봅니다.

| 차량 | 시작 | 끝 |
| - | - | - |
| A & B & C | -14 | -13 |
| D | -5  | -3 |

4. D구간을 A & B & C와 겹쳐봅니다. 겹치는 부분이 없기 때문에, A&B&C와 D는 하나의 카메라로 처리할 수 없습니다. 따라서 -13 위치에 카메라를 한개 설치합니다. 
5. 위의 과정을 보면, 구간을 놓치지 않는 한 최대한 오른쪽에 카메라를 배치하게 됨을 알 수 있습니다. 
6. 마지막으로 D구간이 남았으므로 D구간 맨 오른쪽 -3에 카메라를 설치합니다. 

위와같은 식으로 시작지점부터 순차적으로 처리하면, 구간의 최대갯수인 10000번의 연산 이내인 O(n)의 시간복잡도로 문제를 해결할 수 있습니다. 

## Solution.java
~~~java
import java.util.Arrays;
import java.util.Comparator;

class Solution {
    public int solution(int[][] routes) {
        for(int[] route: routes) {
            this.swap(route);
        }
        Arrays.sort(routes, Comparator.comparingInt(item -> item[0]));
        Interval interval = new Interval();
        
        int answer = 0;
        for(int[] route: routes) {
            if(interval.overlap(route)) continue;
            else answer++;
        }
        return (answer + 1); // add one for last item
    }
    
    private void swap(int[] route) {
        if(route[0] <= route[1]) return;
        int temp = route[0];
        route[0] = route[1];
        route[1] = temp;
    }
    
    class Interval {
        private int start;
        private int end;
        
        public Interval() {
            this.start = -30000;
            this.end = 30000;
        }
        
        public boolean overlap(int[] route) {
            int newStart = Math.max(this.start, route[0]);
            int newEnd = Math.min(this.end, route[1]);
            if(newStart > newEnd) {
                this.start = route[0];
                this.end = route[1];
                return false;
            }
            this.start = newStart;
            this.end = newEnd;
            return true;
        }
    }
}
~~~
