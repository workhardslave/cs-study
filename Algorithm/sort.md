# Merge Sort

- RUN(병합을 하기 위해 묶은 데이터 그룹)을 원소 개수만큼 나눈 후 합병하는 정렬 방법
- 시간복잡도 
  - best : O(nlogn) 
  - worst : O(nlogn)

![img](https://github.com/namjunemy/TIL/blob/master/Algorithm/img/merge_01.png?raw=true)



# Insertion Sort

- 시간복잡도
  - best : O(n) 
  - average : O(n^2)
  - worst : O(n^2)
- 단, insertion sort는 참조 지역성의 원리를 잘 만족하기 때문에 작은 n 에 대해서 quick sort보다 빠를 수 있음.
  - Insertion sort의 상수 C를 Ci, 정렬 알고리즘 중 C 값이 가장 작다고 알려져 있는 Quick sort의 상수 C를 Cq라 할 때, 작은 n에 대하여 C_i*N^2 < C_q×nlog⁡n 이 성립한다
  - https://imgur.com/gallery/omL5k





# Tim Sort
- Merge sort 를 최적화한 정렬 알고리즘
- 시간복잡도 
  - best : O(n)
  - avearge : O(nlogn)
  - worst : O(nlogn)


## Run
- 원소 64개 이하의 경우  binary insertion sort 가 가장 효율적임. 최소 Run의 원소 개수는 32 ~ 64 사이로 권장. 
- Run의 첫 두 원소의 대소 관계로 증가, 감소를 정의하여 minrun만큼 binary insertion sort로 정렬
- 그 이후의 원소에 대해서 정렬조건(증가, 감소)를 만족하는 한 크게 Run을 만든다.
  ![img](https://d2.naver.com/content/images/2020/01/img-4-.png)
- 감소방향으로 정의된 Run은 Merge sort가 [안정적인 정렬](https://j3sung.tistory.com/713)이기 때문에 뒤집을 수 있음
  - 증가하는 run: a0≤a1≤a2≤a3≤..
  - 감소하는 run: a0>a1>a2>a3>..



## Merge
- 안정성을 유지하기 위해 인접한 Run 끼리 병합해야 함
- 비슷한 크기의 Run을 병합하여  최소한의 메모리만을 사용
- 위 두 조건을 만족시키는 두가지 조건
![img](https://d2.naver.com/content/images/2020/01/img-6-.png)
![img](https://d2.naver.com/content/images/2020/01/img-8-.png)


## Merge space overhead
- Merge Sort 는 여분의 메모리 O(n)가 필요
- 사용 메모리 공간을 줄이기 위해 두 Run 중 크기가 작은 Run 을 추가 공간에 복사
- 이후 각 Run의 시작 부분부터 크기를 비교하여 작은 순서대로 앞을 채워가며 병합
- 최악의 경우 O(1/2*n) 공간이 더 필요하게 된다.
![img](https://d2.naver.com/content/images/2020/01/img-9-.gif)


# Intro Sort
- heap Sort + quick sort
- quick sort는 정렬할 데이터가 이미 정렬 혹은 역정렬이 되어 있는 경우 항상 피벗이 한쪽으로 쏠리게 되어 O(N^2)의 시간복잡도를 가짐.
  ![img](https://media.vlpt.us/images/delmasong/post/9b21092d-9c1f-4cd6-a703-52240fe18dff/스크린샷%202020-03-05%20오후%202.02.48.png)
  Intro sort는 함수가 재귀호출할 최대 깊이를 설정하여, 최대 깊이 이상이 된 경우 재귀를 더 들어가지 않고 남은 부분에 대해서는 힙 정렬을 수행하는 정렬기법이다.
- 시간복잡도 : O(nlogn)
- GCC의 `std::sort`는 Intro sort를 변형하여 16개 미만의 원소들에 대해 일단 무시하고 진행한 뒤 마지막에 전체에 대해 삽입 정렬을 수행하는 방법을 선택하여 사용
![img](http://www.secmem.org/assets/images/special-sorts/1.png)



> Ref
>
> 1. http://www.secmem.org/blog/2019/04/10/special-sorts/
> 2. https://medium.com/pocs/locality의-관점에서-quick-sort가-merge-sort보다-빠른-이유-824798181693
> 3. https://d2.naver.com/helloworld/0315536
> 4. http://svn.python.org/projects/python/trunk/Objects/listsort.txt
> 5. https://justicehui.github.io/medium-algorithm/2019/03/24/IntroSort/

