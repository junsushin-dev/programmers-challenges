# 완주하지 못한 선수

출처: https://programmers.co.kr/learn/courses/30/lessons/42576

## 지문

수많은 마라톤 선수들이 마라톤에 참여하였습니다. 단 한 명의 선수를 제외하고는 모든 선수가 마라톤을 완주하였습니다.

마라톤에 참여한 선수들의 이름이 담긴 배열 participant와 완주한 선수들의 이름이 담긴 배열 completion이 주어질 때, 완주하지 못한 선수의 이름을 return 하도록 solution 함수를 작성해주세요.

제한사항
마라톤 경기에 참여한 선수의 수는 1명 이상 100,000명 이하입니다.
completion의 길이는 participant의 길이보다 1 작습니다.
참가자의 이름은 1개 이상 20개 이하의 알파벳 소문자로 이루어져 있습니다.
참가자 중에는 동명이인이 있을 수 있습니다.

입출력 예

participant	| completion	| return
------------|-------------|-------
[leo, kiki, eden]	| [eden, kiki]	| leo
[marina, josipa, nikola, vinko, filipa]	| [josipa, filipa, marina, nikola]	| vinko
[mislav, stanko, mislav, ana]	| [stanko, ana, mislav]	| mislav

입출력 예 설명

예제 1

leo는 참여자 명단에는 있지만, 완주자 명단에는 없기 때문에 완주하지 못했습니다.

예제 2

vinko는 참여자 명단에는 있지만, 완주자 명단에는 없기 때문에 완주하지 못했습니다.

예제 3

mislav는 참여자 명단에는 두 명이 있지만, 완주자 명단에는 한 명밖에 없기 때문에 한명은 완주하지 못했습니다.

## 해설

해시 카테고리의 가장 간단한 1번 문제입니다. 문자열(String)으로 이루어진 리스트에서 처음 리스트에는 포함되었지만, 두번째 리스트에는 포함되지 않은 이름을 찾으면 됩니다. 여기서 각 리스트 내부의 모든 이름들이 다르다면 간단히 문자열 비교를 통해서도 문제를 풀 수 있겠지만, 문제의 요구사항에 따라 <strong>동명이인</strong>이 존재할 수 있기 때문에 해시맵을 사용해야 합니다.

해시맵을 만들어서 Key를 선수들의 이름의 자료형인 String으로, Value를 리스트에 등장한 횟수를 표현할 Integer로 설정합니다.
~~~java
Map<String, Integer> myMap = new HashMap<>();
~~~

주어진 첫 배열인 참여자 명단의 원소들을 체크하면서, 특정 이름을 가진 선수가 몇명인지 기록합니다. 이름이 처음 등장한다면 (이름, 1)과 같은 (Key, Value) 맵핑을 새로 등록하고, 등장한 적이 있는 이름이라면 (이름, 2)와 같이 등장횟수를 1만큼 업데이트 해줍니다.

HashMap의 getOrDefault 함수는 주어진 키가 이미 존재한다면 그 키에 맵핑되는 value 값을 리턴하고, 키가 존재하지 않는다면 주어진 기본값(아래에서는 0)을 반환합니다. 아래와 같이 코드를 구성하면 키가 처음 등록되는 경우와 이미 존재하는 경우를 모두 처리할 수 있습니다.
~~~java
for(String nextGuy: participant) {
    myMap.put(nextGuy, myMap.getOrDefault(nextGuy, 0) + 1);
}
~~~

두번째 배열인 완주자에 대해서는 위의 HashMap에서 반대로 한명씩 숫자를 차감해 줍니다.
~~~java
for(String nextGuy: completion) {
    myMap.put(nextGuy, myMap.get(nextGuy) - 1);
}
~~~

위 작업이 완료되면, 완주를 못한 1명의 선수 빼고는 모두 선수의 이름에 대해서 value값이 0으로 count 되어 있을 것입니다. HashMap을 순회하면서 value가 1로 나오는 이름이 완주하지 못한 선수의 이름입니다.  

~~~java
String answer = "";

for(String name: myMap.keySet()) {
    if(myMap.get(name) != 0) {
        answer = name;
    }
}

return answer;
~~~

## Solution.java
~~~java
import java.util.*;

class Solution {
    public String solution(String[] participant, String[] completion) {
        Map<String, Integer> myMap = new HashMap<>();

        for(String nextGuy: participant) {
            myMap.put(nextGuy, myMap.getOrDefault(nextGuy, 0) + 1);
        }
        for(String nextGuy: completion) {
            myMap.put(nextGuy, myMap.get(nextGuy) - 1);
        }

        String answer = "";

        for(String name: myMap.keySet()) {
            if(myMap.get(name) != 0) {
                answer = name;
            }
        }

        return answer;
    }
}
~~~
