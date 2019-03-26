---
title: "[Rxjs] 2부 Chapter 03. Observable"
date: 2019-03-22 10:00:00 +0900
categories: rxjs
---

# 2부 Chapter 03. Observable

지금까지 다른 Observable의 특징

- 모든 데이터는 Observable 인스턴스로 만들 수 있음
- Observable은 읽기 전용(read-only)
- Observable은 리액티브 함
- Observable은 불변 객체

## 1.1. 함수와 Observable

- 함수와 Observable의 쓰임새를 통해 Observable의 특징을 알아봄

{% highlight javascript %}
{% raw %}
// foo 라는 함수를 사용하려고 하면 foo라는 이름으로 함수를 정의
function foo(value){
	console.log(`I am function ${value}`)
	return value+1;
}

// 실제 함수를 사용 할 때는 아래와 같이 호출
foo(afterAjaxResult);

// 넘겨주어야 할 파라미터(afterAjaxResult) 가 존재하지 않거나 아직 준비가 안되었으면 준비될 때까지 함수 호출을 지연
const xhr = new XMLHttpRequest();
xhr.onload = function(){
	afterAjaxResult = JSON.parse(xhr.responseText);
	const result = foo(afterAjaxResult);
	console.log(result);
}
{% endraw %}
{% endhighlight %}

- `정의`한 함수는 `호출`이라는 과정을 통해 결과 데이터를 받는 시점을 `지연` 할수 있음
- 개발자가 원하는 `시점`에 데이터를 얻음
- 함수에서 외부 변수를 사용하지 않는다면 여러번 호출해도 매번 `동일한 값`을 받음

- Observable도 함수와 유사
- 데이터를 Observable로 만드는 일종의 정의 작업을 함

{% highlight typescript %}
const { interval } = rxjs;
const number$ = interval(1000);
{% endhighlight %}

- Observable 데이터를 받을 준비가 되었다면 구독 과정을 거침
- Observable은 subscribe 메소드를 호출하기 전까지 데이터를 전달하지 않음

