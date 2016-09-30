---
layout: entry
title: 부분 합
author: 김성중
author-email: ajax0615@gmail.com
description: 부분 합이란, 배열의 각 위치에 대해 배열의 시작부터 현재 위치까지의 원소의 합을 구해 둔 배열입니다.
publish: true
---

## Intro
N명의 시험 성적을 내림차순으로 정렬해 둔 배열 scores[]가 있다고 합시다. a등부터 b등까지 평균 점수를 계산하는 함수 average(a, b)를 만들고 싶을 때, 가장 먼저 할 수 있는 방법은 scores[a]부터 scores[b]까지를 순회하면서 각 수를 더하고 이것을 b-a+1로 나누는 것입니다. 이는 반복문의 수행 횟수만큼 **O(N)** 의 시간 복잡도가 됩니다. 평균 점수를 한 번 계산할 거라면 이걸로 충분하지만, average()를 굉장히 여러 번 호출해야 할 경우, 이를 좀 더 최적화하는 방법을 찾아야 합니다. 바로 이 때 유용하게 사용되는 것이 **부분 합(partial sum)** 혹은 누적 합입니다. 부분 합이란, **배열의 각 위치에 대해 배열의 시작부터 현재 위치까지의 원소의 합을 구해 둔 배열** 입니다.

```
psum[i] = scores[j]의 합, j=[0, i]
```

다음 표는 한 예제 배열 scores[]의 각 원소와 해당 배열의 부분 합을 보여줍니다.

| :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| i | 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 |
| scores | 100 | 97 | 86 | 79 | 66 | 52 | 49 | 42 | 31 |
| psum | 100 | 197 | 283 | 362 | 428 | 480 | 529 | 571 | 602 |

psum을 미리 계산해 두면 scores[]의 특정 구간의 합을 **O(1)** 에 구할 수 있습니다. psum[-1]=0이라고 가정할 때, scores[a]부터 scores[b]까지의 합을 다음과 같이 얻을 수 있습니다.

```
psum[b] - psum[a - 1]
```

별 것 아닌 단순한 아이디어지만 부분 합은 종종 아주 유용하게 이용됩니다.

## 부분 합 계산하기
구간 합을 빠르게 계산하기 위해서는 부분 합을 미리 계산해 둘 필요가 있습니다. 부분 합을 계산하는 데 드는 시간은 **수열의 길이에 따라 선형으로 증가** 한다는 점에 유의해야 합니다. 반복문을 통해 구간 합을 구하기 위해 최대 O(N)의 시간이 걸린다는 점을 돌이켜 보면, 구간 합을 두 번 이상 구할 때는 대부분의 경우 부분 합을 사용하는 쪽이 이득임을 쉽게 알 수 있습니다.

```
// 부분합을 계산하는 함수와 이를 이용해 구간합을 계산하는 함수
vector<int> partialSum(const vector<int>& a) {
    vector<int> ret(a.size());
    ret[0] = a[0];
    for (int begin = 1; begin < a.size(); +=begin) {
        ret[begin] = ret[begin - 1] + a[begin];
    }
    return ret;
}

int rangeSum(const vector<int>& psum, int a, int b) {
    if (a == 0) return psum[b];
    return psum[b] - psum[a - 1];
}
```

## 2차원으로의 확장
부분 합은 1차원 배열에서만 쓸 수 있는 것은 아닙니다. 2차원 배열 A[][]이 주어질 때, A[y1, x1]에서 A[y2, x2]까지의 직사각형 구간의 합을 계산해야 한다고 합시다. 다음과 같은 부분 합 배열을 사용해 이 구간 합을 빠르게 구할 수 있습니다.

```
psum[y, x] = A[i, j]의 합, i=[0,y], j=[0,x]
```

다시 말해 psum[y, x]는 (0, 0)을 왼쪽 위 칸, (y, x)를 오른쪽 아래 칸으로 갖는 직사각형 구간에 포함된 원소들의 합입니다. psum[][]을 미리 계산해 두면 2차원 배열에서도 우리가 원하는 구간의 합을 쉽게 구할 수 있습니다.

예제 : [Baekjoon Online Judge - 가장 큰 정사각형](https://www.acmicpc.net/problem/1915)

```
// 어떤 2차원 배열 A[][]의 부분합 psum[][]이 주어질 때,
// A[y1, x1]과 A[y2, x2]를 양 끝으로 갖는 부분 배열의 합을 반환
int gridSum(const vector<vector<int> >& psum, int y1, int x1, int y2, int x2) {
    int ret = psum[y2][x2];
    if (y1 > 0) ret -= psum[y1 - 1][x2];
    if (x1 > 0) ret -= psum[y2][x1 - 1];
    if (y1 > 0 && x1 > 0) ret += psum[y1 - 1][x1 - 1];
    return ret;
}
```

## 합이 0에 가장 가까운 구간
양수와 음수가 모두 포함된 배열 A[]가 있을 때, 그 합이 0에 가장 가까운 구간을 찾는 문제입니다. 예를 들어, 다음과 같은 A[]가 있습니다.

| :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| i | 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 |
| A[i] | -14 | 7 | 2 | 3 | -8 | 4 | -6 | 8 | 9 | 11 |

A[]에는 합이 0인 구간은 없지만, 0에 가장 가까운 구간은 A[2]~A[5]로 그 합이 1입니다. 이런 구간을 찾는 한 가지 방법은 A의 모든 구간을 순회하면서 각각의 합을 계산하는 것입니다. 이렇게 하면 배열의 길이 N에 대해 **O(N^2)** 의 시간이 걸립니다.

부분 합을 사용하면 이 문제를 쉽게 해결 할 수 있습니다. 구간 합을 사용하면 A[i]~A[j] 구간의 합을 다음과 같이 표현할 수 있습니다.

```
A[i]~A[j]의 합 = psum[j] - psum[i - 1]
```

이 값이 0에 가장 가깝다는 말은 psum[]의 두 값의 차이가 가장 적다는 뜻입니다. 주어진 배열에서 가장 가까운 두 값을 찾기 위한 가장 간단한 방법은 이 배열을 정렬한 뒤 인접한 원소들을 확인하는 것입니다. 정렬은 O(NlgN) 시간에 수행할 수 있고, 부분 합을 구하는 것과 인접한 원소들을 확인하는 것 모두 O(N)에 할 수 있으니 이 알고리즘의 수행 시간은 O(NlgN)이 됩니다.