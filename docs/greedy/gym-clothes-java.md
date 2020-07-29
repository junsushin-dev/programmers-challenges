# 체육복

출처: https://programmers.co.kr/learn/courses/30/lessons/42862

## 지문
점심시간에 도둑이 들어, 일부 학생이 체육복을 도난당했습니다. 다행히 여벌 체육복이 있는 학생이 이들에게 체육복을 빌려주려 합니다. 학생들의 번호는 체격 순으로 매겨져 있어, 바로 앞번호의 학생이나 바로 뒷번호의 학생에게만 체육복을 빌려줄 수 있습니다. 예를 들어, 4번 학생은 3번 학생이나 5번 학생에게만 체육복을 빌려줄 수 있습니다. 체육복이 없으면 수업을 들을 수 없기 때문에 체육복을 적절히 빌려 최대한 많은 학생이 체육수업을 들어야 합니다.

전체 학생의 수 n, 체육복을 도난당한 학생들의 번호가 담긴 배열 lost, 여벌의 체육복을 가져온 학생들의 번호가 담긴 배열 reserve가 매개변수로 주어질 때, 체육수업을 들을 수 있는 학생의 최댓값을 return 하도록 solution 함수를 작성해주세요.

제한사항
- 전체 학생의 수는 2명 이상 30명 이하입니다.
- 체육복을 도난당한 학생의 수는 1명 이상 n명 이하이고 중복되는 번호는 없습니다.
- 여벌의 체육복을 가져온 학생의 수는 1명 이상 n명 이하이고 중복되는 번호는 없습니다.
- 여벌 체육복이 있는 학생만 다른 학생에게 체육복을 빌려줄 수 있습니다.
- 여벌 체육복을 가져온 학생이 체육복을 도난당했을 수 있습니다. 이때 이 학생은 체육복을 하나만 도난당했다고 가정하며, 남은 체육복이 하나이기에 다른 학생에게는 체육복을 빌려줄 수 없습니다.

입출력 예
n	|lost	|reserve	|return
--|-----|--------|------
5	|[2, 4]	|[1, 3, 5]	|5
5	|[2, 4]	|[3]	|4
3	|[3]	|[1]	|2

입출력 예 설명

예제 #1

1번 학생이 2번 학생에게 체육복을 빌려주고, 3번 학생이나 5번 학생이 4번 학생에게 체육복을 빌려주면 학생 5명이 체육수업을 들을 수 있습니다.

예제 #2

3번 학생이 2번 학생이나 4번 학생에게 체육복을 빌려주면 학생 4명이 체육수업을 들을 수 있습니다.

## 해설

여유분의 체육복을 최대 인원이 쓸 수 있도록 배분하는 문제입니다. 배분할 수 있는 가짓수를 모두 따질때는 최악의 경우 n! 에 비례하는 연산이 필요하기 때문에 Greedy Algoritm을 이용해서 연산을 줄여야 합니다. 

Greedy Algorithm 을 사용하기 위해서는 현재 상태에서의 최적으로 보이는 판단이 전체적으로도 최적의 판단이 되어야 합니다. 

체육복 배분의 경우, '번호가 낮은 학생부터 시작해서 낮은 번호의 체육복을 선택한다' (혹은 반대로 높은 번호부터 높은 체육복) 라는 규칙을 지키면 위 조건을 만족합니다. 

예시)

여유분: 1, 3

도난당한 학생: 2, 4

- 2번 학생은 1 혹은 3번을 선택할 수 있습니다. 자신이 가장 낮은 번호이므로, 1번 체육복은 자신 외에는 가져갈 사람이 남아있지 않습니다. 따라서 1번 체육복을 선택합니다. 

- 4번 학생은 3번 체육복을 선택할 수 있게 됩니다.

- 모든 체육복이 할당되어 최적의 상황을 달성하였습니다. 

위와 같이 번호 순서대로 할당을 하기 위해서 입력받은 배열들을 정렬해줍니다. 
~~~
Arrays.sort(lost);
Arrays.sort(reserve);
~~~

List의 .contains() 함수를 쓰기 위해서 배열의 내용물을 리스트로 옮겨줍니다. 
~~~
List<Integer> lostList = new ArrayList();
for(int i = 0; i < lost.length; i++) {
    lostList.add(lost[i]);
}

List<Integer> reserveList = new ArrayList();
for(int i = 0; i < reserve.length; i++) {
    reserveList.add(reserve[i]);
}
~~~

체육복을 여유분이 있으면서 도난당한 학생을 빌려줄 수 없으므로, 두 List에 모두 존재하는 번호를 찾아내어 양 리스트에서 제거해줍니다. 
~~~
List<Integer> lostAndFound = new ArrayList();
for(Integer r: reserveList) {
    if(lostList.contains(r)) {
        lostAndFound.add(r);
    }
} 

for(Integer r: lostAndFound) {
    lostList.remove(r);
    reserveList.remove(r);
}
~~~

위에서 말한 규칙과 같이 번호가 낮은 학생부터 번호가 낮은 체육복을 우선으로 가져갑니다. 
~~~
for(Integer r: reserveList) {
    if(lostList.contains(r-1)) {
        int idx = lostList.indexOf(r-1);
        lostList.remove(idx);
    }
    else if(lostList.contains(r+1)) {
        int idx = lostList.indexOf(r+1);
        lostList.remove(idx);
    }
    else {
        ;
    }
}
~~~

전체 학생의 숫자 n 에서 체육복을 끝까지 받지 못한 학생의 수를 빼면 체육시간에 참여할 수 있는 학생의 수를 구할 수 있습니다. 
~~~
int answer = n - lostList.size();
return answer;
~~~

## Solution.java
~~~
import java.util.*;

class Solution {
    public int solution(int n, int[] lost, int[] reserve) {
        
        Arrays.sort(lost);
        Arrays.sort(reserve);
        
        List<Integer> lostList = new ArrayList();
        for(int i = 0; i < lost.length; i++) {
            lostList.add(lost[i]);
        }
        
        List<Integer> reserveList = new ArrayList();
        for(int i = 0; i < reserve.length; i++) {
            reserveList.add(reserve[i]);
        }
        
        List<Integer> lostAndFound = new ArrayList();
        for(Integer r: reserveList) {
            if(lostList.contains(r)) {
                lostAndFound.add(r);
            }
        } 
        
        for(Integer r: lostAndFound) {
            lostList.remove(r);
            reserveList.remove(r);
        }
        
        
        
        for(Integer r: reserveList) {
            if(lostList.contains(r-1)) {
                int idx = lostList.indexOf(r-1);
                lostList.remove(idx);
            }
            else if(lostList.contains(r+1)) {
                int idx = lostList.indexOf(r+1);
                lostList.remove(idx);
            }
            else {
                ;
            }
        }
        
        int answer = n - lostList.size();
        return answer;
    }
}
~~~