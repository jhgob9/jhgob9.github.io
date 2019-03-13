---
title: "[Typescript] 4장 Sport News Combinator"
date: 2019-03-10 10:40:00 +0900
categories: typescript
---

# 4장 Sport News Combinator

이번 장에서 다룰 내용  

- Typescript의 데코레이터 : @Component 와 @NgModule라는 이름으로 애플리케이션에서 데코레이터를 사용하며 데코레이터에 대해 살펴보기
- SNC 기능추가 : 마지막 기능인 서비스 추가, 서비스는 웹 서비스를 호출하여 실시간 데이터를 가져오고, 다른 탭 사이의 탐색을 제공하는 라우팅을 가능하게 해줌
- Angular 서비스 : 애플리케이션에 기능을 추가함과 동시에 Angular의 서비스 개념에 대해 알아봄
- Angular 라우팅 : 라우팅을 구현하기 위해 URL을 기반으로 하는 Angular 내장 라우팅을 사용

---

## 데코레이터

데코레이터는 파사드패턴이나 플라이웨이트 패턴과 같은 구조적 디자인 패턴 중 하나  
Javascript 에서는 정식 지원되는 기능은 아니며 Typesript에서도 실험적인 기능  
ECMA에서 Javascript에 기능을 확정하게 되면 구현이 변경 될 수도 있음  

> **파사드 패턴**  
> 파사드(facade)는 건물의 정면을 의미  
> 소프트웨어 디자인 패턴의 하나로 통일된 인터페이스를 통해 복잡한 서브 시스템을 간단히 사용하도록 만든 패턴  

> **플라이웨이트 패턴**  
> 플라이웨이트(flyweight)는 권투, 레슬링 등의 스포츠에서 50Kg 내외의 경량급 선수를 말함  
> 소프트웨어 디자인 패턴에서는 객체 내부에서 참조하는 객체를 매번 만드는 것이 아니라, 없다면 만들고 있으면 기존 객체를 공유하여 메모리 사용을 최소화하는 패턴  

### 데코레이터 디자인 패턴

클래스를 수정하지 않고 클래스의 기능을 확장할 수 있는 구조적 패턴의 하나  
별도의 클래스를 생성하고 이 새로운 클래스로 메인 클래스를 래핑하여 새로운 기능을 추가할 수 있게 함으로써 가능  
클래스에서 단일 책임 원칙을 지킬 수 있는지 확인하도록 함  
클래스는 계속해서 본인의 주요 책임에 초점을 맞출 수 있으며 클래스가 초기화될 때 활성화되는 데코레이터를 사용하여 새로운 기능들을 추가할 수 있음  
객체 생성 시 실행되기 때문에 데코레이터 패턴을 사용하면 전체 기능에 신경쓰지 않고 특정 인스턴스에 초점을 맞출 수 있음  

Typescipt는 데코레이터 패턴을 래핑하여 영향을 받는 클래스에 어노테이션을 추가하여 기능을 추가  
래핑 패턴의 장점은 기존 코드에서 눈에 띄지 않으며 특정 클래스에서 플러그 앤 플레이로 사용 가능하는 점  


### Typescript 데코레이터

함수로 정의되며 클래스, 프로퍼티, 메서드에 추가하여 기능을 더할 수 있음  
Typescript의 데코레이터는 다양한 형태가 있는데 각각의 형태는 클래스나 메서드 같은 특정 타깃을 중점에 둠  
여러 형태의 데코레이터 간의 주요 차이점음 데코레이터 서명에 있음  
데코레이터는 실험적인 기능이므로 tsconfig.json 파일에서 compilerOptions 속성에 다음 플래그를 추가  

{% highlight javascript %}
"compilerOptions": {
	"experimentalDecorators": true,
	......
}
{% endhighlight %}

Angular는 컴포넌트를 차별화 하기 위해 데코레이터를 광범위 하게 사용  
Angular CLI로 프로젝트를 만들면 기본적으로 이 플래그가 추가 됨  

#### Typescript 데코레이터 정의

Typescript의 데코레이터는 함수 정의일 뿐 이론상으로 단지 Typescript 함수를 작성하기면 하면 됨  
유일한 차이점음 데코레이터가 받는 파라미터의 타입  

{% highlight typescript linenos %}
// 데코레이션 정의
function logger(target:Function){
	// 데코레이션 로직 구현
}

// 데코레이션 클래스에 적용
@logger
class Book{
	constructor(private title:string){}
}
{% endhighlight %}

2행에서 하나의 파라미터를 취하는 표준 Typescript 함수를 정의하며 이 경우 파라미터는 Function 타입  
7행에서 데코레이터를 사용하는데 이름 앞에 @(at) 기호가 붙음  

