# 전화번호 목록

출처: https://programmers.co.kr/learn/courses/30/lessons/42577

## 지문

전화번호부에 적힌 전화번호 중, 한 번호가 다른 번호의 접두어인 경우가 있는지 확인하려 합니다.
전화번호가 다음과 같을 경우, 구조대 전화번호는 영석이의 전화번호의 접두사입니다.

구조대 : 119

박준영 : 97 674 223

지영석 : 11 9552 4421

전화번호부에 적힌 전화번호를 담은 배열 phone_book 이 solution 함수의 매개변수로 주어질 때, 어떤 번호가 다른 번호의 접두어인 경우가 있으면 false를 그렇지 않으면 true를 return 하도록 solution 함수를 작성해주세요.

제한 사항
- phone_book의 길이는 1 이상 1,000,000 이하입니다.
- 각 전화번호의 길이는 1 이상 20 이하입니다.

입출력 예제
phone_book	| return
------------|--------
[119, 97674223, 1195524421]	| false
[123,456,789]	| true
[12,123,1235,567,88]	| false
입출력 예 설명

입출력 예 1

앞에서 설명한 예와 같습니다.

입출력 예2

한 번호가 다른 번호의 접두사인 경우가 없으므로, 답은 true입니다.

입출력 예 3

첫 번째 전화번호, “12”가 두 번째 전화번호 “123”의 접두사입니다. 따라서 답은 false입니다.

## 해설

주어진 배열 안에서 하나의 String이 다른 String의 substring 인 관계가 존재하는지 묻는 문제입니다. Hash 카테고리에 들어있지만, Hash를 사용하지 않아도 되는 문제입니다. 완전탐색으로 모든 쌍을 비교해볼 경우 O(<sup>2</sup>)의 시간복잡도가 걸립니다. 좀더 효율적으로 만들어주기 위해서는 특정 String A가 포함될 수 있는 String들은 A보다 길이가 긴 String만이라는 사실을 이용하여 연산시간을 아낄 수 있습니다.

주어진 전화번호들을 길이에 따라서 정렬하고, 자신보다 긴 번호에 대해서 자신과 같은 길이만큼을 앞에서부터 잘라서 동일한지 비교하면 쉽게 문제를 해결할 수 있습니다.

## 팁

Hash 함수를 사용하고자 한다면, 비교하게 되는 전화번호의 String 함수의 hash 함수를 Override 하거나 새로운 Class를 정의하여 두 전화번호를 비교 시에 양쪽 중 짧은 길이에 해당하는 만큼을 잘라서 비교하여 일치 판정을 내리도록 구성할 수 있습니다.

String 비교시에는 == 연산자가 아닌 .equals 함수를 이용해야 문자열을 제대로 비교할 수 있습니다. == 연산자는 해당 변수가 같은 reference(C의 경우 포인터)를 가리키고 있는 지 체크하고, .equals 함수는 문자열의 내용을 실제로 비교하여 다른 referece를 가지더라도 동일한 문자열인지를 체크해줄 수 있습니다. 

## Solution.java
~~~
import java.util.*;

class Solution {
    public boolean solution(String[] phone_book) {
        Arrays.sort(phone_book, new Comparator<String>() {
            // Override된 compare 함수를 어떻게 정의하냐에 따라서 다양한 정렬이 가능해집니다
            @Override
            public int compare(String o1, String o2) {
                return o1.length() - o2.length();
                // 내림자순 정렬을 하고 싶다면 o2와 o1의 위치를 바꿔줍니다
                // return o2[1] - o1[1];
            }            
        });

        for(int i = 0; i < phone_book.length; i++) {
            String curr = phone_book[i];
            for(int j = i+1; j < phone_book.length; j++) {
                String toCompare = phone_book[j].substring(0, curr.length());
                if(curr.equals(toCompare)) return false;
            }
        }

        boolean answer = true;
        return answer;
    }
}
~~~
