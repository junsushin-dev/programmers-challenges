# 디스크 컨트롤러

출처: https://programmers.co.kr/learn/courses/30/lessons/42627

## 지문

하드디스크는 한 번에 하나의 작업만 수행할 수 있습니다. 디스크 컨트롤러를 구현하는 방법은 여러 가지가 있습니다. 가장 일반적인 방법은 요청이 들어온 순서대로 처리하는 것입니다.

예를들어

- 0ms 시점에 3ms가 소요되는 A작업 요청
- 1ms 시점에 9ms가 소요되는 B작업 요청
- 2ms 시점에 6ms가 소요되는 C작업 요청
와 같은 요청이 들어왔습니다. 이를 그림으로 표현하면 아래와 같습니다.

![timeline1](https://grepp-programmers.s3.amazonaws.com/files/production/b68eb5cec6/38dc6a53-2d21-4c72-90ac-f059729c51d5.png)

한 번에 하나의 요청만을 수행할 수 있기 때문에 각각의 작업을 요청받은 순서대로 처리하면 다음과 같이 처리 됩니다.

![timeline2](https://grepp-programmers.s3.amazonaws.com/files/production/5e677b4646/90b91fde-cac4-42c1-98b8-8f8431c52dcf.png)

- A: 3ms 시점에 작업 완료 (요청에서 종료까지 : 3ms)
- B: 1ms부터 대기하다가, 3ms 시점에 작업을 시작해서 12ms 시점에 작업 완료(요청에서 종료까지 : 11ms)
- C: 2ms부터 대기하다가, 12ms 시점에 작업을 시작해서 18ms 시점에 작업 완료(요청에서 종료까지 : 16ms)
이 때 각 작업의 요청부터 종료까지 걸린 시간의 평균은 10ms(= (3 + 11 + 16) / 3)가 됩니다.

하지만 A → C → B 순서대로 처리하면

![timeline3](https://grepp-programmers.s3.amazonaws.com/files/production/9eb7c5a6f1/a6cff04d-86bb-4b5b-98bf-6359158940ac.png)

- A: 3ms 시점에 작업 완료(요청에서 종료까지 : 3ms)
- C: 2ms부터 대기하다가, 3ms 시점에 작업을 시작해서 9ms 시점에 작업 완료(요청에서 종료까지 : 7ms)
- B: 1ms부터 대기하다가, 9ms 시점에 작업을 시작해서 18ms 시점에 작업 완료(요청에서 종료까지 : 17ms)
이렇게 A → C → B의 순서로 처리하면 각 작업의 요청부터 종료까지 걸린 시간의 평균은 9ms(= (3 + 7 + 17) / 3)가 됩니다.

각 작업에 대해 [작업이 요청되는 시점, 작업의 소요시간]을 담은 2차원 배열 jobs가 매개변수로 주어질 때, 작업의 요청부터 종료까지 걸린 시간의 평균을 가장 줄이는 방법으로 처리하면 평균이 얼마가 되는지 return 하도록 solution 함수를 작성해주세요. (단, 소수점 이하의 수는 버립니다)

제한 사항
jobs의 길이는 1 이상 500 이하입니다.
jobs의 각 행은 하나의 작업에 대한 [작업이 요청되는 시점, 작업의 소요시간] 입니다.
각 작업에 대해 작업이 요청되는 시간은 0 이상 1,000 이하입니다.
각 작업에 대해 작업의 소요시간은 1 이상 1,000 이하입니다.
하드디스크가 작업을 수행하고 있지 않을 때에는 먼저 요청이 들어온 작업부터 처리합니다.

입출력 예
jobs | return
-----|-------
[[0, 3], [1, 9], [2, 6]] | 9

입출력 예 설명

문제에 주어진 예와 같습니다.

0ms 시점에 3ms 걸리는 작업 요청이 들어옵니다.
1ms 시점에 9ms 걸리는 작업 요청이 들어옵니다.
2ms 시점에 6ms 걸리는 작업 요청이 들어옵니다.

## 해설

하드디스크로 문제 상 나와있지만, 실제로는 CPU Scheduling을 시뮬레이션 하는 것과 유사한 문제입니다. (완료시간 - 요청시간)의 평균이 최소화되도록 작업의 처리순서를 구성하고, 결과인 시간을 잘 측정하는 것이 관건입니다.

(완료시간 - 요청시간)을 Turnaround Time(T-Time)이라고 부르도록 하겠습니다.

Average T-Time, 또는 Total T-Time(총합이 크면 당연히 평균이 크겠죠?)을 최소화하기 위해서는 어떤 식으로 Job들을 처리해주어야 할까요? 문제를 잘 보신다면, 어차피 Job별로 처리시간은 확정되어 있기 때문에 T-Time을 줄이기 위해서는 처리를 기다리는 대기시간을 줄여주는 것이 중요하다는 것을 알 수 있습니다.

T-Time = 처리시간 + 대기시간

한번에 하나의 Job 만을 처리하기 때문에, 여러 Job이 기다리고 있다면 필연적으로 고르지 않은 Job들은 자신에게 차례가 오기를 기다려야 합니다. 이때, 기다리고 있는 Job들의 개수가 적을수록 전체적인 대기시간이 줄어들겠죠. 기다리는 Job의 개수를 최소화하기 위해서는, 빨리 내보낼 수 있는 Job부터 내보내야 합니다.

예시)

Jobs:
  - A: 요청시간 0ms 처리시간 3ms
  - B: 요청시간 0ms 처리시간 6ms

A -> B 순으로 처리해줄 경우
  - A: T-Time 3ms + 0ms
  - B: T-Time 6ms + 3ms(A가 완료되길 기다리는 시간)
  - 평균 T-Time: (3 + 9) / 2 = 6

B -> A 순으로 처리해줄 경우
  - A: T-Time 3ms + 6ms(B가 완료되길 기다리는 시간)
  - B: T-Time 6ms + 0ms
  - 평균 T-Time: (9 + 6) / 2 = 7.5

위 예시와 같이 현재 대기중인 작업 중 처리시간이 짧은 Job부터 처리해주는 것이 문제 해결의 핵심입니다. 이를 구현하기 위해서는 처리시간에 따라 오름차순으로 Job을 정렬해주어야 합니다. Heap 자료구조는 Java에서 PriorityQueue 클래스로 구현이 되어있고, 이를 이용하면 Job들을 처리시간에 따라 정리해줄 수 있습니다.

아래 문제풀이 중 waitQueue로 명명된 PriorityQueue가 위의 기능을 하도록 만들어졌습니다. Job이라는 객체는 새로 선언한 클래스이기 때문에, Job의 처리시간을 비교할 수 있는 Comparator를 선언하여 PriorityQueue가 Job을 정렬할 수 있는 기준을 부여해주었습니다.

하지만, 위 PriorityQueue만으로는 문제가 완벽하게 풀리지 않습니다. 문제에서 주어진 예시에는 Job들이 요청시간 순서대로 순차적으로 주어져 있지만, 문제의 설명에는 요청시간 순서대로 Job을 Array에 담아서 준다는 명시적인 말은 없습니다. 따라서 Job이 실제로는 [[3, 6], [2, 1], [0, 4]] 와 같은 식으로 요청시간이 뒤죽박죽으로 오는 경우도 대비해 주어야 합니다.

이를 처리하기 위해서 아래 문제풀이에서는 주어진 모든 Job을 요청시간에 따라 정렬하도록 또 다른 PriorityQueue인 jobQueue를 선언하여 요청시간이 빠른 순서대로 Job을 받아서 처리할 수 있도록 해주었습니다.

## 팁

Comparator를 선언하여 이용할 경우, 아래와 같이 같은 클래스에 대해서도 정렬 순서를 다르게 정의할 수 있습니다.

하나의 정렬 방법만이 필요할 경우, Comparator 대신 클래스가 Comparable을 implement 하도록 해주면 별도의 Comparator 선언 없이도 각종 비교 기능을 사용할 수 있습니다.

## Solution.java
~~~java
import java.util.PriorityQueue;
import java.util.Comparator;
import java.util.ArrayList;

class Solution {
    public int solution(int[][] jobs) {
        ArrayList<Job> jobsList = new ArrayList<Job>();
        PriorityQueue<Job> jobQueue = new PriorityQueue<>(500, new reqTimeComparator());
        PriorityQueue<Job> waitQueue = new PriorityQueue<>(500, new jobTimeComparator());

        for(int i = 0; i < jobs.length; i++) {
            jobsList.add(new Job(jobs[i][0], jobs[i][1]));
        }

        for(Job j: jobsList) {
            jobQueue.add(j);
        }

        int time = 0;
        int jobTime = 0;
        Job currJob = null;

        while(!jobQueue.isEmpty() || !waitQueue.isEmpty() || currJob!=null) {
            // move jobs into priority queue
            while(!jobQueue.isEmpty() && jobQueue.peek().reqTime <= time) {
                waitQueue.add(jobQueue.poll());
            }
            // pop a new job from queue
            if(currJob == null && !waitQueue.isEmpty()) {
                currJob = waitQueue.poll();
            }

            // process new job, increment time
            time += 1;
            if(currJob != null) {
                if(currJob.process() == true) {
                    currJob.endTime = time;
                    currJob = null;
                }
            }
        }

        int answer = 0;

        for(Job j: jobsList) {
            answer += (j.endTime - j.reqTime);
        }

        return answer / jobs.length;
    }

    class Job {
        public Integer reqTime;
        public Integer jobTime;
        public Integer endTime;

        public Job(int r, int j) {
            this.reqTime = r;
            this.jobTime = j;
        }

        public boolean process() {
            this.jobTime -= 1;
            if(this.jobTime == 0) {
                return true;
            }
            else {
                return false;
            }
        }
    }

    class reqTimeComparator implements Comparator<Job> {
        @Override
        public int compare(Job o1, Job o2) {
            return o1.reqTime.compareTo(o2.reqTime);
        }
    }

    class jobTimeComparator implements Comparator<Job> {
        @Override
        public int compare(Job o1, Job o2) {
            return o1.jobTime.compareTo(o2.jobTime);
        }
    }
}
~~~
