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

## TypeScript의 클래스

클래스를 사용하여 프로퍼티와 메서드를 관리  
객체지향 언어의 동작과 유사  

### TypeScript의 객체지향 프로그래밍

클래스의 형태로 코드를 모델링하면 직관적이고 재사용 가능한 코드 작성 가능  
캡슐화, 다형성 및 상속과 같은 기능을 사용 가능  
타입스크립트는 클래스와 인터페이스를 구현하여 객체지향적인 방식으로 코드를 작성  

### 클래스의 이해

{% highlight javascript %}
function Name(firstName,lastName){
   this.firstName = firstName;
   this.lastName = lastName;
   this.fullName = function(){
      return this.firstName + ' ' + this.lastName;
   }
}
{% endhighlight %}

fullName 메서드는 Name 함수 안에 캡슐화되어 있음  
함수에 메서드를 추가하는 다른 방법은 prototype 키워드를 사용하는 것  

{% highlight javascript %}
function Name(firstName,lastName){
   this.firstName = firstName;
   this.lastName = lastName;
}
Name.prototype.fullName = function(){
   return this.firstName + ' ' + this.lastName;
}
{% endhighlight %}

클래스가 없어도 문제를 해결할 수 있지만 대부분의 개발자 커뮤니티에서 선호하지 않는 방식  

클래스는 공통적인 동작을 추상화하여 코드 재사용을 가능하게 함  

{% highlight typescript linenos %}
class News{
   public channelNumber: number;
   public newsTitle: string;
   private author: string = 'ESPN';

   formet():string{
      return `${this.channelNumber} : ${this.newsTitle} was written by ${this.author};`
   }
}
let espn = new News();
espn.channelNumber = 1;
espn.newsTitle = 'NFL Today';
console.log( espn.formet() );
{% endhighlight %}

News 클래스에는 3개의 멤버 프로퍼티와 하나의 메서드가 있음  
각 멤버에는 정의된 타입이 있으며 범위를 정의하는 접근 제어자가 있음  
10행에서 new 키워드를 사용하여 클래스의 객체를 만듬  

### 접근 제어자(access modififer)

객체가 생성되면 도트(.) 연산자를 사용하여 클래스의 public 멤버에 엑세스 가능  
espn 객체의 author 프로퍼티는 private으로 정의되어 있음으로 접근 불가  

#### Public

클래스 외부에서 자유롭게 엑세스 가능  
명시적으로 지정하지 않으면 public을 기본 접근 제어자로 지정  

### Private

클래스 외부에서 엑세스 불가, 클래스 내부에서만 유효   

### Protected

상속 클래스에서 엑세스할 수 있다는 점을 제외하고는 private와 유사하게 동작  

{% highlight typescript %}
class base{
   protected id: number;
}
class child extends base{
   name: string;
   detail():string{
      return `$name has id: $this.id`;
   }
}
{% endhighlight %}

child 클래스는 base 클래스를 확장하였으므로, child 클래스의 id 프로퍼티에 엑세스 가능  
child 클래스의 객체를 만들어도 외부에서는 id 프로퍼티에 엑세스 불가  

### Readonly

읽기 전용 접근 제어자, 값이 할당된 후 수정 불가  
변수 선언 시 또는 생성자에서만 할당 가능  

{% highlight typescript linenos %}
class HelloWorld{
   readonly name: string = 'John';

   changeName(){
      name = 'Jane';
   }
}
{% endhighlight %}

5행의 name 프로퍼티는 읽기 전용이므로 값을 할당할 수 없다는 오류가 발생  

## 클래스에서 변환된 JavaScript

타입스크립트는 자바스크립트의 상위집합이라는 점을 기억해야 함  
브라우져는 타입스크립트에서 변환된 자바스크립트만 인식 함  
tsconfig.json 파일에서 변환할 자바스크리브 버전을 선택 가능(ES3,ES5,ES6)

> 아직 ES6의 호환성이 확보되지 않아 구버전의 브라우져 지원을 위해서는 ES5로 지정하는 것이 좋음

{% highlight typescript linenos %}
class News{
   public channelNumber: number;
   public newsTitle: string;
   private author: string = 'ESPN';

   formet():string{
      return `${this.channelNumber} : ${this.newsTitle} was written by ${this.author};`
   }
}
let espn = new News();
espn.channelNumber = 1;
espn.newsTitle = 'NFL Today';
console.log( espn.formet() );
{% endhighlight %}

