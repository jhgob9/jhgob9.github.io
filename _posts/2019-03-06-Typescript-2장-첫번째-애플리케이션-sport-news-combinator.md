---
title: "[Typescript] 2장 첫 번째 애플리케이션 Sport News Combinator"
date: 2019-03-06 21:00:00 +0900
categories: typescript
---

# 2장 첫 번째 애플리케이션 Sport News Combinator

이번 장에서 다룰 내용  

- SNC(Sport News Combinator) : 첫 번째 애플리케이션과 기능을 소개
- TypeScript의 타입 : 타입은 타입스크립트에서 중요한 역할을 담당. 다양한 타입, 타입 추론 및 타입 선언을 살펴봄
- TypeScript의 클래스 : 객체지향 기능을 제공하는 클래스를 구현
- Angular 소개 : 컴포넌트, 데이터, 바인딩과 같은 기본 개념 소개
- 프로젝트 셋업, 설정 및 코드 설정 : 앵귤러를 사용해서 단일 페이지 애플리케이션을 만든 다음 Angular CLI를 사용하여 애플리케이션의 골격을 만들어 다양한 방법으로 프로젝트를 만드는 방법을 실습
- SNC의 아키텍처 : SNC의 전체 아키텍처를 살펴보고 생성될 다양한 컴포넌트에 대해 실습
- 첫 번째 컴포넌트 작성 : 첫 번째 컴포넌트를 작성하여 페이지에 뉴스 컨텐츠를 표시

---

## Sport News Combinator - SNC

SNC는 다양한 뉴스 아울렛에서 나온 최신 스포츠 뉴스를 볼 수 있는 단일 페이지 웹애플리케이션(SPA)  
NFL, Fox Sports, ESPN 및 BBC Sport에서 뉴스를 가져올 예정  
총 4개의 탭, 뉴스 컨텐츠의 상위 10개 기사를 출력  

### 코드 다운로드

[깃헙이동](https://github.com/sachinohri/SportsNewsCombinator){: target="_blank" }

---

## 타입 시스템(Type System)

- 변수 : 타입스크립트에서 변수와 상수를 선언하는 방법
- 타입 : 원시 타입과 사용자 정의 타입을 모두 포함하여 타입스크립가 제공하는 타입을 실습
- 타입 추론 : 타입스크립트에서 타입 추론을 사용하여 타입을 식별하는 방법
- 타입 호환성 : 타입스크립에는 멤버만을 기준으로 타입을 연관시켜주는 기능 실습

> 타입은 타입스크립트에만 존재하는 개념이며 자바스크립로는 변환하지 않음. 디자인과 컴파일 시에만 사용

### 변수

기존의 var 키워드가 있었고 ES2015에서는 새로운 키워드가 추가 됨 - let, const 키워드  
var 키워드를 사용하여 변수를 선언할 수는 있지만 변수에 대한 범위 지정 및 액세스 방법에 관한 단점이 존재  

### var 키워드

자바스크립트에는 전역변수와 전역변수가 존재  
let를 사용하지 않고 var을 사용 할 경우 변수가 선언된 함수 내에서만 가능한 지역변수가 됨  
동일한 범위를 공유하는 모든 함수에 액세스될 수 있음을 의미  
중첩함수에서 내부함수는 외부함수의 변수에 접근 가능  

{% highlight typescript %}
function outerFunction(){
   var value = 10;
   function innerFunction(){
      console.log(value);
   }
   return innerFunction;
}
var func = outerFunction();
func();
{% endhighlight %}

innerFunction은 outerFunction의 변수 value에 접근이 가능하므로 10이 출력됨  

var로 선언된 변수는 끌어올림 대상 - 함수의 끝에서 변수를 선언해도 런타임에서 변수를 맨 위로 끌어올리기 때문에 선언하기 전에 해당 변수를 사용해도 오류가 발생하지 않음  

{% highlight typescript linenos %}
function scopingExample(hasValue){
   if(hasValue){
      value = 1;
   }else{
      value = 0;
   }
   console.log(value);
   var value;
}
scopingExample(true);
scopingExample(false);
{% endhighlight %}

value 변수는 8행에서 선언되었지만 2행과 5행에서도 변수에 액세스 할 수 있음  
함수가 실행되면 자바스크립트 런타임은 변수 선언을 함수의 맨 위로 올리므로 3행에서 사용 할 수 있음  

### let와 const 키워드

변수에 블록 범위 지정을 제공, 끌어올림을 지원하지 않음  
블록 범위 지정은 변수의 범위가 선언됨 범위로 제한  
일반적으로 중괄호로 정의  
변수가 루프 또는 if 조건 내에서 정의 된 경우 블록 외부에서 사용 할 수 없음  
6행에서 x가 정의되지 않았다는 에러가 발생함  
그러나 4행에서는 10을 정상적으로 출력  

{% highlight typescript linenos %}
function letExample(hasValue){
   if(hasValue){
      let x = 10;
      console.log(x);
   }
   console.log(x);
}
letExample(true);
{% endhighlight %}

올바른 범위에서 변수를 사용하고 있는지 확인하기 때문에 코드오류를 줄일 수 있음  
let 키워드는 끌어올림을 지원하지 않으므로 선언 후에만 액세스 가능  

const 키워드는 let 키워드와 동일한 범위 지정 원칙을 따름  
두 개의 차이점은 const로 선언된 값은 일단 바인딩 되면 변경할 수 없음  

---