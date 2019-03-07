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

## TypeScript의 타입

자바스크립트에서는 명시적 변수 타입 지정이라는 개념이 없어서 런타임 시 오류의 주요 원인이 됨  
자바스크립트는 하나의 변수에 어떤 타입을 할당 했더라도 동일한 변수명으로 다른 타입을 설정할 수 있음  
타입스크립트는 타입 어노베이션을 사용하여 각 변수, 함수, 객체 또는 클래스에 타임을 할당할 수 있음  
타입스크립트는 정적 또는 동적 타입 검사를 함께 수행  
타입은 컴파일러가 해당 변수가 따라야 하는 규칙을 제공  
타입스크립트는 선택적인 정적 타임 언어이므로 각 변수나 함수에 타입을 반드시 할당 할 필요는 없음  
타입스크립트는 변수 또는 코드 흐름에 맞추어 할당 된 값을 기반으로 변수의 타입을 추론  
any 키워드를 지정하여 변수의 타이블 명시적으로 거부하는 옵션을 제공, 타입검사를 하지 않음  
any를 사용하면 기존 자바스크립트를 타입스크립트로 마이그레이션이 용이함  

> 타입스크립트는 컴파일 타임에 타입오류가 있어도 자바스크립트를 생성하므로 점진적으로 자바스크립트 코드를 업데이트 할 수 있음

### 타입 어노테이션

콜론이 앞에 오는 타입 정의 구문을 제공  
타입 어노베이션은 변수 식별자 뒤에 옴  
기본 타입 또는 배열이거나 클래스 및 인터페이스를 사용하는 복합 타입일 수 있음  

{% highlight typescript linenos %}
let num: number = 42;
function example(name: string, age: number): number{
   return 42;
}
{% endhighlight %}

1행에서 num 변수를 정의할 때 값을 지정하는 것은 선택 사항  
example 에서 입력 파라미터의 타입과 반환 값의 타입을 정의할 수 있음  
이는 모든 함수 호출이 올바른 서명 계약을 유지하고 있는지 확인하는데 도움이 됨  


### 원시타입

#### 숫자형(Number)

64비트 부동 소수점 값을 담을 수도 있음  
10진수 및 16진수를 포함한 모든 숫자  

{% highlight typescript %}
let num: number = 42;
let decimal = 42.0;
{% endhighlight %}

#### 문자열(String)

UTF-16 형식의 텍스트 데이터  
단일(') 또는 이중(") 따옴표를 사용  
템플릿 문자열이라 부르는 여러 줄에 걸쳐있는 문자열을 만들 수 있음  

{% highlight typescript %}
let firstName: string = 'John';
let templateHTML: string = '
<h1>Title</h1>'
{% endhighlight %}

templateHTML은 템플릿 문자열을 사용하는 예  
동적 표현식도 사용 가능  
아래의 코드에서 result 변수는 'Top 10 news feed form ESPN'이 됨  

{% highlight typescript %}
let news: string = "ESPN";
let count: number = 10;
let result: string = 'Top $count news feed form $news.'
console.log(result);
{% endhighlight %}

위의 코드를 자바스크립트로 변환하면 아래의 코드로 변환  

{% highlight javascript %}
var news = "ESPN";
var count = 10;
var result = 'Top '+ count +' news feed form '+ news +'.'
console.log(result);
{% endhighlight %}

#### 논리형(Boolean)

true 또는 false 값을 지정 가능  

{% highlight typescript %}
let hasValue: boolean = false;
{% endhighlight %}


### 배열

{% highlight typescript %}
let scores: number[] = [10,20,30,40];
{% endhighlight %}

배열은 0 인덱스를 기바으로 액세스  
첫 번째 요소는 0번째 인덱스에 있음  

{% highlight typescript %}
let scores: number[] = [10,20,30,40];
console.log(scores[0]); // 출력값은 10
{% endhighlight %}

### 튜플형(Tuple)

고급 타입의 배열  
배열에서 동일한 타입이 아닌 요소를 사용할 수 있음  
다음 코드와 같이 string 타입의 첫번째 요소와 number 타입의 두번째 요소를 가짐  

{% highlight typescript %}
let details:[string, number];
details = ['John', 42];
{% endhighlight %}

단일 구조에서 다양한 데이터 타입을 관리함으로서 좀 더 유연하게 코드를 작성 가능  

### Any

특수한 타입으로 해당 변수에 대한 타입 검사를 거부할 수 있음  
이전 자바스크립트 코드를 타입스크립트 코드로 마이그레이션할 때 매우 유용함  

{% highlight typescript %}
let item: any;
item = 10;
item = 'John';
item = [10,20,30];
{% endhighlight %}

any 타입으로 지정했기 때문에 오류나 애러가 발생하지 않음  

### Void

타입이 없는 시나리오를 나타내는데 사용   
반환하지 않는 함수의 경우 유용  

{% highlight typescript %}
function doSomething(num: number):void{
   console.log(num)
}
{% endhighlight %}

### Null과 undefined

모든 변수에 지정할 수 있는 특수 타입  

{% highlight typescript %}
let value = null;
value = 42;
{% endhighlight %}

value 변수는 any 타입  
null은 모든 타입의 하위집합이므로 컴파일러는 변수에 any를 할당  

### 유니온 타입(Union types)

대부분의 경우 특정 변수가 가질 수 있는 데이터 타입을 알기 때문에 해당 변수의 타입을 정할 수 있음  
컴파일 타입 검사를 돕고 변수를 잘못 사용하지 않도록 함  
변수가 특정 타입에만 국한되지 않고 여러 타입의 값을 가질 수 있는 경우가 있음  
여러 타입의 변수를 정의할 수 있음  

{% highlight typescript %}
let data: string | number;
data = 10;
data = 'John';
{% endhighlight %}

정의된 스트링 타입과 숫자 타입을 모두 사용 가능  
이외의 타입을 지정하면 경고를 나타냄  

### 타입 추론(Type Inference)

타입을 변수에 명시적으로 지정하는 것은 선택사항  
타입이 선언되지 않은 경우 타입을 추론 함  

{% highlight typescript %}
let firstName = 'John';
firstName = 10;
{% endhighlight %}

타입스크립트 컴파일러는 firstName 변수가 문자열이고 문자열에 숫자형을 할당할 수 없다는 오류를 알려 줌  
타입스크립트는 firstName 변수의 타입을 식별해서 타입을 추론  
아래와 같은 경우에 타입스크립트는 any로 타입을 유추  

{% highlight typescript %}
let lastName;
lastName = 10;
lastName = 'JJ';
{% endhighlight %}

함수의 리턴 타입에서도 타임 추론이 작동함  
타입스크립트는 코드를 보고 코드 흐름 경로에 따라 가장 적합한 타입을 결정  

{% highlight typescript %}
function doSomething(num: number){
   return "name";
}
{% endhighlight %}

이런 경우 반환 타입을 문자열로 추론  
타입스크립트는 코드 흐름을 기반으로 변수 및 함수 타입을 유추  

### 타입 체크

타입 체크의 예외는 any  

{% highlight typescript %}
let age:number;
age = 10;
age = '42'; // 컴파일 에러: string는 number에 할당할 수 없음
{% endhighlight %}

타입스크립트는 자바스크립트와 다르게 타입을 강제 변환하지 않음  
배열 및 클래스와 같은 사용자 정의 타입에 대해서도 유형 검사를 수행  

---