#### ES6 JavaScript

TypeScript와 비슷하지만 타입과 접근제어자가 없음  

#### ES5 JavaScript

브라우저에서 지원되는 가장 보편적인 자바스크립트 버전  
변환시 클래스를 함수로 변환, 클래스 내부의 메서드는 함수의 프로토타입 형태로 변환, let는 var로 변환  

---

## SNC 개요

Angular와 TypeScript로 제작된 SPA  

---

## Angular - superhero 프레임워크

### SPA

항상 새로운 페이지 전체를 로드 하는 것이 아니라 필요한 내부 컨텐츠만 교체  
서버의 요청 횟수를 줄이고 응답 속도를 높이는 방법  
Angular 외 React, Aurelia, Vue 같은 프레임워크 등이 있음  

### Angular 개념

![앵귤러 개념 설명]({{ "/assets/images/img7.gif" | absolute_url }})

- HTML을 보다 강력하게 함 : HTML에 유연성을 추가하는 구조 지시자를 제공
- 모델 데이터 바인딩 개선 : UI로 데이터 모델을 바인딩하고 변경 내용을 추적하는데 도움이 되는 메카니즘을 제공, 단방향/양방향 바인딩이 가능
- 빠른 로드 시간 : 더 나은 렌더링과 빠른 로드시간을 가짐
- 재사용성 : 모듈형 디자인, 각 기능은 의존성을 관리하는데 도움이 되도록 별도의 모듈로 사용 가능
- 백엔드 시스템과의 쉬운 통합 : 백엔드 서버와 통신하는데 필요한 인터페이스를 제공
- 미래 보장성 : 클래스, 인터페이스, 데코레이터와 같은 자바스크립트와 타입스크립트의 최신 기능을 활용
- 생산성 향상 : 여러 기능들을 쉽게 시작하고 구현할 수 있는 프레임워크를 제공

### Angular- 아키텍쳐

모듈형의 프레임워크  
모든 기능이 별도의 모듈로 관리 됨  
핵심 모듈, 서비스, 파이브, 국제화 모듈이 있음  
재사용과 사용편의성이 보다 높은 모듈형 애플리케이션 제작  

기능을 만들어 애플리케이션에 통합하는 데 도움이 되는 컴포넌트와 서비스로 구성  
특정 기능을 제공하기 위해 여러 컴포넌트를 모듈에 함께 묶어서 모듈로 사용 가능  

![앵귤러 애플리케이션의 구조]({{ "/assets/images/img8.gif" | absolute_url }})

- HTML : 컴포넌트의 UI를 구성하며 템플릿이라 불림, 애플리케이션의 화면을 구성
- 클래스 : TypeScript로 작성되고 뷰에 필요한 모든 프로퍼티와 메서드를 포함
- 프로퍼티 : 뷰에 바인딩된 데이터 멤버로 데이터를 표시하는데 사용
- 메서드 : 버튼 클릭과 같이 뷰에서 수행되는 실행 이벤트

여러 컴포넌트로 모듈을 구성하고 또 다시 여러 모듈을 사용해 애플리케이션을 구성  
이런 방식을 통해 서로 다른 애플리케이션에서 재사용 가능한 컴포넌트를 만들 수 있음  
Angular는 url을 조회할 때 url과 매칭되는 컴포넌트를 로드  

### SNC - 아키텍쳐

개발 전 전체 아키텍쳐를 그려보는 것이 좋음  
컴포넌트와 그 계층을 정의  

![SNC 아키텍쳐]({{ "/assets/images/img9.gif" | absolute_url }})

### SNC - 코드 설정

Angular CLI(Command Line Interface)는 npm 패키지이며 즉시 실행할 수 있는 애플리케이션 제작 가능  
서버 위에서 실행해야 하는 프론트엔드 프레임워크이며 node.js, IIS, Tomcat과 같은 다양한 서버를 지원  

수동 프로젝트 생성을 위해서는 아래와 같은 순서를 진행해야 함  
1. 어플리케이션 폴더를 만듬. 일반적으로 app을 폴더명으로 함
2. 패키지 세부 정보를 입력한 파일과 기타 설정 파일 추가
3. npm을 사용하여 필요한 패키지를 설치
4. 루트 모듈 제작
5. 모듈을 로드할 main.ts 파일 생성
6. index.html 생성
7. 첫번째 컴포넌트를 만들고 app 컴포넌트에서 호출
8. Angular 라우트를 정의하여 Angular가 URL에 대응하여 로드할 컴포넌트를 식별할 수 있게 설정

