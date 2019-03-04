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

---

## Typescript의 장점

타임스크립트는 자바스크립트를 모두 포함하고 있고 하위호환이 됨  
String, Number, Boolean 이외에 Typle, Enum, Never 추가 됨  
객체지향 방식으로 고유한 사용자 정의 타입 제작 가능  
타입스크립트 코드는 자바스크립트 코드로 컴파일되므로 모든 브라우져나 플랫폼에서 실행이 가능  
다양한 에디터(비주얼스튜디오, 비주얼스튜디오코드, 서브라임텍스트, 아톰 등)에서 타입스크립트를 지원함  

---

## Typescript 와 Javascript 비교

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

---

## Javascript의 대안

기존 자바스크립트의 결함과 한계를 극복하기 위해 최종적을 자바스크립트로 컴파일되는 다양한 프로그래밍 언어가 있음  
이러한 언어들은 캡슐화, 스코핑, 타입유추 와 같은 과제를 해결하기 위해 노력 중  

**CoffeeScript** : 파이썬과 매우 유사, 간결한 구문과 람다식에 의존  
CoffeeScript와 Dart는 자바스크립트로 변환되는 새로운 언어를 도입해 캡슐화 하려 함  
TypeScript는 JavaScript를 기반으로 했기 때문에 새롭게 진입하기 쉽지만 CoffeeScript와 Dart는 그렇지 않음  

**Flow** : 페이스북에서 자바스크립트에 타입 검사를 제공하기 위해 도입  
타입 검사와 트랜스파일링(Babel) 정도의 도구만 제공  
TypeScript는 좀 더 포괄적인 도구 모음을 제공 함  

**ES6(ES2015)** : 순수 JavaScript  
모든 브라우져가 지원 하는 것은 아니기에 Babel과 같은 트랜스파일링 프레임워크를 이용해서 ES5로 전환해야 함  
TypeScript는 ES6 기능을 대부분 사용 할 수 있고 타입과 같은 일부 기능을 추가하여 사용 가능  

---

## TypeScript 기능

주요 컴포넌트 5개  

1. 코어 TypeScript 컴파일러 : 기능을 제공하고 자바스크립트로 컴파일 하는 모든 저수준의 작업을 관리
2. 언어 서비스 : 인텔리센스, 디버깅, 문장 자동완성. 기호를 사용한 리맵핑, 포맷팅 같은 에디터에서 필요한 기능을 제공. 자동으로 빌드를 시작하는 watch 옵션 사용도 가능
3. 단독 컴파일러 : TSC를 사용하여 .ts 파일을 컴파일하면 .js 파일을 생성함
4. TypeScript 기능 : 사용가능한 TypeScript 기능들, 언어서비스와 코어 컴파일러의 추상적인 개념
5. 에디터 플러그인 : 다양한 에디터에서 자동빌드, 인텔리센스, 에러탐지와 같은 기능들을 제공

---

## 주요기능

### 데이터타입

데이터의 타입을 정의하면 생성성 및 코드 품질이 향상되고 런타임에 오류가 감소하는 것으로 입증 됨  
타입을 추론하는 기능을 제공  
변수에 여러타입의 할당 할 수 있으면 교차타입도 가능함  

### 제어 흐름 분석(Control flow analysis)

코드흐름을 기반으로 타입 분석을 제공  
여러 타입의 변수가 있고 처리 로직이 변한ㄴ 경우 논리적으로 오류를 줄이는데 도음이 됨

{% highlight typescript linenos %}
function projectStatus(x:string|number){
	if(typeof x === 'string'){ // x는 string 이거나 number 타입
		x = 10;
	}
	return x; // number 타입을 변환
}
console.log(projectStatus('10'));
{% endhighlight %}

### 캡슐화

객체지향 프로그램의 핵심 중 하나  
필요한 멤버를 외부에 노출, 구현 세부사항은 숨김  
캡슐화를 통해 유연하게 시스템의 구형 사항을 변경 할 수 있음  
클래스, 모듈 및 접근 제어자의 개념을 통해 캡슐화 재공  
**클래스** : 공통기능을 포함하는 컨테이너, private/public/protected와 같은 접근 제어자를 사용하여 필수 필드만 노출  
**모듈** : 클래스의 컨테이너이며 특정 기능을 제공하는 클래스 집하에 또 다른 수준의 캡슐화를 제공   

{% highlight typescript linenos %}
class News{
	public channelNumber : number;
	public newsTitle : string;
	private url : string;
}
let espn = new News();
espn.channelNumber = 1;
espn.newsTitle = 'NFL Today';
espn.url = 'http://go.espn.com'; // 'url' 속성은 private이며 'News' 클래스 내에서만 액세스할 수 있습니다.
{% endhighlight %}

### 상속

