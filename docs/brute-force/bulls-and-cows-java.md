# 숫자 야구

출처: https://programmers.co.kr/learn/courses/30/lessons/42841

## 지문

숫자 야구 게임이란 2명이 서로가 생각한 숫자를 맞추는 게임입니다.

각자 서로 다른 1~9까지 3자리 임의의 숫자를 정한 뒤 서로에게 3자리의 숫자를 불러서 결과를 확인합니다. 그리고 그 결과를 토대로 상대가 정한 숫자를 예상한 뒤 맞힙니다.

* 숫자는 맞지만, 위치가 틀렸을 때는 볼
* 숫자와 위치가 모두 맞을 때는 스트라이크
* 숫자와 위치가 모두 틀렸을 때는 아웃

예를 들어, 아래의 경우가 있으면

A : 123

B : 1스트라이크 1볼.

A : 356

B : 1스트라이크 0볼.

A : 327

B : 2스트라이크 0볼.

A : 489

B : 0스트라이크 1볼.

이때 가능한 답은 324와 328 두 가지입니다.

질문한 세 자리의 수, 스트라이크의 수, 볼의 수를 담은 2차원 배열 baseball이 매개변수로 주어질 때, 가능한 답의 개수를 return 하도록 solution 함수를 작성해주세요.

제한사항
질문의 수는 1 이상 100 이하의 자연수입니다.
baseball의 각 행은 [세 자리의 수, 스트라이크의 수, 볼의 수] 를 담고 있습니다.

입출력 예
baseball | return
---------|--------
[[123, 1, 1], [356, 1, 0], [327, 2, 0], [489, 0, 1]] | 2

## 해설

[숫자 야구](https://scratch.mit.edu/projects/131352991/) 라는 게임의 규칙을 이용하여 가능한 해답의 수를 세는 문제입니다. 가능한 경우의 수가 000 ~ 999 까지의 숫자에서

1. 0이 포함된 경우
2. 중복된 숫자가 포함된 경우

를 제외하기 때문에 1000개 이하입니다. 주어진 테스트 케이스의 갯수가 적기 때문에 brute force, 즉 완전탐색을 이용해서 해답을 찾아낼 수 있습니다.

아래 문제풀이에서는 특정 경우의 수가 유효한지를 기록하기 위해서 0 ~ 999의 index를 가지는 boolean array를 이용하였습니다.

우선은 주어진 규칙에 맞도록 0이 포함되지 않고, 중복된 숫자가 없도록 유효한 후보들을 true로 체크해 주었습니다.

~~~
for(int i = 1; i < 10; i++) {
    for(int j = 1; j < 10; j++) {
        if(i == j) continue;
        for(int k = 1; k < 10; k++) {
            if(i == k || j == k) continue;
            else valid[i * 100 + j * 10 + k] = true;
        }
    }
}
~~~

문제 풀이 시 최초에는 모든 숫자를 true로 체크한 이후, 0이 포함된 숫자와 중복되는 숫자를 가진 경우를 false로 체크하는 방법을 사용하려고 했으나, 자릿수별 숫자의 중복을 체크하는 노력보다 위와 같이 brute force로 조건에 부합하는 모든 경우를 true로 표시하는 것이 더 간결하여 위 방법을 사용하였습니다.

다음으로는 후보 숫자와 baseball에서 주어진 숫자를 비교하여 스트라이크와 볼 갯수를 계산해내는 메서드를 정의해주어야 합니다. 하기 문제풀이에서는 아래의 함수들이 그 역할을 담당하고 있습니다.

~~~
public int strike(int guess, int question);
public int ball(int guess, int question);
~~~

위 함수들은 어떤 방식이든 상관없이 좋아하는 방식으로 구현하시면 됩니다. 과도하게 불필요한 코드가 포함되지 않은 이상 시간제한에 걸릴 경우는 적을 것 같습니다.

스트라이크, 볼 개수를 체크하는 메서드를 정의한 이후에는 유효한 후보 숫자들에 대해서 baseball 배열의 각 결과에 부합하지 않는 경우들을 지워 나가면 마지막에 모든 케이스를 통과할 수 있는 후보들만을 남길 수 있습니다. 이 후보들의 갯수를 세어서 return 하면 문제의 해답을 완성하셨다고 볼 수 있겠네요!

## 팁

### int[], boolean[] 의 초기값

java에서 boolean array를 선언할 경우 boolean 자료형의 기본값인 false로 초기화되기 때문에 별도의 코드로 false 값 초기화를 해주실 필요는 없습니다.

비슷하게 int array 또한 모든 값이 0으로 초기화됩니다.

그러나 더 엄밀하고 의도한 바를 전달하기 위해서는 명시적으로 모든 값을 초기화하는 코드를 포함하는 것이 좋을 수 있습니다.

### 게임의 영문 이름
숫자 야구의 영문 게임명은 [bulls and cows](https://en.wikipedia.org/wiki/Bulls_and_Cows) 입니다.

## Solution.java
~~~
import java.util.*;

class Solution {
    public int solution(int[][] baseball) {
        // for numbers 000 ~ 999
        boolean[] valid = new boolean[1000];

        for(int i = 1; i < 10; i++) {
            for(int j = 1; j < 10; j++) {
                if(i == j) continue;
                for(int k = 1; k < 10; k++) {
                    if(i == k || j == k) continue;
                    else valid[i * 100 + j * 10 + k] = true;
                }
            }
        }

        for(int j = 0; j < baseball.length; j++) {
            int num = baseball[j][0];
            int numStrike = baseball[j][1];
            int numBall = baseball[j][2];
            for(int i = 111; i < 1000; i++) {
                if(valid[i]) {
                    if ( strike(i, num) != numStrike || ball(i, num) != numBall ) {
                        valid[i] = false;
                    }
                }
            }
        }

        int answer = 0;
        for(int i = 0; i < valid.length; i++) {
            if(valid[i]) {
                answer += 1;
            }
        }

        return answer;
    }

    public int strike(int guess, int question) {
        String guessStr = Integer.toString(guess);
        String questionStr = Integer.toString(question);
        int strikes = 0;
        for(int i = 0; i < 3; i++) {
            if(guessStr.charAt(i) == questionStr.charAt(i)) strikes += 1;
        }
        return strikes;
    }

    public int ball(int guess, int question) {
        String guessStr = Integer.toString(guess);
        String questionStr = Integer.toString(question);
        ArrayList<Character> guessChars = new ArrayList<>();
        for(Character ch: guessStr.toCharArray()) {
            guessChars.add(ch);
        }
        int balls = 0;
        for(Character ch: questionStr.toCharArray()) {
            if(guessChars.contains(ch)) {
                balls += 1;
                guessChars.remove(ch);
            }
        }
        return balls - strike(guess, question);
    }
}
~~~