Angular CLI는 이러한 모든 과정을 자동화 해줌  
Node.js 서버를 내장하고 있음  

> Angular CLI는 Webpack과 Node.js 내장 서버가 있어서 초기 웹 어플리케이션을 만드는데 도움이 됨
> 공식 Angular 팀에 의해 정의된 모범 사례를 따르고 코드를 관리하는데 올바른 폴더 구조를 제공

### Angular CLI 셋업

node와 npm이 기본으로 설치되어 있어야 함  
[node 이동](https://node.org/ko/){: target="_blank" }  

터미널을 창에서 아래 명령어를 입력하여 angular-cli 패키지를 글로벌 범위에 설치  

> npm install -g angular-cli

### 애플리케이션 셋업

터미널에서 아래 명령을 입력

>ng new SportsNewsCombinator

SportsNewsCombinator 폴더가 생성되고 완련 파일들이 설치  
내장 노드 서버를 실행하기 위해서는 아래 코드를 터미널 창에서 실행  

> ng serve

http://localhost:4200/ 를 열어 애플리케이션 확인  

> ng serve -open

-open 옵션으로 실행시키면 브라우져가 바로 열림  

### SNC - 폴더 구조

Angular CLI는 Angular 팀에서 권장하는 폴더 구조를 만듬  

![Angular CLI가 생성한 폴더 구조]({{ "/assets/images/img10.png" | absolute_url }})

- e2e : 애플리케이션의 종단간(end-to-end) 테스트 케이스를 만들고 관리하는데 사용
- node_modules : package.json 에서 정한 패키지들이 설치되는 폴더. npm install 을 통해서 설치되는 파일들
- src : 애플리케이션 관련 파일들. app폴더에 애플리케이션 코드가 assets 폴더에는 아이콘과 이미지 같은 외부 리소스가 있음
- environments : 애플리케이션을 빌드할 때 환경 설정을 제공

---

## 모델 만들기

모델은 어떤 논리 데이터를 나타내는 엔티티  
news와 article을 만들 것   
article 모델은 특정 웹 사이트에서 가져온 기사를 나타냄  
news 모델은 기사를 감싸고 있는 모델로 기사 배열을 가지고 있음  

애플리케이션 초기 바인딩을 보여주기 위한 테이터를 하드코딩  

{% highlight javascript %}
{
   "status": "ok",
   "source": "nfl-news",
   "sortBy": "top",
   "articles":[
      {
         author:"Lakisha Jackson",
            title:"Mike Williams denies report on season-ending surgery",
            description:"Los Angeles Chargers first-round pick Mike Williams is denying reports that he might need season-ending back surgery.",
            url:"http://www.nfl.com/news/story/0ap3000000821316/article/mike-williams-denies-report-on-seasonending-surgery",
            urlToImage:"http://static.nfl.com/static/content/public/photo/2017/07/22/0ap3000000821315_thumbnail_200_150.jpg",
            publishedAt:"2017-07-22T23:21:00Z"
      },
      {
         author:"Jeremy Bergman",
            title:"Tamba Hali, upset with snaps, launches tweetstorm",
            description:"We've got ourselves a Saturday afternoon tweetstorm in late July, courtesy of Chiefs pass rusher Tamba Hali.",
            url:"http://www.nfl.com/news/story/0ap3000000821309/article/tamba-hali-upset-with-snaps-launches-tweetstorm",
            urlToImage:"http://static.nfl.com/static/content/public/photo/2017/07/22/0ap3000000821310_thumbnail_200_150.jpg",
            publishedAt:"2017-07-22T20:30:00Z"
      }
   ]
}
{% endhighlight %}

첫 번째 부분은 상태(status), 소스(source), 정렬기준(sortBy) 같은 웹 서비스 호출의 기본 정보를 제공  
두 번째 부분은 웹사이트에서 반환되는 기사의 배열  

- author
- title
- description
- url
- urlToImage
- publishedAt

src 폴더 아래에 module라는 새 폴더를 추가  
이 폴더에는 애플리케이션의 데이터를 관리하는데 필요한 모든 모델이 포함  

*article.ts 파일 추가*
{% highlight typescript linenos %}
export class Article{
   author: string;
   title: string;
   description: string;
   url: string;
   urlToImage: string;
   publishedAt: Date;
}
{% endhighlight %}

웹 서비스 응답에서 확인한 모든 프로퍼티를 Article 클래스로 만들었음  

*news.ts 파일 추가*
{% highlight typescript linenos %}
import { Article } from './article';
export class News{
   status: string;
   source: string;
   sortBy: string;
   articles: Article[];
}
{% endhighlight %}

Article의 상위 모델이며 기사 배열과 세부 상태 정보를 가짐  
다른 파일을 참조해야 하므로 맨 위에 import를 해줌  

---

## 첫 번째 컴포넌트 - NewsComponent

컴포넌트는 Angular 애플리케이션의 기본 구성 요소  
컴포넌트 간의 결합을 통해 풍부한 사용자 경험을 제공  

Angular 컴포넌트는 네 부분을 구성  
- 템플릿
- TypeScript로 쓰여진 클래스
- Angular에서 클래스에 대해 알려주는 메타데이터
- 애플리케이션의 다른 컴포넌트와 서비스에 대한 참조를 제공하는 import 구문

![컴포넌트구성]({{ "/assets/images/img11.gif" | absolute_url }})

### 템플릿

템플릿은 컴포넌트에 사용자 인터페이스를 제공  
HTML로 작성되며 해당 컴포넌트에 표현된 엘리먼트를 자세히 설명  
인라인 또는 별도의 파일로 정의 가능  
두 가지 방법으로 인라인 템플릿 생성 가능  

- @Component 데코레이션 안에 template 속성을 사용하고 HTML을 정의
    {% highlight typescript %}
    template: '<h1>article.title</h1>'
    {% endhighlight %}

- HTML이 두줄 이상인 경우 ES2015 백틱(숫자1키보드 왼쪽 키)을 사용하여 여러줄로 정의 가능
    {% highlight typescript %}
    template:`
          <li>
             <div>
                article.description
             </div>
          </li>`
    {% endhighlight %}

- 별도의 파일로 정의
    {% highlight typescript %}
    templateUrl: './new.component.html'
    {% endhighlight %}

### 컴포넌트 클래스

타입스크립트로 작성되며 프로퍼티와 메서드를 포함  

- 프로퍼티 : 컴포넌트의 데이터 멤버를 정의. Angular 바인딩 구문을 사용하여 템플릿에 바인딩 됨
- 메서드 : 사용자 인터페이스를 통해 호출되는 이벤트에 대한 인터페이스를 제공

컴포넌트 클래스를 개발하는 모범사례 중 하나는 클래스에 newsComponent와 같은 component 키워드를 붙이는 것  
클래스 앞에 export 키워드를 사용하면 클래스를 애플리케이션의다른 컴포넌트에서 사용 할 수 있음  
아래 코드는 클래스의 예  

{% highlight typescript %}
export class itemComponent{
   item: string;
   itemCount: number;
   constructor(){
      console.log('Constructor called');
   }
   printItemDetail(){
      console.log(`${this.item} has total ${this.itemCount}  copies`);
   }
}
{% endhighlight %}

### 컴포넌트 메타데이터

메타데이터는 데이터에 대한 정보를 의미  
우리가 사용하는 데이터의 세부 정보를 제공할 때 이 세부 정보를 메타데이터라고 함  
Angular에게 클래스를 컴포넌트로 인식 시키려면 해당 클래스가 어떤 것인지 메타데이터를 제공해야 함  
메타데이터는 Angular에게 해당 컴포넌트의 템플릿, HTML의 CSS 스타일, 컴포넌트를 나타내는데 사용되는 selector와 같은 세부 정보를 제공  

메타데이터는 클래스 상단에 데코레이터를 사용하여 정의  
데코레이터는 ES7에서 제안된 개념으로 @ 기호로 데코레이터를 식별할 수 있음  

- template/templateUrl : 컴포넌트의 템플릿을 정의
- selector : 컴포넌트의 선택자 이름. 이 이름을 사용하여 로드할 HTML을 식별
- styleUrls : 컴포넌트의 css 파일 경로를 정의. 인라인 또는 별도의 파일로 정의

다음은 메타테이터 샘플  

{% highlight typescript %}
@Component({
   selector: 'snc-news',
   templateUrl: './news.component.html',
   styleUrls: ['./news.component.css']
})
{% endhighlight %}

### Import

대부분의 경우 개발하려는 컴포넌트는 어느 정도 외부 함수 또는 클래스를 참조 함  
이런 기능을 사용하려면 컴포넌트로 import  
외부 메서드와 프로퍼티에 접근 가능  

import 키워드 문법 샘플

{% highlight typescript %}
import { Component, Oninit } from '@angular/core'
{% endhighlight %}

### newsComponent

newsComponent라는 첫번째 컴포너트를 만듦  
뉴스 소스에서 읽은 최신 뉴스를 표시  
애플리케이션을 시작할 때 로드할 주요 컴포넌트가 됨  

Angular CLI는 애플리케이션용 컴포넌트를 생성하는 명령을 제공  
기본 클래스를 생성할 뿐만 아니라 외부 템플릿도 생성하고 app 모듈 컴포넌트에 대한 참조도 추가  
터미널을 열고 아래 구문을 입력하여 컴포넌트를 생성

> ng generate component dashboard/news --spec false

dsahboard 폴더에 news 폴더에 news 컴포넌트를 만듦  
spec false 옵션은 테스트 케이스에 대한 spec 파일을 생성하지 않도록 지시  

app.module.ts 파일이 업데이트 되며 app 폴더 하위에 있는 모든 컴포넌트를 정의  
Angular는 이 모듈 파일을 사용하여 연관된 컴포넌트를 식별하고 적절히 로드  

### newsComponent 비즈니스 로직

1. 먼저 필요한 모델 news와 Article에 대한 참조를 가져옴
    {% highlight typescript %}
    import { News } from './../../../module/news';
    import { Article } from './../../../module/article';
    {% endhighlight %}
2. newsComponent 클래스 내에 News 모델의 객체 만들기
    {% highlight typescript %}
    latest_news: News = new News();
    {% endhighlight %}
    news 객체를 생성하는 private 메서드를 생성  
    이 메서드는 화면에 표시할 하드 코드된 객체를 생성하는 것  
    {% highlight typescript %}
    private seedNewsData(): News{
        let news: News = new News();
        news.status = "ok";
        news.source = "nfl";
        news.sortBy = "top";
        news.articles = this.seedArcticles();
        return news;
    }
    private seedArcticles():Article[]{
        let articles: Article[] = new Array();
            articles.push({
        });
        ..........
        return articles;
    }
    {% endhighlight %}
    이 private 메서드는 ngOnInit 메서드에서 호출  
    ngOnInit 메서드는 Angular에서 제공하는 라이프 사이클 후크 중 하나  
    라이프 사이클 후크는 컴포넌트 로드 및 언로드 이벤트에 논리를 추가하기 위해 Angular가 노출하는 메서드  
    ngOnInit 메서드는 Angular 코어 모듈에서 노출  
    {% highlight typescript %}
    ngOnInit() {
        this.latest_news = this.seedNewsData();
    }
    {% endhighlight %}
3. 뉴스 데이터를 새 객체에 할당하면 뉴스 프로퍼티를 새 컴포넌트의 HTML 템플릿에 바인딩할 수 있음

### newsComponent template 로직

Angular CLI에서 컴포넌트를 만들면 템플릿용으로 별도의 파일이 생성됨  
이 파일을 사용하여 뉴스 세부 정보에 대한 HTML을 정의하고 컴포넌트에서 정의한 뉴스 속성을 바인딩하여 데이터를 표시  

Angular 구조 지시자 *ngFor를 사용  

{% highlight typescript %}
<li *ngFor='let article of latest_news.articles'>
{% endhighlight %}

Angular가 뉴스 개체의 모든 기사를 반복하고 article 변수에 할당하도록 지시  
도트(.) 구문을 사용하여 article 프로퍼티에 접근  

Angular는 이중 중괄호 구문을 사용하여 클래스의 프로피티 값을 HTML에 바인딩 하는 것이 가능  
{% highlight %}
'''<p>
	{{article.description}}
</p>'''
{% endhighlight %}

---

## SNC - 코드 실행

ng serve 를 실행하여 브라우져에서 확인  
http://localhost:4200