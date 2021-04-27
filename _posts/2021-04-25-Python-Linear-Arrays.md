---
title: "선형 배열(Linear Arrays)"
excerpt: ""

categories:
  - 
tags:
  - 
last_modified_at:
---

배열: 같은 종류의 데이터가 줄지어 늘어서 있는 것
Python에서 List로 배열을 표현할 수 있고, 서로 다른 종류의 데이터를 줄세울 수도 있음

- 리스트의 길이와 무관한 연산(상수시간, O(N))
1. 원소 덧붙이기
   .append()
2. 끝에서 꺼내기
   .pop()

   ```python
   # 리스트 선언
   L = ['a', 'b', 'c', 'd']
   # 원소 덧붙이기
   L.append('e') # L = ['a', 'b', 'c', 'd', 'e']
   # 끝에서 꺼내기
   L.pop() # e
   ```
   
- 리스트의 길이와 비례하는 연산(선형시간, O(N))
1. 원소 삽입하기
   .insert(i, x) # index i의 위치에 원소 x 삽입
2. 원소 삭제하기
   .del(L[i]) # index i의 위치에 원소 삭제
   
   ```python
   # 리스트 선언
   L = ['a', 'b', 'c', 'd']
   # 원소 삽입하기
   L.insert(3, 'x') # L = ['a', 'b', 'c', 'x', 'd']
   # 원소 삭제하기
   L.delete(3) # L = ['a', 'b', 'c', 'd']
   ```
   
1. 원소 탐색하기
   .index(x) # x라는 원소가 몇 번째 인덱스에 있는지 찾기
2.
