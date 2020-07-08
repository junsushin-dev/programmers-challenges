# H-index

출처: https://programmers.co.kr/learn/courses/30/lessons/42747

## 지문

H-Index는 과학자의 생산성과 영향력을 나타내는 지표입니다. 어느 과학자의 H-Index를 나타내는 값인 h를 구하려고 합니다. 위키백과1에 따르면, H-Index는 다음과 같이 구합니다.

어떤 과학자가 발표한 논문 n편 중, h번 이상 인용된 논문이 h편 이상이고 나머지 논문이 h번 이하 인용되었다면 h의 최댓값이 이 과학자의 H-Index입니다.

어떤 과학자가 발표한 논문의 인용 횟수를 담은 배열 citations가 매개변수로 주어질 때, 이 과학자의 H-Index를 return 하도록 solution 함수를 작성해주세요.

제한사항
과학자가 발표한 논문의 수는 1편 이상 1,000편 이하입니다.
논문별 인용 횟수는 0회 이상 10,000회 이하입니다.

입출력 예

citations	| return
----------|-------
[3, 0, 6, 1, 5]	| 3

입출력 예 설명

이 과학자가 발표한 논문의 수는 5편이고, 그중 3편의 논문은 3회 이상 인용되었습니다. 그리고 나머지 2편의 논문은 3회 이하 인용되었기 때문에 이 과학자의 H-Index는 3입니다.

## 해설

주어진 자료를 정렬하고, 정렬된 자료에서 H-index의 최대값을 구하는 방법을 떠올릴 수 있다면 간단한 문제입니다.

논문 별 인용횟수를 그렇다면 어떤 식으로 정렬해야 할까요? 그 방법은 어떻게 H-Index를 구해나갈 것이냐에 따라 달라집니다. 제가 생각한 H-Index를 구해가는 방법은 다음과 같습니다.

<H-Index 구하기>

1. 인용횟수가 높은 순서부터
2. 현재까지 처리한 논문의 갯수와
3. 현재 처리하는 논문의 인용횟수 중
4. 낮은 값을 현재의 H-Index로 도출
5. 기존의 H-Index와 비교하여 값이 높을경우 업데이트

예시)

인용횟수: [6, 5, 3, 1, 0]

index | 현재까지 논문 수 | 현재 논문 인용 수 | H-Index | 최대 H-Index
------|-----------------|-----------------|---------|------
0 | 1 | 6 | 1 | 1
1 | 2 | 5 | 2 | 2
2 | 3 | 3 | 3 | 3
3 | 4 | 1 | 1 | 3
4 | 5 | 0 | 0 | 3

위와 같이 인용 수 내림차순으로 논문들을 보면서 각 단계별로 H-Index를 구하고, 최대 H-Index를 도출할 수 있습니다.

정렬의 경우 java의 내장된 sort 기능을 사용할 수 있습니다. java에 내장된 정렬은 오름차순 정렬이 기본이기 때문에, 내림차순 정렬을 위해서는 내림차순임을 알려주는 Comparator를 이용해야 합니다. 혹은 오름차순 정렬 이후, 배열의 맨 뒤부터 보는 방식으로 내림차순으로 인용 수를 검토할 수 있습니다.

~~~
import java.util.Arrays;
import java.util.Collections;

Arrays.sort(citations, Collections.reverseOrder());
~~~

아래 문제풀이에서는 연습 목적으로 java의 sort 기능 대신 간단한 내림차순 merge sort를 직접 구현하여 풀이하였으니 참고하시기 바랍니다.  

## Solution.java
~~~
class Solution {
    public int solution(int[] citations) {
        Sorter mySorter = new Sorter();
        mySorter.mergeSort(citations, 0, citations.length-1);

        int answer = 0;

        for(int i = 0; i < citations.length; i++) {
            int h = Math.min(i+1, citations[i]);
            answer = Math.max(answer, h);
        }

        return answer;
    }

    class Sorter {
        private int[] data;

        // sort in descending order
        public void mergeSort(int[] arr, int from, int to) {

            if(from == to) {
                return;
            }
            else if(to - from == 1) {
                if(arr[from] < arr[to]) {
                    int temp = arr[from];
                    arr[from] = arr[to];
                    arr[to] = temp;
                    return;
                }
                else {
                    return;
                }
            }
            else {
                int mid = (from + to) / 2;
                mergeSort(arr, from, mid);
                mergeSort(arr, mid+1, to);
                int fstIndex = from;
                int sndIndex = mid+1;
                int[] tempArr = new int[to-from+1];
                for(int i = 0; i < tempArr.length; i++) {
                    if(fstIndex > mid) {
                        tempArr[i] = arr[sndIndex];
                        sndIndex += 1;
                    }
                    else if(sndIndex > to) {
                        tempArr[i] = arr[fstIndex];
                        fstIndex += 1;
                    }
                    else if(arr[fstIndex] >= arr[sndIndex]) {
                        tempArr[i] = arr[fstIndex];
                        fstIndex += 1;
                    }
                    else {
                        tempArr[i] = arr[sndIndex];
                        sndIndex += 1;
                    }
                }
                for(int i = 0; i < tempArr.length; i++) {
                    arr[from + i] = tempArr[i];
                }
            }
        }
    }

}
~~~
