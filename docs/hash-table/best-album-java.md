# 베스트앨범

출처: https://programmers.co.kr/learn/courses/30/lessons/42579

## 지문

스트리밍 사이트에서 장르 별로 가장 많이 재생된 노래를 두 개씩 모아 베스트 앨범을 출시하려 합니다. 노래는 고유 번호로 구분하며, 노래를 수록하는 기준은 다음과 같습니다.

속한 노래가 많이 재생된 장르를 먼저 수록합니다.
장르 내에서 많이 재생된 노래를 먼저 수록합니다.
장르 내에서 재생 횟수가 같은 노래 중에서는 고유 번호가 낮은 노래를 먼저 수록합니다.
노래의 장르를 나타내는 문자열 배열 genres와 노래별 재생 횟수를 나타내는 정수 배열 plays가 주어질 때, 베스트 앨범에 들어갈 노래의 고유 번호를 순서대로 return 하도록 solution 함수를 완성하세요.

제한사항
- genres[i]는 고유번호가 i인 노래의 장르입니다.
- plays[i]는 고유번호가 i인 노래가 재생된 횟수입니다.
- genres와 plays의 길이는 같으며, 이는 1 이상 10,000 이하입니다.
- 장르 종류는 100개 미만입니다.
- 장르에 속한 곡이 하나라면, 하나의 곡만 선택합니다.
- 모든 장르는 재생된 횟수가 다릅니다.

입출력 예
genres	| plays	| return
[classic, pop, classic, classic, pop]	| [500, 600, 150, 800, 2500]	| [4, 1, 3, 0]

입출력 예 설명

classic 장르는 1,450회 재생되었으며, classic 노래는 다음과 같습니다.

- 고유 번호 3: 800회 재생
- 고유 번호 0: 500회 재생
- 고유 번호 2: 150회 재생

pop 장르는 3,100회 재생되었으며, pop 노래는 다음과 같습니다.

- 고유 번호 4: 2,500회 재생
- 고유 번호 1: 600회 재생

따라서 pop 장르의 [4, 1]번 노래를 먼저, classic 장르의 [3, 0]번 노래를 그다음에 수록합니다.

## 해설

해시맵을 이용해서 노래들을 장르로 분류하고, 규칙에 따라서 순서를 재구성하는 문제입니다. 실제 음악 어플리케이션 백엔드에서는 이것보다 훨씬 복잡한 로직이 돌아가고 있겠죠?

문제에 따르면 우선 개별 노래가 장르에 속해야 하고,

- 장르는 장르에 속한 노래의 재생횟수의 총합에 따라 내림차순 정렬
- 노래는 장르 안에서 재생횟수에 따라 내림차순 정렬

이 되어야 하는 구조입니다.

아래 문제풀이에서는 우선 이 분류를 해결하기 위해서 Song과 Genre라는 클래스를 정의하였습니다.

~~~java
class Genre implements Comparable<Genre>{
    String name;
    List<Song> songs;
    int plays;

    public Genre(String n) {
        this.name = n;
        this.songs = new ArrayList<>();
    }

    public void add(Song s) {
        songs.add(s);
        plays += s.plays;
    }

    public void sortSongs() {
        Collections.sort(songs, Collections.reverseOrder());
    }

    public int compareTo(Genre o) {
        int s1 = this.plays;
        int s2 = o.plays;
        return Integer.compare(s1, s2);
    }    
}
~~~
장르는 장르에 속한 노래들의 리스트를 가지고 있으며, 재생횟수의 총합을 parameter로 가지고 있습니다.

~~~java
class Song implements Comparable<Song>{
    int num;
    int plays;

    public Song(int n, int p) {
        this.num = n;
        this.plays = p;
    }

    public int compareTo(Song o) {
        return Integer.compare(this.plays, o.plays);
    }
}
~~~
노래는 스스로의 고유번호와, 재생횟수를 가지고 있습니다.

위 클래스 두개 모두 Comparable 인터페이스를 이용하여, 재생횟수(plays)에 따라 정렬이 가능하도록 만들었습니다.

