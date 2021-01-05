# 소수 찾기

출처: https://programmers.co.kr/learn/courses/30/lessons/42839

## 지문

한자리 숫자가 적힌 종이 조각이 흩어져있습니다. 흩어진 종이 조각을 붙여 소수를 몇 개 만들 수 있는지 알아내려 합니다.

각 종이 조각에 적힌 숫자가 적힌 문자열 numbers가 주어졌을 때, 종이 조각으로 만들 수 있는 소수가 몇 개인지 return 하도록 solution 함수를 완성해주세요.

제한사항
numbers는 길이 1 이상 7 이하인 문자열입니다.
numbers는 0~9까지 숫자만으로 이루어져 있습니다.
013은 0, 1, 3 숫자가 적힌 종이 조각이 흩어져있다는 의미입니다.

입출력 예
numbers|return
-------|-------
"17"|3
"011"|2

## 해설

1. 주어진 string을 이용한 모든 경우의 수를 완전 탐색
2. 소수인지 여부를 체크

위 두가지를 구현하는 것이 핵심인 문제입니다.

여기서 까다로운 것은, 주어진 정수들을 순서대로 처리하여

- 숫자 구축에 사용할 것인지 여부
- 모든 가능한 순서로 구축

이 두가지를 모두 염두에 두고 알고리즘을 구현하여야 합니다.

예시) a, b, c를 배열하는 경우 순서대로 처리한다면

- a 사용
  - b 사용
    - c 사용 : a, b, c를 배열하는 경우의 수(abc, abc, bac, bca, cab, cba)
    - c 미사용: a, b를 배열하는 경우의 수(ab, ba)
  - b 미사용
    - c 사용: a, c를 배열하는 경우의 수(ac, ca)
    - c 미사용: a
- a 미사용
  - b 사용
    - c 사용: b, c를 배열하는 경우의 수(bc, cb)
    - c 미사용: b
  - b 미사용
    - c 사용: c
    - c 미사용: 빈 String "" -> 예외처리

위와 같은 경우의 수를 모두 테스트하여야 합니다.

아래의 문제풀이는 depth-first-search 를 응용한 방법을 사용하여 모든 경우의 수를 탐색합니다. 이미 사용된 숫자로 구축된 string을 가진 상태에서, 남은 후보 정수들로 시도할 수 있는 모든 경우의 수를 테스트합니다.

남은 후보 중 정수 n을 선정한 상태에서,
- n string 에 붙이고, n을 제외한 남은 후보들로 dfs 진행
- n 을 사용하지 않고, n을 제외한 남은 후보들로 dfs 진행

위와 같은 방법으로 각 단계에서 숫자를 사용하거나, 사용하지 않는 경우의 수를 모두 체크할 수 있습니다.  

소수를 체크하는 알고리즘으로는 간단히 n 이 주어졌을 때, 2부터 n의 제곱근까지의 수로 모두 나누어보는 방법을 사용하였습니다. n이 만약 두 수의 곱으로 이루어졌다면, 가장 큰 인수를 가지는 경우 25 = 5^2와 같이 같은 두 수의 곱으로 이루어지는 경우이기 때문에 제곱근까지를 체크하는 경우 소수임을 검증할 수 있습니다.

예시) 25를 체크하는 경우
2, 3, 4, 5로 나누어서 나머지가 0인지를 검증한다.
5를 초과하는 숫자로 검증하는 경우에는 곱하는 다른쪽 숫자가 5보다 작을 것이므로 이미 검증된 조합이다. (예를 들어 36을 검증하는 경우, 4로 나누었을 때 이미 소수가 아님을 알 수 있으므로, 9로는 나누어보지 않아도 괜찮음)

조금 더 최적화를 하고 싶을 경우에는 2로 한번 나누어본 이후에 다른 짝수로는 나누는 것이 불필요하므로, 나누는 숫자를 3부터 2씩 증가시켜 홀수들로만 나누어보는 방법을 사용할 수 있습니다.


## Solution.java
~~~java
import java.util.*;

class Solution {
    public HashSet<Integer> mySet = new HashSet<>();

    public int solution(String numbers){
        ArrayList<Integer> myList = new ArrayList<>();
        for(char ch: numbers.toCharArray()){
            myList.add(Character.getNumericValue(ch));
        }

        dfs("", myList);

        int answer = mySet.size();
        return answer;
    }

    public boolean checkPrime(int num){
        if(num <= 1) return false;

        for(int i = 2; i <= Math.sqrt(num); i++){
            if(num % i == 0){
                return false;
            }
        }
        return true;
    }

    public void dfs(String given, ArrayList<Integer> candidates){
        if(candidates.isEmpty()){
            if(given.length() == 0) return;
            int num = Integer.parseInt(given);
            if(checkPrime(num)) {
                mySet.add(num);
                return;
            }
        }
        for(int i = 0; i < candidates.size(); i++){
            ArrayList<Integer> nextList = (ArrayList<Integer>)candidates.clone();
            Integer next = nextList.get(i);
            nextList.remove(i);
            dfs(given, nextList);
            dfs(given+next.toString(), nextList);
        }
    }
}
~~~

## 해설 - JavaScript
자바스크립트도 똑같은 로직을 구현하여 해결하면 됩니다. 배열에서 특정 원소를 제거하는 것을 spread operator로 해결할 수 있습니다. 
for... in 문의 경우에는 [Array에 사용하는 것이 추천되지 않으므로](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/for...in#Array_iteration_and_for...in)사용하지 않는 것이 좋습니다. 

## solution.js - 소요시간 30분
~~~javascript
const checkPrime = (num) => {
    if(num < 2) return false;
    for(let i = 2; i < num; i++) {
        if(num % i === 0) return false;
    }
    return true;
}

const permutation = (str, arr, res) => { 
    if(arr.length === 0) {
        if(str.length === 0) return;
        const num = parseInt(str, 10);
        if(checkPrime(num)) res.add(num);
        return;
    }
    for(let idx = 0; idx < arr.length; idx++) {
        const num = arr[idx];
        const newArr = [...arr.slice(0, idx), ...arr.slice(idx + 1)];
        permutation(str, newArr, res);
        permutation(str + num, newArr, res); 
    }
}

function solution(numbers) {
    const answerSet = new Set();
    permutation("", [...numbers], answerSet);
    return answerSet.size;
}
~~~