이런 유형을 *클래스 데코레이터*라고 함  
클래스를 생성할 때 클래스의 생성자를 데코레이터의 파라미터로 전달하므로 7번째 행처럼 데코레이터를 사용할 때 파라미터를 명시적으로 전달할 필요가 없음  

#### 데코레이터 팩토리

실제 애플리케이션에서는 클래스 또는 함수의 데코레이터에 일부 사용자 정의 값을 전달할 수 있는 유연성이 필요함  
그래서 데코레이션 팩토리가 필요함  
데코레이터 팩토리를 사용하면 클래스의 값을 데코레이터로 전달할 때 추가로 파라미터를 정의할 수 있음  

{% highlight typescript linenos %}
{% raw %}
// 데코레이션 정의
function logger(name: string) {
	return function(target:Function){
		console.log(`클래스 : ${name}`);
	}
}

// 데코레이션 클래스에 적용
@logger('Book')
class Book {
	constructor(private title: string) { }
}
{% endraw %}
{% endhighlight %}

클래스가 데코레이터에 전달해야 하는 파라미터가 있다면 원하는 만큼 파라미터를 추가로 정의하면 됨  
이 함수는 클래스 데코레이터를 정의하는데 필요한 서명이 있는 함수를 반환하기 때문에 *팩토리 함수*라고 함
클래스 데코레이터는 Typescript의 클래스 생성자를 전달하는 function 타입의 파라미터 하나를 가지고 있으므로 내부 함수에서는 3행처럼 단일 파라미터를 허용하는 서명을 가짐  

Javascript와 Typescript에서는 클로저(closure)라는 기능이 있기 때문에 팩토리 함수 내부에서도 데코레이터 함수에서 정의한 파라미터를 참조할 수 있음  
따라서 4행에서 name	속성에 접근할 수 있음  
이 파라미터는 9행에서 볼 수 있듯이 클래스에 어노테이션이 달릴 때 파리미터로 전달된 값  

데코레이터 팩토리는 어노테이션이 달린 클래스, 함수 또는 프로퍼티에 추가로 파라미터를 전달할 수 있게 해줌  
이것은 데코레이터 함수를 반환하는 팩토리 함수를 통해서 가능한 일  

#### 클래스 데코레이터

클래스 데코레이터가 가장 일반적  
Typescript가 구현된 로직을 해당 클래스의 생성자에 삽입하여 클래스의 기능을 향상시킬 수 있기 때문  

SNC와 이후에 빌드할 애플리케이션은 Angular가 @Component로 정의한 데코레이터를 사용  
클래스 데코레이터이며 Angular가 특정 클래스를 컴포넌트로 식별하고 런타임에 특정 행동을 할 수 있게 해줌  

클래스 데코레이터는 두 가지 방법으로 정의 가능  
하나는 생성자 함수를 대체하는 데코레이터  
다른 하나는 그렇지 않은 데코레이터  
이 둘은 클래스 데코레이터 함수에서 정의한 리턴 타입으로 구분  

클래스 데코레이터는 클래스 생성자 타입의 파라미터 한개를 받음  

{% highlight typescript%}
function logger(target:Function):void{
	console.log('로깅 기능 구현');
}
{% endhighlight %}

위 코드는 Function 타입의 파리미터 하나를 받아서 문자열을 출력하는 logger 함수   
이 데코레이터를 클래스에 사용하면 Typescript는 클래스의 생성자를 파라미터로 데코레이터에 전달  
단순히 데코레이터 함수가 호출 되었음을 알려주는 기능만 있음  
이 유형의 데코레이터는 아무것도 반환하지 않고 내부 코드를 실행한 뒤 클래스의 생성자로 흐름을 넘겨 줌  
그래서 반환 타입이 void  

클래스의 반환 타입으로 void를 사용하면 생성자의 동작을 바꾸지 않고 추가 동작을 실행  
반면 데코레이터가 리턴 타입을 가지고 있다면 클래스의 생성자에서 정의한 동작을 데코레이터의 동작으로 대체  
다음과 같이 데코레이터를 클래스에 응용  

{% highlight typescript%}
// 클래스 데코레이터
@logger
class Book{
	constructor(private title: string) { }
}
{% endhighlight %}

데코레이터를 사용하려면 엔티티(클래스/함수/프로퍼티) 앞에 @ 기호와 데코레이터 이름을 지정  
Typescript에게 데코레이터 함수를 호출하고 Book 클래스의 생성자를 파라미터로 전달하여 함수를 실행하고 생성자로 돌아오라고 지시하게 됨  
위 코드를 실행하면 콘솔창에 '로깅 기능 구현' 이라는 메시지가 출력 됨  

