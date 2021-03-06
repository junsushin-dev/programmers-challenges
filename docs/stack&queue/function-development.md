# 기능개발

출처: https://programmers.co.kr/learn/courses/30/lessons/42586

## 지문

프로그래머스 팀에서는 기능 개선 작업을 수행 중입니다. 각 기능은 진도가 100%일 때 서비스에 반영할 수 있습니다.

또, 각 기능의 개발속도는 모두 다르기 때문에 뒤에 있는 기능이 앞에 있는 기능보다 먼저 개발될 수 있고, 이때 뒤에 있는 기능은 앞에 있는 기능이 배포될 때 함께 배포됩니다.

먼저 배포되어야 하는 순서대로 작업의 진도가 적힌 정수 배열 progresses와 각 작업의 개발 속도가 적힌 정수 배열 speeds가 주어질 때 각 배포마다 몇 개의 기능이 배포되는지를 return 하도록 solution 함수를 완성하세요.

제한 사항
작업의 개수(progresses, speeds배열의 길이)는 100개 이하입니다.
작업 진도는 100 미만의 자연수입니다.
작업 속도는 100 이하의 자연수입니다.
배포는 하루에 한 번만 할 수 있으며, 하루의 끝에 이루어진다고 가정합니다. 예를 들어 진도율이 95%인 작업의 개발 속도가 하루에 4%라면 배포는 2일 뒤에 이루어집니다.

입출력 예
progresses | speeds | return
-----------|--------|-------
[93,30,55]|[1,30,5]|[2,1]

입출력 예 설명

첫 번째 기능은 93% 완료되어 있고 하루에 1%씩 작업이 가능하므로 7일간 작업 후 배포가 가능합니다.
두 번째 기능은 30%가 완료되어 있고 하루에 30%씩 작업이 가능하므로 3일간 작업 후 배포가 가능합니다. 하지만 이전 첫 번째 기능이 아직 완성된 상태가 아니기 때문에 첫 번째 기능이 배포되는 7일째 배포됩니다.
세 번째 기능은 55%가 완료되어 있고 하루에 5%씩 작업이 가능하므로 9일간 작업 후 배포가 가능합니다.

따라서 7일째에 2개의 기능, 9일째에 1개의 기능이 배포됩니다.

## 해설

각 작업의 선행작업이 정의되어 있고, 선행작업이 완료되어야만 작업이 완료 될 수 있는 상황을 보여주는 문제입니다. 내 앞에 작업이 먼저 나가야 나도 나갈 수 있다는 것은 일종의 줄서기와 같다고 보시면 되는데요, 이 경우 Queue를 이용하여 쉽게 문제를 해결할 수 있습니다. (실제로 영어의 Queue는 놀이공원 등에서 선 줄이라는 의미가 있습니다!)

각 작업이 완료되는 시점 또는 현황을 알아야 선행되어야 하는 작업이 완료되었는지 알 수 있는데, 이 경우 완료여부를 체크하기 위해서 여러가지 방법을 사용할 수 있습니다.

예를 들어,

1. while문을 통해서 일자를 1씩 증가시키고, 완성도를 업데이트하며 100이 넘어가는지 체크

2. 미리 기존 완성도, 1일에 채워지는 완성도를 이용하여 작업별 완료일자를 계산

등의 여러가지 방법이 있을 수 있습니다.

아래 풀이과정에서는 이 중 2번 방법을 사용하여,

완료에 필요한 일수 = (100 - 최초완성도) / 일별완성속도

의 식을 사용하여 각 작업별로 필요한 일자를 미리 계산하였습니다. 이후에는 Queue 구조를 이용하여, 이번에 나가는 작업의 완료 일자보다 뒤따르는 작업들의 완료일자가 빠를 경우 한꺼번에 Queue에서 나갈 수 있도록 코드를 구성하였습니다.

문제에서 한꺼번에 나오는 작업의 갯수들만을 요구하고 있기 때문에, 이런식으로 한번에 나오는 작업의 갯수만을 세고 저장하면 문제의 요구사항을 만족할 수 있습니다. 

## Solution.java
~~~java
import java.util.LinkedList;
import java.util.Queue;
import java.util.List;

class Solution {
    public int[] solution(int[] progresses, int[] speeds) {
        Queue<Integer> myQ = new LinkedList<>();
        List<Integer> ans = new LinkedList<>();

        for(int i = 0; i < progresses.length; i++) {
            Integer currDays = (100 - progresses[i]) / speeds[i];
            if((100 - progresses[i]) % speeds[i] > 0 ) currDays += 1;
            myQ.add( currDays );
        }

        int day = 0;

        while(!myQ.isEmpty()) {
            day = myQ.peek();
            int release = 0;
            while(!myQ.isEmpty() && myQ.peek() <= day) {
                myQ.poll();
                release += 1;
            }
            ans.add(release);
        }

        int[] answer = new int[ans.size()];
        for(int i = 0; i < ans.size(); i++) {
            answer[i] = ans.get(i);
        }
        return answer;
    }
}
~~~

## solution.js
~~~javascript
function solution(progresses, speeds) {
    let day = 0;
    let count = 0;
    const answer = [];
    progresses.forEach((initProgress, i) => {
        const speed = speeds[i];
        const progress = initProgress + day * speed;  
        if(progress >= 100) return count++;
        answer.push(count);
        count = 0;
        const offset = Math.ceil((100 - progress) / speed);
        day += offset;
        return count++;
    })
    answer.push(count);
    answer.shift();
    return answer;
}
~~~