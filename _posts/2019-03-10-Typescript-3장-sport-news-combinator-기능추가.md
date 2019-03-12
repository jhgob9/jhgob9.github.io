---
title: "[Typescript] 3장 Sport News Combinator 기능추가"
date: 2019-03-10 10:40:00 +0900
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

**lexical scoping(렉시컬 스코핑)**  
- 컴파일러가 어휘 분석 중에 범위를 지정
- 소스 코드가 있는 상태 그대로 작업을 하기 때문에 정적(static) scoping이라고도 함
- 소스 코드를 분석하여 계층 구조의 유효 범위를 설정하고 각 범위마다 변수 테이블을 유지

**dynamic scoping(다이나믹 스코핑)**  
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
Pascal 형태의 대문자로 시작하는 함수를 만들고 new 키워드를 사용해 해당 함수를 호출하는 객체를 만드는 것  
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
[참고사이트](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Operators/this){: target="_blank" }

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
TypeScript는 옵션이라고 명시적으로 선언하지 않으면 파라미터가 반드시 필요함  

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

이름은 같지만 구현이 다른 여러 함수를 정의할 수 있게 해줌  
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
let typeScript = new Book('Sachin Ohri', 'TypeScript by Example', 300);
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
let typeScript = new Book('Sachin Ohri', 'TypeScript by Example', 300);
{% endraw %}
{% endhighlight %}

### 프로퍼티와 메서드

클래스와 인터페이스의 중요한 차이점 중의 하나는 클래스에 메서드의 실제 구현이 포함되어 있는지 여부  

#### 프로퍼티

클래스의 프로퍼티를 정의하는 두가지 방법이 있음  

첫번째는 위의 예제와 같고 도트(.) 구문을 사용하여 프로퍼티의 값을 설정하고 가져올 수 있음
두번째는 사용자 지정 접근자를 사용하여 정의

##### 접근자(accessor)

접근자는 프로퍼티에 어덯게 접근할 수 있는지를 제어해주는 특별한 구문을 가진 getter 및 setter 함수  
다음 예제는 title 프로퍼티에 접근자 get와 set 키워드를 사용하여 정의  
상위 레벨에서는 접근자는 함수일 뿐이지만 둘다 같은 이름을 가지고 있음

{% highlight typescript %}
class Book {
	private _title:string;
	get title():string{
		return this._title;
	}
	set title(value:string){
		if(value != ''){
			this._title = value;
		}
	}
}
let typeScript = new Book();
typeScript.title = 'TypeScript by Example';
{% endhighlight %}

getter는 항상 이름 뒤에 빈 괄호를 포함  
setter는 정확히 하나의 파라미터를 전달해야하며 반환 타입을 지정할 수 없음  
접근자 내부에서 프로퍼티에 필요한 특수한 작업을 할 수 있음  
클래스의 객체는 도트(.) 구문을 사용하여 프로퍼티에 접근  

##### 메서드

메서드는 아주 직관적으로 클래스 안에 정의된 함수  

{% highlight typescript %}
{% raw %}
getFullTitle():string{
	return `${this.title} by ${this.author}`;
}
{% endraw %}
{% endhighlight %}

getFullTitle의 메서드도 동일하게 클래스에서 도트(.) 구문을 사용하여 접근 가능  

#### 정적(static) 프로퍼티

정적 프로퍼티는 앞의 Book 예제처럼 클래스 인스턴스가 아닌 클래스 자체의 프로퍼티  
정적 프로퍼티의 장점은 클래스에 고유한 데이터를 저장할 수 있고 다른 인스턴스에서 변경되지 않는다는 것  

{% highlight typescript %}
{% raw %}
class Book {
	public author: string;
	public title: string;
	public length: number;
	static publisher: string = 'Packt Pub';
	constructor(author: string, title: string, length: number) {
		this.author = author;
		this.title = title;
		this.length = length;
	}
	getFullTitle(): string {
		return `${this.title} by ${this.author}`;
	}
}
let typeScript = new Book('Sachin Ohri', 'TypeScript by Example', 300);
let publisher = Book.publisher;
{% endraw %}
{% endhighlight %}

author, title, length 프로퍼티는 클래스의 모든 인스턴스에서 사용 가능  
publisher 속성은 static 프로퍼티로 static 키워드를 접두사로 붙여 정의  
publisher 속성은 class에서만 유효하며 인스턴스에서는 사용 불가  
publisher 속성을 참조하려면 Book 클래스 자체를 통해 참조  

### 상속

