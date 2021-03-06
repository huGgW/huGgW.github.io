---
layout: post
title: 조합 도출 알고리즘
subtitle: 유클리드 호제법
categories: Algorithm
tags: [Algorithm, Math, Dynamic Programming]
---

최근에 백준에 있는 단계별 알고리즘 문제를 처음부터 풀고 있다.  
그 과정에서 잊고 있었던, 고등학교 때 배웠던 수학적 사실들을 다시 찾아보는 기회가 되었다.  
***그 중에서도 인상깊었던 조합과 관련된 내용을 간단하게 살펴보겠다.***

## 조합
조합, $\, _n C _k$란 서로 다른 n개의 원소를 가지는 집합에서 순서에 관계없이 k개의 원소를 선택하는 방법의 가짓수를 의미한다.  
조합의 값은 아래와 같다.  
$$
 _n C _k = \frac{n!}{k! (n-k)!}$$

그러나 이 식대로 조합을 구하는 것은 많은 수의 곱연산이 필요하므로 비효율적이다.  
이를 해결하기 위해 조합의 수학적 성질을 살펴보면 아래와 같다.  

## 조합 관련 성질
1.  
 $$
 _n C _k = _n C _{n-k} $$
> 증명:
> $$
> _n C _k = \frac{n!}{k! (n-k)!} = \frac{n!}{(n-k)! k!} = _n C _{n-k} $$  
<br>

2.  (파스칼의 법칙)  
 $$
 _{n} C _{k} = _{n-1} C _{k-1} + _{n-1} C _k $$
> 증명:  
> $n$개의 서로 다른 원소에서 임의로 1개를 지정하자.  
> 이 때 $\,n$개의 원소에서 $\,k$개의 원소를 순서와 관계없이 뽑는 경우를 생각해보면,  
> 우리가 지정한 1개의 원소가 $\,k$개의 원소 안에 포함되어 있는 케이스와 포함되어있지 않은 케이스로 나뉜다.  
> 지정한 원소가 포함되어있는 경우, 이 원소를 제외한 나머지 $\,n-1$개의 원소에서 $\,k-1$개의 원소를 순서와 관계없이 골라야 하고,  
> 반대로 지정한 원소가 포함되어있지 않은 경우, 이 원소를 제외한 나머지 $\,n-1$개의 원소에서 $\,k$개의 원소를 순서와 관계없이 골라야 한다.  
> 이 설명을 수식으로 나타내면 위와 같이 된다.  

## 알고리즘
기본적으로, 위의 2번째 성질을 이용하여 재귀적으로 알고리즘을 구현해보면, 아래와 같이 될 것이다.  
```java
int combination_naive(int n, int k) {
    if ((n == k) || (k == 0)) {
        return 1;
    }
    else {
        return combination_naive(n-1, k-1) + combination_naive(n-1, k);
    }
}
```

하지만, 위 알고리즘의 경우 매우 많은 재귀호출이 발생할 것이고, 그 과정에서 겹치는 값을 구하기 위한 호출도 많기 때문에 비효율적이다.  
즉, 부분문제의 중복이 발생하게 된다.  
따라서, Dynamic Programming을 이용하여 알고리즘을 구현하면 중복되는 연산을 제거할 수 있어 알고리즘이 효율적으로 될 것이다.  
이를 구현해보면 아래와 같이 될 것이다.  
```java
int combination(int n, int k) {
    // 성질 1을 이용하여 가능한 최대 k_값을 n/2로 줄임
    int k_ = (k > n-k) ? n-k : k; 

    // combination 값들 저장된 table
    int[][] comb = new int[n+1][(n/2) + 1]; 
    // 1부터 n까지를 index로 사용하기 위해 편의상 n+1로 설정

    // 성질 2를 이용하여 combination 아래부터 채우기
    comb[1][0] = 1;
    for (int i = 2; i <= n; i++) {
        comb[i][0] = 1;
        for (int j = 1; j <= (i/2); j++) {
            comb[i][j] =                  // 성질1
                comb[i-1][j-1] + comb[i-1][((i-1)-j > j) ? j : (i-1)-j];
        }
    }

    return comb[n][k_];
}
```

## Reference
<https://ko.wikipedia.org/wiki/%EC%9D%B4%ED%95%AD_%EA%B3%84%EC%88%98>