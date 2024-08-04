---
title: "더 맵개(JS)"
date: "2024-07-27"
thumbnail: "/assets/img/thumbnail/1.jpeg"
---

# [더 맵게](https://school.programmers.co.kr/learn/courses/30/lessons/42626, "프로그래머스 더 맵게")

---

## 문제 설명

매운 것을 좋아하는 Leo는 모든 음식의 스코빌 지수를 K 이상으로 만들고 싶습니다. 모든 음식의 스코빌 지수를 K 이상으로 만들기 위해 Leo는 스코빌 지수가 가장 낮은 두 개의 음식을 아래와 같이 특별한 방법으로 섞어 새로운 음식을 만듭니다.

```
섞은 음식의 스코빌 지수 = 가장 맵지 않은 음식의 스코빌 지수 + (두 번째로 맵지 않은 음식의 스코빌 지수 \* 2)
```

Leo는 모든 음식의 스코빌 지수가 K 이상이 될 때까지 반복하여 섞습니다.
Leo가 가진 음식의 스코빌 지수를 담은 배열 scoville과 원하는 스코빌 지수 K가 주어질 때, 모든 음식의 스코빌 지수를 K 이상으로 만들기 위해 섞어야 하는 최소 횟수를 return 하도록 solution 함수를 작성해주세요.

### 제한 사항

scoville의 길이는 2 이상 1,000,000 이하입니다.
K는 0 이상 1,000,000,000 이하입니다.
scoville의 원소는 각각 0 이상 1,000,000 이하입니다.
모든 음식의 스코빌 지수를 K 이상으로 만들 수 없는 경우에는 -1을 return 합니다.

### 입출력 예

| scoville             | K   | return |
| -------------------- | --- | ------ |
| [1, 2, 3, 9, 10, 12] | 7   | 2      |

### 입출력 예 설명

스코빌 지수가 1인 음식과 2인 음식을 섞으면 음식의 스코빌 지수가 아래와 같이 됩니다.
새로운 음식의 스코빌 지수 = 1 + (2 \* 2) = 5
가진 음식의 스코빌 지수 = [5, 3, 9, 10, 12]

스코빌 지수가 3인 음식과 5인 음식을 섞으면 음식의 스코빌 지수가 아래와 같이 됩니다.
새로운 음식의 스코빌 지수 = 3 + (5 \* 2) = 13
가진 음식의 스코빌 지수 = [13, 9, 10, 12]

모든 음식의 스코빌 지수가 7 이상이 되었고 이때 섞은 횟수는 2회입니다.

## 문제풀이

---

먼저 시간복잡도를 크게 생각하지 않고, 문제를 해결하는 것에 큰 의미를 두며 문제 풀이를 시작했습니다.
<code>scoville.sort((a, b) => b - a)</code> 를 통해 가장 매운 음식이 배열의 앞쪽, 덜 매운 음식이 뒤쪽에 오도록 배열을 내림차순으로 정리했습니다.

배열의 끝 두개의 요소를 pop으로 제거하여 가장 덜 매운 음식을 선택했습니다. 이후 새로운 스코빌 지수를 계산하고, 새로운 스코빌 지수를 배열에 추가하였습니다.

이후 섞은 횟수를 1을 증가 시켰으며 가장 맵지 않은 음식이 여전히 K보다 작을 때는 -1을 반환하도록 하였습니다.

## 작성코드

---

```typescript
function solution(scoville, K) {
  var answer = 0;

  scoville.sort((a, b) => b - a);

  while (scoville.length > 1 && scoville[scoville.length - 1] < K) {
    const f = scoville.pop();
    const s = scoville.pop();

    const newScoville = sum(f, s);
    scoville.push(newScoville);
    scoville.sort((a, b) => b - a);

    answer += 1;
  }

  if (scoville[0] < K) {
    return -1;
  }

  return answer;
}

function sum(f, s) {
  return f + s * 2;
}
```
