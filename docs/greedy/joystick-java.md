# H-index

출처: https://programmers.co.kr/learn/courses/30/lessons/42860

## 지문

조이스틱으로 알파벳 이름을 완성하세요. 맨 처음엔 A로만 이루어져 있습니다.
ex) 완성해야 하는 이름이 세 글자면 AAA, 네 글자면 AAAA

조이스틱을 각 방향으로 움직이면 아래와 같습니다.

~~~
▲ - 다음 알파벳
▼ - 이전 알파벳 (A에서 아래쪽으로 이동하면 Z로)
◀ - 커서를 왼쪽으로 이동 (첫 번째 위치에서 왼쪽으로 이동하면 마지막 문자에 커서)
▶ - 커서를 오른쪽으로 이동
~~~

예를 들어 아래의 방법으로 JAZ를 만들 수 있습니다.

~~~
- 첫 번째 위치에서 조이스틱을 위로 9번 조작하여 J를 완성합니다.
- 조이스틱을 왼쪽으로 1번 조작하여 커서를 마지막 문자 위치로 이동시킵니다.
- 마지막 위치에서 조이스틱을 아래로 1번 조작하여 Z를 완성합니다.
따라서 11번 이동시켜 "JAZ"를 만들 수 있고, 이때가 최소 이동입니다.
~~~
만들고자 하는 이름 name이 매개변수로 주어질 때, 이름에 대해 조이스틱 조작 횟수의 최솟값을 return 하도록 solution 함수를 만드세요.

제한 사항
- name은 알파벳 대문자로만 이루어져 있습니다.
- name의 길이는 1 이상 20 이하입니다.


입출력 예
name | return
-----|-------
"JEROEN" | 56
"JAN"	| 23

## 해설

두가지 파트로 나누어서 풀 수 있는 문제입니다. 예전에 오락실 랭킹에 이름 적는 방식을 떠올리게 하는 문제네요.

1. 알파벳을 바꾸는 최소의 ▲ ▼ 횟수
초기값이 A로 세팅되어 있기 때문에, A, B, C... 와 같이 위로 이동하여 원하는 알파벳을 찾을 지 또는 A, Z, Y, X...와 같이 밑으로 이동하여 원하는 알파벳을 찾을 지 두가지 경우를 비교해야 합니다.

알파벳은 26가지가 존재하기 때문에, A에서부터 해당 알파벳 순번까지의 거리와, 26에서 이 거리를 뺀 숫자를 비교하면 쉽게 최적의 수를 찾을 수 있습니다.  

~~~
int answer = 0;

for(char ch: name.toCharArray()) {
    answer += Math.min(ch - 'A', 26 - (ch - 'A'));
}
~~~

2. 모든 A가 아닌 알파벳 위치를 방문하는 최소의 ◀ ▶ 횟수

위 알파벳 보다는 경우의 수를 따지기가 까다롭습니다. 아래 문제풀이에서는 이동할 수 있는 경우의 수가

- 현재 위치에서 A가 아닌 문자가 나올때까지 왼쪽으로 이동
- 현재 위치에서 A가 아닌 문자가 나올때까지 오른쪽으로 이동

두 경우만을 존재하는 것을 이용하여, 방문한 문자를 지워주면서 완전탐색 방식의 시뮬레이션을 돌리는 것으로 문제를 해결하였습니다. 아래 문제풀이에서는 simulate 함수가 역할을 담당하고 있습니다.

예시)

현재위치 | 문자열 상태 | 비고 | 이동횟수
--------|------------|------|---------
0 | <strong>B</strong>ABAABBB | 초기상태 | 0
0 | <strong>A</strong>ABAABBB | 현재위치 방문완료처리 | 0
1 | AA<strong>B</strong>AABBB | 오른쪽 이동 | 2
6 | AABAABB<strong>B</strong> | 왼쪽 이동 | 1

오른쪽이동, 왼쪽이동을 수행한 상태에서 다시 재귀적으로 각 상황에서의 오른쪽, 왼쪽으로 이동하는 경우를 평가하여 모든 상황을 테스트해 볼 수 있습니다.

base case는 모든 문자가 A로 변경된 경우로, 이 경우 작업이 완료되었으므로 이동이 불필요하므로 0을 반환해 주면 됩니다.


## 팁

java의 primitive type 중 하나인 char의 경우에는 더하기, 빼기를 할 경우 ASCII Code 값 연산이 가능합니다. 따라서 A부터 해당 알파벳까지의 순번을 구할 경우 다음 코드로 간단히 구현 가능합니다.

예시)

'A'의 ASCII Code: 65
'G'의 ASCII Code: 71

'A' ~ 'G' 까지의 거리: 71 - 65 = 6
~~~
int diff = 'G' - 'A';
~~~

## Solution.java
~~~
class Solution {
    public int solution(String name) {
        int answer = 0;

        for(char ch: name.toCharArray()) {
            answer += Math.min(ch - 'A', 26 - (ch - 'A'));
        }

        boolean[] notA = new boolean[name.length()];
        for(int i = 0; i < name.length(); i++) {
            if(name.charAt(i) != 'A') {
                notA[i] = true;
            }
        }

        answer += simulate(notA, 0);

        return answer;
    }

    public int simulate(boolean[] notA, int pos) {        
        notA[pos] = false;

        boolean allA = true;
        for(int i = 0; i < notA.length; i++) {
            if(notA[i]) allA = false;
        }

        if(allA) return 0;

        int left = 1;
        int posLeft = (pos-1+notA.length) % notA.length;
        boolean[] leftArr = notA.clone();
        // try moving left
        while( !leftArr[(posLeft+notA.length) % notA.length] ) {
            left += 1;
            posLeft = (posLeft-1+notA.length) % notA.length;
        }
        left += simulate(leftArr, posLeft);

        int right = 1;
        int posRight = (pos+1) % notA.length;
        boolean[] rightArr = notA.clone();
        // try moving left
        while( !rightArr[(posRight) % notA.length] ) {
            right += 1;
            posRight = (posRight+1) % notA.length;
        }
        right += simulate(rightArr, posRight);

        return Math.min(left, right);
    }
}
~~~
