# 이중우선순위큐

출처: https://programmers.co.kr/learn/courses/30/lessons/42628

## 지문

이중 우선순위 큐는 다음 연산을 할 수 있는 자료구조를 말합니다.

명령어 |	수신 탑(높이)
-- | --
I 숫자 |	큐에 주어진 숫자를 삽입합니다.
D 1 |	큐에서 최댓값을 삭제합니다.
D -1 |	큐에서 최솟값을 삭제합니다.

이중 우선순위 큐가 할 연산 operations가 매개변수로 주어질 때, 모든 연산을 처리한 후 큐가 비어있으면 [0,0] 비어있지 않으면 [최댓값, 최솟값]을 return 하도록 solution 함수를 구현해주세요.

제한사항
- operations는 길이가 1 이상 1,000,000 이하인 문자열 배열입니다.
- operations의 원소는 큐가 수행할 연산을 나타냅니다.
  - 원소는 “명령어 데이터” 형식으로 주어집니다.
  - 최댓값/최솟값을 삭제하는 연산에서 최댓값/최솟값이 둘 이상인 경우, 하나만 삭제합니다.
- 빈 큐에 데이터를 삭제하라는 연산이 주어질 경우, 해당 연산은 무시합니다.

입출력 예
operations | return
-- | --
["I 16","D 1"] |	[0,0]
["I 7","I 5","I -5","D -1"] |	[7,5]

입출력 예 설명

16을 삽입 후 최댓값을 삭제합니다. 비어있으므로 [0,0]을 반환합니다.

7,5,-5를 삽입 후 최솟값을 삭제합니다. 최대값 7, 최소값 5를 반환합니다.

## 해설

최소 또는 최대값을 효율적으로 반환하는 일반적인 Heap 자료구조와 다르게 최소, 최대값 모두를 효율적으로 반환할 수 있는 이중우선순위큐(double-ended-priority-queue)를 구현하는 문제입니다. 

이중우선순위큐를 만들기 위해서는 다양한 구현방식을 택할 수 있습니다. (dual heap, interval heap, min-max heap...)

참고자료: https://en.wikipedia.org/wiki/Double-ended_priority_queue

여기서는 가장 간단한 dual-heap 구조를 통해서 구현을 해보도록 하겠습니다. dual-heap 구조는 min heap과 max heap을 병렬적으로 사용합니다. 
- 원소가 삽입이 될 경우 
  - min heap과 max heap 양쪽에 원소를 새로 넣어줍니다. 
  - heap의 삽입연산을 두번 하게 되지만, 시간복잡도는 O(log n)으로 유지됩니다. 
- 원소를 삭제할 경우
  - 최소값: min heap에서 head를 삭제한 이후, 해당 원소를 max heap에서 검색해서 삭제합니다. 
  - 최대값: max heap에서 head를 삭제한 이후, 해당 원소를 min heap에서 검색해서 삭제합니다. 
  - 두 연산 모두 시간복잡도는 O(log n) 입니다.  
- 원소를 가져올 경우
  - 최소값: min heap의 head를 참조하여 O(1) 시간복잡도로 조회가 가능합니다. 
  - 최대값: max heap이 head를 참조하여 O(1) 시간복잡도로 조회가 가능합니다. 

아래 풀이에서는 Java.util에 내장된 PriorityQueue를 이용하여 MinMaxHeap 클래스를 새로 정의하고, 상단 Wikipedia 페이지에 정의된 이중우선순위큐의 함수들을 구현하였습니다. 입력값을 간단히 파싱해준 후, 구현한 클래스를 통해서 문제를 해결할 수 있습니다.  

## Solution.java
~~~java
import java.util.PriorityQueue;
import java.util.Comparator;

class Solution {
    public int[] solution(String[] operations) {
        
        MinMaxHeap depq = new MinMaxHeap();
        
        for(String operation: operations) {
            char op = operation.charAt(0);
            int param = Integer.parseInt(operation.substring(2));
            if(op == 'I') depq.put(param);
            if(op == 'D') {
                if(param == 1) depq.removeMax();
                if(param == -1) depq.removeMin();
            }
        }
                   
        if(depq.isEmpty()) {
            int[] answer = { 0, 0 };
            return answer;
        }
        else {
            int[] answer = { depq.getMax(), depq.getMin() };
            return answer;
        }
    }
}

class MinMaxHeap {
    private PriorityQueue<Integer> minHeap;
    private PriorityQueue<Integer> maxHeap;
    public MinMaxHeap() {
        this.minHeap = new PriorityQueue<>();
        this.maxHeap = new PriorityQueue<>(new maxComparator());
    }
    
    public boolean isEmpty() {
        return this.minHeap.isEmpty() && this.maxHeap.isEmpty();
    }
    
    public int size() {
        return this.minHeap.size();
    }
    
    public int getMin() {
        return this.minHeap.peek();
    }
    
    public int getMax() {
        return this.maxHeap.peek();
    }
    
    public void put(int i) {
        this.minHeap.add(i);
        this.maxHeap.add(i);
    }
    
    public void removeMin() {
        if(this.isEmpty()) return;
        int minVal = this.minHeap.poll();
        this.maxHeap.remove(minVal);
    }
    
    public void removeMax() {
        if(this.isEmpty()) return;
        int maxVal = this.maxHeap.poll();
        this.minHeap.remove(maxVal);
    }
}

class maxComparator implements Comparator<Integer> {
    @Override
    public int compare(Integer o1, Integer o2) {
        return - o1.compareTo(o2);
    }
}
~~~