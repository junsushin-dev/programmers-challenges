# 라면공장

출처: https://programmers.co.kr/learn/courses/30/lessons/42629

## 지문
라면 공장에서는 하루에 밀가루를 1톤씩 사용합니다. 원래 밀가루를 공급받던 공장의 고장으로 앞으로 k일 이후에야 밀가루를 공급받을 수 있기 때문에 해외 공장에서 밀가루를 수입해야 합니다.

해외 공장에서는 향후 밀가루를 공급할 수 있는 날짜와 수량을 알려주었고, 라면 공장에서는 운송비를 줄이기 위해 최소한의 횟수로 밀가루를 공급받고 싶습니다.

현재 공장에 남아있는 밀가루 수량 stock, 밀가루 공급 일정(dates)과 해당 시점에 공급 가능한 밀가루 수량(supplies), 원래 공장으로부터 공급받을 수 있는 시점 k가 주어질 때, 밀가루가 떨어지지 않고 공장을 운영하기 위해서 최소한 몇 번 해외 공장으로부터 밀가루를 공급받아야 하는지를 return 하도록 solution 함수를 완성하세요.

dates[i]에는 i번째 공급 가능일이 들어있으며, supplies[i]에는 dates[i] 날짜에 공급 가능한 밀가루 수량이 들어 있습니다.

제한사항
- stock에 있는 밀가루는 오늘(0일 이후)부터 사용됩니다.
- stock과 k는 2 이상 100,000 이하입니다.
- dates의 각 원소는 1 이상 k 이하입니다.
- supplies의 각 원소는 1 이상 1,000 이하입니다.
- dates와 supplies의 길이는 1 이상 20,000 이하입니다.
- k일 째에는 밀가루가 충분히 공급되기 때문에 k-1일에 사용할 수량까지만 확보하면 됩니다.
- dates에 들어있는 날짜는 오름차순 정렬되어 있습니다.
- dates에 들어있는 날짜에 공급되는 밀가루는 작업 시작 전 새벽에 공급되는 것을 기준으로 합니다. 예를 들어 9일째에 밀가루가 바닥나더라도, 10일째에 공급받으면 10일째에는 공장을 운영할 수 있습니다.
- 밀가루가 바닥나는 경우는 주어지지 않습니다.

입출력 예

stock	|dates	|supplies	|k	|result
--|--|--|--|--
4	|[4,10,15]	|[20,5,10]	|30	|2

입출력 예 설명

- 현재 밀가루가 4톤 남아 있기 때문에 오늘과 1일 후~3일 후까지 사용하고 나면 모든 밀가루를 다 사용합니다. 따라서 4일 후에는 반드시 밀가루를 공급받아야 합니다.

- 4일째 공급받고 나면 15일 이후 아침에는 9톤의 밀가루가 남아있게 되고, 이때 10톤을 더 공급받으면 19톤이 남아있게 됩니다. 15일 이후부터 29일 이후까지 필요한 밀가루는 15톤이므로 더 이상의 공급은 필요 없습니다.

- 따라서 총 2회의 밀가루를 공급받으면 됩니다.

## 해설

Heap(또는 PriorityQueue)를 이용해서 공장이 작동하기 위한 최소 공급 횟수를 구하는 문제입니다. 주어지는 값은 최초의 재고, 공급일자, 공급일자별 공급량, 공장이 가동되어야 하는 날짜입니다.

기본적으로 공급횟수를 최소화하고, 횟수 당 공급량은 답과 무관하기 때문에 한번 공급받을 때 많은 량을 공급받는 것이 유리합니다. 다만, 라면공장이 가동되는 일자 중 밀가루가 떨어지면 안되기 때문에 공급량이 더 많은 공급일이 나중에 있다고 하여도 당장 밀가루가 떨어지면 공급을 받아야 할 것입니다.

예시)

최초 재고: 4

(공급일, 공급량): (2, 5), (6, 20)

- 공급량이 6일째가 20으로 훨씬 많지만, 2일째의 공급을 받지 않을 경우 최초재고 4로는 5일째에 밀가루가 떨어지기 때문에 2일째 공급을 필수적으로 받아야 함

위의 조건에 따라서 아래의 문제풀이에서는 이와 같이 문제를 접근하였습니다.

