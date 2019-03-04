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
{% highlight javascript linenos %}
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
{% highlight javascript linenos %}
var score = [1,2,3,4,5,6];
console.log( score.slice(2,4) ); // [3,4] 반환
score = 10;
console.log( score.slice(2,4) ); // 에러발생
{% endhighlight %}

### 동등비교(Equality comparision)
따옴표로 묶인 숫자는 문자열로 판단  
하지만 if문에서 숫사 상수와 비교하면 자동으로 문자열을 숫자로 변환하여 시도  
비교시 === 을 사용하면 자동변환이 되지 않음
{% highlight javascript linenos %}
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

----

## Typescript 구조대

타입스크립트는 크로스플랫폼 언어, 윈도우/리눅스/맥OS에서 작동
대규모 개발에서의 복잡성을 관리 할 수 있도록 정적 타입 결정, 모듈 및 클래스를 사용한 캡슐화, 사용자 정의 타입, 인터페이스 등과 같은 기능을 제공  

### Typescript의 장점

타임스크립트는 자바스크립트를 모두 포함하고 있고 하위호환이 됨  
String, Number, Boolean 이외에 Typle, Enum, Never 추가 됨  
객체지향 방식으로 고유한 사용자 정의 타입 제작 가능  
타입스크립트 코드는 자바스크립트 코드로 컴파일되므로 모든 브라우져나 플랫폼에서 실행이 가능  
다양한 에디터(비주얼스튜디오, 비주얼스튜디오코드, 서브라임텍스트, 아톰 등)에서 타입스크립트를 지원함  

### Typescript 와 Javascript 비교

{% highlight javascript linenos %}
function getlargestNumber(arr){
	result = 0;
	for (index = 0; index < arr.length; index++) {
		if(result < arr[index]){
			result = arr[index];
		}
	}
	if (result > 0) {
		result = true;
	}else{
		result = false;
	};
	return result;
}
score = [1,2,3,4,5,6];
highestScore = getlargestNumber(score);
console.log(highestScore);
{% endhighlight %}

위의 코드를 실행하면 getlargestNumber에서 true를 반환
이 코드의 잠재적 버그는
- result, index, score, highestScore 변수들을 var 이나 let를 사용해서 선언되지 않았기 때문에 해당 함수내의 지역 변수임
- result에 가장 큰 숫자 값을 저장하고 result에 저장된 값을 기반으로 boolean 값을 다시 할당

이 코드를 타입스크립트에 복사하면 아래 이미지처럼 오류가 발생됨  

![에러이미지]({{ "/assets/images/error1.png" | absolute_url }})

모든 변수 선언에 let 키워드를 추가 하면 첫번째 오류는 수정
8줄은 result는 숫자라고 추론하고 다시 boolean으로 바꿀수 없다고 경고 함
첫줄의 arr 매개변수의 타입을 정의하지 않았기 때문에 오류를 경고함, 이를 any 타입으로 지정

{% highlight typescript linenos %}
function getlargestNumber(arr: number[]){
	let result = 0;
	for (let index = 0; index < arr.length; index++) {
		if(result < arr[index]){
			result = arr[index];
		}
	}
	/* if (result > 0) {
		result = true;
	}else{
		result = false;
	}; */
	return result;
}
let score = [1,2,3,4,5,6];
let highestScore = getlargestNumber(score);
console.log(highestScore);
{% endhighlight %}

만약 score 의 값에 숫자가 아닌 문자열을 넣는다면 에러가 발생됨  
변수 선언 시 반드시 데이터 타입을 정의해야 하는 강타입 언어는 버그를 최소화 하고 생산성을 높여 고품질의 코드 작성이 가능함  

> 타입스크립트는 디자인/컴파일 타임에서 오류가 있어도 코드를 컴파일하고 자바스크립트를 생성함(에러는 컴파일 작동이 멈춰짐)