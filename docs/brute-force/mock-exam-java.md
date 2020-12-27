# 모의고사

출처: https://programmers.co.kr/learn/courses/30/lessons/42840

## 지문
수포자는 수학을 포기한 사람의 준말입니다. 수포자 삼인방은 모의고사에 수학 문제를 전부 찍으려 합니다. 수포자는 1번 문제부터 마지막 문제까지 다음과 같이 찍습니다.

1번 수포자가 찍는 방식: 1, 2, 3, 4, 5, 1, 2, 3, 4, 5, ...

2번 수포자가 찍는 방식: 2, 1, 2, 3, 2, 4, 2, 5, 2, 1, 2, 3, 2, 4, 2, 5, ...

3번 수포자가 찍는 방식: 3, 3, 1, 1, 2, 2, 4, 4, 5, 5, 3, 3, 1, 1, 2, 2, 4, 4, 5, 5, ...

1번 문제부터 마지막 문제까지의 정답이 순서대로 들은 배열 answers가 주어졌을 때, 가장 많은 문제를 맞힌 사람이 누구인지 배열에 담아 return 하도록 solution 함수를 작성해주세요.

제한 조건
- 시험은 최대 10,000 문제로 구성되어있습니다.
- 문제의 정답은 1, 2, 3, 4, 5중 하나입니다.
- 가장 높은 점수를 받은 사람이 여럿일 경우, return하는 값을 오름차순 정렬해주세요.

입출력 예

answers	| return
--------|--------
[1,2,3,4,5]	| [1]
[1,3,2,4,2]	| [1,2,3]

입출력 예 설명

입출력 예 1

수포자 1은 모든 문제를 맞혔습니다.

수포자 2는 모든 문제를 틀렸습니다.

수포자 3은 모든 문제를 틀렸습니다.

따라서 가장 문제를 많이 맞힌 사람은 수포자 1입니다.

입출력 예 2

모든 사람이 2문제씩을 맞췄습니다.

## 해설

문제의 요구사항에 맞추어서 모든 경우의 수를 따지는 완전탐색을 통해 해결하는 문제입니다. 기본적으로 주어진 수포자 1~3이 찍는 방식을 시뮬레이션을 한다고 생각하면 됩니다.

수포자의 찍는 방식과 점수를 개념적으로 분리하기 위해서 TestTaker 클래스를 정의하였습니다.
~~~java
class TestTaker {
    public int length;
    public int currIndex;
    public int[] answers;
    public int score;

    public TestTaker(int length, int[] answers) {
        this.length = length;
        this.currIndex = 0;
        this.answers = answers;
        this.score = 0;
    }

    public int pickAnswer() {
        int ret = answers[currIndex];
        this.currIndex = (this.currIndex + 1) % this.length;
        return ret;
    }

    public void scoreOne() {
        this.score += 1;
    }

    public int getScore() {
        return this.score;
    }

}
~~~
TestTaker 클래스는 length, index 등의 기본적인 변수와 돌아가며 찍는 번호의 배열인 answers, 그리고 점수인 score를 가지고 있습니다. pickAnswer() 함수를 통해 다음 찍을 번호를 가져오고, scoreOne()을 통해 답안을 맞출 경우 score를 1씩 증가시킬 수 있습니다.

정의된 클래스를 이용하여 수포자 1~3에 해당하는 인스턴스를 만들어 줍니다. 만들어준 인스턴스들은 for 문에서 사용하기 위해 리스트에 저장합니다.
~~~java
TestTaker first = new TestTaker(5, new int[]{1,2,3,4,5});
TestTaker second = new TestTaker(8, new int[]{2,1,2,3,2,4,2,5});
TestTaker third = new TestTaker(10, new int[]{3,3,1,1,2,2,4,4,5,5});

List<TestTaker> testTakers = new ArrayList<>();
testTakers.add(first);
testTakers.add(second);
testTakers.add(third);
~~~

주어진 문제번호에 대해서 각 수포자의 찍는 과정을 시뮬레이션합니다.
~~~java
for(int i = 0; i < answers.length; i++) {
    for(TestTaker person: testTakers) {
        if(person.pickAnswer() == answers[i]) {
            person.scoreOne();
        }
    }
}
~~~

최고점수를 계산하고 최고점수를 받은 수포자의 번호를 답으로 반환하기 위하여 추가해 줍니다. 동점자의 경우 오름차순 정렬이 필요하나, 0부터 시작하여 낮은 번호부터 추가하기 때문에 별도의 정렬과정은 필요 없습니다.
~~~java
int maxScore = 0;
for(TestTaker person: testTakers) {
    maxScore = Math.max(maxScore, person.getScore());
}

List<Integer> topScore = new ArrayList<>();
for(int i=0; i<3; i++) {
    if(testTakers.get(i).getScore()==maxScore) {
        topScore.add(i+1);
    }
}

int[] answer = new int[topScore.size()];
for(int i=0; i<topScore.size(); i++) {
    answer[i] = topScore.get(i);
}

return answer;
~~~

## Solution.java
~~~java
import java.util.*;

class Solution {
    public int[] solution(int[] answers) {

        class TestTaker {
            public int length;
            public int currIndex;
            public int[] answers;
            public int score;

            public TestTaker(int length, int[] answers) {
                this.length = length;
                this.currIndex = 0;
                this.answers = answers;
                this.score = 0;
            }

            public int pickAnswer() {
                int ret = answers[currIndex];
                this.currIndex = (this.currIndex + 1) % this.length;
                return ret;
            }

            public void scoreOne() {
                this.score += 1;
            }

            public int getScore() {
                return this.score;
            }

        }

        TestTaker first = new TestTaker(5, new int[]{1,2,3,4,5});
        TestTaker second = new TestTaker(8, new int[]{2,1,2,3,2,4,2,5});
        TestTaker third = new TestTaker(10, new int[]{3,3,1,1,2,2,4,4,5,5});

        List<TestTaker> testTakers = new ArrayList<>();
        testTakers.add(first);
        testTakers.add(second);
        testTakers.add(third);

        for(int i = 0; i < answers.length; i++) {
            for(TestTaker person: testTakers) {
                if(person.pickAnswer() == answers[i]) {
                    person.scoreOne();
                }
            }
        }

        int maxScore = 0;
        for(TestTaker person: testTakers) {
            maxScore = Math.max(maxScore, person.getScore());
        }

        List<Integer> topScore = new ArrayList<>();
        for(int i=0; i<3; i++) {
            if(testTakers.get(i).getScore()==maxScore) {
                topScore.add(i+1);
            }
        }

        int[] answer = new int[topScore.size()];
        for(int i=0; i<topScore.size(); i++) {
            answer[i] = topScore.get(i);
        }

        return answer;
    }
}
~~~