상속은 하위 클래스에 추가 기능을 만들어 기존 클래스를 확장할 수 있게 해줌  
상속의 이점은 코드의 재사용  
부모 클래스에서 공통된 프로퍼티와 메서드를 만들어 놓으면 자식 클래스는 상속받아 사용 가능  

TypeScript에서 자식 클래스를 작성하려면 extends 키워드 뒤에 부모 클래스의 이름을 작성  

![]({{ "/assets/images/extends.png" | absolute_url }})

15행에서 볼 수 있듯이 부모 클래스인 Book를 확장하는 새로운 클래스 TypeScript를 통해 부모 클래스의 프로퍼티에도 접근  

#### Super

Super은 부모 클래스를 참조하는 TypeScript 키워드  
자식 클래스에서 생성자를 정의했다면 반드시 super 키워드를 사용하여 명시적으로 부모 클래스의 생성자를 호출해야 함  

{% highlight typescript %}
{% raw %}
class Book {
	constructor(
		public author: string,
		public title: string,
		public length: number
	) {}
	getFullTitle(): string {
		return `${this.title} by ${this.author}`;
	}
}
class TypeScript extends Book{
	constructor(
		author: string,
		title: string,
		length: number,
		public releaseDate: string
	) {
		super(author, title, length);
	}	
}
{% endraw %}
{% endhighlight %}

상의 클래스의 생성자에 필요한 파라미터를 전달할 수 있도록 super을 호출 해야만 함  

> 부모 생성자가 필요로 하는 파라미터가 있는데 자식 클래스에서 모든 파라미터를 정의하지 않은 경우  
> 자식 클래스 인스턴스 생성 시 적절한 값으로 파라미터를 넘겨야 함  

### 추상(Abstract) 클래스

추상 클래스는 정의한 모든 메서드를 구현할 필요가 없는 특별한 타입의 클래스  
부모 클래스에서 공통적인 동작을 정의하고 하위 클래스에서 차이를 관리 할 수 있게 하는 것  
대규모 객체지향 어플리케이션 작성시 매우 유용  

{% highlight typescript %}
{% raw %}
abstract class Book{
	constructor(
		public author: string,
		public title: string,
		public length: number
	) {}
	abstract getFullTitle():string;
}
class TypeScript extends Book{
	getFullTitle():string{
		return `${this.title} by ${this.author}`;
	}
}
{% endraw %}
{% endhighlight %}

추상 클래스로 정의하려면 클래스 이름 앞에 abstract 키워드를 추가  
클래스의 메서드에도 동일하게 적용 가능  
부모클래스를 확장하여 9번줄 처럼 getFullTitle를 구현한 하위 클래스를 만들 수 있음  

> 추상 클래스에서도 일반 클래스처럼 프로퍼티, 생성자, 메서드와 접근자를 똑같이 구현할 수 있음  

---

## 인터페이스

인터페이스는 TypeScript에만 있는 개념  
클래스에 강제할 수 있는 계약을 제공  
인터페이스를 사용하여 고유한 사용자 정의 타입을 만들고 타입의 올바른 사용 여부를 컴파일 타임에 검사  
인터페이스는 프로퍼티와 메서드의 모음으로 구현은 없음  

### 인터페이스 정의 

{% highlight typescript %}
interface IArticle{
	// 프로퍼티
	author:string;
	title:string;
	discription:string;
	// 선택적 프로퍼티
	url?:string;
	// 메서드
	getFormattedDate():string;
}
{% endhighlight %}

인터페이스는 interface 키워드와 그 뒤에오는 이름으로 정의  
중괄호 안에 위치  
인터페이스는 정의만 있고 구현은 없으므로 getFormattedDate 메서드는 파라미터를 사용하지 않고 문자열을 반환하는 인터페이스 메소드  

### 덕 타이핑(duck typing)

덕 타이핑 이라는 용어는 덕 테스트(duck test)에서 유래  
덕 테스트는 "만역 어떤 새가 오리처럼 걷고, 헤엄치고, 꽥꽥거리는 소리를 낸다면 나는 그 새를 오리라고 부를 것이다." 라는 명제를 통해 대상을 확인하는 과정  
컴퓨터 프로그래밍 분야에서 덕 타이핑은 "어떤 타입이 정의한 것과 동일한 프로퍼티와 메서드를 갇는 객체를 발견하면 해당 타입으로 취급 할 수 있으며 명시적으로 해당 타입으로 표시할 필요가 없다" 는 것을 의미, 타입의 유연성을 제공  

{% highlight typescript linenos %}
interface IArticle{
	// 프로퍼티
	author:string;
	title:string;
	// 메서드
	formatDate():void;
}
class Article{
	author: string;
	title: string;
	// 메서드
	formatDate(): void{
		// 상세구현
	}
}
function news(article: IArticle) {
	// 구현	
}
let espn = new Article;
news(espn);
{% endhighlight %}