부모 클래스의 기능을 확장  
자식 클래스에서 extends 키워드를 사용해 부모 클래스를 참조  
자식 클래스는 부모 클래스의 기능을 확장하여 부모 클래스의 모든 public 멤버에 접근 가능  

{% highlight typescript linenos %}
class Editor{
	constructor(
		public name : string,
		public isTypeScriptCompatible : Boolean
	){}
	details(){
		console.log( 'Editor : ' + this.name + ', TypeScript installed : ' + this.isTypeScriptCompatible );
	}
}

class VisualStudioCode extends Editor{
	constructor(
		name : string,
		isTypeScriptCompatible : Boolean,
		public OSType : string
	){
		super(name,isTypeScriptCompatible);
		this.OSType = OSType;
	}
}

let VS = new VisualStudioCode('VSCode',true,'all');
VS.details();
{% endhighlight %}

VisualStudioCode 클래스는 Editor 클래스를 확장  
VisualStudioCode의 인스턴트는 details 메서드를 상속  

### 인터페이스

인터페이스의 주 목적은 코드의 일관성을 높이는 것  
정의된 계약이며 인터페이스를 구현하는 모든 클래스는 해당 인터페이스의 모든 프로퍼티를 구현  
인터페이스는 순수한 TypeScript 개념 ECMAScript의 일부가 아님  
자바스크립트로 변환 될 때 인터페이스는 변환되지 않음  

{% highlight typescript linenos %}
interface Planet{
	name: string,
	weather: string
}
class Earth implements Planet{
	name: string;
	weather: string;
}
let planet: Planet = new Earth();
{% endhighlight %}

### 쉐이프

객체의 프로퍼티가 동일하다면 다른 식별자에도 객체를 할당 할 수 있음  
두개의 객체가 동일한 프로퍼티를 가지고 있으면 동일한 타입으로 간주 됨  

{% highlight typescript linenos %}
interface Planet{
	name: string,
	weather: string
}
class Earth implements Planet{
	name: string;
	weather: string;
}
let planet: Planet;

class Pluto{
	name: string,
	weather: string
}

planet =  new Pluto();
{% endhighlight %}

Pluto는 Planet을 구현하지 않았지만 Pluto의 인스턴트를 planet에 할당 함   
Pluto 클래스의 프로퍼티와 타입이 Planet 인터페이스와 동일하기 때문   

### 데코레이터

현재로서는 타입스크립트에 특화된 개념(정식이 될 가능성도 있음)  
Gang of Four의 구조적 디자인 패턴 중 하나로 객체에 추가적인 책임을 부여  
실험적인 기능이라서 tsconfig.js 에서 활성화 해야 함  
클래스, 프로퍼티, 메서드 및 접근자에 데코레이터 사용 가능하며 일반적으로 앵귤러에서 사용 됨  

---

## TypeScript 문법과 키워드

클래스, 함수 및 변수를 포함하는 모듈로 구성  
모듈에 포함된 클래스나 함수들은 export를 사용해 클래스 또는 함수를 외부 모듈에 노출  
import를 사용해서 접근 가능  
각 클래스에는 변수와 특정 기능을 제공하는 함수가 포함  

![타입스크립트 구조]({{ "/assets/images/img.gif" | absolute_url }})

| 키워드  | 설명  |
|---|---|
| 기본타입  | boolean, number, string, array, any, never, null, undefined  |
| class  | 프로퍼티와 함수를 위한 컨테이너, 자바스크립트 함수로 컴파일 됨  |
| constructor  | 클래스의 객체가 생성될 때 호출되는 메서드를 제공, 클래스의 프로퍼티를 초기화하는데 사용  |
| interface  | 클래스에 구현될 계약을 정의, 프로퍼티와 함수선언을 가질 수 있음  |
| implements  | 컴파일러에게 클래스에 의해 구현될 인터페이스를 알려주는 키워드  |
| ...  | 나머지 파라미터, 함수가 여러 파라미터를 배열로 받을 수 있게 함  |
|   |   |
| =>  | 뚱뚱한 화살표 함수, 함수를 선언하는 대안을 제공  |
| module  | 클래스를 담는 컨테이너  |
| import / export  | 모듈의 어떤 멤버가 export되고 import될지 정의  |
| generics  | 제네릭으로 다른 데이터 다입을 수용할 수 있는 함수를 작성, 재사용 가능한 함수를 작성하라 수 있게 해줌  |
| enum  | 열거형으로 숫자 값과 연결된 상수를 정의할 수 있음  |
| iterators   | System.iterator를 구현한 어떤 객체도 열거 가능, 객체가 반복할 수 있는 값의 목록을 반환 한다는 것을 의미  |

---

## 설치와 설정

### TypeScript 설치

터미널에서 아래 명령을 실행하여 타입스크립트를 설치  

> npm install -g typescript

타입스크립트 버전 확인  

> tsc -v

tsc 도움말  

> tsc -h

### TypeScript 에디터


