---
title: "[TypeScript] 6장 트렐로에 기능추가하기"
date: 2019-03-20 10:40:00 +0900
categories: typescript
---

# 6장 트렐로에 기능추가하기

이번 장에서 다룰 내용  

- Typescript의 반복자
- 비동기 프로그래밍
- Angular에서 Typescript를 사용하여 비동기 프로그래밍 구현
- Angular의 사용자 정의 파이프
- 테스트 기능을 추가하기 위한 의존성 주입

---

## 반복자(iterator)

- 값에 순차적으로 접근하기 위한 수단
- 반복 가능한 모든 객체에는 Symbol.iterator 프로퍼티가 있어야 함
- Symbol.iterator 프로퍼티는 객체의 요소를 반복 처리할 수 있는 메서드를 제공

### Javascript 반복자

{% highlight javascript %}
let stringArray = 'Learning TypeScript';
for(let c of stringArray){
	console.log(c);
}
{% endhighlight %}

- 문자열의 각 문자를 반복하여 출력
- 문자열에 Symbol.iterator 프로퍼티가 있고
- for ... of가 반복 가능한 객체에 대해 루프를 돌며 출력
- Symbol.iterator 프로퍼티를 명시적으로 사용하여 다시 작성 가능


{% highlight javascript %}
let stringArray = 'Learning TypeScript';
let iter = stringArray[Symbol.iterator]();
console.log(iter.next().value);
{% endhighlight %}

### TypeScript 반복자  
- iteratable 프로퍼티를 가진 데이터 타입을 제공
- Array 인터페이스에는 IterableIterator 인터페이스 타입의 Symbol.iterator 프로퍼티가 있음
- IterableIterator 인터페이스는 iterator 인터페이스를 구현하여 next(필수), return, throw의 3개의 메서드를 가짐
- next, return, throw 메서드는 IteratorResult 타입이면 done, value 두가지 프로퍼티를 가지고 있음