20번째 행에서 볼 수 있듯이 명시적으로 Article 클래스가 IArticle 인터페이스 타입이라고 선언하지 않았음에도 Article 클래스의 함수를 news 함수에 전달 가능  

### 인터페이스 확장

인터페이스도 다른 인터페이스로 확장이 가능하며 기존 타입에서 새로운 사용자 정의 타입을 생성하여 재사용성을 높일 수 있음  

{% highlight typescript %}
interface IArticle{
	// 프로퍼티
	author:string;
	title:string;
}
interface IEspn extends IArticle {
	// 프로퍼티
	discription: string;
}
let news: IEspn = {
	author:"ESPN",
	title:"Lastst News",
	discription:"Lastst ESPN News"
}
{% endhighlight %}

### 인터페이스 구현  

클래스를 사용하여 인터페이스르 구현  
TypeScript 에서는 클래스에 implements 키워드를 사용하여 인터페이스 구현  
클래스가 인터페이스를 구현하면 컴파일러는 모든 프로퍼티와 메서드가 클래스에 구현되었는지 여부를 확인  

{% highlight typescript %}
interface IArticle{
	// 프로퍼티
	author:string;
	title:string;
	formatDate():void;
}
class Article implements IArticle{
	author: string;
	title: string;
	formatDate(): void{
		// 상세 구현
	}
}
let espn:IArticle = new Article();
{% endhighlight %}

Article 클래스는 IArticle 인터페이스를 구현  
이 인터페이스는 TypeScript 컴파일러에 Article이 적어도 IArticle에 정의된 모든 프로퍼티와 메서드를 가져야 한다고 지시  
Article 클래스에는 다른 프로퍼티와 메서드도 있을 수 있지만 IArticle에서 정의한 프로퍼티와 메서드는 반드시 있어야 함  

---

## SNC에 기능 추가

![SNC 아키텍쳐]({{ "/assets/images/img9.gif" | absolute_url }})

2장에서는 뉴스컴포넌트와 앱컴포넌트를 개발  
이번에는 인터페이스와 함수를 사용해서 기존 코드를 리팩토링하여 앱을 좀 더 견고하게 만들 것  
헤더와 푸터에 몇가지 컴포넌트를 추가  
Anguar 관련 바인딩과 삽입식 기법도 실습  

### 모델 재구성  

인터페이스를 사용하여 기사 인터페이스를 정의하고 Article 클래스에서 해당 인터페이스를 구현  

#### Article 클래스에 인터페이스 추가

models 폴더에 IArticle.ts 파일 생성 후 아래 코드 추가  

*/module/IArticle.ts*  

{% highlight typescript %}
export interface IArticle {
	author: string;
	title: string;
	description: string;
	url: string;
	urlToImage: string;
	publishedAt: string;
	getFormattedDate(): string;
}
{% endhighlight %}

IArticle 인터페이스에는 Article 클래스에 필요한 모든 속성과 날짜를 변환하는 getFormattedDate 함수가 있음  
interface 키워드 앞에 export 라는 키워드가 사용된 이유  

- 인터페이스는 구현이 아닌 정의만을 제공해야 함으로 이 인터페이스는 클래스에서 구현해야 함  
- 특정 클래스/인터페이스가 다른 곳에서 참조된다면 해당 엔티이를 외부에 노출할 것이라고 알리는 방법이 필요함  
- 이때 클래스/인터페이스 앞에 export 키워드를 붙임 

*/module/Article.ts* 

Article 클래스에 IArticle 인터페이스를 구현  

{% highlight typescript %}
export class Article implements IArticle {
	.....
}
{% endhighlight %}

Article 클래스에 IArticle 인터페이스를 import 

{% highlight typescript %}
import { IArticle } from './IArticle';
{% endhighlight %}

#### 뉴스 모델 수정

뉴스 모델 클래스의 목적은 각 뉴스 채널에 있는 기사 모음을 캡슐화 하는 것  
클래스를 리팩토링하여 IArticle 인터페이스와 생성자를 사용  

*/module/news.ts* 

기존의 프로퍼티를 생성자 함수를 사용하여 세 개의 프로퍼티 값을 설정하는 것으로 수정  

{% highlight typescript %}
constructor(public status: string, public source: string, public sortBy: string) {}
{% endhighlight %}

기사배열의 속성을 접근자를 통해 수정하도록 하는 것  
Article 배열에 대한 get, set 프로퍼티를 정의할 때 예상된 값만 할당하도록 특별한 논리를 지정  

