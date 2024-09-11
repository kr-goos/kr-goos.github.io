---
title: "[Python] 프로그래머스 고득점 Kit 전화번호 목록"
description: 전화번호 목록 문제에 대해 풀이해 해보겠습니다.
author: 김우석
date: 2024-09-10 08:40:00 +0900
categories: [Python, CodingTest]
tags: [Python, 코딩테스트]
image:
  path: /assets/img/posts/python/python.svg
---

## 문제 설명
전화번호부에 적힌 전화번호 중, 한 번호가 다른 번호의 접두어인 경우가 있는지 확인하려 합니다.
전화번호가 다음과 같을 경우, 구조대 전화번호는 영석이의 전화번호의 접두사입니다.

- 구조대 : 119
- 박준영 : 97 674 223
- 지영석 : 11 9552 4421

전화번호부에 적힌 전화번호를 담은 배열 phone_book 이 solution 함수의 매개변수로 주어질 때, 어떤 번호가 다른 번호의 접두어인 경우가 있으면 false를 그렇지 않으면 true를 return 하도록 solution 함수를 작성해주세요.

## 제한사항
- phone_book의 길이는 1 이상 1,000,000 이하입니다.
	- 각 전화번호의 길이는 1 이상 20 이하입니다.
	- 같은 전화번호가 중복해서 들어있지 않습니다.

## 입출력 예
{% raw %}
| phone\_book | return |
| --- | --- |
| \["119", "97674223", "1195524421"\] | false |
| \["123","456","789"\] | true |
| \["12","123","1235","567","88"\] | false |
{% endraw %}

## 입출력 예 설명
**입출력 예 #1**

앞에서 설명한 예와 같습니다.

**입출력 예 #2**

한 번호가 다른 번호의 접두사인 경우가 없으므로, 답은 true입니다.

**입출력 예 #3**

첫 번째 전화번호, “12”가 두 번째 전화번호 “123”의 접두사입니다. 따라서 답은 false입니다.

## 풀이 
### 정렬을 이용한 방법

```python
def solution(phone_book: List[int]) -> bool:
    phone_book.sort()
    for i in range(len(phone_book)-1):
        if phone_book[i] == phone_book[i+1][:len(phone_book[i])]:
            return False
    return True
```

1. 정렬
	- 전화번호 목록을 **사전식**으로 정렬 ( 접두어 관계가 있는 번호들이 인접하게 정렬 )
2. 접두어 확인 및 비교
	- 각 전화번호(`phone_book`)를 순차적으로 확인
	- 정렬된 상태에서, **인접한 두 전화번호**를 비교하여 접두어 관계를 확인
		- `i+1` 번째 번호의 앞부분을 `i` 번째 번호의 길이만큼 자른 후 비교
		- 만약 인접한 두 값이 같다면 같은 접두어라는 의미로 `False` 반환
3. 접두어 관계가 없으면 `True` 반환

#### 실행결과
```
정확성: 83.3
효율성: 16.7
합계: 100.0 / 100.0
```


### 해시맵을 이용한 방법
```python
def solution(phone_book: List[int]) -> bool:
    hash_map: Dict[str,int] = {k: 0 for k in phone_book}

    for phone_number in phone_book:
        prefix = ''
        for numeric_str in phone_number:
            prefix += numeric_str
            if prefix == phone_number:
                continue

            if prefix in hash_map:
                return False
            
    return True
```
1. 해시맵 초기화
	- `hash_map` : 전화번호를 키로하고, 초기값은 0 인 딕셔너리로 초기화 (전화번호를 상수 시간 복잡도로 검색하기 위함)
2. 전화번호 순회
3. 접두어 생성
	- 각 전화번호에 대해 빈 문자열 `prefix`를 초기화하고, 해당 전화번호의 각 숫자를 순회하여 `prefix` 에 추가
	- 현재 전화번호의 접두어를 한 글자씩 추가하여 생성
4. 접두어 확인
	- 현재 접두어가 원래 전화번호와 같으면, 더이상 확인할 필요가 없으므로 다음 숫자로 넘어감
	- 현재 생성된 접두어가 `hash_map` 에 존재하는지 확인하고 만약 존재한다면 어떤 번호의 접두어라는 의미이므로 `False`를 반환
5. 모든 전화번호를 순회한 후에도 접두어 관계가 발견되지 않았다면 `True` 반환

#### 실행결과
```
정확성: 83.3
효율성: 16.7
합계: 100.0 / 100.0
```


## [Github 바로가기](https://github.com/kr-goos/coding-test-solutions/blob/master/programmers/HighScoreKit/hash/phone_number_list/solution.py)