데코레이터를 데코레이터 팩토리로 구현하지 않았기 때문에 데코레이터에 사용자 정의 값을 전달할 수는 없음  

##### 데코레이터에 파라미터 전달

Typescript는 데코레이터 팩토리라는 개념을 통해 사용자 정의값을 데코레이터에 전달하여 처리할 수 있도록 하였음  
이 기능은 데코레이터가 클래스에서 정의한 다양한 기준에 따라 실행되도록 하기 때문에 매우 중요한 기능  

다음은 기존 코드를 리팩토링하여 하나의 name 파라미터를 받는 로거 데코레이션

{% highlight typescript %}
{% raw %}
// 데코레이션 정의
function logger(name: string) {
	return function(target:Function){
		console.log(`클래스 : ${name}`);
	}
}
{% endraw %}
{% endhighlight %}

여기서는 name을 파라미터로 받는 메인 함수 안에 데코레이터 함수가 래핑되어 있음  
Book 클래스에 데코레이터를 사용하면 Typescript는 다음 스크린샷과 같이 name 문자열 파라미터를 전달해야 한다고 알려줌  

![]({{ "/assets/images/decorator.png" | absolute_url }})

Typescript는 이 파라미터 값을 클래스의 생성자 함수와 함께 데코레이터로 전달  

{% highlight typescript %}
// 데코레이션 클래스에 적용
@logger('Typescript')
class Book {
	constructor(private title: string) { }
}
{% endhighlight %}

##### 데코레이터 생성자 오버로딩

기본적으로 Typescript의 클래스 데코레이터는 생성자를 재정의하지 않고 생성자 구현 위에 논리를 추가  
그러나 기존 생성자를 재정의하고 새로운 생성자 구현을 반환하는 데코레이터를 사용하려는 경우가 있음  
이것이 Typescript의 장점  
데코레이터의 반환 타입이 차이점  

{% highlight typescript linenos %}
// 데코레이션 정의
function logger(name: string) {
	return function <newFunction extends Function> (target: newFunction): newFunction{
		let newConstructor : Function;
		// 새로운 constructor 구현
		return <newFunction> newConstructor;
	}
}
{% endhighlight %}

생성자를 오버라이드하고 데코레이터가 문자열 파라미터를 가짐  
2행은 생성자를 오버라이드하지 않았을 경우와 같음  

생성자를 오버라이드하고 싶은 경우, 반환 값은 데코레이터로 전달된 생성자의 값과 같은 타입이어야 함  
그래서 함수 타입을 newFunction이라는 제네릭 타입으로 확장해야 함  
이 newFunction은 반환되는 생정자의 타입을 나타냄  

반환되는 타입은 데코레이터 함수에서 파라미터로 받은 클래스의 생성자와 같은 타입이어야 함  
그래서 target 속성도 newFunction 타입  
이제 데코레이터에서 새로운 생성자를 반환하고 적절한 값을 할당함  
이 데코레이터를 사용하는 것은 생성자를 오버라이드하지 않은 데코레이터를 사용하는 것과 정확히 동일  

#### 메서드 데코레이터

메서드 데코레이터는 메서드에 어노테이션을 붙여서 추가기능을 제공하는데 사용  

{% highlight typescript %}
{% raw %}
function writable(table:Object,
	propertyKey: string,
	descriptor: PropertyDecorator){
	console.log(`Setting ${propertyKey}`);
	descriptor.writable = true;
}
{% endraw %}
{% endhighlight %}

메서드 데코레이터에는 세개의 파라미터가 있음  
첫번째 파라미터는 클래스 데코레이터의 파라미터와 동일하며 정적 메서드인 경우 생성자를 가리키고 인스턴스 멤버인 경우 클래스의 프로토타입  
두번째 파라미터는 데코레이팅 할 메서드의 이름  
세번째 파라미터는 데로케이팅 메서드의 프로퍼티를 제어하는데 사용할 수 있는 프로퍼티 설명자  
프로퍼티 설명자는 Typescript 인터페이스이며 Javascript ES5에서 소개된 기능  
프로퍼티 설명자는 메서드의 동작을 제어하는 데 사용할 수 있는 여러 속성을 가지고 있음  
다음은 이러한 속성의 일부  

{% highlight typescript %}
interfacePropertyDecorator {
	configurable?: boolean;
	enumerable?: boolean;
	value?: any;
	writable?: boolean;
	get? (): any;
	set? (v:any): void;
}
{% endhighlight %}

메서드 데코레이터를 사용하는 것은 매우 직관적   
메서드 위에 @와 함께 데코레이터 이름을 붙이면 됨  

