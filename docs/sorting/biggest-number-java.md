# 가장 큰 수

출처: https://programmers.co.kr/learn/courses/30/lessons/42746

## 지문

0 또는 양의 정수가 주어졌을 때, 정수를 이어 붙여 만들 수 있는 가장 큰 수를 알아내 주세요.

예를 들어, 주어진 정수가 [6, 10, 2]라면 [6102, 6210, 1062, 1026, 2610, 2106]를 만들 수 있고, 이중 가장 큰 수는 6210입니다.

0 또는 양의 정수가 담긴 배열 numbers가 매개변수로 주어질 때, 순서를 재배치하여 만들 수 있는 가장 큰 수를 문자열로 바꾸어 return 하도록 solution 함수를 작성해주세요.

제한 사항
numbers의 길이는 1 이상 100,000 이하입니다.
numbers의 원소는 0 이상 1,000 이하입니다.
정답이 너무 클 수 있으니 문자열로 바꾸어 return 합니다.

입출력 예

numbers |	return
--------|--------
[6, 10, 2]|"6210"
[3, 30, 34, 5, 9]|"9534330"

## 해설

주어진 정수로 가장 큰 수를 구현하기 위해서는, Input의 원소들을 정렬하여 가장 큰 수를 구성할 수 있는 순서대로 앞에서부터 String을 구성하여야 합니다.

단순히 숫자값의 정렬이나, 사전식 String 정렬로는 문제의 요구사항을 충족할 수 없기 때문에 스스로의 정렬 규칙을 만들어야 합니다.

동일한 자릿수의 숫자의 경우 내장된 정렬을 사용할 수 있지만, 다른 자릿수의 정렬규칙을 정확히 구현하는 것이 핵심입니다.

예시 중 3, 30, 34의 경우에는 34, 3, 30 순서로 내림차순 정렬을 하는것이 가장 이상적이라고 볼 수 있습니다.

경우의 수

1. 34330
2. 34303
3. 33430
4. 33034
5. 30343
6. 30334

위 경우의 수를 볼 경우, 34가 두번째 자릿수를 4로 만들어주기 때문에 가장 유리하며, 3이 다음 자릿수를 다른 숫자를 이용해서 3으로 만들수 있기 때문에 두번째로 유리하며, 30이 가장 불리한 것을 알 수 있습니다.

위에서 3은 33으로 치환하여 정렬한 것과 같은 순서를 지니는 것을 확인할 수 있는데, 정확히는 다른 자릿수를 가진 숫자를 비교할 경우 두 숫자의 자릿수의 최소공배수만큼 같은 패턴을 연장하여 비교하여야 합니다.

예시)
- 12 vs 122 -> 121212 vs 122122 로 122가 우선함
- 34 vs 3443 -> 3434 vs 3443 으로 3443이 우선함

하지만 이보다 쉬운 방법은, 두 숫자로 구성할 경우 무조건 두가지 경우의 수가 존재하는 점을 이용하여 비교 시 숫자1 + 숫자2와 같이 구성한 string과 숫자2 + 숫자1과 같이 이어붙인 string을 비교하는 방법입니다.

예시)
- 12 vs 122 -> 12122 vs 12212 로 122가 우선함
- 34 vs 3443 -> 343443 vs 344334로 3443이 우선함

아래의 문제풀이는 두번째 방법을 사용하여 두개의 String을 비교하는 Comparator를 정의하고, 해당 Comparator를 이용하여 주어진 int 배열을 변환한 string 배열을 정렬하여 내림차순으로 answer를 구성하고 있습니다.

마지막으로는 문제의 요건이 가장 큰 수를 요구하고 있으며, 숫자는 0이 아닌이상 0으로 시작하지 않기 때문에 연속된 0으로 시작되는 string이 구성되었을 경우 앞에 붙어있는 0들을 떼어주는 작업을 해주어야 합니다.

## Solution.java

~~~
import java.util.*;

class Solution {
    public String solution(int[] numbers) {
        String[] myStrs = new String[numbers.length];
        for(int i = 0; i < numbers.length; i++){
            myStrs[i] = Integer.toString(numbers[i]);
        }
        Arrays.sort(myStrs, new MyComparator());
        String answer = "";
        for(int i = myStrs.length - 1; i >= 0; i--){
            answer += myStrs[i];
        }

        int start = 0;
        for(int i = 0; i < answer.length()-1; i++) {
            if(answer.charAt(i) == '0') start++;
            else break;
        }

        return answer.substring(start);
    }
}

class MyComparator implements Comparator<String>{
    @Override
    public int compare(String p1, String p2){
        String q1 = p1 + p2;
        String q2 = p2 + p1;
        return q1.compareTo(q2);
    }
}
~~~
