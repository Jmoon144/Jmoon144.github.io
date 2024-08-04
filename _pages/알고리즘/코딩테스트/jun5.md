---
title: "전화번호 목록(JS)"
date: "2024-07-27"
thumbnail: "/assets/img/thumbnail/1.jpeg"
---

# [전화번호 목록](https://school.programmers.co.kr/learn/courses/30/lessons/42577, "프로그래머스 더 맵게")

---

## 문제 설명

전화번호부에 적힌 전화번호 중, 한 번호가 다른 번호의 접두어인 경우가 있는지 확인하려 합니다.
전화번호가 다음과 같을 경우, 구조대 전화번호는 영석이의 전화번호의 접두사입니다.

- 구조대 : 119
- 박준영 : 97 674 223
- 지영석 : 11 9552 4421

전화번호부에 적힌 전화번호를 담은 배열 phone_book 이 solution 함수의 매개변수로 주어질 때, 어떤 번호가 다른 번호의 접두어인 경우가 있으면 false를 그렇지 않으면 true를 return 하도록 solution 함수를 작성해주세요.

### 제한 사항

- phone_book의 길이는 1 이상 1,000,000 이하입니다.
  - 각 전화번호의 길이는 1 이상 20 이하입니다.
  - 같은 전화번호가 중복해서 들어있지 않습니다.

### 입출력 예

| phone_book                        | return |
| --------------------------------- | ------ |
| ["119", "97674223", "1195524421"] | false  |
| ["123","456","789"]               | true   |
| ["12","123","1235","567","88"]    | false  |

### 입출력 예 설명

입출력 예 #1
앞에서 설명한 예와 같습니다.

입출력 예 #2
한 번호가 다른 번호의 접두사인 경우가 없으므로, 답은 true입니다.

입출력 예 #3
첫 번째 전화번호, “12”가 두 번째 전화번호 “123”의 접두사입니다. 따라서 답은 false입니다.

## 문제풀이

---

while 반복문은 전화번호 목록이 비어 있지 않고 answer가 true인 동안 계속 실행됩니다. 이 반복문을 통해 목록의 모든 번호를 검사하여 접두어 관계가 있는지를 확인합니다.

for 반복문을 사용하여 목록의 각 전화번호를 순회합니다. 첫 번째 전화번호가 다음 전화번호의 접두어인지 확인하기 위해 startsWith 메소드를 사용합니다. 만약 첫 번째 번호가 다음 번호의 시작 부분과 일치한다면, 이는 접두어 관계가 있는 것이므로 answer를 false로 설정합니다.

<code>phone_book[i + 1]?.startsWith(phone_book[0])</code> 이 코드는 옵셔널 체이닝을 사용하여 i + 1이 phone_book의 범위를 벗어나지 않을 경우에만 startsWith 메소드를 호출합니다.
만약 첫 번째 번호가 다음 번호의 접두어라면 answer를 false로 설정합니다.

<code>phone_book.shift()</code>를 통해 목록의 첫 번째 요소를 제거하여 다음 반복에서 새로운 첫 번째 요소를 비교하게 합니다.

최종적으로 접두어 관계가 발견되지 않았으면 true를 반환하고, 발견되었으면 false를 반환하도록 했습니다.

## 작성코드

---

```typescript
function solution(phone_book) {
  var answer = true;

  phone_book.sort();

  while (phone_book.length !== 0 && answer === true) {
    for (let i = 0; i < phone_book.length; i++) {
      if (phone_book[i + 1]?.startsWith(phone_book[0])) {
        answer = false;
      }
    }

    phone_book.shift();
  }

  return answer;
}
```
