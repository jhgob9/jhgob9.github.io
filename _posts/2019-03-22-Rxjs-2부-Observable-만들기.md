---
title: "[Rxjs] 2부 Chapter 02. Observable 만들기"
date: 2019-03-22 10:00:00 +0900
categories: rxjs
---

# 2부 Chapter 02. Observable 만들기  

## 1.1. Observable 생성자를 이용하여 Observable 만들기  
- Observable은 클래스이므로 new 키워드를 이용하여 객체를 생성  
- Observable은 rxjs 네임스페이스에 있음  
- 객체의 첫 번째 파라미터로는 fucntion subscribe(observer) 함수를 받을 수 있음  
- subscribe 함수는 observer를 파라미터로 받는 함수  
{% highlight typescript %}
const { Observable } = rxjs;
const number$ = new Observable(function subscribe(observer){
	observer.next(1);
	observer.next(2);
	observer.next(3);
});
number$.subscribe( v => console.log(v) );
{% endhighlight %}
[코드실행](https://codepen.io/nemohoon/pen/wOQRLW){:target="_black"}  
**Observable.create 정적 메소드**  
Observable 생성자의 별칭으로 Observable.create를 제공  
{% highlight typescript %}
const { Observable } = rxjs;
const number$ = Observable.create(function subscribe(observer){
	observer.next(1);
	observer.next(2);
	observer.next(3);
});
number$.subscribe( v => console.log(v) );
{% endhighlight %}

---

## 1.2. Observable 구현시 고려해야 할 것들  
- Observable은 항상 정상적으로 데이터를 전달하지는 않음  
- Observable은 자신이 구독하고 있는 observer에게 데이터를 전달하기도 하지만 지금의 상태도 전달해야 함  
- 이런 경우를 처리하기 위해 Observer는 next(전달), error(에러), complete(완료) 메소드를 가지고 있음  

### 1.2.1. 에러발생  
{% highlight typescript %}
const { Observable } = rxjs;
const number$ = new Observable(function subscribe(observer){
	try{
		observer.next(1);
		observer.next(2);
		// 강제 에러 생성
		throw new Error('데이터 전달 도중 에러 발생');
		observer.next(3);
	} catch(e) {
		observer.error(e);
	}
});
number$.subscribe({
	next: v => console.log(v),
	error: e => console.error(e)
});
{% endhighlight %}  
[코드실행](https://codepen.io/nemohoon/pen/GewzNZ){:target="_black"}  
- Observer.error가 호출되면 Observer와의 구독을 자동으로 해지  

### 1.2.2. 데이터 전달이 완료 된 경우  
{% highlight typescript %}
const { Observable } = rxjs;
const number$ = new Observable(function subscribe(observer){
	try{
		observer.next(1);
		observer.next(2);
		observer.next(3);
		observer.complete(); // 데이터 전달이 완료 되었다고 알려줌
	} catch(e) {
		observer.error(e);
	}
});
number$.subscribe({
	next: v => console.log(v),
	error: e => console.error(e),
	complete: () => console.log('데이터 전달 완료') // 데이터 전달 완료시 메세지 출력
});
{% endhighlight %}
[코드실행](https://codepen.io/nemohoon/pen/moQvwP){:target="_black"}  
- Observer.complete가 호출되면 Observer와의 구독을 자동으로 해지  

### 1.2.3. 구독 해제  
- 앞선 예제들은 유한한 데이터를 전달하는 Observable이기 때문에 구독이 해제될 때 자원을 해지할 필요가 없음  
- 엘리먼트에 이벤트 핸들러를 붙여서 데이터를 계속 전송받는 경우, interval을 통해 데이터를 지속적으로 전달받는 경우에는 반드시 자원을 해제해야함  
- Observable의 구독 해제는 subscription의 unsubscribe를 호출  
- unsubscribe에서는 Observable 생성시 할당한 자원도 함께 제거  
- 개발자는 Observable 구현시 구독이 해지되는 순간 의도적으로 자원을 해제  

- Observable 생성자의 첫번째 파라미터로 전달되는 subscribe 함수에서 자원을 해제하는 함수를 반환  
- 이 함수가 subscription의 unsubscribe 호출시 실행되는 함수, 이를 통해 자원을 해제  

{% highlight typescript %}
const { Observable } = rxjs;
const interval$ = new Observable(function subscribe(observer){
	const id = setInterval(function(){
		observer.next(new Date().toString());
	},1000);
	return function(){ // 자원을 해지하는 함수
		console.log('interval 제거');
		clearInterval(id);
	};
});
const subscription = interval$.subscribe(v => console.log(v));

//5초 후 구독을 해제
setTimeout(function(){
	subscription.unsubscribe();
},5000)
{% endhighlight %}
[코드실행](https://codepen.io/nemohoon/pen/gEQqzg){:target="_black"}  

---

## 1.3. rxjs 네임스페이에 있는 생성 함수로 Observable 만들기  
- RxJS 에서는 Observable 생성시 반복적으로 고려해야 할 것들에 대한 처리를 미리 해놓은 함수를 rxjs 네임스페이스에서 제공  
> 가급적 Observable을 생성할 때는 rxjs 네임스페이스에서 제공하는 생성 함수를 이용  

### 1.3.1. of  
- 각각의 단일 데이터 값을 전달하는 Observable을 만들 때 사용  
- 모든 값이 전달되면 complete을 호출하고 구독을 자동으로 해제  
> of(value: ...T, scheduler: Scheduler): Observable<T>  
{% highlight typescript %}
const { of } = rxjs;

of(10,20,30)
.subscribe({
	next: console.log,
	error: console.error,
	complete: () => console.log('완료')
});
{% endhighlight %}
[코드실행](https://codepen.io/nemohoon/pen/xBQMBx){:target="_black"}  

### 1.3.2. range  
- 범위 내 수의 값들을 전달하는 Observable을 만들 때 사용  
- 범위(start~count) 내의 값을 1씩 증가시킨 숫자 데이터를 전달  
- 모든 값이 전달되면 complete을 호출하고 구독을 자동으로 해제  
> range(start: number, count: number, scheduler: Scheduler): Observable  
{% highlight typescript %}
const { range } = rxjs;

range(1,10)
.subscribe({
	next: console.log,
	error: console.error,
	complete: () => console.log('완료')
});
{% endhighlight %}
[코드실행](https://codepen.io/nemohoon/pen/bZQzPv){:target="_black"}  

### 1.3.3. fromEvent  
- 브라우져에서 발생하는 Event를 Observable을 만들 때 사용  
> fromEvent(target: EventTargetLike, eventName: string, options: EventListenerOptions, selector: SelectorMethodSignature<T>): Observable<T>  
{% highlight typescript %}
const { fromEvent } = rxjs;

const click$ = fromEvent(document, 'click');
const subscription = click$.subscribe({
	next: v => console.log('click 이벤트 발생'),
	error: e => console.error(e),
	complete: () => console.log('완료')
});
{% endhighlight %}
[코드실행](https://codepen.io/nemohoon/pen/zbMbYO){:target="_black"} 
- fromEvent로 만든 Observale은 이벤트 핸들러를 등록하여 자원을 사용하고 있는 경우  
- 이벤트 핸들러를 제거하기 전까지 자원을 계속 사용  
- 사용자가 의도적으로 자원을 제거하지 않는 이상 Observable은 계속 완료되지 않음  

### 1.3.4. from  
- 배열, 배열 같은 객체, iterable 객체, Promise등 거의 모든 데이터를 Observable을 만들 때 사용  
> from(ish: ObservableInput<T>, scheduler: Scheduler): Observable<T>  

#### (1) 배열, 배열 같은 객체  
- 배열과 arguments나 nodelist와 같이 배열 형태를 가진 객체를 Observable을 만들 때 사용  
- 모든 값이 전달되면 complete을 호출하고 구독을 자동으로 해제  
{% highlight typescript %}
const { from } = rxjs;

from([10,20,30])
.subscribe({
	next: v => console.log(v),
	error: e => console.error(e),
	complete: () => console.log('완료')
});

const arguments$ = (function(){
	return from(arguments);
})(1,2,3).subscribe({
	next: v => console.log(v),
	error: e => console.error(e),
	complete: () => console.log('완료')
});
{% endhighlight %}
[코드실행](https://codepen.io/nemohoon/pen/VRVNpG){:target="_black"}  

#### (2) Iterable 객체  
- Map, Set, String 과 같은 Iterable 객체를 Observable을 만들 때 사용  
- 모든 값이 전달되면 complete을 호출하고 구독을 자동으로 해제  
{% highlight typescript %}
const { from } = rxjs;

const map$ = from(new Map([[1,2],[2,4],[4,8]]));
map$.subscribe({
	next: v => console.log(v),
	error: e => console.error(e),
	complete: () => console.log('완료')
});
{% endhighlight %}
[코드실행](https://codepen.io/nemohoon/pen/vPQMpb){:target="_black"}  

#### (3) Promise 객체  
- Promise를 Observable을 만들 때 사용  
- Promise가 성공(resolve)하면 데이터를 전달하고 complete을 호출하고 구독을 자동으로 해제  
- Promise가 실패(reject)하면 데이터를 전달하고 error를 호출하고 구독을 자동으로 해제  
{% highlight typescript %}
const { from } = rxjs;

const success$ = from(Promise.resolve(100));
success$.subscribe({
	next: v => console.log(v),
	error: e => console.error(e),
	complete: () => console.log('완료')
});

const fail$ = from(Promise.reject('에러'));
fail$.subscribe({
	next: v => console.log(v),
	error: e => console.error(e),
	complete: () => console.log('완료')
});
{% endhighlight %}
[코드실행](https://codepen.io/nemohoon/pen/oVQOda){:target="_black"}  

### 1.3.5. interval  
- 지정된 시간 간격마다 0부터 1씩 증가하는 데이터를 만드는 Observable을 만들 때 사용  
- 에니메이션과 같이 일정 간격의 시간마다 데이터 변경이 일어나는 작업을 할 때 주로 사용  
> interval(period: number, scheduler: Scheduler): Observable  
{% highlight typescript %}
const { interval } = rxjs;

interval(1000)
.subscribe({
	next: v => console.log(v),
	error: e => console.error(e),
	complete: () => console.log('완료')
});
{% endhighlight %}
[코드실행](https://codepen.io/nemohoon/pen/MxzRPo){:target="_black"}  
- fromEvent와 같이 사용자가 의도적으로 구독을 해제하거나 에러가 발생하지 않는 이상 complete이 자동으로 호출되지 않음  
- 영원히 데이터를 전달하는 Observable  

---

## 1.4. 특별한 용도의 Observable 만들기  
- 간단한 역할을 하는 Observable을 반환하는 홤수  
- Observable의 데이터나 조건에 따라 Observable의 상태를 변경하려고 할 때 유용  

### 1.4.1. empty  
- Observable 완료 상태를 전달하는 Observable을 생성  
- 항상 동일한 Observable 인스턴스를 반환  
> empty(scheduler: Scheduler): Observable  
- empty 함수 내부의 코드는 다음과 같음  
{% highlight typescript %}
Observable.creat(observer => {
	observer.complete();
})
{% endhighlight %}
- 바로 complete 상태를 전달하고 구독을 해지  
- 상황에 따라서 현재 Observable이 완료되었단 것을 사용자에게 알려줄 때 사용  
{% highlight typescript %}
const { empty, of } = rxjs;
const { map } = rxjs.operators;

of(1,-1,3).pipe(
	map( number => number<0?empty():number )
)
.subscribe({
	next: v => console.log(v),
	error: e => console.log(e),
	complete: () => console.log('완료')
});
{% endhighlight %}
[코드실행](https://codepen.io/nemohoon/pen/JzeqPw){:target="_black"}  

### 1.4.2. throwError  
- Observable 에러 상태를 전달하는 Observable을 생성  
- 항상 동일한 Observable 인스턴스를 반환  
> throwError(error: any, scheduler: Scheduler): Observable  
- throwError 함수 내부의 코드는 다음과 같음  
{% highlight typescript %}
const error; // 전달된 error 데이터
Observable.creat(observer => {
	observer.error(new Error(error));
})
{% endhighlight %}
- empty와 마찬자기로 현재 Observable의 상태를 변경하고자 할 때 사용  
- 사용자에게 에러가 발생했다는 것을 알려주기 위해 에러 상태를 전달하고 전달하고 구독을 해지  
{% highlight typescript %}
const { throwError, of } = rxjs;
const { map } = rxjs.operators;

of(1,-1,3).pipe(
	map(number => number < 0 ? throwError('number는 0보다 커야한다') : number)
)
.subscribe({
	next: v => console.log(v),
	error: e => console.error('d',e),
	complete: () => console.log('완료')
});
{% endhighlight %}
[코드실행](https://codepen.io/nemohoon/pen/Gewaqo){:target="_black"}  

### 1.4.3. never  
- 아무것도 하지 않는 Observable을 생성  
- 항상 동일한 Observable 인스턴스를 반환  
> never(scheduler: Scheduler): Observable  
- never 함수 내부의 코드는 다음과 같음  
{% highlight typescript %}
const error; // 전달된 error 데이터
Observable.creat(observer => {
})
{% endhighlight %}
- empty, throwError와 다르게 Observable의 상태를 변경하지 않음  
- 현재의 전달된 데이터를 전달하지 않고자 할 때 사용  
{% highlight typescript %}
const { NAVER, of } = rxjs;
const { map } = rxjs.operators;

of(1,-1,3).pipe(
	map(number => number < 0 ? NAVER : number)
)
.subscribe({
	next: v => console.log(v),
	error: e => console.error('d',e),
	complete: () => console.log('완료')
});
{% endhighlight %}
[코드실행](https://codepen.io/nemohoon/pen/PLxvmG){:target="_black"}  