#### TypeScript 사용자정의 반복자  
- 1부터 시작, 100이 되면 다시 0으로 돌아가는 카운터  
{% highlight typescript %}
class customCounter implements Iterator<number>{
	private calculatedVal:number = 0;
	next(value?: any): IteratorResult<number>{
		this.calculatedVal = this.calculatedVal > 99 ? 0 : ++this.calculatedVal;
		return{
			done: false,
			value: this.calculatedVal
		}
	}
}
let c = new customCounter();
for(let i = 0; i < 101; i++){
	console.log(c.next().value);
}
{% endhighlight %}
(코드보기)[https://codepen.io/nemohoon/pen/vPVmvJ]{:target="_blank"}  
- Iterator를 구현하는 customCounter 클래스  
- 이 클래스는 calculatedVal 이라는 private 프로퍼티가 하나 있음
- 이 프로퍼티는 0으로 초기화 되었고 반복자에서 반환하는 값
- Iterator 인터페이스는 next, return, throw 3개의 메서드가 있지만 코드에서는 필수인 next 만 구현  
- next 메서드는 두개의 프로퍼티가 있는 IteratorResult 타입의 인터페이스 객체를 반환
- next 메서드는 현재 값이 99보다 크면 0으로 초기화 하고 그렇지 않다면 1을 더해 그 값을 반환  
- customCounter 객체를 만들고 메서드는 101번 호출하여 값 출력  

### TypeScript for...of와 for...in 루프  
- for...of는 Javascript에서 지원  
- TypeScript는 for...in이라는 다른 함수도 제공  
- 두 함수의 차이점은 어떤 값으로 반환하는지 여부  
- for...of는 각 요소의 값을 반환  
- for...in은 각 요소의 인덱스를 반환  
{% highlight typescript %}
let sampleArry = ['TypeScript','Angular','Node'];
for(let val of sampleArry){
	console.log(val);
}
for(let val in sampleArry){
	console.log(val);
}
{% endhighlight %}
[코드보기](https://codepen.io/nemohoon/pen/NJOgvZ){:target="_blank"}  
- 두 함수는 반환되는 값은 다르지만 Iterator 인터페이스를 사용하여 객체를 순환  

---

## TypeScript를 이용한 비동기 프로그래밍  
- 비동기 프로그래밍은 이해하기 까다롭지만 반응형 애플리케이션을 만들 때 필수적인 개념  
- Javascript는 단일 스레드 애플리케이션 이므로 해당 스레드에서 모든 작업을 수행하면 느려짐  

### 콜백 함수  
- 비동기 처리가 완료된 후에 호출되는 예약함수  
- 파라미터 형태로 다른 함수에 전달되고 비동기 처리가 완료될 때 호출  

{% highlight javascript %}
setTimeout(callback,2000);
function callback(){
	document.writeln('콜백 함수 호출');
}
{% endhighlight %}

#### TypeScript 콜백  
{% highlight typescript %}
function doWork(clientName:string,callback:(boards:Board[],status:string)=>void):void{
	let response:Board[];
	// id를 기준으로 웹 서비스를 호출하고 보드의 목록을 구한다.
	// 그 다음 필요한 파라미터와 함께 지정한 콜백 함수를 호출한다.
	callback(response,'Success')
}
{% endhighlight %}
- doWork 함수는 두개의 파라미터를 입력으로 사용  
- 하나는 보드를 가져올 클라이언트의 이름  
- 다른 하나는 콜백함수  
- 콜백함수에는 타입도 정의되어 있으며 두개의 파라미터, 보드배열과 웹서비스 호출 상태를 받음  
- 함수가 호출되면 내부적으로 웹 서비스를 호출하고 웹 서비스 응답 후에 콜백함수가 실행  
{% highlight typescript %}
function callback(boards:Board[],status:string){
	if(status != 'Success'){
		console.log(status)
	}else{
		boards.forEach(x=>console.log(x.title));
	}
}
{% endhighlight %}
- 상태를 확인하고 성공이 아니면 상태를 출력  
- 성공이라면 모든 내용을 콘솔에 출력  
- 이런 방식은 애플리케이션이 응답을 처리하는 동안에도 반응형으로 동작  

##### 콜백 함수 정리  
- 인터페이스를 사용하면 좀 더 간결하고 재사용 가능하게 사용 가능  
{% highlight typescript %}
interface ICallBack{
	(boards:Board[],status:string):void;
}
function doWork(clientName:string,callback:ICallBack):void{
	let response:Board[];
	// id를 기준으로 웹 서비스를 호출하고 보드의 목록을 구한다.
	// 그 다음 필요한 파라미터와 함께 지정한 콜백 함수를 호출한다.
	callback(response,'Success')
}
{% endhighlight %}

### 프로미스(Promise)  
- 콜백은 코드가 길고 절차가 복잡  
- 비동기 안에서 비동기 작업을 여러번 호출하려는 경우 콜백 지옥에 빠질 수 있음  
- Typescript tsconfig.json 에서 target 옵션을 ES2015로 해야 사용 가능  
- 프로미스는 비동기 호출에 의해 반환되는 개체로 호출에 의해 최종적으로 반환되는 값을 나타냄  
- 반환되는 값을 임시로 메꿔주는 공간, 실제 값이 반환되면 실제 값으로 채워 짐  
- 콜백 함수와의 차이점은 프로미스는 콜백을 전달하지 않고 API를 사용하여 콜백을 프로미스에 첨부한다는 점  
- 콜백보다 이해하기 쉽고 간결하게 코드 작성이 가능  

#### 프로미스 API  
- 프로미스의 서명은 비동기 작업을 수행하는 함수를 파라미터로 받음  
- 이 함수는 resolve(성공), reject(실패)라는 두개의 파라미터를 사용  
{% highlight typescript %}
let p:Promise<Board[]> = new Promise((resolve, reject) => {
	// ...
	// 임의의 비동기 작업 수행  
	// ...
	if(success){
		resolve(result);
	}else{
		reject(errorMsg);
	}
});
{% endhighlight %}
- 새로운 객체를 만들고 화살표 함수로 전달  
- 화살표 함수는 두개의 파라미터를 사용, 호출에 성공하면 resolve, 실패하면 reject를 호출  
- 변수 p는 제네릭 서명이 있는 Promise 타입, 보드 배열 타입의 프로미스를 할당 -> 프로미스가 성공하면 보드 배열 타입의 결과를 반환  

#### 응답 처리  
- 프로미스에 의해 반환된 응답을 처리  
- then, catch 두 가지 기능을 제공  
- then 함수 -> 프로미스의 성공적인 완료를 위해 호출  
- 프로미스가 resolve될 때 호출하는 또 다른 함수를 지정해야 함  
{% highlight typescript %}
p.then(boards=>boards.forEach(x=>console.log(x.title)));
{% endhighlight %}
- 보드 배열을 가져온 다음 각 요소를 반복하여 보드의 제목을 출력  
- 프로미스가 성공적으로 반환되면 이 메서드가 호출 됨  
{% highlight typescript %}
p.chtch(msg=>console.log(msg));
{% endhighlight %}
- reject 함수가 오류 메시지를 전달했기 때문에 chtch에서 오류 메시지만 출력  
- 여러 프로미스를 연결하는 것도 가능  

#### promise 연결(chaining)  
- 여러 비동기 작업을 차례로 호출하는 경우 여러 호출을 관리할 수 있는 체인을 제공  
- then 함수는 초기 프로미스 객체와 다른 또 다른 새로운 프로미스를 반환  
- 이전 프로미스 작업과 해당 프로미스의 콜백이 완료되었음을 나타냄  
{% highlight typescript %}
let p:Promise<Board[]> = new Promise((resolve, reject) => {
	// ...
	// 임의의 비동기 작업 수행  
	// ...
	if(success){
		resolve(result);
	}else{
		reject(errorMsg);
	}
});

let newPromise = p.then(boards=>boards.forEach(x=>console.log(x.title)));
newPromise.then(result => console.log(result));
{% endhighlight %}
- p가 resolve될 때 then 함수에서 또 다른 doSomeMoreAsyncWork 비동기 작업을 호출  
- then 함수는 새로운 프로미스를 반환  
- 이를 통해 여러 개의 비동기 작업을 연결하고 이전 작업 완료 후 다름 작업이 실행될 수 있도록 함  
- catch 함수를 통해 작업 실패시 실행될 작업을 연결할 수도 있음  
{% highlight typescript %}
let newPromise = p.catch(boards=>doSomeMoreAsyncWork(boards));
newPromise.then(result => console.log(result));
{% endhighlight %}

### Async-await  
- Typescript Async-await는 프로미스를 기반으로 구축되었고 보다 직관적으로 비동기 코드를 작성할 수 있게 해줌  
- 개발자가 동기 방식의 코드를 작성하는 것과 비슷하게 작성하여 가독성이 높고 깨끗한 코드를 제공  
{% highlight typescript linenos %}
async function callAsyncFunction(id:number){
	console.log('비동기 작업 호출 전');
	await doAsyncWork(id);
	console.log('비동기 작업 완료');
	return '성공';
}
function doAsyncWork(id:number){
	// 웹 서비스 호출
}
console.log('비동기 작업 호출 전');
let p = callAsyncFunction(1).then(x=>console.log(x));
console.log('비동기 작업 호출 후');
{% endhighlight %}
- async 함수가 프로미스 위에서 실행되기 때문에 결국은 프로미스를 반환  
- 1행에서 async 키워드가 접두어로 사용되는 callAsyncFunction 함수는 컴파일러에게 함수 내부에 병렬 수행할 부분임을 알려줌  
- 병렬로 수행되길 원하는 메서드는 doAsyncWork와 같이 await 키워드를 접두어로 사용  
- callAsyncFunction 메서드는 문자열을 반환하므로 반환 타입은 컴파일러에 의해 Promise<string>으로 반환, 비동기 작업 수행 후 프로미스를 반환한다는 뜻  
- 만약 callAsyncFunction 함수에서 return이 없었다면 Promise는 void 타입이었을 것  
- callAsyncFunction 함수가 프로미스를 반환하기 때문에 프로미스 API의 then을 사용하여 함수의 반환값을 가져올 수 있음  
[코드보기](https://codepen.io/nemohoon/pen/wOYQOV?editors=1111){:target="_blank"}  

#### Async-await 에러 처리  
- 프로미스와 동일하게 catch 메서드를 사용해 에러 처리  
{% highlight typescript %}
let p = callAsyncFunction(1).then(x=>console.log(x)).catch(errorMsg=>console.log(errorMsg));
{% endhighlight %}

---

## 예제 트렐로 애플리케이션에 기능 추가  



