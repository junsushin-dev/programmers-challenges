# 등굣길

출처: https://programmers.co.kr/learn/courses/30/lessons/42898

## 지문

계속되는 폭우로 일부 지역이 물에 잠겼습니다. 물에 잠기지 않은 지역을 통해 학교를 가려고 합니다. 집에서 학교까지 가는 길은 m x n 크기의 격자모양으로 나타낼 수 있습니다.

아래 그림은 m = 4, n = 3 인 경우입니다.

![예시1](https://grepp-programmers.s3.amazonaws.com/files/ybm/056f54e618/f167a3bc-e140-4fa8-a8f8-326a99e0f567.png)

가장 왼쪽 위, 즉 집이 있는 곳의 좌표는 (1, 1)로 나타내고 가장 오른쪽 아래, 즉 학교가 있는 곳의 좌표는 (m, n)으로 나타냅니다.

격자의 크기 m, n과 물이 잠긴 지역의 좌표를 담은 2차원 배열 puddles이 매개변수로 주어집니다. 오른쪽과 아래쪽으로만 움직여 집에서 학교까지 갈 수 있는 최단경로의 개수를 1,000,000,007로 나눈 나머지를 return 하도록 solution 함수를 작성해주세요.

제한사항
- 격자의 크기 m, n은 1 이상 100 이하인 자연수입니다.
- m과 n이 모두 1인 경우는 입력으로 주어지지 않습니다.
- 물에 잠긴 지역은 0개 이상 10개 이하입니다.
- 집과 학교가 물에 잠긴 경우는 입력으로 주어지지 않습니다.

입출력 예
m|	n|	puddles|	return
-|-|-|-
4|	3|	[[2, 2]]|	4

입출력 예 설명

![예시2](https://grepp-programmers.s3.amazonaws.com/files/ybm/32c67958d5/729216f3-f305-4ad1-b3b0-04c2ba0b379a.png)

## 해설

Dynamic Programming 으로 경우의 수 계산을 효율적으로 만드는 문제입니다. 

Brute Force 방식으로 문제를 해결한다면 아래와 같은 방법을 생각해볼 수 있습니다. 

1. 집에서 학교까지 가기 위해서는 오른쪽(R) 3번, 아래쪽(D) 2번 이동이 필요합니다.
2. 어느 순서로 오른쪽, 아래쪽을 택하던지 횟수만 채우면 도착하게 됩니다. 따라서 학교에 갈 수 있는 길의 경우의 수는 RRRDD를 섞어서 배열할 수 있는 경우의 수와 같습니다. 
- e.g) m = 4, n = 3 
  - 오른쪽 3번, 아래쪽 2번 필요
  - 경우의 수: <sub>5</sub>C<sub>2</sub>
  - RRRDD, RRDRD, RRDDR, RDRRD, RDRDR, RDDRR, DRRRD, DRRDR, DRDRR, DDRRR
3. 웅덩이가 있을 경우에는 웅덩이를 거쳐 가는 경로들을 모두 빼주어야 합니다. 
- e.g) (2, 2)에 웅덩이가 있을 경우
  - 시작(1,1) -> 웅덩이(2,2) -> 학교(4, 3) 경로를 빼주어야 함.
  - 시작 -> 웅덩이 경우의 수: <sub>2</sub>C<sub>1</sub> (RD 배열하기)
  - 웅덩이 -> 학교 경우의 수: <sub>3</sub>C<sub>1</sub> (RRD 배열하기)
  - 답안: 10 - 2 x 3 = 4
4. 하지만 웅덩이가 여러개가 있을 경우 위와 같은 방법을 사용하면 중복으로 경우의 수가 빠지게 됩니다. 
- e.g)
  - 시작 -> 학교: 10
  - 시작 -> 웅덩이1(2,2) -> 학교: 6
  - 시작 -> 웅덩이2(3,2) -> 학교: 6
  - 시작 -> 웅덩이1(2,2) -> 웅덩이2(3,2) -> 학교: 2
  - 답안: 10 - (6 + 6 - 4) = 2

웅덩이가 늘어날수록 계산해주어야 하는 경우의 수가 과도하게 많아지는 것을 알 수 있습니다. 또한 웅덩이의 선후관계를 설정하는 수고로움이 따릅니다. 

위와 같은 Brute Force 방식을 사용하지 않고, 아래와 같은 아이디어를 이용해볼 수 있습니다.
~~~
- 오른쪽과 아래쪽으로만 이동이 가능하다.
- 따라서 현재 위치에 도달하는 방법은 왼쪽에서 오거나, 위쪽에서 오는 방법밖에 없다. 
- 현재 위치의 도달하는 경우의 수는 왼쪽 타일에 도달하는 경우의 수와 위쪽 타일에 도달하는 경우의 수의 합이다.
~~~

최초 시작지점은 1로 처리하고, 웅덩이에서는 올 수 없기 때문에 0으로 처리해주면 간단하게 웅덩이까지 고려한 경우의 수를 얻을 수 있습니다. 

![f167a3bc-e140-4fa8-a8f8-326a99e0f567](https://user-images.githubusercontent.com/32405358/103172006-ad4f9b80-4893-11eb-8124-4e62368ed878.png)

위와 같은 로직을 사용하면 각 타일 당 1번씩만 더하기, 나머지 연산을 해주면 결과를 얻을 수 있다는 것을 알 수 있습니다. 따라서 시간복잡도는 O(M * N)이 됩니다. M과 N이 100이 상한선이므로, worst case 10000번의 연산으로 해결이 가능함을 알 수 있습니다. 보통 1억번은 연산이 1초 정도 걸린다고 가정해볼 수 있는데, 이정도면 넉넉하겠죠?

## Solution.java
~~~java
class Solution {
    public int solution(int m, int n, int[][] puddles) {
        int[][] cells = new int[m+1][n+1];
        
        for(int x = 1; x <= m; x++) {
            for(int y = 1; y <= n; y++) {
                cells[x][y] = -1; // not visited
            }
        }
        
        cells[1][1] = 1; // start position
        for(int[] puddle: puddles) {
            int x = puddle[0];
            int y = puddle[1];
            cells[x][y] = 0;
        }
        
        for(int x = 1; x <= m; x++) {
            for(int y = 1; y <= n; y++) {
                if(cells[x][y] != -1) continue;
                cells[x][y] = (cells[x-1][y] + cells[x][y-1]) % 1000000007;
            }
        }
        
        int answer = cells[m][n];
        return answer;
    }
}
~~~