---
title: "[Typescript] 3장 Sport News Combinator 기능추가"
date: 2019-03-08 10:40:00 +0900
categories: typescript
---

# 3장 Sport News Combinator 기능추가

이번 장에서 다룰 내용  

- Typescript의 함수 : 타입을 사용하여 TypeScript 함수 규칙을 유연하게 설정
- 클래스와 인터페이스의 대한 깊이 있는 고찰 : 생성자와 스코핑 및 멤버 선언과 같은 클래스의 기능 살펴보기, 인터페이스를 사용하면 보다 객체지향적인 코드 작성이 가능함
- SNC 기능추가 : 헤더와 풋터를 위해 몇가지 다른 컴포넌트를 추가하여 기본 애플리케이션을 개선
- Angular 템플릿과 모듈 : Angular의 템플릿과 데이터 바인딩이 어떻게 동작하는지 살펴보기, 애플리케이션에서 모듈을 정의하는 방법 살펴보기

---

## 함수

TypeScript 함수는 Javascript 함수에 더 이해하기 쉽고 오류가 없는 코드를 작성 할 수 있도록 몇가지 기능을 추가  

{% highlight typescript %}
function printFullName(firstName:string,lastName:string):string{
	return firstName + ' ' + lastName;
}
{% endhighlight %}

TypeScript에서는 주로 클래스 내부의 메서드를 작성하기 위해 함수를 사용함  
클래스 내부에서 사용하는 경우에는 function 키워드가 필요하지 않음  

### 함수의 타입

TypeScript 함수는 파타리터와 반환 값을 정의하기 위해 타입을 사용  
함수를 호출할 때 따라야 하는 규약을 만들 수 있음  
위의 예제에서 printFullName 이라는 함수는 firstName과 lastName 이라는 두개의 문자열 파라미터가 있음  

컴파일러는 전달된 값의 타입 일치 여부를 확인  
반환 값의 타입을 정의할 수도 있음  

#### 화살표 함수(Arrow Function)

뚱뚱한(fat) 화살표 또는 람다(lambda) 함수라도고 불리움  
es6에 새로 도입되었으며 간단한 익명 함수를 작성하게 해줌  
위의 코드를 화살표 함수로 바꿔보면 아래와 같음  

{% highlight typescript %}
(firstName:string,lastName:string) => firstName + ' ' + lastName;
{% endhighlight %}

=> 를 기준으로 왼쪽에는 함수의 파라미터, 오른쪽에는 함수 본문  
아래는 화살표 함수에서 파라미터를 관리 다양한 방법  

- 빈 파라미터 : 파라미터가 없을 경우 빈 괄호를 사용
{% highlight typescript %}
   () => console.log('Hello World!!');
{% endhighlight %}
- 단일 파라미터 : 전달할 파라미터가 하나일 경우 괄호로 묶을 필요가 없음
{% highlight typescript %}
   id => console.log(id);
{% endhighlight %}
- 다중 파라미터 : 파라미터가 여러개 있는 경우 파라미터를 괄호로 묶어야 함 
{% highlight typescript %}
   (firstName:string,lastName:string) => firstName + ' ' + lastName;
{% endhighlight %}
- 다중 행 함수 본문 : 함수 본문이 여러 줄인 경우에는 중괄호로 묶음
{% highlight typescript %}
   (firstName,lastName,age) => {
      console.log(firstName + ' ' + lastName);
      return age+1;
   }
{% endhighlight %}

#### Javascript의 스코프(scope)
scope는 변수의 유효범위를 뜻함  
변수가 유효한 영역을 scope block이라고 부르며 동일한 변수명이라도 스코프 블록을 벗어난 경우 다른 것을 가르키거나 아무것도 가르키지 않을 수 있음  
범위를 지정하는 작업은 scoping 이라고 함  

**lexical scoping**  
- 컴파일러가 어휘 분석 중에 범위를 지정
- 소스 코드가 있는 상태 그대로 작업을 하기 때문에 정적(static) scoping이라고도 함
- 소스 코드를 분석하여 계층 구조의 유효 범위를 설정하고 각 범위마다 변수 테이블을 유지

**dynamic scoping**  
- 실행 중에 범위를 지정
- 하나의 global 변수 테이블을 가지고 있고 동일한 변수명이 있다면 stack 형태로 push

