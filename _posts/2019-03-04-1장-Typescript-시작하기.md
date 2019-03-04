---
title: "[Typescript] 1장 타입스크립트 시작하기"
date: 2019-03-04 11:16:00 +0900
categories: typescript
---

# 1장 타입스크립트 시작하기
타입스크립트와 그 기능에 대한 설명  
간단한 TODO 애플리케이션 만들기  

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