- 현재 보유한 밀가루가 떨어질 때까지 날짜 진행
- 보유한 밀가루가 떨어진 경우, 현재 시점까지 받을 수 있었던 공급기회들을 검토
- 공급기회들을 검토하고 가장 많은 공급량을 가진 시점을 선택해서, 해당 일자에 공급을 받은 것으로 가정

위와 같이 알고리즘을 진행하면, 라면공장에 공급이 끊기지 않는 것을 보장하면서 최소의 공급횟수를 알아낼 수 있습니다.

예시)

stock	|dates	|supplies	|k	|result
--|--|--|--|--
4	|[4,10,15]	|[20,5,10]	|30	|2

아래 재고량은 날짜의 아침 기준입니다. (오늘의 밀가루 사용 전)

날짜 |stock	|공급기회(날짜, 공급량) |내용
--|--|--|--
0	|	4| |
1	|	3| |
2	|	2| |
3	|	1| |
4	|	0| (4,20)|
4	|	20| | (4,20) 사용
5~24	|19~0	|(10,5),(15,10) |
24| 10| (10,5) | (15,10) 사용
25~29|9~5 | (10,5)

위와 같이 알고리즘을 진행하기 위해서, 각 공급기회를 공급량 기준 내림차순으로 정렬해서 각 시점에 가장 공급량이 높은 기회를 알아내야 합니다. 이를 위해서는 PriorityQueue와 Heap 자료구조를 사용하면 됩니다. Java의 정렬순서가 기본값이 오름차순이기 때문에, Comparator를 새로 정의하여 내림차순으로 바꾸어 주었습니다.

~~~java
PriorityQueue<Integer> myQueue = new PriorityQueue<>(dates.length, new Comparator<Integer>() {
    @Override
    public int compare(Integer p1, Integer p2) {
       return p1 <= p2 ? 1 : -1;
    }
});
~~~

PriorityQueue를 만든 이후에는, 위 사례의 알고리즘을 그대로 while문으로 구현하면 됩니다.

~~~java
int currStock = stock;
        int answer = 0;
        for(int i = 0; i < k; i++) {
            if(!supplyQueue.isEmpty() && supplyQueue.peek().date == i){
                myQueue.add(supplyQueue.poll().supply);
            }    
            if(currStock == 0){
                currStock += myQueue.poll();
                answer += 1;
            }
            currStock -= 1;
        }
~~~

## 팁

Method Override를 이용해서 Comparator를 이와 같이 PriorityQueue 선언시에 만들어주면 별도로 이름을 붙은 Comparator 클래스를 만들지 않아도 됩니다. 명시적으로 Comparator를 만들고 싶은 경우에는 클래스를 새로 선언하고 인스턴스를 만들어 PriorityQueue에 넣어 주어도 됩니다.

~~~java
PriorityQueue<Integer> myQueue = new PriorityQueue<>(dates.length, new Comparator<Integer>() {
    @Override
    public int compare(Integer p1, Integer p2) {
       return p1 <= p2 ? 1 : -1;
    }
});
~~~

## Solution.java
~~~java
import java.util.*;

class Solution {
    public class Pair{
        public int date;
        public int supply;

        public Pair(int x, int y){
            this.date = x;
            this.supply = y;
        }
    }

    public int solution(int stock, int[] dates, int[] supplies, int k) {
        PriorityQueue<Integer> myQueue = new PriorityQueue<>(dates.length, new Comparator<Integer>() {
            @Override
            public int compare(Integer p1, Integer p2) {
               return p1 <= p2 ? 1 : -1;
            }
        });
        Queue<Pair> supplyQueue = new LinkedList<>();

        for(int i = 0; i < dates.length; i++){
            supplyQueue.add(new Pair(dates[i], supplies[i]));
        }

        int currStock = stock;
        int answer = 0;
        for(int i = 0; i < k; i++) {
            if(!supplyQueue.isEmpty() && supplyQueue.peek().date == i){
                myQueue.add(supplyQueue.poll().supply);
            }    
            if(currStock == 0){
                currStock += myQueue.poll();
                answer += 1;
            }
            currStock -= 1;

        }

        return answer;
    }
}
~~~
