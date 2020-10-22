# 주식가격

출처: https://programmers.co.kr/learn/courses/30/lessons/42584

## 지문

초 단위로 기록된 주식가격이 담긴 배열 prices가 매개변수로 주어질 때, 가격이 떨어지지 않은 기간은 몇 초인지를 return 하도록 solution 함수를 완성하세요.

제한사항
- prices의 각 가격은 1 이상 10,000 이하인 자연수입니다.
- prices의 길이는 2 이상 100,000 이하입니다.

입출력 예

prices |	return
- | -
[1, 2, 3, 2, 3] |	[4, 3, 1, 1, 0]

입출력 예 설명

- 1초 시점의 ₩1은 끝까지 가격이 떨어지지 않았습니다.
- 2초 시점의 ₩2은 끝까지 가격이 떨어지지 않았습니다.
- 3초 시점의 ₩3은 1초뒤에 가격이 떨어집니다. 따라서 1초간 가격이 떨어지지 않은 것으로 봅니다.
- 4초 시점의 ₩2은 1초간 가격이 떨어지지 않았습니다.
- 5초 시점의 ₩3은 0초간 가격이 떨어지지 않았습니다.

## 해설

Stack을 통해서 과거 가격과 현재 가격을 효율적으로 비교하는 문제입니다.

왼쪽부터 시간 순서대로 가격을 체크해나갔을때, 자신보다 낮은 가격을 만나게 될 때 까지의 시간을 answer에 기록하게 됩니다. 단순히 자신보다 나중에 오는 모든 가격들과 비교할 수도 있지만, 좀더 비교연산의 횟수를 줄이기 위해서 Stack을 사용합니다.

기본적으로 가격들을 순서대로 Stack에 넣어가면서, 만약 현재의 가격이 Stack의 최상단에 있는 가격보다 낮다면 이때 Stack을 pop 해주어서 '너보다 낮은 가격이 나왔어!' 라는 의미로 현재 시점과 해당 가격이 나타났던 시점을 비교해서 answer에 값을 넣어줍니다. 

Stack에 들어있는 가격보다 낮은 가격이 새로 나왔을 경우 pop이 이루어지기 때문에, Stack 안의 모든 가격들은 깊이 들어갈수록(과거의 값일 수록) 가격이 낮아지는 구조가 됩니다. 이에 따라서 새로운 가격이 나왔을 때, Stack안에서 새로운 가격보다 가격이 낮아지는 첫 숫자까지만 비교를 하고 나머지 값들은 비교를 하지 않아도 됩니다. 

## Solution.java
~~~
import java.util.Stack;

class Solution {
    public int[] solution(int[] prices) {
        
        int[] answer = new int[prices.length];
        
        Stack<Log> logs = new Stack<>();
        
        for(int time = 0; time < prices.length; time++) {
            int price = prices[time];
            while(!logs.empty() && logs.peek().price > price) {
                Log curr = logs.pop();
                answer[curr.time] = time - curr.time;
            }
            logs.push(new Log(time, price));
        }
        
        int lastTime = prices.length - 1;
        
        while(!logs.empty()) {
            Log curr = logs.pop();
            answer[curr.time] = lastTime - curr.time;
        }
        
        return answer;
    }
}

class Log {
    public int time;
    public int price;
    public Log(int time, int price) {
        this.time = time;
        this.price = price;
    } 
}
~~~