{% highlight typescript %}
class Book {
	constructor(private title: string) { }
	@writable
	getDetails(name:string){}
}
{% endhighlight %}

메서드 데코레이터도 사용자 파라미터를 받을 수 있음  
다음은 한 개의 파라미터를 가진 writable 함수의 수정된 버전  

{% highlight typescript %}
{% raw %}
function writable(naem:string){
	return function (table: Object,
		propertyKey: string,
		descriptor: PropertyDecorator) {
		console.log(`Setting ${propertyKey}`);
		descriptor.writable = true;
	}
}
{% endraw %}
{% endhighlight %}

#### 프로퍼티 데코레이터

프로퍼티 데코레이터는 데코레이팅된 속성의 이름을 나타내는 파라미터가 추가 됨  

{% highlight typescript %}
function propertyDecorator(target: Object, propertyKey: string){
	// 데이터 로직 구현
}
{% endhighlight %}

첫번째 파라미터가 static 객체인 경우 클래스의 생성자, 그렇지 않은 경우 프로퍼티를 포함하는 클래스의 프로토타입  
두번째 파라미터는 데코레이팅된 프로퍼티의 이름을 정의하는 문자열  

사용법은 클래스 데코레이터와 비슷하며 프로퍼티에 @기호를 접두사로 하여 데코레이터 이름을 달기만 하면 됨  

#### 파라미터 데코레이터

파라미터 데코레이터는 3개의 파리미터가 있음  
함수의 특정 파라미터에 적용  
함수의 어떤 파라미터에 데코레이팅되었는지 확인하기 위해 세번째 파라미터를 사용  

{% highlight typescript %}
function parameterDecorator(target: Object, propertyKey: string, index: number) {
	// 데이터 로직 구현
}
{% endhighlight %}

첫번째 파라미터는 클래스의 생성자  
두번째 파라미터는 파라미터가 속한 함수의 이름  
세번째 파라미터는 데코레이팅된 함수에서 파라미터의 위치를 지정

### SNC 데코레이터

Angular에서 컴포넌트와 메타데이터를 식별하도록 하기 위해 클래스 데코레이터를 사용 했음  

{% highlight typescript %}
@Component({
  selector: 'app-news',
  templateUrl: './news.component.html',
  styleUrls: ['./news.component.scss']
})
{% endhighlight %}

Component라는 클래스의 데코레이터가 있으며 여러 파라미터가 연결되어 있음  

---

## SNC 추가 기능

- 서비스 : 애플리케이션에 웹 서비스 호출을 담당할 서비스를 추가
- HTTP 호출 : 서비스 레이어에서 Angular의 HTTP 인터페이스를 사용하여 Get 메서드로 최신 스포츠 뉴스를 가져옴
- 라우팅 : 사용자가 서로 다른 뉴스 소스를 탐색할 수 있도록 Angular 라우팅을 사용

### SNC에 서비스 추가

컴포넌트에 상주하는 UI로직  
서비스에 상주하는 비즈니스 로직  

비즈니스 로직은 데이터 퍼시스턴스나 HTTP 호출과 같이 컴포넌트 간에 공통적으로 적용되는 로직을 캡슐화  

> 데이터 퍼시스턴스(data persistence)  
> 프로그래밍에서 퍼시스턴스는 어떤 상태 값이 해당 상태를 생성한 프로세스가 종료되더라도 사라지지 않고 유지되는 특성을 말함  
> 주로 파일이나 DB에 저장  
> 이 과정에서 데이터를 저장하거나 자료 구조에 매핑하는 등의 로직이 필요한데 이런 작업들의 관리자를 데이터 퍼시스턴스라고 함  

서비스는 매우 특정한 목적을 가진 클래스이며 컴포넌트와 독립적인 기능을 구현하는데 사용됨  
컴포넌트와 서비스의 책임을 명확히 분리하여 단일 책임 원칙을 효과적으로 준수할 수 있음  


#### 서비스 생성

Angular로 서비스를 생성하는 과정은 3단계로 나뉨  

- 서비스 클래스 정의
- 메타데이터 추가
- 필요한 종속성 import

서비스를 구현하는 첫 번째 단계는 서비스 클래스를 만드는 것  
app 폴더 아래에 service라는 새 폴더를 만들고 newsapi.service.ts 라는 새 파일을 만듦  
NewsapiService 라는 표준 Typescript 클래스를 정의  

*/app/service/newsapi.service.ts*  

{% highlight typescript %}
export class NewsapiService {
}
{% endhighlight %}

export 키워드는 애플리케이션의 다른 곳에서 이 서비스에 엑세스할 수 있도록 해줌  
NewsapiService는 선택한 뉴스 소스를 기반으로 웹 서버에서 최신 스포츠 뉴스를 가져 오는 메서드 한개를 제공  

