# N으로 표현

출처: https://programmers.co.kr/learn/courses/30/lessons/42895

## 지문

### 문제 설명
아래와 같이 5와 사칙연산만으로 12를 표현할 수 있습니다.
- 12 = 5 + 5 + (5 / 5) + (5 / 5)
- 12 = 55 / 5 + 5 / 5
- 12 = (55 + 5) / 5

5를 사용한 횟수는 각각 6,5,4 입니다. 그리고 이중 가장 작은 경우는 4입니다.

이처럼 숫자 N과 number가 주어질 때, N과 사칙연산만 사용해서 표현 할 수 있는 방법 중 N 사용횟수의 최솟값을 return 하도록 solution 함수를 작성하세요.

### 제한사항
- N은 1 이상 9 이하입니다.
- number는 1 이상 32,000 이하입니다.
- 수식에는 괄호와 사칙연산만 가능하며 나누기 연산에서 나머지는 무시합니다.
- 최솟값이 8보다 크면 -1을 return 합니다.

### 입출력 예

|N	|number	|return |
|---|-------|-------|
|5	|12	|4 |
|2	|11	|3 |

### 입출력 예 설명
예제 #1
- 문제에 나온 예와 같습니다.

예제 #2
- 11 = 22 / 2와 같이 2를 3번만 사용하여 표현할 수 있습니다.

## 해설

Dynamic Programming을 이용해서 가능한 경우의 수를 줄이는 문제입니다. 

Brute Force 방식으로 시도할 경우, 연산 횟수가 많아질 수 있을 뿐더러 괄호와 같은 연산 순서의 처리가 힘들어질 수 있습니다. 간단한 아이디어에 착안해서 동적계획법으로 문제를 풀어보도록 합시다. 

1. 모든 식은 사칙연산의 순서대로 계산된다. 
2. 마지막 순서의 사칙연산이 모든 식에 존재한다. 
3. 마지막 순서 이전의 상태를 보면 아래와 같이 표현될 것이다. a, b가 그 전까지의 연산 결과일때,
- a + b
- a - b
- a * b
- a / b
와 같이 두 숫자의 사칙연산으로 최종 숫자가 계산된다. 
4. a, b는 각각 스스로 어떠한 식의 계산 결과물이다.

위의 설명을 예시로 풀어보면,

12 = (55 + 5) / 5 일때,
a = (55 + 5)는 5를 3개를 사용한 연산의 결과물이고,
b = 5는 5를 1개를 사용한 연산의 결과물임을 알 수 있습니다. 

따라서 a와 b를 연산을 해준 결과물은, 두 연산에 들어있던 '5'의 개수를 합친 '5' 4개짜리 결과물임을 알 수 있습니다. 

위와 같은 아이디어를 통해서, 
- '5' 1개로 만들 수 있는 결과의 집합: 
  - [5]
- '5' 2개로 만들 수 있는 결과의 집합: 
  - [55, 5+5, 5-5, 5*5, 5/5]
  - = [55, 10, 25, 1] (number가 0이 아니므로 0은 제외) 
- ...
- '5' 8개로 만들 수 있는 결과의 집합

이런 식으로 구축해나가면 연산 횟수를 줄이면서 가능한 결과들을 만들어나갈 수 있습니다. 

## 팁
0과 음수값의 결과는 저장할 필요가 없습니다.

a - b = c (<0) 이 존재한다면, 조합 순서에 따라서 b - a = -c가 존재할 수 밖에 없습니다. 

d + c로 만들 수 있는 결과는 d - (-c)로 만들 수 있고, 

d - c로 만들 수 있는 결과는 d + (-c)로 만들 수 있기 때문에,

양수 결과만 저장해도 모든 조합을 계산해낼 수 있습니다.

곱하기와 나누기 연산에서도, 답의 범위에 음수의 값이 포함되지 않기 때문에 음수 결과를 저장하는 것은 불필요함을 알 수 있습니다. 

## 소요시간
50분

## Solution.java
~~~java
import java.util.*;

class Solution {
    static int MAX_ITER = 8;
    static List<Set<Integer>> memo;
    
    public int solution(int N, int number) {
        String nStr = Integer.toString(N);
        memo = new ArrayList();
        memo.add(new HashSet()); // stub for index 0 
        
        for(int i = 1; i <= MAX_ITER; i++) {
            Set<Integer> resSet = new HashSet();
            String toAdd = "";
            for(int j = 0; j < i; j++) {
                toAdd += nStr;
            }
            resSet.add(Integer.parseInt(toAdd));
            memo.add(resSet);
        }
        
        for(int iter = 1; iter <= MAX_ITER; iter++) {
            Set<Integer> resSet = memo.get(iter);
            for(int first = 1; first < iter; first++) {
                int second = iter - first;
                for(int firstItem: memo.get(first)) {
                    for(int secondItem: memo.get(second)) {
                        int sum = firstItem + secondItem;
                        int sub = firstItem - secondItem;
                        int mul = firstItem * secondItem;
                        int div = firstItem / secondItem;
                        int[] items = {sum, sub, mul, div};
                        for(int item: items) {
                            if(item > 0) {
                                resSet.add(item);
                            }
                        }
                    }
                }
            }
            if(resSet.contains(number)) return iter;
        }
        
        int answer = -1;
        return answer;
    }
}
~~~
