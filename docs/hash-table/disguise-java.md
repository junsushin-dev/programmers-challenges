# 위장

출처: https://programmers.co.kr/learn/challenges

## 지문

스파이들은 매일 다른 옷을 조합하여 입어 자신을 위장합니다.

예를 들어 스파이가 가진 옷이 아래와 같고 오늘 스파이가 동그란 안경, 긴 코트, 파란색 티셔츠를 입었다면 다음날은 청바지를 추가로 입거나 동그란 안경 대신 검정 선글라스를 착용하거나 해야 합니다.

종류	이름
얼굴	동그란 안경, 검정 선글라스
상의	파란색 티셔츠
하의	청바지
겉옷	긴 코트
스파이가 가진 의상들이 담긴 2차원 배열 clothes가 주어질 때 서로 다른 옷의 조합의 수를 return 하도록 solution 함수를 작성해주세요.

제한사항
clothes의 각 행은 [의상의 이름, 의상의 종류]로 이루어져 있습니다.
스파이가 가진 의상의 수는 1개 이상 30개 이하입니다.
같은 이름을 가진 의상은 존재하지 않습니다.
clothes의 모든 원소는 문자열로 이루어져 있습니다.
모든 문자열의 길이는 1 이상 20 이하인 자연수이고 알파벳 소문자 또는 _ 로만 이루어져 있습니다.
스파이는 하루에 최소 한 개의 의상은 입습니다.

입출력 예
clothes	| return
--------|-------
[[yellow_hat, headgear], [blue_sunglasses, eyewear], [green_turban, headgear]]	| 5
[[crow_mask, face], [blue_sunglasses, face], [smoky_makeup, face]]	| 3

입출력 예 설명

예제 1

headgear에 해당하는 의상이 yellow_hat, green_turban이고 eyewear에 해당하는 의상이 blue_sunglasses이므로 아래와 같이 5개의 조합이 가능합니다.

1. yellow_hat
2. blue_sunglasses
3. green_turban
4. yellow_hat + blue_sunglasses
5. green_turban + blue_sunglasses

예제 2

face에 해당하는 의상이 crow_mask, blue_sunglasses, smoky_makeup이므로 아래와 같이 3개의 조합이 가능합니다.

1. crow_mask
2. blue_sunglasses
3. smoky_makeup

## 해설

주어진 의상으로 조합할 수 있는 경우의 수를 구하는 문제입니다. 의상 종류별로 주어진 의상 중 하나를 입거나 / 아예 해당 종류의 의상을 입지 않을 수 있기 때문에, 각 의상 종류별로 (의상의 수 + 1)의 선택지를 가집니다. 예전에 네이버 게임 시절 유행하던 옷 입히기 게임을 생각하시면 편할 것 같습니다.

예시)

셔츠 - 파란셔츠, 빨간셔츠 (2가지)
바지 - 노란바지, 초록바지, 보라바지 (3가지)

경우의 수: (2+1) X (3+1) = 12가지

위와 같이 의상을 조합할 경우, 모든 의상 종류에 대해서 입지 않는다는 선택을 하게 된다면 적어도 하나의 의상을 사용해야 하는 문제의 조건에 위배됩니다. 따라서 위와 같이 계산한 경우의 수에서 1을 빼서 최종 답을 구해야 합니다.  

문제를 푸는 방법을 알고 난 이후에는, 각 종류별로 의상의 갯수를 세는 방법만 적절히 구현하면 쉽게 문제를 풀 수 있습니다. 아래의 풀이에서는 HashMap으로 각 의상의 종류를 Key로, Value를 의상의 갯수로 설정하여 주어진 의상에 대해서 업데이트를 수행하였습니다.

## 팁

Java의 HashMap을 이용할 때, 처음 주어지는 Key에 대한 값이 존재하는지 체크하기 위해서 if 문을 많이 사용하게 되는 경우가 있습니다. HashMap의 merge 함수를 이용하면 if 문의 사용을 줄이고 한줄로 key가 존재하지않는/존재하는 경우를 모두 처리할 수 있습니다.

merge(K key, V value, BiFunction<? super V,? super V,? extends V> remappingFunction)

<Parameters>
1. 키값
2. 키값이 존재하지 않을 경우 넣을 value 값
3. 키값이 존재할 때 기존의 value값과 새로 입력한 value값을 처리하여 새로운 value를 만드는 함수

아래 풀이에서는 기존에 키값이 없을 경우 1개의 의상이 존재하게 되므로 1을 넣고, 기존에 키값이 있을 경우 기존의 value값에 1을 더하는 lambda 함수를 정의하여 넣었습니다.

~~~
numClothes.merge(clothes[i][1], 1, (Integer v1, Integer v2) -> v1 + 1);
~~~

## Solution.java
~~~
import java.util.HashMap;

class Solution {
    public int solution(String[][] clothes) {
        HashMap<String, Integer> numClothes = new HashMap<>();
        for(int i = 0; i < clothes.length; i++) {
            numClothes.merge(clothes[i][1], 1, (Integer v1, Integer v2) -> v1 + 1);
        }

        int answer = 1;
        for(Integer value: numClothes.values()) {
            answer *= (value + 1);
        }

        return answer-1;
    }
}
~~~
