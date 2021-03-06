# 큰 수 만들기

출처: https://programmers.co.kr/learn/courses/30/lessons/42883

## 지문

어떤 숫자에서 k개의 수를 제거했을 때 얻을 수 있는 가장 큰 숫자를 구하려 합니다.

예를 들어, 숫자 1924에서 수 두 개를 제거하면 [19, 12, 14, 92, 94, 24] 를 만들 수 있습니다. 이 중 가장 큰 숫자는 94 입니다.

문자열 형식으로 숫자 number와 제거할 수의 개수 k가 solution 함수의 매개변수로 주어집니다. number에서 k 개의 수를 제거했을 때 만들 수 있는 수 중 가장 큰 숫자를 문자열 형태로 return 하도록 solution 함수를 완성하세요.

제한 조건
number는 1자리 이상, 1,000,000자리 이하인 숫자입니다.
k는 1 이상 number의 자릿수 미만인 자연수입니다.
입출력 예
number|k|return
------|-|------
"1924"|2|"94"
"1231234"|3|"3234"
"4177252841"|4|"77584"

## 해설

Greedy Algorithm을 이용하는 문제로서, 시간 제한을 맞추는 것이 핵심인 문제입니다. 시간제한내에 문제를 풀기 위해서는,

1. 시간복잡도를 최적화
2. 코드 내 불필요한 처리를 최소화

를 해야 이 문제를 주어진 시간 내에 풀이할 수 있습니다.

시간복잡도를 최적화 하기 위해서는 모든 숫자를 한번씩 지워보는 식으로는 해결을 할 수 없습니다. 문제를 푸는 규칙을 잘 파악해보면, 높은 자릿수에서 값이 낮은 수를 지워주는 것이 결과값을 최대화한다는 것을 발견할 수 있습니다.

예시) "1924", k=2
- 1을 지워주고, 9를 가장 앞에 놓는 것이 높은 값을 만들게 됨
- 9가 가장 앞에 온 이후에는, 9를 지울경우 앞자리가 2로 바뀌면서 숫자가 오히려 작아지므로 뒤쪽의 2를 지우는 것이 유리함

그렇다면 전체 수에서 가장 작은 1부터 2, 3... 순서로 숫자들을 지워주면 될까요? 하지만 아래와 같은 경우에 낮은 숫자만을 지운다면 최적의 답에 도달할 수 없습니다.

예시) "12313", k=2
- 1을 우선적으로 지울 경우, "233"이 남게 됨
- 하지만 앞의 1과 2를 지울 경우, "313"을 구성가능

위 예시를 통해서, 무조건적으로 작은 숫자인 1, 2 등을 지우는 것이 아니라 앞자리가 크게 구성되도록 지워주어야 최적의 답에 도달할 수 있음을 볼 수 있습니다.

이를 위해서는 앞자리부터 검사하면서 '다음 숫자가 자신보다 클 경우'(=해당 숫자를 지우는 것이 최적의 수인 경우) 숫자들을 삭제해주면 됩니다. 자릿수가 높은 숫자를 1이라도 늘리는 것이 아래 자릿수를 업데이트 하는 것보다 무조건적으로 답을 크게 만들어주므로, 이 조건만을 체크하면서 숫자들을 k 개를 지워줄 경우 최적해에 도달할 수 있습니다.

모든 자릿수를 검사하고도 지울 문자가 남은 경우, 앞쪽의 숫자가 높은 것이 보장되므로 뒤에서부터 남은 갯수만큼의 숫자를 지워주면 됩니다.

코드 내 불필요한 처리를 최소화하기 위해서는 String 대신 StringBuffer를 사용하였습니다.

String의 경우 java에서 immutable한 객체로서, "ABC" + "D"의 산식을 통해 "ABCD"라는 string을 만드는 경우 실제로는 "ABC"에 뒤에 "D"를 붙이는 것이 아니라 "ABCD"라는 객체를 새로 만들어서 선언합니다.

메모리를 할당하고 객체를 매번 만드는 overhead를 줄이기 위해서, character 단위로 조작이 가능한 stringbuffer를 이용하여 위에서 만든 조건에 해당하는 문자들을 지워주는 방식으로 답안을 구성했습니다.

## Solution.java
~~~java
class Solution {
    public String solution(String number, int k) {
        StringBuffer str = new StringBuffer(number);

        int index = 0;

        while(k > 0 && index < str.length()-1) {
            if(str.charAt(index) < str.charAt(index+1)) {
                str.deleteCharAt(index);
                k -= 1;
                index = (index==0) ? 0 : (index-1);
            }
            else {
                index += 1;
            }
        }

        return str.substring(0, str.length()-k);
    }
}
~~~

## 해설 - JavaScript
기본적인 논리는 위 Java 풀이법과 같습니다. 
JavaScript의 string 연산은 기본적으로 immutable 합니다. 새로운 객체 생성에 overhead가 발생하므로, 효율적인 방식으로 위 로직을 구현해 주어야 합니다. 계속 string을 업데이트 해주기 보다는, stack을 선언하고 문자를 하나씩 넣어주는 방식으로 처리하였습니다. 

1. stack이 비어있다면 현재 문자열을 넣고,
2. 아니라면 stack의 head를 보고, 현재 char보다 작은 경우 pop 시키며 k를 1씩 줄여줍니다. 

이런식으로 k가 0이 되거나, char를 모두 순회할때까지 처리하면 됩니다. 순회 이후에는 stack의 문자들을 join해서 정답으로 만들어주면 됩니다. 

## solution.js - 45분 소요
~~~javascript
function solution(number, k) {
    const stack = [];
    for(let char of number) {
        if(k === 0) {
            stack.push(char);
            continue;
        }
        if(stack.length === 0) {
            stack.push(char);
            continue;
        }
        while(stack.length > 0 && k > 0 && stack[stack.length - 1] < char) {
            stack.pop();
            k--;
        }
        stack.push(char);
    }
    return stack.slice(0, stack.length - k).join("");
}
~~~
