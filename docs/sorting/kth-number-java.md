# K번째수

출처: https://programmers.co.kr/learn/courses/30/lessons/42748

## 지문

배열 array의 i번째 숫자부터 j번째 숫자까지 자르고 정렬했을 때, k번째에 있는 수를 구하려 합니다.

예를 들어 array가 [1, 5, 2, 6, 3, 7, 4], i = 2, j = 5, k = 3이라면

1. array의 2번째부터 5번째까지 자르면 [5, 2, 6, 3]입니다.
2. 1에서 나온 배열을 정렬하면 [2, 3, 5, 6]입니다.
3. 2에서 나온 배열의 3번째 숫자는 5입니다.

배열 array, [i, j, k]를 원소로 가진 2차원 배열 commands가 매개변수로 주어질 때, commands의 모든 원소에 대해 앞서 설명한 연산을 적용했을 때 나온 결과를 배열에 담아 return 하도록 solution 함수를 작성해주세요.

제한사항
- array의 길이는 1 이상 100 이하입니다.
- array의 각 원소는 1 이상 100 이하입니다.
- commands의 길이는 1 이상 50 이하입니다.
- commands의 각 원소는 길이가 3입니다.

입출력 예
array	| commands	| return
--|--|--
[1, 5, 2, 6, 3, 7, 4]	| [[2, 5, 3], [4, 4, 1], [1, 7, 3]]	| [5, 6, 3]

입출력 예 설명

[1, 5, 2, 6, 3, 7, 4]를 2번째부터 5번째까지 자른 후 정렬합니다. [2, 3, 5, 6]의 세 번째 숫자는 5입니다.

[1, 5, 2, 6, 3, 7, 4]를 4번째부터 4번째까지 자른 후 정렬합니다. [6]의 첫 번째 숫자는 6입니다.

[1, 5, 2, 6, 3, 7, 4]를 1번째부터 7번째까지 자릅니다. [1, 2, 3, 4, 5, 6, 7]의 세 번째 숫자는 3입니다.

## 해설

문제에서 요구하는 배열 slicing과 배열의 정렬을 그대로 구현하면 되는 문제입니다. 자바의 경우 내장된 Arrays.copyOfRange로 주어진 구간을 자른 새로운 배열을 만들 수도 있지만, 아래 문제풀이에서는 별도로 직접 배열을 자르는 함수를 정의하여 사용하였습니다.

~~~
public int[] slice(int[] array, int from, int to) {
    int length = to - from + 1;
    int[] ret = new int[length];
    for(int i = 0; i < length; i++) {
        ret[i] = array[i+from-1];
    }
    return ret;
}
~~~

원하는 구간의 배열을 얻은 이후에는 간단하게 Arrays.sort 함수를 이용하여 배열을 정렬해주고, 그 다음 주어진 index에 해당하는 원소를 반환하면 됩니다.

~~~
for(int i = 0; i < commands.length; i++) {
    int[] sliced = slice(array, commands[i][0], commands[i][1]);
    Arrays.sort(sliced);
    answer[i] = sliced[commands[i][2]-1];
}

return answer;
~~~

전반적으로 정렬 카테고리의 문제 중에 가장 쉬운 난이도인 문제이기 때문에, 문제에서 요구하는 사항을 그대로 구현하기만 하면 시간, 공간복잡도에 대해서는 고민하지 않아도 문제를 해결할 수 있습니다.

## Solution.java
~~~
import java.util.*;

class Solution {
    public int[] solution(int[] array, int[][] commands) {
        int[] answer = new int[commands.length];
        for(int i = 0; i < commands.length; i++) {
            int[] sliced = slice(array, commands[i][0], commands[i][1]);
            Arrays.sort(sliced);
            answer[i] = sliced[commands[i][2]-1];
        }

        return answer;
    }

    public int[] slice(int[] array, int from, int to) {
        int length = to - from + 1;
        int[] ret = new int[length];
        for(int i = 0; i < length; i++) {
            ret[i] = array[i+from-1];
        }
        return ret;
    }
}
~~~