{% highlight typescript %}
// Observable 데이터가 전달되지 않음
// subscribe가 호출되는 순간부터 데이터가 전달 됨
number$.subscribe(value=>console.log(value));
{% endhighlight %}
[코드보기](https://codepen.io/nemohoon/pen/NJmxBo){:target="_blank"}

- Observable도 `정의`를 하고 `구독`이라는 과정을 통해 데이터를 받을 시점을 `지연`
- 여러번 구독하더라도 정의부가 매번 호출되어 `동일한 값` 데이터 결과를 반환

- 시간 차를 두고 2번 구독하여 각각 데이터를 전달하는 예

{% highlight typescript %}
{% raw %}
const { interval } = rxjs;
const number$ = interval(1000);

// subscribe 메서드를 호출하여 구독
number$.subscribe(value=>console.log(`첫 번째 ${value}`));
// 2초 후에 subscribe 메서드를 호출
setTimeout(()=>{
	number$.subscribe(value=>console.log(`두 번째 ${value}`));
},2000);
{% endraw %}
{% endhighlight %}

- 함수와 다른 점
	- 함수는 호출 시 반환값이 한 개, Observable은 Observer.next를 호출하여 구독 이후 여러 개의 데이터를 전달 가능
	- 함수는 개발자가 데이터를 요청하는 형태(Pull), Observable은 Observable이 데이터를 개발자에게 전달하는 형태(Push)

---

## 1.2. Promise 와 Observable

- Promise를 통해 Observable의 특징을 살펴봄

### 1.2.1 Lazy

- 성공하면 1을 전달, 오류가 발생하면 'error promise'라는 문자열을 전달하는 Promise 예제
- Promise도 데이터를 Promise로 만드는 일종의 정의 작업을 함

{% highlight typescript %}
const promise = new Promise((resolve,reject)=>{
	try{
		resolve(1)
	} catch(e){
		reject('error promise');
	}
});
{% endhighlight %}

- Promise의 상태를 확인하고자 할 때는 then이라는 과정을 거침
- then은 성공(resolve)와 실패(reject)했을 때의 상태를 처리할 수 있는 함수를 받음

{% highlight typescript %}
promise.then(
	value => console.log(value),
	error = console.error(error)
);
{% endhighlight %}

- Promise도 Observable과 마찬가지로 `정의`와 `호출` 부분으로 나눠짐
- 상태의 결과 또한 개발자의 호출에 의해 전달되지 않고 Promise에 의해 전달
- Promise도 Observable과 같이 Push 방식으로 데이터를 전달
- Promise도 함수나 Observable과 같이 then을 호출할 때마다 `동일한 값`을 받을 수 있음
- 하지만 동작 방식은 함수나 Observable과 다름
	- Promise는 Observable 함수와 다르게 정의부가 생성 시 딱 한번만 호출
	- 상태(resolve,reject)가 결정되면 변경되지 않음
	- Promise의 then은 호출될 때 이미 결정된 상태값만을 반환하기 때문에 동일한 값을 반환
	- 사용자에게는 Lazy 하게 동작하는 것 처럼 보이지만 실제로는 그렇지 않음

{% highlight typescript %}
{% raw %}
const promise = new Promise((resolve,reject)=>{
	console.log('프로미스 생성');
	try{
		resolve(1)
	} catch(e){
		reject('error promise');
	}
});

// then은 결정된 상태값만 반환
promise.then(
	value => console.log(`첫 번째 promise ${value}`),
	error = console.error(`첫 번째 promise ${error}`)
);

promise.then(
	value => console.log(`두 번째 promise ${value}`),
	error = console.error(`두 번째 promise ${error}`)
);

// 콘솔 출력 결과
// 프로미스 생성
// 첫 번째 promise 1
// 두 번째 promise 1
{% endraw %}
{% endhighlight %}

- 위의 코드를 Observable로 작성

{% highlight typescript %}
{% raw %}
const { Observable } = rxjs;
const number$ = Observable.create(observer=>{
	console.log('creat observable');
	try{
		observer.next(1);
	} catch(e){
		observer.error('error observable')
	} finally{
		observer.complete();
	}
});

// 구독
number$.subscribe(
	value => console.log(`첫 번째 observable ${value}`),
	error => console.log(`첫 번째 observable ${error}`)
);
number$.subscribe(
	value => console.log(`두 번째 observable ${value}`),
	error => console.log(`두 번째 observable ${error}`)
);
{% endraw %}
{% endhighlight %}
[코드실행](https://codepen.io/nemohoon/pen/WmWwLx){:target="_blank"}

- Observable로 만든 예제에서는 creat observable가 콘솔에 2번 출력
- Observable은 Promise와 다르게 next,error,comolete의 상태를 가짐
- Observable은 여러 개의 데이터를 보낼 수 있기 때문에 complete 상태가 존재

### 1.2.2. Cancellation

- Promise는 취소를 위한 인터페이스가 없지만 Observable은 취소를 위한 인터페이스를 제공

{% highlight typescript %}
{% raw %}
const promise = new Promise((resolve,reject)=>{
	try{
		let value = 0;
		setInterval(()=>{
			console.log(`is going ${value}`);
			resolve(value++);
		},1000);
	} catch(e) {
		reject('error promise');
	}
});

promise.then(
	value => console.log(`promise value ${value}`)
);
{% endraw %}
{% endhighlight %}
[코드실행](https://codepen.io/nemohoon/pen/EMJyzq){:target="_blank"}

- promise가 성공하거나 실패 하더라도 계속적으로 interval이 호출 됨

- 동일한 기능을 Observable로 만들기

{% highlight typescript %}
{% raw %}
const { Observable } = rxjs;
const obj$ = new Observable(observer=>{
	let id;
	try {
		let value = 0;
		id = setInterval(()=>{
			console.log(`is going ${value}`);
			observer.next(value++);
		},1000);
	} catch(e) {
		observer.error(e);
	}
	// unsubscribe 될 때 호출되는 함수르 반환 teardown
	return ()=>{
		clearInterval(id);
		console.log('cancelled');
	}
});

const subscription = obj$.subscribe(
	value => console.log(`observable value ${value}`)
);

// 3초 후에 observable의 구독을 취소
setTimeout(()=>subscription.unsubscribe(),3000);
{% endraw %}
{% endhighlight %}
[코드실행](https://codepen.io/nemohoon/pen/aMxmzb){:target="_blank"}

- Promise와는 다르게 teardown 함수(자원을 해지하는 함수)를 반환하는 코드를 추가
- Observable은 subscription의 구독이 해제되거나 에러가 발생한 경우, 작업이 완료된 경우에 자동으로 teardown 함수를 호출하여 Observable을 중지
- Promise도 성공이나 실패코드 바로 다음에 interval을 중지시키는 코드를 넣으면 취소할수 있지만 별도의 취소 인터페이스가 없기 때문에 번거로움

---

## 1.3. 함수 vs Observable vs Promise

| 구분 | 함수 | Observable | Promise |
| | | | |
| 정의 | 함수 선언 | Observable 객체 생성 | Promise 객체 생성 |
| 호출 | 함수 호출 | Observable.subscribe | Promise.then |
| 호출 시 정의부 실행 여부 | 매번 정의부 실행 | 매번 정의부 실행 | 생성시 단 한번 호출 |
| 지연(lazy) 기능 여부 | O | O | X<br>(정해진 상태값만 호출) |
| 데이터 | 한 개 | 여러 개 | 한 개 |
| 에러처리 지원 | 별도로 없음 | error 상태 | reject 상태 |
| 취소 지원 | X | O | X |
| 전달 방식 | Pull | Push | Push |

---

## 1.4. Pull 과 Push가 가지는 의미

- Pull 방식
	- 개발자가 주도적으로 데이터의 상태를 확인하고 관리
	- 개발자가 데이터의 변경 여부를 주기적으로 확인해야 하며 개발자가 많은 것을 고려해서 처리
- Push 방식
	- 데이터를 전달해주는 주체의 상태를 신경쓰지 않아도 됨
	- 내가 관심있는 데이터에 한정하여 관리 가능
	- 데이터 거부권을 가지고 있음

### 1.4.1. Push 방식은 데이터 처리에 따른 오류 처리가 필요 없다

- Push 방식은 '데이터가 존재한다'는 대전제 아래 데이터가 전달되었을 때 처리를 실행
- 데이터가 없을 경우에 데이터 재조회, 다른 데이터 제공 등 별도의 오류 처리를 할 필요가 없음

### 1.4.2. Push 방식은 리액티브하다

- Push 방식은 데이터가 전달되었을 때 바로 처리, Pull 방식보다 빠르게 변경에 반응하는 것이 가능
- 데이터의 변경에 따라 자동으로 다른 객체에 데이터를 전달
