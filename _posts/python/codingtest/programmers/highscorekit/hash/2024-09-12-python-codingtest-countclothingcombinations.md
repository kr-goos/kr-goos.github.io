---
title: "[Python] 프로그래머스 고득점 Kit 의상"
description: 의상 문제에 대해 풀이해 해보겠습니다.
author: 김우석
date: 2024-09-12 08:40:00 +0900
categories: [Python, CodingTest]
tags: [Python, 코딩테스트]
image:
  path: /assets/img/posts/python/python.svg
---

## 문제 설명
코니는 매일 다른 옷을 조합하여 입는것을 좋아합니다.

예를 들어 코니가 가진 옷이 아래와 같고, 오늘 코니가 동그란 안경, 긴 코트, 파란색 티셔츠를 입었다면 다음날은 청바지를 추가로 입거나 동그란 안경 대신 검정 선글라스를 착용하거나 해야합니다.

| 종류 | 이름 |
| --- | --- |
| 얼굴 | 동그란 안경, 검정 선글라스 |
| 상의 | 파란색 티셔츠 |
| 하의 | 청바지 |
| 겉옷 | 긴 코트 |

- 코니는 각 종류별로 최대 1가지 의상만 착용할 수 있습니다. 예를 들어 위 예시의 경우 동그란 안경과 검정 선글라스를 동시에 착용할 수는 없습니다.
- 착용한 의상의 일부가 겹치더라도, 다른 의상이 겹치지 않거나, 혹은 의상을 추가로 더 착용한 경우에는 서로 다른 방법으로 옷을 착용한 것으로 계산합니다.
- 코니는 하루에 최소 한 개의 의상은 입습니다.

코니가 가진 의상들이 담긴 2차원 배열 clothes가 주어질 때 서로 다른 옷의 조합의 수를 return 하도록 solution 함수를 작성해주세요.


## 제한사항
- clothes의 각 행은 [의상의 이름, 의상의 종류]로 이루어져 있습니다.
- 코니가 가진 의상의 수는 1개 이상 30개 이하입니다.
- 같은 이름을 가진 의상은 존재하지 않습니다.
- clothes의 모든 원소는 문자열로 이루어져 있습니다.
- 모든 문자열의 길이는 1 이상 20 이하인 자연수이고 알파벳 소문자 또는 '_' 로만 이루어져 있습니다.


## 입출력 예
{% raw %}
| clothes | return |
| --- | --- |
| \[\["yellow\_hat", "headgear"\], \["blue\_sunglasses", "eyewear"\], \["green\_turban", "headgear"\]\] | 5 |
| \[\["crow\_mask", "face"\], \["blue\_sunglasses", "face"\], \["smoky\_makeup", "face"\]\] | 3 |
{% endraw %}

## 입출력 예 설명
**입출력 예 #1**

headgear에 해당하는 의상이 yellow_hat, green_turban이고 eyewear에 해당하는 의상이 blue_sunglasses이므로 아래와 같이 5개의 조합이 가능합니다.

```
1. yellow_hat
2. blue_sunglasses
3. green_turban
4. yellow_hat + blue_sunglasses
5. green_turban + blue_sunglasses
```

**입출력 예 #2**

face에 해당하는 의상이 crow_mask, blue_sunglasses, smoky_makeup이므로 아래와 같이 3개의 조합이 가능합니다.

```
1. crow_mask
2. blue_sunglasses
3. smoky_makeup
```


## 풀이 
```python
def solution(clothes):
    clothes_dict = {}
    for name, kind in clothes:
        if kind in clothes_dict:
            clothes_dict[kind] += 1  
        else:
            clothes_dict[kind] = 1   
    
    answer = 1
    for count in clothes_dict.values():
        answer *= (count + 1)  

    return answer - 1  
```

1. `clothes_dict`  딕셔너리 초기화
2. `clothes` 순회
    - 각 의상의 이름(`name`)과 종류(`kind`)를 추출
3. 딕셔너리(`clothes_dict`)에 현재 옷(`kind`)의 종류가 있는지 확인
    - 옷이 있다면 : 현재 옷에 대한 카운트 증가
    - 옷이 없다면 : 현재 옷을 딕셔너리 추가하고 1 값으로 초기화
4. `answer` 초기화
    - 조합 수를 계산하기 위해 1로 초기화
5. 조합의 수 계산
    - 딕셔너리에 저장된 각 종류별 의상 개수(`count`)를 가져온 후 answer 에 `(count + 1)` 을 곱해 조합 수를 계산
        - \
6. `answer -1` 반환
    - 최종적으로 모든 경우에서 의상을 전혀 입지 않은 경우를 제외하고 조합 수를 반환


### 실행결과
```
채점 결과
정확성: 100.0
합계: 100.0 / 100.0
```


## 문제에서 의상 조합의 개념
이 문제에서는 각 의상의 종류별로 **최대 하나의 의상**만 선택할 수 있으며, **아무것도 입지 않을** 수도 있습니다. 즉, 각 의상 종류마다 두 가지 선택이 가능합니다.

1. 그 종류의 의상을 하나 고르기
2. 그 종류에서 아무것도 입지 않기

## 위 문제에서 `count + 1` 을 한 이유
`clothes = [["yellow_hat", "headgear"], ["green_turban", "headgear"], ["blue_sunglasses", "eyewear"]]` 의 경우로 예를 들어보겠습니다.

### 의상 종류별로 나눠보기
- `headgear` : 2개 (yellow_hat, green_turban)
- `eyewear` : 1개 (blue_sunglasses)

1. headgear의 경우
- 선택할 수 있는 경우 : yellow_hat 또는 green_turban
- 아무것도 선택하지 않을 경우
- 따라서, `headgear`에서 선택 가능한 경우는 총 3가지 입니다.
    - yellow_hat 선택
    - green_turban 선택
    - 아무것도 선택하지 않기
- 총 경우의 수 : 2 (의상 개수) + 1 (아무것도 입지 않은 경우) = 3

2. eyewear의 경우
- 선택할 수 있는 경우 : blue_sunglasses
- 아무것도 선택하지 않을 경우
- 따라서, `eyewear`에서 가능한 선택은 총 2가지 입니다.
    - blue_sunglasses 선택
    - 아무것도 선택하지 않기
- 총 경우의 수 : 1 (의상 개수) + 1 (아무것도 입지 않은 경우) = 2

### 조합의 수 계산
- 위에서 계산한 경우의 수를 모두 곱해줍니다.
    - 3 (`headgear`에서 선택할 수 있는 경우) * 2 (`eyeweard`에서 선택할 수 있는 경우)

### 좋합의 수에서 1을 뺴는 이유?
- 현재 문제에서는 **최소 하나의 의상**은 입어야 한다고 되어있습니다. 즉, 아무것도 입지 않은 경우는 허용되지 않습니다. 위에서 구한 6가지의 경우 중에는 **아무것도 입지 않은 경우**가 포함되어 있으므로, 이를 제외해야 합니다.
- 따라서 조합의 수는 (3 * 2) - 1

        

## [Github 바로가기](https://github.com/kr-goos/coding-test-solutions/blob/master/programmers/HighScoreKit/hash/count_clothing_combinations/solution.py)