이 클래스를 Angular에서 사용하기 위한 서비스로 정의  
이를 위해 Angular에서 정의한 @Injectable() 데코레이터를 사용해 간단히 클래스를 서비스로 만듦  

필요한 종속성을 클래스에 import  
@Injectable() 데코레이터를 참조하기 위해 Angular core 모듈이 필요  

{% highlight typescript %}
import { Injectable } from '@angular/core';
@Injectable()
export class NewsapiService {
	constructor() {}
	// 웹 서버에서 데이터를 가져오는 역할을 하는 메서드
	public fetchNewsFeed(){}
}
{% endhighlight %}

#### 서비스 등록

Angular는 내장 주입기를 사용하여 애플리케이션에 서비스를 등록할 수 있는 방법을 제공  
Angular 주입기는 서비스의 단일 인스턴스를 생성하여 싱글턴 형태로 제공  

> 싱글턴(singleton)
> 클래스의 최초 생성자 호출 시 객체 인스턴스를 만들도 다음 생성자 호출부터는 해당 인스턴스를 반환하도록 하는 패턴

Angular 주입기는 Angular로 등록한 모든 서비스의 컨테이너 역할을 함  

서비스 인스턴스를 유지하는 주입기의 장점은 두 가지  
인스턴스 관리의 책임을 Angular에게 넘길 수 있음  
서비스를 컴포넌트 간에 데이터를 공유하는 용도로 사용 가능  

Angular 주입기를 사용하여 서비스를 등록하려면 공급자가 필요  
공급자는 서비스 인스턴스를 만들고 반환하는 역할을 하며 컴포넌트 또는 모듈에서 정의  
서비스는 공급자를 등록한 곳을 기준으로 접근 가능  

NewsComponent에 공급자를 등록하면 NewsComponent와 하위 컴포넌트에서 서비스에 접근 가능  
앱 모듈에 공급자를 등록한 서비스는 애플리케이션 전체에서 사용할 수 있으면 전역 서비스로 동작  
따라서 어디서 서비스를 등록할지 결정하는 것이 중요  

공급자를 앱 모듈에 등록하여 모든 곳에서 사용할 수 있도록 작성

*app.module.ts*  

{% highlight typescript %}
@NgModule({
  declarations: [
    AppComponent,
    NewsComponent,
    HeaderComponent,
    FooterComponent
  ],
  imports: [
    BrowserModule,
	HttpModule
  ],
  providers: [NewsapiService],
  bootstrap: [AppComponent]
})
{% endhighlight %}

공급자 배열에서 NewsapiService에 대한 참조를 추가했으므로 NewsapiService를 import 해야 함  

{% highlight typescript %}
import { NewsapiService } from './service/newsapi.service';
{% endhighlight %}

#### 서비스 주입(Injection)

서비스 사용의 마지막 단계는 그것을 필요로 하는 컴포넌트에 주입하는 것  
컴포넌트가 서비스 인스턴스를 필요로 하는 경우 해당 서비스에 대한 의존성의 정의하기만 하면 Angular가 의존성을 해결  
이 프로세스를 의존성 주입이라고 하며 클래스는 인스턴스를 직접 생성할 필요 없이 외부에서 필요한 의존성을 가져올 수 있음  

Angular는 서비스를 사용하여 컴포넌트 간에 데이터를 공유할 수 있음  
Angular는 컴포넌트의 객체 생성 시 컴포넌트에 의존성을 주입  
따라서 클래스의 생성자에서 의존성을 주입하는 것이 좋음  

NewsComponent 클래스에서 서비스를 사용할 필요가 있으므로 생성자에서 NewsapiService를 주입할 것  

*/app/dashboard/news/news.component.ts*  

{% highlight typescript %}
import {NewsapiService} from '../../service/newsapi.service';
constructor(private _service:NewsapiService){}
{% endhighlight %}

NewsComponent 클래스에 서비스 공급자를 추가할 필요는 없음  
이미 앱 모듈에 추가했기 때문  
Angular는 앱 모듈에 포함된 모든 컴포넌트에서 이 서비스를 주입할 수 있음  

로컬 변수 _service 에는 NewsComponent 클래스의 객체가 만들어 질 때 값이 할당 됨  
NewsComponent 클래스에 NewsapiService에 대한 import문을 추가  

{% highlight typescript %}
import {NewsapiService} from '../../service/newsapi.service';
{% endhighlight %}

### SNC에서 HTTP 호출 구현

Angular는 HTTP에서 데이터를 가져오기 위해 observable을 사용  

#### HTTP Observable