{% highlight typescript linenos %}
import { IArticle } from './IArticle';

export class News {
	private _articles: IArticle[];
	constructor(public status: string, public source: string, public sortBy: string) {}
	get Articles(): IArticle[] {
		return this._articles;
	}
	set Articles(value: IArticle[]) {
		if (value.length > 0) {
			this._articles = value;
		}
	}
}
{% endhighlight %}

1행에서 보듯이 IArticle 인터페이스를 import 하면 IArticle 타입을 참조 및 할당  
6행과 9행에서 private 변수 _articles에 대한 getter과 setter를 정의  

### 코어 컴포넌트 

이전 장에서 애프리케이션의 헤더가 있었지만 뉴스 컴포넌트의 일부 였음  
이는 애플리케이션에 새로운 컴포넌트나 기능을 추가해야 할 때마다 헤더 HTML이 잘 포함되어 있는지 확인해야 하므로 확장성이 좋지 않은 방법  
앵귤러는 한 페이지에 여러 컴포넌트가 함께 결합될 수 있게 함으로서 이런 문제를 해결  

#### 헤더 컴포넌트  

헤어 컴포넌트는 애플리케이션 이미지, 이름, 4개의 뉴스 아울렛에 대한 내비게이션 기능을 가지고 있음  

> ng generate component core/header –spec false 

##### 컴포넌트 파일  

HTML과 일부 라우팅 링크를 제외하고 지금의 헤더파일에는 별다른 로직이 없음  

{% highlight typescript %}
import { Component, OnInit } from '@angular/core';

@Component({
  selector: 'app-header',
  templateUrl: './header.component.html',
  styleUrls: ['./header.component.scss']
})
export class HeaderComponent implements OnInit {
  constructor() { }
  ngOnInit() { }
}
{% endhighlight %}

##### HTML 파일

{% highlight html %}
<nav class="navbar navbar-light" style="background-color:whitesmoke">
       <a class="navbar-brand" href="#" style="padding: 10px">
    <img src="assets/Logo.png" width="35" height="35" alt="">
  </a>
  <div class="container-fluid">
    
    <div class="navbar-header">
      <a class="navbar-brand" href="#">Sports News Combinator</a>
    </div>
    <ul class="nav navbar-nav">
      <li><a routerLink="/nfl" routerLinkActive="active" >NFL</a></li>
          <li><a routerLink="/espn" routerLinkActive="active" >ESPN</a></li>
          <li><a routerLink="/fox" routerLinkActive="active" >FOX</a></li>
          <li><a routerLink="/bbc" routerLinkActive="active" >BBC</a></li>
    </ul>
  </div>
</nav>
{% endhighlight %}

routerLink, routerLinkActive 속성은 Angular에서 라우팅을 할 때 사용  
라우팅은 내비게이션 규칙을 정의하는 방법  
URL을 index.html에서 로드해야 하는 컴포넌트와 매핑  

#### 풋터 컴포넌트

