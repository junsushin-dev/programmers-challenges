# 프린터

출처: https://programmers.co.kr/learn/courses/30/lessons/42587

## 지문

일반적인 프린터는 인쇄 요청이 들어온 순서대로 인쇄합니다. 그렇기 때문에 중요한 문서가 나중에 인쇄될 수 있습니다. 이런 문제를 보완하기 위해 중요도가 높은 문서를 먼저 인쇄하는 프린터를 개발했습니다. 이 새롭게 개발한 프린터는 아래와 같은 방식으로 인쇄 작업을 수행합니다.

1. 인쇄 대기목록의 가장 앞에 있는 문서(J)를 대기목록에서 꺼냅니다.
2. 나머지 인쇄 대기목록에서 J보다 중요도가 높은 문서가 한 개라도 존재하면 J를 대기목록의 가장 마지막에 넣습니다.
3. 그렇지 않으면 J를 인쇄합니다.
예를 들어, 4개의 문서(A, B, C, D)가 순서대로 인쇄 대기목록에 있고 중요도가 2 1 3 2 라면 C D A B 순으로 인쇄하게 됩니다.

내가 인쇄를 요청한 문서가 몇 번째로 인쇄되는지 알고 싶습니다. 위의 예에서 C는 1번째로, A는 3번째로 인쇄됩니다.

현재 대기목록에 있는 문서의 중요도가 순서대로 담긴 배열 priorities와 내가 인쇄를 요청한 문서가 현재 대기목록의 어떤 위치에 있는지를 알려주는 location이 매개변수로 주어질 때, 내가 인쇄를 요청한 문서가 몇 번째로 인쇄되는지 return 하도록 solution 함수를 작성해주세요.

제한사항
- 현재 대기목록에는 1개 이상 100개 이하의 문서가 있습니다.
- 인쇄 작업의 중요도는 1~9로 표현하며 숫자가 클수록 중요하다는 뜻입니다.
- location은 0 이상 (현재 대기목록에 있는 작업 수 - 1) 이하의 값을 가지며 대기목록의 가장 앞에 있으면 0, 두 번째에 있으면 1로 표현합니다.

입출력 예

priorities	|  location	|  return
-|-|-
[2, 1, 3, 2]| 	2	|  1
[1, 1, 9, 1, 1, 1]|	0|	5

입출력 예 설명

예제 1

문제에 나온 예와 같습니다.

예제 2

6개의 문서(A, B, C, D, E, F)가 인쇄 대기목록에 있고 중요도가 1 1 9 1 1 1 이므로 C D E F A B 순으로 인쇄합니다.

## 해설

문제가 설명하는 규칙을 Queue를 통해서 구현하면 되는 문제입니다. Priority만을 queue에 넣어서 처리하면 내가 처음에 요청한 작업이 언제 처리되는지 알수가 없으므로, 처음 요청한 작업의 location을 변경하면서 기억하거나 별도로 저장하여 시뮬레이션 중 요청한 작업이 나오는지 체크해야 합니다.

아래 문제풀이의 경우, 초기 주어진 location 값을 가지는 class Job을 새로 정의하였습니다. Comparable 인터페이스를 사용하고 compareTo 함수를 정의해 줄 경우, Collections.max 연산 또는 비교 연산은 그대로 priority를 사용할 수 있습니다.

초기에 주어진 location 값으로 아래에서는 요청한 작업(Job target)의 reference를 최초에 기억하고, Queue에서 처리되는 작업과 비교하여 일치할 경우 while문을 끝내는 방법을 사용하였습니다. 

## Solution.java
~~~
import java.util.*;

class Solution {
    public int solution(int[] priorities, int location) {
        Queue<Job> myQ = new LinkedList<>();
        Job target = null;
        for(int i = 0; i < priorities.length; i++) {
            Job newJob = new Job(priorities[i], i);
            myQ.add(newJob);
            if(i == location) target = newJob;
        }

        int answer = 0;
        while(!myQ.isEmpty()) {
            Job curr = myQ.poll();
            if(myQ.isEmpty() || curr.compareTo(Collections.max(myQ)) >= 0) {
                answer += 1;
                if(curr == target) break;
            }
            else {
                myQ.add(curr);
            }
        }

        return answer;
    }

    class Job implements Comparable<Job>{
        int priority;
        int initPos;

        public Job(int p, int i) {
            this.priority = p;
            this.initPos = i;
        }

        public int compareTo(Job o) {
            return Integer.compare(this.priority, o.priority);
        }
    }
}
~~~