Observable은 백엔드 서버에서 비동기적으로 오는 데이터 스트림  
Angular는 observable을 구현하기 위해 외부 라이브러리인 Reactive Extensions(RX)를 사용  
Observable은 백엔드 요소가 채워지는 비동기 배열  
첫 번째 요소를 얻고 시간차를 두고 잠시후 두번째 요소가 들어오는 형태  

전체 응답을 한꺼번에 보내는 대신 덩어리로 전송되는 웹 응답을 처리할 수 있음  
Observable을 배열의 형태로 관리하면 map, catch, do와 같은 연산자를 사용하여 효과적으로 응답을 처리  
Reactive Extension을 사용하면 HTTP에서 새로운 데이터 스트림이 도학할 때 알림을 받을 수 있는 observable을 구독 가능  
데이터가 도착하면 Reactive Extension 라이브러리에서 제공하는 연산자를 사용하여 애플리케이션에 맞게 데이터를 수정할 수 있음  

Observable은 일정 기간동안 들어오는 여러 값(데이터 스트림)을 처리  
Observable은 취소가 가능, 사용자 입력에 따라 지속적으로 여러 웹 서비스 호출을 수행하는 기능을 개발할 때 유용  
예를 들어 사용자 입력에 따라 자동완성을 제공하는 경우 observable을 사용하여 이전 요청을 취소하고 가장 최근 요청에 대한 응답만 처리하도록 처리  

#### Observable을 사용한 HTTP 요청

최신 뉴스 서비스를 가져오기 위해 HTTP 요청을 구현, 두가지를 구현해야 함  

- GET 호출을 위한 HTTP 요청 구현
- HTTP 응답 구독과 UI를 채우기 위한 데이터 사용

이 두 단계는 서로 다른 파일에서 구현  
하나는 newsapi.service.ts 파일에서 HTTP 호출을 구현  
news.component.ts에서 HTTP 응답에 대한 구독  

##### HTTP 요청 구현

서비스 클래스에서 HTTP GET 요청을 하려면 Angular의 HttpModule이 필요  
이 모듈을 사용하려면 NewsapiService 클래스에 HTTP를 클래스의 생성자 함수를 사용하여 의존성으로 주입  
그러면 Angular는 클래스의 인스턴스를 만들 때 HttpModule의 인스턴스를 클래스에 전달  

*/app/service/newsapi.service.ts*  

{% highlight typescript %}
import { Injectable } from '@angular/core';
import { Http } from '@angular/http';
@Injectable()
export class NewsapiService {
	constructor(private _http: Http) {}
}
{% endhighlight %}

코드의 주요 변경사항 두가지  

- HTTP 생성자에 의존성을 추가
- 주어진 모듈을 가져오기 위해 import문을 추가

HTTP 클라이언트는 웹 서비스 호출을 위한 인터페이스를 제공(GET/PUT/POST/DELETE)  
GET 메서드 : 최신 뉴스를 가져옴, 뉴스 모델로 변환될 observable을 반환  
웹 서비스 호출을 하기 위해 클래스에 fetchNewsFeed 매서드 추가  
fetchNewsFeed는 source 파라미터 한개를 사용  
source 파라미터는 스포츠 뉴스를 가져오는 뉴스 아울렛  
HTTP GET이 observable을 반환하기 때문에 fetchNewsFeed 메서드는 News 타입의 observable을 반환  

{% highlight typescript %}
{% raw %}
import { Injectable } from '@angular/core';
import { Observable } from 'rxjs/Observable';
import { Http, Response } from '@angular/http';

import 'rxjs/add/operator/map';
import 'rxjs/add/operator/catch';
import 'rxjs/add/operator/do';

import { News } from '../../models/news';

@Injectable()
export class NewsapiService {
	baseUrl: string;

	static apiKey: string = "b07f98f6575d47d99fd6057668f21cb2";
	constructor(private _http: Http) {
		this.baseUrl = 'https://newsapi.org/v1/articles';
	}
	public fetchNewsFeed(source: string): Observable<News> {
		return this._http.get(`${this.baseUrl}/?source=${source}&sortBy=top&apiKey=${NewsapiService.apiKey}`)
			.map((response: Response) => <News>response.json())
			.do(data => console.log('All: ' + JSON.stringify(data)))
			.catch(this.handleError);
	}
	private handleError(error: Response) {
		console.error(error);
		return Observable.throw(error.json().error || 'Server error');
	}
}
{% endraw %}
{% endhighlight %}

코드에 대한 변경 사항  

