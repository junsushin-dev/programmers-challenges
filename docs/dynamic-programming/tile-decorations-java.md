# 타일 장식물

출처: https://programmers.co.kr/learn/courses/30/lessons/43104

## 지문

대구 달성공원에 놀러 온 지수는 최근에 새로 만든 타일 장식물을 보게 되었다. 타일 장식물은 정사각형 타일을 붙여 만든 형태였는데, 한 변이 1인 정사각형 타일부터 시작하여 마치 앵무조개의 나선 모양처럼 점점 큰 타일을 붙인 형태였다. 타일 장식물의 일부를 그리면 다음과 같다.

![타일사진](https://grepp-programmers.s3.amazonaws.com/files/production/3e31bedd54/fcc48066-e72f-45c8-af03-e4360b58b589.png)

그림에서 타일에 적힌 수는 각 타일의 한 변의 길이를 나타낸다. 타일 장식물을 구성하는 정사각형 타일 한 변의 길이를 안쪽 타일부터 시작하여 차례로 적으면 다음과 같다.
[1, 1, 2, 3, 5, 8, .]
지수는 문득 이러한 타일들로 구성되는 큰 직사각형의 둘레가 궁금해졌다. 예를 들어, 처음 다섯 개의 타일이 구성하는 직사각형(위에서 빨간색으로 표시한 직사각형)의 둘레는 26이다.

타일의 개수 N이 주어질 때, N개의 타일로 구성된 직사각형의 둘레를 return 하도록 solution 함수를 작성하시오.

제한 사항
N은 1 이상 80 이하인 자연수이다.

입출력 예
N|return
-|-
5|26
6|42

## 해설

Dynamic Programming 의 단골 소재인 피보나치 수열을 이용하는 문제입니다.

위 예시 그림을 볼 경우, 다음 타일을 추가할 경우 항상 전 타일과 전전타일의 한 변의 길이를 합한 타일을 추가하게 됨을 알 수 있습니다. 예리한 분들이라면 예시의 [1,1,2,3,5,8,...] 을 보고도 피보나치 수열을 떠올렸을 것입니다.

피보나치 수열의 계산은 정직한 재귀를 통해서 f(n) = f(n-1) + f(n-2)의 형식으로 계산을 수행할 경우 약 2<sup>n-1</sup>번의 계산을 거쳐서 구해낼 수 있습니다. 이 경우 시간복잡도는 O(2^n)이 됩니다.

하지만 Dynamic Programming을 이용해서 한번 계산한 결과를 저장하여 호출할 경우, 계산 횟수를 n번에 근사하도록 줄일 수 있습니다.

예시)

- 일반 재귀: 3번째 피보나치 수를 독립적으로 2번 계산함
  - f(4) = f(3) + f(2)
  - f(5) = f(4) + f(3)

- DP: 두번째로 f(3)이 필요해지는 경우 기존에 계산하여 저장한 값을 호출함 결과적으로 각 f(n)을 한번씩만 구하게 되며, 이에 따라서 계산횟수가 n에 근사함.
  - f(4) = f(3) + f(2)
  - f(5) = f(4) + f(3)(전단계에서 구한 f(3) 사용)

위와 같이 Dynamic Programming을 구현하기 위해서, 배열 또는 리스트를 선언하여 N번째 피보나치 수열에 해당하는 수를 계속적으로 저장하면서 나가면 O(n) 의 시간복잡도로 문제를 해결할 수 있습니다.

## Solution.java
~~~java
import java.util.ArrayList;

class Solution {
    public ArrayList<Long> fibos = new ArrayList<>();

    public long solution(int N) {
        fibos.add(1L);
        fibos.add(1L);
        long answer = fibo(N+2) * 2;
        return answer;
    }

    public long fibo(int N) {
        if(fibos.size() >= N) {
            return fibos.get(N-1);
        }
        else {
            fibos.add(fibo(N-2) + fibo(N-1));
            return fibos.get(N-1);
        }
    }
}
~~~
