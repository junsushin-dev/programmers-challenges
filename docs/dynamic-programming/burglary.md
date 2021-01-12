# 도둑질

출처: https://programmers.co.kr/learn/courses/30/lessons/42897

## 지문

도둑이 어느 마을을 털 계획을 하고 있습니다. 이 마을의 모든 집들은 아래 그림과 같이 동그랗게 배치되어 있습니다.

![houses](https://grepp-programmers.s3.amazonaws.com/files/ybm/e7dd4f51c3/a228c73d-1cbe-4d59-bb5d-833fd18d3382.png)

각 집들은 서로 인접한 집들과 방범장치가 연결되어 있기 때문에 인접한 두 집을 털면 경보가 울립니다.

각 집에 있는 돈이 담긴 배열 money가 주어질 때, 도둑이 훔칠 수 있는 돈의 최댓값을 return 하도록 solution 함수를 작성하세요.

제한사항

- 이 마을에 있는 집은 3개 이상 1,000,000개 이하입니다.
- money 배열의 각 원소는 0 이상 1,000 이하인 정수입니다.

입출력 예

|money	|return |
|-------|-------|
|[1, 2, 3, 1]|4 |

## 해설 - Java
DP를 이용해서 최대값을 구하는 문제입니다. 평소의 DP 문제와 다르게 첫번째 원소와 마지막 원소가 맞닿아 있기 때문에, 마지막 부분에서 관련된 처리를 해주어야 합니다. 

Brute Force(완전탐색)으로 경우의 수를 따져볼 경우, 집의 개수가 n개 일때 대략 <sub>n</sub>C<sub>n/2</sub>의 경우의 수가 발생할 것입니다. (n개의 집이 존재하고, 하나는 건너뛰어야 하므로 대략 반정도의 집을 털게 됩니다)

연산 횟수를 줄이기 위해서, DP를 이용해서 구간별로 최대값을 갱신해 나가는 방법을 사용하도록 하겠습니다. 

index 0 부터 시작했을 때, 
0 ~ a 구간의 최대값은
- a번째 집을 터는 경우: 0 ~ a-1 구간의 최대값과 동일
- a번째 집을 털지 않는 경우: 0 ~ a-2 구간의 최대값 + money[a]
위 두 값의 최대값이 될 것입니다. 

문제는 마지막 집의 처리입니다. 0 ~ n구간의 합을 구할 때, 0 ~ n-2 구간의 최대값에 money[n]을 더하는 경우 문제가 발생할 수 있습니다. 
0과 n이 맞닿아 있기 때문에, 0 ~ n-2 구간의 최대값을 구하는 과정에 money[0]을 사용하면 안됩니다. 

따라서 money[n]을 더할 수 있는 경우는,
- money[0]을 사용하지 않음
- money[n-1]을 사용하지 않음
위 두 가지를 모두 만족하는 경우입니다.

마지막에 위 조건을 만족해주기 위해서, 4가지 경우로 나누어서 index 별로 최대값을 관리합니다. 

0 ~ a 구간의 최대값일 때,
1. 0 X, a X = max(0 ~ a-1 구간의 0 X a X, 0 ~ a-1 구간의 0 X a O)
2. 0 X, a O = 0 ~ a-1 구간의 0 X a X + money[a]
3. 0 O, a X = max(0 ~ a-1 구간의 0 O a X, 0 ~ a-1 구간의 0 O a O)
4. 0 O, a O = 0 ~ a-1 구간의 0 O a X + money[a]

위 로직과 같이 각 구간의 경우별 최대값을 업데이트 할 수 있습니다. 마지막 원소를 체크할 때에는, 더할 수 있는 경우가 0, n-1 원소를 모두 사용하지 않은 경우입니다. 따라서 0 ~ n - 1 구간의 0 X a X + money[n]과, 0 ~ n - 1 구간의 나머지 경우들 중에 최대값을 찾아주면 됩니다. 

## 팁
1 부터 시작하는 구간을 별도로 관리해서 마지막 원소를 더할 수 있는 경우를 고려해주는 방법도 있습니다. 모든 원소에 대해서 0 ~ a 구간 최대값과 1 ~ a 구간의 최대값을 유지하면서 업데이트합니다. 이 방법을 이용한 해답은 Solution.java 2를 참조해 주세요. 

## Solution.java 1- 소요시간 45분
~~~java
import java.util.*;

class Solution {
    public int solution(int[] money) {
        Node[] nodes = new Node[money.length];
        Node firstNode = new Node();
        firstNode.noFirstNoLast = 0;
        firstNode.noFirstYesLast = 0;
        firstNode.yesFirstNoLast = 0;
        firstNode.yesFirstYesLast = money[0];
        nodes[0] = firstNode;
        
        for(int i = 1; i < nodes.length - 1; i++) {
            Node prev = nodes[i - 1];
            Node node = new Node();
            node.noFirstNoLast = Math.max(prev.noFirstYesLast, prev.noFirstNoLast);
            node.noFirstYesLast = prev.noFirstNoLast + money[i];
            node.yesFirstNoLast = Math.max(prev.yesFirstNoLast, prev.yesFirstYesLast);
            node.yesFirstYesLast = prev.yesFirstNoLast + money[i];
            nodes[i] = node;
        }
        
        int lastIndex = nodes.length - 1;
        Node secondLastNode = nodes[lastIndex - 1];
        int[] candidates = { secondLastNode.noFirstNoLast + money[lastIndex], secondLastNode.noFirstYesLast, secondLastNode.yesFirstNoLast, secondLastNode.yesFirstYesLast };
        
        int answer = Arrays.stream(candidates).max().getAsInt();
        return answer;
    }
    
    class Node {
        int noFirstNoLast;
        int noFirstYesLast;
        int yesFirstNoLast;
        int yesFirstYesLast;
    }
}
~~~

## Solution.java 2 - 소요시간 20분
~~~java
class Solution {
    public int solution(int[] money) {
        int[][] DP = new int[2][money.length];
        
        DP[0][0] = money[0];
        DP[0][1] = Math.max(money[0], money[1]);
        DP[1][0] = 0;
        DP[1][1] = money[1];    
        
        for(int i = 2; i < money.length; i++) {
            if(i < money.length - 1) DP[0][i] = Math.max(DP[0][i-2] + money[i], DP[0][i-1]);
            else DP[0][i] = DP[0][i-1];
            DP[1][i] = Math.max(DP[1][i-2] + money[i], DP[1][i-1]);
        }
        
        int answer = Math.max(DP[0][money.length - 1], DP[1][money.length - 1]);
        return answer;
    }
}
~~~