1. apiKey와 baseUrl  
newsapi 웹 서버에서 뉴스 기사를 가져오려면 이것들을 등록하고 서비스 호출에 사용할 고유키를 발급 받아야 함  
2. fetchNewsFeed 메서드  
URL을 전달하여 get 메서드로 호출  
URL은 baseUrl, source, sortBy, apiKey로 구성  
상위 10개 뉴스를 가져올 것이므로 sortBy 값은 top  
3. GET 메서드가 Observable을 반환하므로 map 연산자를 사용  
fetchNewsFeed 메서드에서 호출하여 받은 응답은 뉴스 모델로 형 변환  
4. do 함수에서는 응답 데이터를 디버깅 용도로 콘솔에 출력  
5. 예외처리  
catch 연산자를 사용하여 호출에 실패할 경우 catch 블록의 handleError 실행
6. 오류의 내용을 기록한 다음 반환된 에러메세지를 throw하는 에러 핸들러 함수 작성  

실시간 웹서비스를 가져올 준비가 됨, 그러나 그전에 fetchNewsFeed 메서드에서 반환되는 Observable을 구독해야 함  

##### HTTP 응답 구독

페이지를 로드할 때 새 기사가 로드되도록 하려면 NewsComponent 클래스의 ngOnInit 메서드에서 웹서비스를 호출 해야 함  
ngOnInit 메서드는 Angular에서 제공하는 라이프 사이클 훅 중 하나로 페이지로드 사이클을 가로채게 함  
RxJs  Observable 라이브러리의 subscribe 메서드에 두가지 오버로드된 구현이 있음  

{% highlight typescript %}
this._service(successFunc, errorFunc);
this._service(successFunc, errorFunc, completeFunc);
{% endhighlight %}

Observable이 오류 없이 응답을 반환하면 successFunc가 호출  
웹서버에서 오류가 반환되면 errorFunc가 호출  
completeFunc은 Observable의 구독을 취소하는데 사용

다음은 newsapi 서비스 클래스를 호출하기 위한 코드

*/app/dashboard/news/news.component.ts*  

{% highlight typescript %}
import { Observable } from 'rxjs/Observable';
this._service.fetchNewsFeed(this.feedType)
	.subscribe(
		items => this.latest_news = items,
		error => { this.errorMessage = 'Could not load ' + this.feedType + ' stories.'; console.log(this.errorMessage) }
	);
{% endhighlight %}

fetchNewsFeed의 응답을 구독하고 성공 시 반환된 응답을 로컬 latest_news 변수에 할당  
오류가 있는 경우 errorMessage 프로퍼티에 오류 내용을 기록하고 콘솔에 출력  
메서드 호출시 가려오려는 데이터의 뉴스 소스를 나타내는 feedType 속성을 전달  

### SNC 라우팅

SNC는 아직 어떤 종류의 탐색도 하지 않고 HeaderComponent에서 만든 하이퍼링크도 처리하지 않음  
웹 서비스 호출 시 데이터를 가져올 때는 뉴스 소스의 이름이 필요하고 이것은 모두 라우팅에 의해 만들어 짐  

라우팅은 애플리케이션이 뷰 사이를 탐색하는 개념  
애플리케이션은 각 뷰를 표시할 고유 경로를 정의하여 라우팅에 사용  
사용자가 링크를 누르면 해당 경로가 활성화되어 선택한 컴포넌트와 해당 템플릿이 로드 됨  

#### 라우트(route) 정의

Anguar는 라우트를 등록할 수 있는 라우트 모듈을 제공함  
라우터 지시자 : RouterLink와 RouterOutlet  
구성된 라우터를 노출하는 메커니즘을 제공  

라우터 모듈을 사용하면 우선 import 해야 함

{% highlight typescript %}
import { Routes, RouterModule } from '@angular/router';
{% endhighlight %}

관심사를 잘 분리하기 위해 newsroutingmodule 이라는 새로운 모듈을 만들어 모든 라우터를 관리  
app.route.ts 파일 생성후 위의 라우터 모듈 import  
라우터의 새 모듈을 만드려면 Angular에서 ModuleWithProviders 모듈도 참조 해야 함

*/app/app.route.ts*  

{% highlight typescript %}
import { ModuleWithProviders } from '@angular/core';
import { newsroutingmodule } from './app.route';
{% endhighlight %}

##### 애플리케이션 라우트

라우터는 라우트 목록과 함께 구성되어야하며 각 항목은 라우트를 정의  
라우트 모듈은 라우트의 속성을 제공하고 라우트 속성은 특정 라우트를 정의하는 객체의 배열로 정의  

{% highlight typescript linenos %}
import { ModuleWithProviders } from '@angular/core';
import { Routes, RouterModule } from '@angular/router';

import { NewsComponent } from './dashboard/news/news.component';