{% highlight bash %}
x=1 // bash에서 변수 선언은 띄어쓰기 없음
function g() { echo $x; x=2 }
function f() { local x=3; g; } // 지역변수를 선언하는 local 키워드 가정
f // 함수 f 호출
each $x
{% endhighlight %}
- lexical scoping : local x=3; 이 g에 아무 영향을 주지 않기 때문에 1과 2개 출력됨  
- dynamic scoping : local x=3;에 의해 3의 값을 가진 x가 x라는 global stack에 push 될 것이므로 3이 출력되고, local을 선언한 함수가 return될 때 다시 pop되기 때문에 마지막 each문에서는 처음 선언한 1이 출력 될 것

- dynamic scoping의 장점 : 하위 모듈에서도 자유롭게 변수의 의미를 재정의 가능
- dynamic scoping의 단점 : 동일한 모듈이 호출 당시의 상황에 따라 다르게 동작, 오류를 찾기가 어려움

그래서 현대의 프로그래밍 언어는 대부분 코드만을 보고 분석과 이해가 쉬운 lexical scoping을 사용  

**Javascript**  
함수 또는 블록 레벨에서 유효범위를 결정하는 lexical scoping을 따르지만 this는 dynamic scoping 방식을 따르기 때문에 주의해야 함

#### Javascript의 this

Javascript의 this는 함수의 호출 방식에 따라 값이 달라짐

- 함수에서 호출
this는 상위 스코프가 따로 없기 때문에 전역개체를 가르키는데 브라우져에서는 window, 다만 ECMAScript 5.1에서 엄격모드(use strict)를 사용하면 this는 undefined
- 메서드에서 호출
객체 안에서 정의한 함수를 호출하는 것, 메서드 호출을 사용하려면 반드시 속성 접근자를 사용해야 함
obj.myFunc(); 형태로 호출하면 메서드가 실행
var someFunc = obj.myFunc; someFunc(); 형태로 호출하거나 setTimeout(obj.myFunc,1000) 형태로 호출하면 메서드가 객체로 부터 분리되어 함수 형태로 호출
myFunc 메서드 안에 innerFunc를 정의한 경우 역시 innerFunc는 메서드가 아니라 함수가 됨
- 생성자에서 호출
Pascal 형태의 대문자로 시작하는 함수를 만들고 new 키워드를 사용해 해당 함수르 호출하는 객체를 만드는 것
new로 만든 객체에는 this가 바인딩 됨
생성자 함수를 만들때 함수명이 대문자일 필요는 없지만 생성자를 호출하려면 반드시 new 키워드를 사용
{% highlight typescript %}
   function ConstructorFunc(msg){
      this.msg = msg;
   }
   ConstructorFunc.prototype.hello = function(){
      console.log(this.msg) // 생성자 호출 패턴에 의해 this를 사용할 수 있다.
   }
   var obj = new ConstructorFunc('Hello World!!')
   obj.hello() // Hello World!!
{% endhighlight %}
- apply에서 호출
apply 함수를 사용해 호출하여 함수 내부에서 사용할 this를 지정하는 것
apply 함수의 첫번째 인자로 null을 넘기면 전역 객체인 window가 this가 됨
- bind에서 호출
ES5에서 소개된 것으로 함수가 어떻게 호출되었는지 상관없이 this값을 직접 정함  
[참고사이트](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Operators/this){: target="_blank" }

#### 화살표 함수에서의 this 키워드

전통적인 JavaScript에서 this 키워드의 스코프는 함수가 실행될 때를 기준으로 함  

{% highlight typescript linenos %}
function Book(title) {
	this.title = title;
	this.printTitle = function() {
		this.title = this.title + 'by Sachin Ohri';
		console.log(this.title)
	}
}
var typeScript = new Book('TypeScript By Example');
setTimeout(typeScript.printTitle, 1000); // 함수호출
setTimeout(function () { typeScript.printTitle()},2000) // 메서드 호출
{% endhighlight %}

위의 함수가 실행되면 다음과 같은 결과가 출력 됨

> undefinedby Sachin Ohri  
> TypeScript By Exampleby Sachin Ohri  

