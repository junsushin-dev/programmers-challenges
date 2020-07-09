# 정수 삼각형

출처: https://programmers.co.kr/learn/courses/30/lessons/43105

## 지문

![triangle](https://grepp-programmers.s3.amazonaws.com/files/production/97ec02cc39/296a0863-a418-431d-9e8c-e57f7a9722ac.png)

위와 같은 삼각형의 꼭대기에서 바닥까지 이어지는 경로 중, 거쳐간 숫자의 합이 가장 큰 경우를 찾아보려고 합니다. 아래 칸으로 이동할 때는 대각선 방향으로 한 칸 오른쪽 또는 왼쪽으로만 이동 가능합니다. 예를 들어 3에서는 그 아래칸의 8 또는 1로만 이동이 가능합니다.

삼각형의 정보가 담긴 배열 triangle이 매개변수로 주어질 때, 거쳐간 숫자의 최댓값을 return 하도록 solution 함수를 완성하세요.

제한사항
삼각형의 높이는 1 이상 500 이하입니다.
삼각형을 이루고 있는 숫자는 0 이상 9,999 이하의 정수입니다.

입출력 예

triangle | result
---------|-------
[[7], [3, 8], [8, 1, 0], [2, 7, 4, 4], [4, 5, 2, 6, 5]]	| 30

## 해설

동적 할당법(Dynamic Programming)을 이용하여 연산횟수를 줄이는 문제입니다.

완전탐색을 이용할 경우, 삼각형의 층 수가 n 이라고 했을 때, 꼭대기부터 내려가면서 각 층마다 왼쪽, 오른쪽을 2개의 경우의 수가 생기기 때문에 총 2<sup>n</sup> 개에 근사하는 경로에 대한 계산이 필요합니다.

시간복잡도: O(2<sup>n</sup>)

동적 할당법을 이용하여 각 층마다 해당 위치에서 얻을 수 있는 최대값을 저장하면, 모든 경로를 탐색할 필요 없이 각 위치에서는 전 위치인 상위 2개의 값만 체크하고 최대값을 고르면 되기 때문에, 각 위치에서 한번의 비교연산만을 해주면 됩니다. 이 경우 계산 횟수는 삼각형 내부 숫자의 개수인 (1+n) * n / 2 번에 근사합니다.

시간복잡도: O(n<sup>2</sup>)

위 방법을 구현하기 위해서 최초에 주어진 삼각형의 모양을 clone()으로 복사하여 DP를 위한 2차원 배열을 마련하였습니다. 그 이후에는 꼭대기 층을 제외한 층부터 위층의 값을 체크하면서 최대값을 갱신해나가면 문제를 해결할 수 있습니다. 

## Solution.java
~~~
class Solution {
    public int solution(int[][] triangle) {
        int[][] DP = triangle.clone();
        int rows = triangle.length;

        DP[0][0] = triangle[0][0];
        for(int r = 1; r < rows; r++) {
            for(int c = 0; c < triangle[r].length; c++) {
                if(c == 0) {
                    DP[r][c] = triangle[r][c] + DP[r-1][c];
                }
                else if(c == triangle[r].length - 1) {
                    DP[r][c] = triangle[r][c] + DP[r-1][c-1];
                }
                else {
                    DP[r][c] = triangle[r][c] + Math.max(DP[r-1][c-1], DP[r-1][c]);
                }
            }
        }

        int answer = 0;

        for(int c = 0; c < triangle[rows-1].length; c++) {
            answer = Math.max(answer, DP[rows-1][c]);
        }    

        return answer;
    }
}
~~~