const routes: Routes = [
	{ path: '', redirectTo: 'nfl', pathMatch: 'full' },
	{
		path: 'nfl',
		component: NewsComponent,
		data: { feedType: 'nfl-news', source: 'nfl' }
	},
	{
		path: 'espn',
		component: NewsComponent,
		data: { feedType: 'espn', source: 'espn' }
	},
	{
		path: 'fox',
		component: NewsComponent,
		data: { feedType: 'fox-sports', source: 'Fox Sports' }
	},
	{
		path: 'bbc',
		component: NewsComponent,
		data: { feedType: 'bbc-sport', source: 'BBC Sports' }
	},
	{
		path: '**',
		redirectTo: 'nfl', pathMatch: 'full'
	}

];
{% endhighlight %}

1행에서 볼수 있듯이 객체 배열을 포함하는 Routes 타입의 상수 변수를 정의  
각 라우트 객체에는 URL의 경로를 정의하는 path 속성이 있음  
특정 라우트가 활성화 되면 이 path 속성이 애플리케이션 URL에 추가됨  
각 라우트의 고유 URL을 관리할 수 있으며 사용자가 북마크도 가능함  

선택한 path가 활성화 됐을 때 라우팅할 컴포넌트를 나타내는 component 속성도 있음  

7행과 같이 첫번째 라우트를 기본 라우트라고 함  
redirectTo : 기본 페이지를 방문 할때 리다이렉트할 path  
pathMatch : 입력한 경로가 정의한 경로와 정확하게 일치해야 함  

29행과 같이 마지막 경로는 와일드카드 라우트라고 하면 {% raw %}**{% endraw %}로 표시  
이전에 정의되지 않은 모든 라우트를 정의  

> 라우팅 정의시 순서가 아주 중요함  
> Angular는 라우트를 확인 할 때 처음 일치 정책을 사용하므로 일반적인 라우트 앞에 특수한 라우트를 배치 해야 함  

data 라는 속성은 컴포넌트에 사용자 정의 속성을 추가로 전달  
이 코드에서는 feedType과 source 두 속성을 전달  
NewsComponent는 이러한 속성을 사용하여 링크를 식별하고 헤더에 표시  

##### 라우터 모듈에 추가

이전에 정의한 라우트를 라우터 모듈에 바인딩  
Angular는 forRoot 메서드를 제공(모든 라우트 공급자와 지시자를 포함하여 라우터 모듈을 만들어 줌)  

{% highlight typescript %}
export const newsroutingmodule: ModuleWithProviders = RouterModule.forRoot(routes);
{% endhighlight %}

#### app 모듈에 임포트

생성한 라우팅 모듈을 기본 모듈인 app 모듈로 가지고 오는 것  

{% highlight typescript %}
imports: [
	BrowserModule,
	newsroutingmodule,
	HttpModule
],
{% endhighlight %}

#### 라우터 링크 구현  

라우트를 정의했으므로 라우트를 사용자 액션에 연결  
Angular의 라우팅 모듈은 URL 세그먼트를 정의하는데 도움이 되는 지시자를 제공  

*/app/Core/header/header.component.html*  

{% highlight html %}
<ul class="nav navbar-nav">
	<li><a routerLink="/nfl">NFL</a></li>
	<li><a routerLink="/espn">ESPN</a></li>
	<li><a routerLink="/fox">FOX</a></li>
	<li><a routerLink="/bbc">BBC</a></li>
</ul>
{% endhighlight %}

라우트와 함께 각 메뉴 옵션에 연결 하려면 routerLink 지시자를 사용  
routerLink 지시자는 속성 지시자로 HTML 코드와 함께 사용  

router-outlet 이라는 지시자는 라우트의 컴포넌트를 출력할 위치를 지정함  

*/app/app.component.html*  

{% highlight html %}
<div>
    <app-header></app-header>
    <router-outlet></router-outlet>
    <snc-footer></snc-footer>
</div>
{% endhighlight %}

#### NewsComponent에서 라우트 접근

마지막 단계로서 선택한 메뉴의 뉴스 아울렛에서 웹 서비스 호출을 통해 뉴스 기사를 가져오는 것 

*/app/dashboard/news/news.component.ts*  

{% highlight typescript linenos %}
ngOnInit() {
	this.route.data.subscribe(data => {
		this.feedType = (data as any).feedType;
		this.source = (data as any).source;
	});

	this._service.fetchNewsFeed(this.feedType)
		.subscribe(
			items => this.latest_news = items,
			error => { this.errorMessage = 'Could not load ' + this.feedType + ' stories.'; console.log(this.errorMessage) }
		);
}
{% endhighlight %}

2행에서 라우터를 구독하고 선택한 라우트에 대한 데이터 객체를 가져옴  