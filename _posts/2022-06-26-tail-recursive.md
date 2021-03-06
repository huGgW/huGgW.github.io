---
layout: post
title: 재귀의 최적화 (Optimization of Recursion)
subtitle: 꼬리 재귀 (Tail Recursion)
categories: [Programming Language]
tags: [Programming Language, Algorithm, Optimization]
---

## 재귀와 반복?
어떤 수업에서 교수님께서는 다음과 같이 말씀하셨다.  
> 같은 알고리즘이라고 할 지라도 
> 재귀의 경우 반복문에 의해 Cost가 크기 때문에,
> 가능하다면 반복문으로 작성하는 것이 좋다.  

그러나 다른 수업에서 교수님께서 다음과 같이 말씀하셨다.
> 최근에는 프로그래밍 언어가 발달하여 
> 재귀문의 Cost가 반복문과 비슷하기 때문에, 
> 원하는 방식으로 프로그램을 작성하면 된다.

무엇이 사실일까? 
그리고 애초에 왜 두 방식의 Cost가 차이나는 것일까? 

## 재귀에서의 성능적 불리함
재귀란 자기 자신을 계속 호출하는 함수를 말한다.

***이 때 재귀는 반복문에 비해 같은 알고리즘일지라도 더 많은 메모리를 사용하게 된다.***

간단하게 1부터 n까지 더하는 함수를
    재귀함수로 만들어 보면 아래와 같다.  
```java
int sum(int n) {
    if (n <= 0) {
        return 0;
    }
    else {
        return sum(n-1) + n;
    }
}
```
여기서 n이 0보다 큰 경우 일어나는 과정을 살펴보면 다음과 같다.
1. sum(n-1)을 호출
2. 호출된 함수의 반환값과 n을 더함
3. 더한 값을 반환  

이 때, sum(n-1)이 호출될 때, 기존 호출된 sum(n)의 정보가 저장된 채로
    sum(n-1)과 관련된 값들이 새로 메모리에 저장되면서 호출되게 된다.

( 정확히는 sum(n)에 해당하는 Stack frame이 있는 상태로 
    새로 호출된 sum(n-1)에 해당하는 Stack frame이 
    메모리의 스택 영역에 push된다. )

기존에 호출된 함수의 정보가 저장되어야 하는 이유는,
    함수 안에서 호출된 새로운 함수의 작업이 끝난 후, 
    기존 함수 안에서 나머지 부분의 작업을 진행해야 되기 때문일 것이다.

위 재귀함수에서의 예시에서는 2번 부분 ( 호출된 함수의 반환값과 n을 더함)이
    나머지 부분에 해당할 것이다.

바로 기존에 호출된 함수의 정보가 저장되어 있다는 요인 때문에
    재귀에서는 반복문에 비해 각 시행마다 추가적으로 메모리를 차지하게 되고,
    이것이 재귀의 성능적인 약점인 것이다.

***그렇다면, 기존에 호출된 함수의 정보를 지속적으로 저장하지 않도록 하면***
    ***이런 추가적인 메모리의 차지를 제거할 수 있지 않을까?***

## 꼬리 재귀 (Tail Recursion)
***꼬리 호출 (Tail Call)이란, 함수 안에서 함수의 호출이 발생할 때,***
    ***이 호출이 함수의 가장 마지막 순서에 위치하는 것을 말한다.***

***그리고 이러한 꼬리 호출인 재귀 함수를 꼬리 재귀 (Tail Recursion)라고 한다.***

위에서 보았던 1부터 n까지 더하는 함수를 꼬리 재귀로 구현해보면 아래와 같다.
```java
int sum(int n, int s) {
    if (n <= 0) {
        return 0;
    }
    else {
        return sum(n-1, s+n);
    }
}
```
여기서 n이 0보다 큰 경우 일어나는 과정을 살펴보면 다음과 같다.
1. sum(n-1, s+n)을 호출
2. 호출된 함수의 반환값을 반환

이 때, 재귀 함수 sum(n-1, s+n)의 호출이 함수의 실행 순서 가장 마지막에 위치하기 때문에
    함수의 호출 이후에 해야 할 작업은 호출된 함수 sum(n-1, s+n)의 반환값을 반환하는 것 뿐이다.

이 말은, 재귀 함수 sum(n-1, s+n)의 호출 이후 해야 할 작업이 없다는 것을 의미하고,
    이는 결국 새로운 재귀 함수의 호출 시 기존에 호출되었던 함수 sum(n, s)의 정보는
    더 이상 필요없다는 것을 의미한다.

***즉, 기존에 호출되었던 함수 sum(n, s)의 정보를 새로운 재귀 함수 호출 시에 제거해도***
    ***문제가 없다는 것을 의미한다.***

***( 새로운 함수의 호출 시 기존 함수의 Stack frame을 pop해도 된다는 것을 의미한다. )***

## 꼬리 호출 최적화 (Tail Call Optimization)
이런 식으로 작성된 꼬리 재귀 함수는 
    프로그램의 컴파일러(인터프리터)에 의해 다음 재귀 함수 호출 시
    기존 함수의 저장되어있던 정보를 메모리에서 제거함으로써
    ( 기존 함수의 Stack frame을 pop함으로써 )
    재귀 함수의 동작을 최적화하게 된다.

***이를 꼬리 호출 최적화 (Tail Call Optimization)라고 한다.***

이러한 꼬리 호출 최적화를 거치면, 재귀 함수로 프로그램을 작성해도
    각 반복마다 추가적인 메모리의 사용이 사라졌기 때문에
    결과적으로 반복문과 비슷한 메모리 사용량을 가지게 된다.

***즉, 재귀와 반복문이 비슷한 성능을 가지게 되는 것이다.***

그러나, 아쉽게도 모든 프로그래밍 언어가 이러한 꼬리 호출 최적화를 지원하지는 않는다.

대표적인 예시로 C++, Haskell, Ocaml 같은 경우 꼬리 호출 최적화를 지원하고,
반대로 Java, Python, Go의 경우 꼬리 호출 최적화를 지원하지 않는다.

## 결론
>기본적으로 재귀 함수는 다음 재귀 함수의 호출 시 기존 재귀 함수의 정보를 저장해야 되기 때문에
    반복문에 비해 많은 메모리를 차지하게 된다.

>그러나 꼬리 재귀를 통해 기존 재귀 함수의 정보를 저장할 필요성을 제거하고,
    꼬리 호출 최적화를 통해 다음 재귀 함수 호출 시 저장된 기존 재귀 함수의 정보를 제거하도록 최적화하면
    반복문과 비슷한 성능을 보이게 된다.

>다만, 일부 프로그래밍 언어만 이를 지원하므로, 특정 케이스의 경우만 재귀를 반복문과 
    같은 성능으로 사용할 수 있다고 생각할 수 있겠다.

## 추가 자료
<https://en.wikipedia.org/wiki/Tail_call>