---
title: "H-Index(JS)"
date: "2024-08-05"
thumbnail: "/assets/img/thumbnail/1.jpeg"
---

# [H-Index](https://school.programmers.co.kr/learn/courses/30/lessons/42747#fn1, "프로그래머스 H-Index")

[H-Index 이해하기](https://www.ibric.org/bric/trend/bio-series.do;jsessionid=CEFD3E6FF855827098CD477C0FE37423?mode=series_view&newsArticleNo=8802417&articleNo=8882714&beforeMode=latest_list#!/list, "H-inex 이해하기")

## 문제 설명

H-Index는 과학자의 생산성과 영향력을 나타내는 지표입니다. 어느 과학자의 H-Index를 나타내는 값인 h를 구하려고 합니다. 위키백과1에 따르면, H-Index는 다음과 같이 구합니다.

어떤 과학자가 발표한 논문 n편 중, h번 이상 인용된 논문이 h편 이상이고 나머지 논문이 h번 이하 인용되었다면 h의 최댓값이 이 과학자의 H-Index입니다.

어떤 과학자가 발표한 논문의 인용 횟수를 담은 배열 citations가 매개변수로 주어질 때, 이 과학자의 H-Index를 return 하도록 solution 함수를 작성해주세요.

### 제한 사항

- 과학자가 발표한 논문의 수는 1편 이상 1,000편 이하입니다.
- 논문별 인용 횟수는 0회 이상 10,000회 이하입니다.

### 입출력 예

| citations       | return |
| --------------- | ------ |
| [3, 0, 6, 1, 5] | 3      |

### 입출력 예 설명

이 과학자가 발표한 논문의 수는 5편이고, 그중 3편의 논문은 3회 이상 인용되었습니다. 그리고 나머지 2편의 논문은 3회 이하 인용되었기 때문에 이 과학자의 H-Index는 3입니다.

## 문제풀이

---

H-Index는 피인용 수가 논문 수와 같아지거나 논문 수보다 작아지기 시작하는 지점에서 결정됩니다. 이를 기준으로 코드를 작성하면, 인용 횟수가 i 값보다 작아지기 시작하는 경우의 i 값이 H-Index가 됩니다.

<code>while (i + 1 <= citations[i])</code> 이와 같은 while문 을 사용하였고,

i = 0: 1 <= 6이므로 i++
i = 1: 2 <= 5이므로 i++  
i = 2: 3 <= 3이므로 i++  
i = 3: 4 <= 1이 성립하지 않으므로 while문을 종료 시켰습니다.

반복문이 종료된 후의 i 값이 H-Index를 나타냅니다.

## 작성코드

---

```typescript
function solution(citations) {
  citations.sort((a, b) => b - a);

  var i = 0;

  while (i + 1 <= citations[i]) {
    i++;
  }

  return i;
}
```
