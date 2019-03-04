---
title: "[Typescript] 1장 타입스크립트 시작하기"
date: 2019-03-04 11:16:00 +0900
categories: typescript
---

# 1장 타입스크립트 시작하기
타입스크립트와 그 기능에 대한 설명  
간단한 TODO 애플리케이션 만들기  

----

## 자바스크립트의 좋지 않은 부분

### 기능
자바스크립트에서 반드시 이해해야 할 4가지
- 타입유추(Type Inference)
- 배열
- 동등 비교(Equality Inference)
- Null과 Undefined

### 타입유추(Type Inference)
자바스크립트의 주요 특징 중 하나는 동적으로 타입을 결정한다는 것  
타입 불일치가 발생해도 오류를 일으키지 않음  
동일한 변수에 문자열, 숫자, 배열, 객체 를 할당 할 수 있음  
오류 발생의 가능성이 높음  
{% highlight typescript linenos %}
var num = 1;
num += 1;
console.log(typeof(num)); // number 출력
console.log(num); // 2 출력
num = 'str';
console.log(typeof(num)); // string 출력
num += 1;
console.log(num); // str1 출력
{% endhighlight %}

### 배열
자바스크립트의 동적 타입 결정이 실패하는 매우 일반적인 예
배열일 때는 slice 함수가 동작하지만 배열이 아니면 에러가 발생
{% highlight typescript linenos %}
var score = [1,2,3,4,5,6];
console.log( score.slice(2,4) ); // [3,4] 반환
score = 10;
console.log( score.slice(2,4) ); // 에러발생
{% endhighlight %}

### 동등비교(Equality comparision)
따옴표로 묶인 숫자는 문자열로 판단  
하지만 if문에서 숫사 상수와 비교하면 자동으로 문자열을 숫자로 변환하여 시도  
비교시 === 을 사용하면 자동변환이 되지 않음
{% highlight typescript linenos %}
var result = '1';
console.log( result == 1 ); // true
console.log( result === 1 ); // false
{% endhighlight %}

### Null or undefined
자바스크립트에서는 변수에 Null 또는 undefined 할당 가능  
적절한 유효성 검사가 필요함  
타입스크립트에서는 타입 선언을 통해 문제 발생을 줄임  
컴파일러 플래그 strictNullChecks를 제공, Null 또는 undefined 타입을 인스턴트에 할당하지 못하도록 하는 것이 가능  
타입스크립트는 변수, 파라미터 및 함수의 반환 값에 타입을 추가  
동일한 변수에 충돌하는 타입 발생시 식별 가능

> 타입스크립트에는 '''any''' 라는 특수한 타입이 있음  
> 변수의 타입이 확실하지 않은 경우 사용  
> 기존의 자바스크립트 코드를 타입스크립트로 전환 할 때 유용함 