위와 같이 클래스를 정의한 이후에는, 주어진 input을 처리하여 genres에 들어있는 장르명 별로 노래를 분류하기 위해서 HashMap을 이용하였습니다.

HashMap<String, Genre> : 장르의 이름 String을 Genre 클래스로 맵핑
~~~java
HashMap<String, Genre> genreMap = new HashMap<>();

for(String g: genres) {
    genreMap.putIfAbsent(g, new Genre(g));
}

for(int i = 0; i < genres.length; i++) {
    Song nSong = new Song(i, plays[i]);
    genreMap.get(genres[i]).add(nSong);
}
~~~

이후에는 쉬운 정렬을 위해서 Genre 객체들을 별도의 리스트에 넣어줍니다.

~~~java
List<Genre> genreList = new ArrayList<>();
for(Genre g: genreMap.values()) {
    g.sortSongs();
    genreList.add(g);
}

Collections.sort(genreList, Collections.reverseOrder());
~~~

마지막으로는 문제의 규칙에 따라서 우선순위가 높은 장르별로 2개(장르 안 노래가 하나일 경우 1개)씩 노래의 번호를 기록해줍니다.

~~~java
List<Integer> ansList = new ArrayList<>();

for(Genre g: genreList) {
    if(g.songs.size() <= 1) {
        ansList.add(g.songs.get(0).num);
    }
    else {
        ansList.add(g.songs.get(0).num);
        ansList.add(g.songs.get(1).num);
    }
}
~~~

위 리스트를 문제에서 요구하는 int[] 배열로 변환하면 문제를 해결할 수 있습니다.

## 팁

Collection 인터페이스를 구현한 클래스의 경우에는 내림차순 정렬을 다음과 같이 이용할 수 있습니다.

~~~java
Collections.sort(정렬대상, Collections.reverseOrder())
~~~

Java의 기본적인 정렬 규칙은 오름차순이기 때문에, 역방향 정렬의 경우 내림차순 정렬이 됩니다.

## Solution.java
~~~java
import java.util.*;

class Solution {
    public int[] solution(String[] genres, int[] plays) {
        HashMap<String, Genre> genreMap = new HashMap<>();

        for(String g: genres) {
            genreMap.putIfAbsent(g, new Genre(g));
        }

        for(int i = 0; i < genres.length; i++) {
            Song nSong = new Song(i, plays[i]);
            genreMap.get(genres[i]).add(nSong);
        }

        List<Genre> genreList = new ArrayList<>();
        for(Genre g: genreMap.values()) {
            g.sortSongs();
            genreList.add(g);
        }

        Collections.sort(genreList, Collections.reverseOrder());

        List<Integer> ansList = new ArrayList<>();

        for(Genre g: genreList) {
            if(g.songs.size() <= 1) {
                ansList.add(g.songs.get(0).num);
            }
            else {
                ansList.add(g.songs.get(0).num);
                ansList.add(g.songs.get(1).num);
            }
        }

        int[] answer = new int[ansList.size()];
        for(int i = 0; i < answer.length; i++) {
            answer[i] = ansList.get(i);
        }
        return answer;
    }

    class Genre implements Comparable<Genre>{
        String name;
        List<Song> songs;
        int plays;

        public Genre(String n) {
            this.name = n;
            this.songs = new ArrayList<>();
        }

        public void add(Song s) {
            songs.add(s);
            plays += s.plays;
        }

        public void sortSongs() {
            Collections.sort(songs, Collections.reverseOrder());
        }

        public int compareTo(Genre o) {
            int s1 = this.plays;
            int s2 = o.plays;
            return Integer.compare(s1, s2);
        }

    }

    class Song implements Comparable<Song>{
        int num;
        int plays;

        public Song(int n, int p) {
            this.num = n;
            this.plays = p;
        }

        public int compareTo(Song o) {
            return Integer.compare(this.plays, o.plays);
        }
    }
}
~~~