깃 코드 참조  
[깃헙이동](https://github.com/sachinohri/SportsNewsCombinator/tree/master/Chapter%2003/src/app/Core/footer){: target="_blank" }


#### 앱(app) 컴포넌트

모듈에 필요한 모든 종속성과 시작 컴포넌트를 지정하는 시작모듈이 app.module.ts  

{% highlight typescript %}
import { BrowserModule } from '@angular/platform-browser';
import { NgModule } from '@angular/core';

import { AppComponent } from './app.component';
import { NewsComponent } from './dashboard/news/news.component';
import { HeaderComponent } from './core/header/header.component';
import { FooterComponent } from './core/footer/footer.component';

@NgModule({
  declarations: [ // 이 모듈에 속한 컴포넌트 목록
    AppComponent,
    NewsComponent,
    HeaderComponent,
    FooterComponent
  ],
  imports: [
    BrowserModule // 가지고 오려고 하는 외부 모듈
  ],
  providers: [], // 참조하려는 서비스
  bootstrap: [AppComponent] // 부트스크랩 시작 컴포넌트
})
export class AppModule { }
{% endhighlight %}

app.module 파일을 로드하면 bootstrap 컴포넌트를 확인한 다음 AppComponent를 렌더링  
app.component.html 파일에 있는 뉴스 컴포넌트와 함께 헤더, 풋터 컴포넌트를 선언하여 단일 웹페이지를 구성  


### 템플릿과 삽입식(interpolation)

Angular는 사용자 인터페이스에 힘을 실어 줄 수 있는 데이터 바인딩과 지시자를 제공  
데이터 바인딩은 정보 표시와 사용자 액션에 응답하는 기능을 제공  
Angular 지시자를 사용해 if 나 for 문도 사용 가능  

#### 템플릿

앵귤러가 컴포넌트를 로드하면 컴포넌트 메타데이터에서 템플릿의 위치나 css 위치 같은 다양항 속성을 확인  
템플릿을 식별하면 앵귤러가 페이지에서 컴포넌트의 html을 처리하고 렌더링  

- 인라인 템플릿 : html이 몇줄 아되는 경우 컴포넌트 데코레이터에서 쉽게 관리 
- 링크 템플릿 : templateUrl 프로퍼티에 정의

#### 지시자

지시자는 html을 확장 할 수 있는 사용자 정의 html 태그  

{% highlight typescript %}
@Component({
  selector: 'app-news', // 컴포넌트 선택자 이름
  templateUrl: './news.component.html',
  styleUrls: ['./news.component.scss']
})
{% endhighlight %}

app-news를 지시자로 사용한 app.component.html  

{% highlight html %}
<div>
<app-header></app-header>
<app-news></app-news>
<app-footer></app-footer>
</div>
{% endhighlight %}

지시자를 사용하는 첫번재 단계를 컴포넌트의 선택자 이름을 다른 컴포넌트의 html 태그로 사용하는 것  

---

## Angular의 지시자 로드 절차

- AppModule
	- AppComponent
		- NewsComponent
		- HeaderComponent
		- FooterComponent

모든 애플리케이션은 AppModule이라고 불리우는 root 모듈이 있어야 함  
AppModule에 속한 AppComponent는 root 컴포넌트가 됨  

### Angular 내장 지시자

 *ngIf 와 *ngFor 는 기존 템플릿의 구조를 수정하기 때문에 구조 지시자라고 함  

#### *ngIf 지시자

조건문을 사용하여 HTML에 요소를 추가 하거나 제거  

{% highlight html %}
<div>
<table class="table" *ngIf="article && article.length">
	<thead></thead>
	<tbody></tbody>
</table>
</div>
{% endhighlight %}

#### *ngFor 지시자

반복문을 사용하여 HTML에 요소를 추가 하거나 제거  

{% highlight html %}
{% raw %}
<tr *ngFor='let article of latest_news.articles'>
	<td>
		<img [src]='article.urlToImage' [title]='article.title | uppercase' [style.width.px]='50' [style.margin.px]='5'>
	</td>
	<td colspan="3">
		<a href="{{article.url}}" target="_blank">{{article.title}}</a>
		<p>{{article.description}}</p>
	</td>
	<td>{{ article.author | uppercase }}</td>
	<td>{{ article.publishedAt}}</td>
</tr>
{% endraw %}
{% endhighlight %}

### 바인딩

템플릿의 데이터 값이 변경되거나 이벤트가 발생하는 경우 애플리케이션에서 관련된 프로퍼티를 식별하거나 메서드를 호출  

#### 삽입식

컴포넌트와 템플릿 같에 단방향 바인딩을 제공  
단방향 바인딩은 데이터 흐름이 컴포넌트 속성에서 연돤된 템플릿 태그까지만 발생한다는 것을 의미  
템플릿에 변경 사항이 있는 경우 컴포넌트에는 업데이트 되지 않음  

{% highlight html %}
{% raw %}
<h4>{{source}}</h4> <!-- 가장 간단한 삽입 형태 -->
<h4>{{'News Outlet: ' + source}}</h4> <!-- 문자열 연결 작업 -->
<h4 innerText={{source}}></h4> <!-- 삽입식을 사용하여 html 엘리먼트 속성에 값을 채움 -->
<h4>{{getSource()}}</h4> <!-- 컴포넌트 클래스에서 메서드 호출 -->
{% endraw %}
{% endhighlight %}

#### 프로퍼티 바인딩

삽입식의 또 다른 형태  
html 엘리먼트의 기존 프로퍼티를 컴포넌트에 정의된 프로퍼티나 메서드에 바인딩 가능  
아래의 예는 같은 데이터를 바인딩 함  

{% highlight html %}
{% raw %}
<img [src]='img.url'>
<img src={{img.url}}>
{% endraw %}
{% endhighlight %}

#### 양방향 바인딩

사용자가 인터페이스에서 수정한 값을 컴포넌트에 반영하는 경우 사용  
ngModel 지시자를 사용하여 양방향 바인딩 사용  

{% highlight html %}
{% raw %}
<input [(ngModel)]="name">
{% endraw %}
{% endhighlight %}

input의 값이 수정될 때마다 이벤트가 생성되어 컴포넌트에 발생 사실을 알려 줌  
컴포넌트 프로퍼티에 변경이 발생하면 사용자 인터페이스로 전파  













