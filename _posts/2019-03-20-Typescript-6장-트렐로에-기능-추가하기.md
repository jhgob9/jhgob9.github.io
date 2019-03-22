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
[코드보기](https://codepen.io/nemohoon/pen/vPVmvJ){:target="_blank"}  
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

[코드보기](https://codepen.io/nemohoon/pen/wOYQOV?editors=1111){:target="_blank"}  

- async 함수가 프로미스 위에서 실행되기 때문에 결국은 프로미스를 반환  
- 1행에서 async 키워드가 접두어로 사용되는 callAsyncFunction 함수는 컴파일러에게 함수 내부에 병렬 수행할 부분임을 알려줌  
- 병렬로 수행되길 원하는 메서드는 doAsyncWork와 같이 await 키워드를 접두어로 사용  
- callAsyncFunction 메서드는 문자열을 반환하므로 반환 타입은 컴파일러에 의해 Promise<string>으로 반환, 비동기 작업 수행 후 프로미스를 반환한다는 뜻  
- 만약 callAsyncFunction 함수에서 return이 없었다면 Promise는 void 타입이었을 것  
- callAsyncFunction 함수가 프로미스를 반환하기 때문에 프로미스 API의 then을 사용하여 함수의 반환값을 가져올 수 있음  

#### Async-await 에러 처리  
- 프로미스와 동일하게 catch 메서드를 사용해 에러 처리  
{% highlight typescript %}
let p = callAsyncFunction(1).then(x=>console.log(x)).catch(errorMsg=>console.log(errorMsg));
{% endhighlight %}

---

## 예제 트렐로 애플리케이션에 기능 추가  
- http 데이터 : http 호출을 통해 데이터를 가져옴, 프로미스를 사용하여 구현  
- 기능 구현 : 새 작업 추가, 새 하위 작업 추가, 새 보드 추가  
- 데이터 포맷팅 : Angular는 특정 데이터를 실행 중에 포맷팅 할 수 있는 파이프 메카니즘 제공  

### 예제 트렐로에서 프로미스 사용하기  

#### 보드 JSON  
- /src/api/board 경로에 다음과 같이 json 파일 생성  

*/src/api/boardboards.json*  
{% highlight json %}
...
[
  {
    "id":1,
    "title":"Learn TypeScript",
    "task":[
      {
         "id":"1",
         "title":"Basics",
         "taskheaderId":"1",
         "subtask":[
           {
              "id":"1",
              "title":"Types"
           },
           {
              "id":"2",
              "title":"Classes and Interfaces"
           }
         ]
      },
      {
         "id":"2",
         "title":"Advanced",
         "taskheaderId":"2",
         "subtask":[
           {
              "id":"1",
              "title":"Generics"
           },
           {
              "id":"2",
              "title":"Modules"
           }
         ]
      }
    ]
  },
  ....
]
{% endhighlight %}
- http 호출을 사용하여 이 json을 가져온 다음 Board 객체를 홈페이지 컴포넌트에 전달  

### 프로미스 구현  
- http 호출과 미로미스 객체에서 응답을 가져오기 위해 의존성을 추가해야 함  

#### HTTP 의존성 추가  
*/src/app/app.module.ts*  
{% highlight typescript %}
import { HttpClientModule } from '@angular/common/http';
....
@NgModule({
	...
	imports: [
		..
		HttpClientModule,
		...
	],
	...
})
{% endhighlight %}
*/src/app/service/trello.service.ts*  
{% highlight typescript %}
import { HttpClient } from '@angular/common/http';
...
@Injectable()
export class TrelloService {
	...
	constructor(private _http: HttpClient) { }
	...
}
{% endhighlight %}

#### HTTP 호출 로직  
- _http 객체를 사용하여 웹 서비스 호출  

*/src/app/service/trello.service.ts*  
{% highlight typescript linenos %}
...
@Injectable()
export class TrelloService {
	...
	getBoardsWithPromises(): Promise<Board[]> {
		if (this.Boards == undefined) {
			return this._http.get(this._boardUrl).toPromise()
				.then((response: Response) => {
					this.Boards = response as unknown as Board[];
					return response as unknown as Board[];
				});
		} else {
			return Promise.resolve(this.Boards);
		}
	}
}
{% endhighlight %}
- 6행 : 함수의 이름과 반환 타입, Board 타입의 프로미스를 반환  
- 7행 : 보드를 이미 가져온 적이 있는지 확인, 가져온 적이 없다면 http 호출, 가져온 적이 있다면 14행처럼 이미 저장한 보드 배열 객체를 사용하여 프로미스를 반환  
- http 객체를 사용하여 HTTP GET 호출  
- rxjs의 toPromise를 사용하여 응답을 프로미스로 변환  
- Promise API의 then 메서드를 사용해 API 결과를 확인  
- 응답을 확인하고 지역 변수인 보드 배열을 응답으로 설정  

### 홈페이지 컴포넌트에서 호출하기  
- 이전 장에서는 homepage.component.ts 에서 trello.service.ts의 seedData 메서드를 호출해서 데이터를 가져왔음  
- 이것을 프로미스를 사용하는 새로운 메서드로 대체  

*/src/app/homepage/homepage.component.ts*  
{% highlight typescript %}
...
export class HomepageComponent implements OnInit {
	...
	ngOnInit() {
		// this.boards.push(this._trelloService.seedData()); 코드 삭제
		this._trelloService.getBoardsWithPromises()
			.then(boards => this.boards = boards,
				error => this.errorMessage = error as any);
	}
	...
}
{% endhighlight %}
- getBoardsWithPromises 메서드를 호출 한 다음 then 함수를 사용해 응답을 받음  
- then 함수에서 성공과 실패를 함께 처리, 첫 번째 파라미터는 성공, 두 번째 파라미터는 실패  

---

## 기능 구현  
- 작업을 추가하는 기능  
- 특정 작업에서 새 하위 작업을 추가하는 기능  
- 보드, 작업, 하위 작업의 제목을 변경하는 기능  
- 새 작업이 추가되면 홈페이지에 변경 사항을 반영하는 기능  

### 구현 - 새로운 작업 추가  
- 사용자가 새 작업 추가 섹션을 클릭하면 입력 상자에 커서 포커스  
- 사용자가 입력을 완료하고 엔터키를 누르면 보드에 새 작업 추가  
- 엔터키를 누르지 않고 포커스가 아웃되더라도 새 작업 추가  

#### 보드 컴포넌트  
*/src/app/board/board.component.html*  
{% highlight html %}
{% raw %}
...
<section *ngIf="board" id="main">
  ...
  <div class="add-task" (click)="enableAddtask()">
    <input (keyup)="addtaskOnEnter($event)" (blur)="addtaskOnBlur()" [(ngModel)]="addtaskText" placeholder="작업추가" />
  </div>
</section>
{% endraw %}
{% endhighlight %}
- 작업 추가 입력 상자를 캡슐화 하는 div  
- enableAddtask, addtaskOnEnter, addtaskOnBlur 세가지 함수  
- addtaskText 프로퍼티 - 사용자가 입력한 텍스트  

*/src/app/board/board.component.ts*  

##### enableAddtask 함수  
- 새로운 작업을 추가하려는 입력 상자를 찾아 포커스를 설정  
{% highlight typescript %}
...
export class BoardComponent implements OnInit {
	...
	enableAddtask() {
		const input = this.el.nativeElement
			.getElementsByClassName('add-task')[0]
			.getElementsByTagName('input')[0];

		setTimeout(() => input.focus(), 0);
	}
	...
}
{% endhighlight %}

##### addtaskOnEnter 함수  
- 사용자가 입력 상자에 입력을 완료하고 엔터키를 누를 때 호출  
- 보드에 새로운 작업을 추가 하는 것이 목적  
{% highlight typescript %}
...
export class BoardComponent implements OnInit {
	...
	addtaskOnEnter(event: KeyboardEvent) {
		if (event.keyCode === 13) {
			if (this.addtaskText && this.addtaskText.trim() !== '') {
				this.addtask();
			} else {
				this.clearAddtask();
			}
		} else if (event.keyCode === 27) {
			this.clearAddtask();
		}
	}
	...
}
{% endhighlight %}
- 내부적으로 몇 가지 다른 함수를 호출(addtask, clearAddtask)  
- 엔터키(키코드 13)을 누르면 입력 값이 있을 경우 addtask를 호출  
- ESC(키코드 27)을 누르면 작업 취소라고 생각하고 작업을 추가 하지 않음  

##### addtask 함수  
- 보드 배열에 작업을 추가하는 메서드, 해당 내용은 UI에도 반영  
{% highlight typescript %}
...
export class BoardComponent implements OnInit {
	...
	addtask() {
		const newID = this.board.task.length + 1;
		const newtask = {
			title: this.addtaskText,
			id: newID
		} as Task;
		this.board.task.push(newtask);
		this.addtaskText = '';
	}
	...
}
{% endhighlight %}
- 새로운 작업에 대한 작업 번호를 1씩 증가시켜 새로운 id에 할당  
- 새로운 Task 객체를 만들고 id와 title을 설정  
- 새로운 Task 객체는 Board 컴포넌트에 있는 board 객체의 task에 추가  
- @Input, @Output으로 양방향 바인딩 연결을 해두었으므로 추가된 자동으로 UI에 업데이트  

##### clearAddtask 함수  
- addtaskText 프로퍼티의 값을 지워서 UI 입력 상자의 placeholder의 텍스트를 다시 보이게 함  
{% highlight typescript %}
...
export class BoardComponent implements OnInit {
	...
	clearAddtask() {
		this.addtaskText = '';
	}
	...
}
{% endhighlight %}

##### addtaskOnBlur 함수  
- 입력 상자에 텍스트가 있는지 확인, 있으면 addtask 메서드 호출, 없으면 clearAddtask 호출  
{% highlight typescript %}
...
export class BoardComponent implements OnInit {
	...
	addtaskOnBlur() {
		if (this.addtaskText && this.addtaskText.trim() !== '') {
			this.addtask();
		}
		this.clearAddtask();
	}
}
{% endhighlight %}

### 구현 - 새로운 하위 작업 추가하기  
- 새 작업을 추가하는 것과 유사하지만 작업하는 데이터 객체에 차이가 있음  
- 새 하위 작업을 추가하려면 먼저 부모 작업이 무엇인지부터 식별  
- 그 후 추가한 변경 사항을 부모 작업에 반영  

#### 작업 템플릿  
*/src/app/task/task.component.html*  
- 작업 템플릿의 일부로 하위 작업을 추가하기 위한 UI 제공  
{% highlight html %}
{% raw %}
<div class="task" [attr.task-id]="task.id">
	...
	<div class="add-subTask" (click)="enableAddsubTask()">
		<input (keyup)="addsubTaskOnEnter($event)" (blur)="addsubTaskOnBlur()" [(ngModel)]="addsubTaskText" placeholder="Add a new SubTask" />
	</div>
</div>
{% endraw %}
{% endhighlight %}
- 새로운 작업을 추가할 때와 동일한 이벤트를 처리  
- 하위 작업을 작업 목록에 추가하는 것  

*/src/app/task/task.component.ts*  
{% highlight typescript %}
...
export class TaskComponent implements OnInit {
	...
	enableAddsubTask() {
		const input = this.el.nativeElement
			.getElementsByClassName('add-subTask')[0]
			.getElementsByTagName('input')[0];

		setTimeout(() => input.focus(), 0);
	}
	addsubTaskOnEnter(event: KeyboardEvent) {
		if (event.keyCode === 13) {
			if (this.addsubTaskText && this.addsubTaskText.trim() !== '') {
				this.addsubTask();
				this.addsubTaskText = '';
			} else {
				this.clearAddsubTask();
			}
		} else if (event.keyCode === 27) {
			this.clearAddsubTask();
		}
	}
	addsubTaskOnBlur() {
		if (this.addsubTaskText && this.addsubTaskText.trim() !== '') {
			this.addsubTask();
		}
		this.clearAddsubTask();
	}
	addsubTask() {
		this.subTasks = this.subTasks || [];
		const newsubTask = { // 새로운 하위 작업 객체를 생성
			title: this.addsubTaskText // 객체의 프로퍼티에 title을 할당
		} as SubTask;
		let selectedtask: Task;
		for (const v of this.board.task) { // 보드에 있는 모든 작업을 반복
			if (v.id == this.task.id) { // 선택한 작업이 무엇인지 식별
				selectedtask = v;
				break;
			}
		}
		if (selectedtask.subtask == undefined) { // 선택한 작업에 하위 작업 배열이 있는지 확인
			selectedtask.subtask = new Array(); // 하위 작업 배열이 없다면 비어있는 배열로 초기화
		}
		selectedtask.subtask.push(newsubTask); // 하위 작업을 선택된 작업에 추가
		this.subTasks = selectedtask.subtask;
		this.onAddsubTask.emit(newsubTask); // 부모 컴포넌트에 변경 사항을 알리고 새 하위 작업 컴포넌트를 전달하기 위해 이벤트를 발생
	}
}
{% endhighlight %}

### 보드, 작업, 하위 작업의 제목 변경하기  
- 이벤트를 처리하고 각 프로퍼티를 업데이트 하는 로직은 세 컴포넌트 모두 비슷  
- 자세한 코드는 [Github](https://github.com/sachinohri/SampleTrelloApplication/tree/master/Chapter6){:target="_blank"}에서 확인  

#### 보드 제목 변경하기  
- 보드 제목을 클릭하면 제목을 바꿀수 있는 기능 구현  

*/src/app/board/board.component.html*  
{% highlight html %}
{% raw %}
<div *ngIf="board" class="board-title">
	<span [style.display]="editingTitle ? 'none' : ''" (click)="editTitle()">{{ board?.title }}</span>
	<input [style.display]="editingTitle ? '' : 'none' " (keyup)="blurOnEnter($event)" (blur)="updateBoard()" [(ngModel)]="board.title" />
</div>
...
{% endraw %}
{% endhighlight %}
- 사용자가 작업의 제목(span)을 클릭하면 editTitle을 호출  
- keyup, blur 이벤트로 보드 제목을 내부적으로 업데이트 하도록 함  

*/src/app/board/board.component.ts*  
{% highlight typescript %}
...
export class BoardComponent implements OnInit {
	...
	updateBoard() {
		this.editingTitle = false;
		document.title = this.board.title + ' | 일반 작업 관리자';
		this._trelloService.Boards
			.find(x => x.id == this.board.id) // 선택한 보드를 식별
			.title = this.board.title; // 보드 제목을 업데이트
	}
	...
}
{% endhighlight %}
*/src/app/board/board.component.html*  
{% highlight html %}
{% raw %}
[(ngModel)]="board.title"
...
{% endraw %}
{% endhighlight %}
- board 객체의 title 프로퍼티를 보드 컴포넌트에 직접 바인딩되어 있음  

#### 홈페이지 변경사항 반영하기  
- Homepage 컴포넌트는 Angular 바인딩을 사용하여 보드 이름과 함께 작업의 수를 보여줌  
- Angular 바인딩은 UI에 바인딩된 모든 프로퍼티를 추적하며 변경이 발생되면 새 값을 전파(board.component.ts 의 updateBoard 메소드)  

---

## Angular의 데이터 포맷팅  
- Angular는 파이프라는 기능을 제공하여 사용자 인터페이스에 따라 데이터를 포맷팅 가능  

### 내장 파이프  
- 내장 파이프를 사용하면 별도의 import 없이 사용  
- 파이프 기호 뒤에 원하는 함수명 입력  
- task.title 프로퍼티에 uppercase 파이프 함수를 사용  

*/src/app/task/task.component.html*  
{% highlight html %}
{% raw %}
...
<h4 [style.display]="editingtask ?'none' : '' " (click)="edittask()">{{task.title | uppercase}}</h4>
...
{% endraw %}
{% endhighlight %}
- 다음은 통화 파이프 예시  
{% highlight html %}
{% raw %}
{{book.price | currency:'USD':true:'1.1-2'}}
{% endraw %}
{% endhighlight %}

### 사용자 정의 파이프  
- Angular는 사용자 정의 파이프를 만들 수 있는 인터페이스를 제공  
- 새로운 서비스, 컴포넌트를 만드는 것과 매우 유사  

#### 트렐로용 정렬 파이프
*/src/app/shared/customsort_pipe.ts*  
{% highlight typescript linenos %}
import {Pipe, PipeTransform} from '@angular/core';
import { Task } from '../model/task';
@Pipe({
	name: 'customSort'
})
export class CustomSort implements PipeTransform {
	transform(value: Task[], sort: boolean): Task[] {
		if (sort) {
			return value.sort(this.compare);
		} else {
			return value;
		}
	}
	private compare(a: any, b: any) {
		if (a.title < b.title) {
			return -1;
		}
		if (a.title > b.title) {
			return 1;
		}
		return 0;
	}
}
{% endhighlight %}
- 1행 : 파이프 모듈을 import  
- 3행 : @Pipe 데코레이터를 사용해 클래스 정의  
- 6행 : PipeTransform 인터페이스 구현  
- 7행 : PipeTransform 인터페이스는 하나의 메서드 transform만 노출  
	- 첫번째 파라미터는 파이프 함수에 전달되는 값  
	- 두번째 파라미터는 정렬을 할지 여부  
	- 반환 타입은 내용을 재정렬하기만 했으므로 작업 배열 타입 그대로  
	- 파이프 함수 내부의 로직은 객체 배열을 문자열 기준으로 정렬하는 표준적인 코드  

#### 파이프 의존성 추가  
*/src/app/app.module.ts*  
{% highlight typescript %}
...
import { CustomSort } from './shared/customsort_pipe';
...
@NgModule({
	declarations: [
		...
		CustomSort
	],
	...
})
...
{% endhighlight %}

#### 사용자 정의 파이프 사용하기  
- 내장 파이프를 사용하는 것과 동일  
*/src/app/board/board.component.html*  
{% highlight html %}
{% raw %}
...
<section *ngIf="board" id="main">
	<div *ngFor="let task of board.task | customSort:true" class="sortable-task">
		...
	</div>
	...
</section>
{% endraw %}
{% endhighlight %}

---

## Angular 의존성 주입 이해하기  
- 의존성 주입은 Angular의 중요한 개념 중 하나  
- Angular 애플리케이션을 쉽게 테스트할 수 있는 주요 이유  
- 크렐로 애플리케이션은 다음과 같이 클래스 내에 의존성을 주입  
{% highlight typescript %}
constructor(private _route: ActivatedRoute, private _trelloService: TrelloService) { } // board.component.ts
constructor(private _http: HttpClient) { } // trello.service.ts
{% endhighlight %}

### 의존성 관리  
- Angular는 특정 클래스의 의존성을 판별하면 객체 생성 시 해당 의존성을 클래스에 주입  
- trello의 경우 
	- Angular는 HttpClient 모듈이 어디 있는지 확인하고 app.module 파일에서 찾음  
	- 그런 다음 새로운 HTTP 인스턴스를 만들거나 기존 HTTP 인스턴스를 트렐로 서비스의 생성자에 전달  
- 개발자는 클래스의 의존성을 확인하고 생성하는 책임에서 벗어날 수 있음  
- 의존성을 고려하지 않음으로써 보다 쉽게 테스트할 수 있는 코드를 작성  