9번 라인에서의 printTitle은 객체로부터 분리된 메서드 이기 때문에 함수 호출이 됨  
이때 this는 window를 가르킴, 하지만 window의 title은 설정된 적이 없으므로 undefined가 됨  

10번 라인의 printTitle은 메서드 호출이므로 this는 메서드의 상위 스코프인 typeScript 객체를 가르킴  
title은 앞의 생성자 호출에 의해 설정된 "TypeScript By Example" 값을 더해 "TypeScript By Exampleby Sachin Ohri"가 됨  

화살표 함수는 위의 예처럼 실행 시에 범위를 지정하는 것이 아니라 선언 시에 this를 할당 함으로서 this의 스코핑 문제를 해결  
아래는 화살표 함수를 사용하는 예인데 모두 "TypeScript By Exampleby Sachin Ohri"를 반환함  

{% highlight typescript %}
function Book(title){
	this.title = title;
	this.printTile = ()=> console.log(this.title + 'by Sachin Ohri');
}
var typeScript = new Book('TypeScript By Example');
setTimeout(typeScript.printTitle, 1000); // 함수호출
setTimeout(function () { typeScript.printTitle() }, 2000) // 메서드 호출
{% endhighlight %}

> 실행 당시의 컨텍스트 위에서 this 키워드를 사용해야 하는 경우 화살표 함수를 사용하면 안됨

### 선택적(Optional)/기본(default) 파라미터

JavaScript는 함수의 모든 파라미터가 선택적임  
TypeScript는 옵션이라고 명시적으로 선억하지 않으면 파라미터가 반드시 필요함  

다음 예제에서 length는 선택적 파라미터  

{% highlight typescript %}
function Book(title: string, length?:number){}
{% endhighlight %}

파라미터 뒤에 ?를 추가  
선택적 파라미터는 모든 필수 파라미터가 정의된 후 마지막에만 허용  

기본 파라미터를 사용하면 선택적 파라미터 또는 필수 파라미터에 기본 값을 지정 가능  

{% highlight typescript %}
function Book(title: string, length:number=300){}
{% endhighlight %}

length 파마리터를 작성하지 않고 호출하면 함수는 300으로 가정  
기본 파라미터가 필수 파라미터의 마지막에 있을 경우 TypeScript는 해당 파라미터를 선택적 파라미터로 간주함  

### 나머지(Rest) 파라미터

함수에 가변 개수의 파라미터를 전달하고 배열로 사용 가능  
파라미터 이름 앞에 생략부호(세개의 점)를 붙임으로서 정의  
함수를 호출할 때 여러 개의 파라미터를 전달할 수 있으며 함수는 그것들을 배열 형식으로 받아들임  

{% highlight typescript %}
function School(name:string,...id:number[]) {}
let harvard = new School('Havard', 1,2,3,4,5);
{% endhighlight %}

두 번째 이후 전달된 모든 파라미터는 number 배열에 합쳐짐  

### 함수 오버로딩

이름은 같지만 구현이 다른 여러 함수르 정의할 수 있게 해줌  
TypeScript도 함수 오버로드를 지원하지만 구문과 구현이 다소 어색함  

동일한 이름의 여러 함수를 정의하되 하나만 구현  
구현된 함수는 모든 형태의 다른 함수 정의를 호출된 형태에 따라 처리함  
다음 예제는 두개의 함수 오버로드와 모든 오버로드를 처리하는 하나의 구현을 정의  

{% highlight typescript %}
function getCustomer(name:string):string;
function getCustomer(id:number):string;
function getCustomer(property:any):string{
	if (typeof property == 'string'){
		// 고객 이름에 따라 고객 정보 반환
	} else if (typeof property == 'number'){
		// 고객 ID에 따라 고객 정보 반환
	}
	return "customer"
}
{% endhighlight %}

getCustomer 함수는 두 가지 오버로드가 있음  
첫번째는 문자열 입력 파라미터, 두번째는 숫자열 입력 파라미터, 세번째는 입력 파라미터로 any를 취하는 함수  
세번째 함수는 전달된 파라미터를 typeof 기준으로 구분하겨 적절한 처리를 구현  
아래는 위 코드를 Javascript로 변환한 코드  

