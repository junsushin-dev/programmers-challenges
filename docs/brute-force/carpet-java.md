# 카펫

출처: https://programmers.co.kr/learn/courses/30/lessons/42842

## 지문

Leo는 카펫을 사러 갔다가 아래 그림과 같이 중앙에는 노란색으로 칠해져 있고 테두리 1줄은 갈색으로 칠해져 있는 격자 모양 카펫을 봤습니다.

![carpet](https://grepp-programmers.s3.ap-northeast-2.amazonaws.com/files/production/b1ebb809-f333-4df2-bc81-02682900dc2d/carpet.png)

Leo는 집으로 돌아와서 아까 본 카펫의 노란색과 갈색으로 색칠된 격자의 개수는 기억했지만, 전체 카펫의 크기는 기억하지 못했습니다.

Leo가 본 카펫에서 갈색 격자의 수 brown, 노란색 격자의 수 yellow가 매개변수로 주어질 때 카펫의 가로, 세로 크기를 순서대로 배열에 담아 return 하도록 solution 함수를 작성해주세요.

제한사항
갈색 격자의 수 brown은 8 이상 5,000 이하인 자연수입니다.
노란색 격자의 수 yellow는 1 이상 2,000,000 이하인 자연수입니다.
카펫의 가로 길이는 세로 길이와 같거나, 세로 길이보다 깁니다.

입출력 예

brown	| yellow	| return
-- | -- | --
10	|2|	[4, 3]
8	|1|	[3, 3]
24	|24|	[8, 6]

## 해설

타일의 갯수가 주어졌을 때, 카펫의 가로 세로 길이를 구하는 문제입니다. 완전탐색 문제로 분류되어 있으나, brown, yellow 에 대한 값이 주어졌을 때, 카펫의 가로길이(x)와 세로길이(y) 두 가지 값을 구하는 문제이기 때문에 방정식으로 풀이가 가능합니다. 

brown = B = 갈색 타일의 갯수
yellow = Y = 노란 타일의 갯수
x = 카펫의 가로 길이
y = 카펫의 세로 길이

B + Y = xy (가로 x 세로 = 전체 타일 갯수)
B = 2x + 2y - 4 (갈색 타일 갯수 = 가로 두줄 + 세로 두줄 = 4(겹치는 타일))

y = (B + Y) / x

B / 2 = x + y - 2 = x + (B + Y) / x - 2

x<sup>2</sup> - (2 + B/2)x + (B + Y) = 0

ax<sup>2</sup> + bx + c = 0 에서 근의 공식은,

x = (-b +- root(b<sup>2</sup> - 4ac)) / 2a

a = 1
b = - (2 + B/2)
c = xy

위 변수들을 그대로 대입하고 코드로 적용하면 풀이가 가능합니다. 

## Solution.java
~~~java
class Solution {
    public int[] solution(int brown, int yellow) {
        int a = 1;
        int b = - (brown / 2 + 2);
        int c = brown + yellow;
        
        int x = (int)(-b + Math.sqrt(b * b - 4 * a * c)) / 2;
        int y = (brown + yellow) / x;
            
        int[] answer = {x, y};
        return answer;
    }
}
~~~