{% highlight javascript %}
function getCustomer(property){
	if (typeof property == 'string'){
		// 고객 이름에 따라 고객 정보 반환
	} else if (typeof property == 'number'){
		// 고객 ID에 따라 고객 정보 반환
	}
	return "customer"
}
{% endhighlight %}

---

## 타입스크립트의 클래스

객체지향 프로그래밍을 위해 다음과 같은 기능을 제공  

- 상속
- 다형성
- 캡슐화
- 추상화

### 클래스의 정의

클래스는 관련(related)된 프로퍼티와 메서드로 구성된 논리적 컨테이너  
독립적인 구조를 가진 모든 항목들을 논리적으로 묶어서 코드를 조직화하는데 도움을 줌  

객체를 만드는데 사용되는 프로퍼티와 메서드를 포함하여 템플릿 형태로 구성됨  
각 객체는 동일한 프로퍼티와 메서드를 가짐  
클래스의 가장 중요한 기능은 애플리케이션 전체에서 사용 가능하고 재사용 가능한 코드를 캡슐화해서 제공 하는 것  
상속 및 추상 클래스를 지원하고 동시에 생성자나 접근 제어자 또한 지원  

{% highlight typescript %}
{% raw %}
class Book{
	public author:string;
	public title:string;
	public length:number;
	setFullTitle():string{
		return `${this.title} by ${this.author}`;
	}
}
let typeScript = new Book();
typeScript.title = 'TypeScript by Example';
typeScript.author = 'Sachin Ohri';
typeScript.length = 300;
{% endraw %}
{% endhighlight %}

클래스는 class 키워드와 그 뒤에 클래스 이름으로 정의  
그 다음 중괄호 안에 클래스와 관련된 프로퍼티와 메서드가 있음  
클래스의 객체를 생성하려면 new 키워드와 객체의 이름을 사용  

> 모든 public 프로퍼티와 메서드에 도트(.) 연산자를 사용하여 객체에 액세스 가능

### 생성자

클래스의 새 인트턴스를 초기화 하는데 사용  
객체 생성 시 클래스의 프로퍼티에 초기 값을 전달해야한다면 생성자를 사용  
생성자의 이름은 항상 constructor이어야 함  

{% highlight typescript %}
{% raw %}
class Book{
	public author:string;
	public title:string;
	public length:number;
	constructor(author:string, title:string, length:number){
		this.author = author;
		this.title = title;
		this.length = length;
	}
	setFullTitle():string{
		return `${this.title} by ${this.author}`;
	}
}
let typeScript = new Book('TypeScript by Example', 'Sachin Ohri', 300);
{% endraw %}
{% endhighlight %}

클래스의 새 인스턴트를 만들 때 생성자가 예상하는 파라미터를 전달  
생성자는 직접 호출 하는 것이 아닌 클래스 이름과 new 키워드를 사용해서 호출  
생성자 함수를 실행하고 클래스의 새 인스턴스를 반환  

클래스당 하나의 생성자만 만들 수 있음  
선택적 파라미터를 이용하면 생성자가 여러 형식을 가진 것처럼 작동 가능  

{% highlight typescript %}
constructor(author:string, title:string, length?:number){}
{% endhighlight %}

#### 생성자 파라미터

생성자 프로퍼티를 사용하면 간단한 방법으로 클래스에서 프로퍼티를 정의하고 할당할 수 있음  
위의 코드를 생성자 파리미터를 생성하면 아래 코드와 같다  

{% highlight typescript %}
{% raw %}
class Book{
	constructor(
		public author:string,
		public title: string,
		public length:number
	){
		this.author = author;
		this.title = title;
		this.length = length;
	}
	setFullTitle():string{
		return `${this.title} by ${this.author}`;
	}
}
let typeScript = new Book('TypeScript by Example', 'Sachin Ohri', 300);
{% endraw %}
{% endhighlight %}

### 프로퍼티와 메서드

클래스와 인터페이스의 중요한 차이점 중의 하나는 클래스에 메서드의 실제 구현이 포함되어 있는지 여부  

#### 프로퍼티

클래스의 프로퍼티를 정의하는 두가지 방법이 있음  

첫번째는 위의 예제와 같고 도트(.) 구문을 사용하여 프로퍼티의 값을 설정하고 가져올 수 있음
두번째는 사용자 지정 접근자를 사용하여 정의
