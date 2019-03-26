---
title: "[TypeScript] 7장 트렐로 애플리케이션 테스트"
date: 2019-03-25 10:40:00 +0900
categories: typescript
---

# 7장 트렐로 애플리케이션 테스트

이번 장에서 다룰 내용  
- 수동 테스트와 비교하여 테스트 케이스를 작성하는 것이 어떤 의미를 갖는지 살펴보기
- 테스트 기반 개발에 대해 알아보기
- 예제 트렐로 애플리케이션에 테스트 케이스를 구현
- 테스트 케이스 작성과 실행을 위해 자스민(Jasmine)과 카르마(Karma)를 살펴보기

---

## 기본개념  

- 상용화 직전, 상용화를 마친 직후에 심각한 버그를 발견했다면 긴급하게 해결하기 위해 얼마나 많은 시간을 소요해야 할까?  
- 개발자와 개발팀이 철저한 테스트를 했는데도 발생하는 버그는 어떻게 테스트를 피해 갔을까?  
- 코드를 재검토 하고 버그가 있는 시나리오를 발견  
- 시나리오를 테스트 케이스에 추가  
- 6개월 주기로 사이클 반복  

### 테스트의 어려움  

- 개발자가 자신의 로직에 추가의 코드를 작성하는 것이 어려운 이유는 테스팅 프레임워크를 설정하는 것이 너무 복잡했기 때문  
- Angular는 의존성 주입을 통해 개발자가 테스트 코드를 쉽게 작성할 수 있도록 개선  
- 모든 모듈이 느슨하게 결합되도록 하였고 프레임워크에서 런타임에 의존성을 주입하는 것을 지원하도록 설계  
- 각각의 컴포넌트에 대해 독립적으로 의존성을 흉내내어 테스트하는 것이 가능  

### 테스트 기반 개발  

- 테스트 케이스를 작성하는 시기에 관련한 두가지 의견
	- 테스트 주도 개발
	- 코드를 구현할 때 테스트를 작성
- 테스트 주도 개발(test-driven development - TDD)
	- 테스트를 먼저 작성한 다음 테스트를 통과하는 코드를 작성  
	- 코드가 테스트를 통과하면 리팩토링을 통해 코드를 최적화
	- 테스트를 통해 코드가 깨지지 않았다는 것을 재확인
	- 모든 경우를 생각해보게 되고 결과적으로 모든 경우를 안전하게 처리하는 코드를 작성
	- 요구사항이 자주 변경되는 경우에는 큰 부담이 됨
	- 요건이 변경될 때마다 테스트를 만들어야 하기 때문
- 코드를 구현할 때 테스트
	- TDD와의 차이는 테스트 케이스를 기준으로 개발하지 않는다는 점
	- 단점은 실패 극복의 기회를 놓칠수 있음
	- 반면 요구사항이 변경되어도 추가 작업을 하지 않아도 됨

### 단위 테스트 vs 종단간 테스트  

- Angular의 두가지 테스팅 기법
	- 단위 테스트
	- 종단간 테스트

#### 단위 테스트  

- 의존성과 상관없이 특정 코드의 정확성을 확인  
- 단일 책임의 원칙을 따르는 클래스이거나 컴포넌트가 대상  
- 클래스 내부에 있는 코드만 테스트, 해당 클래스 가진 의존성은 테스트 하지 않음  
- 외부 웹서비스 같은 의존성이 있는 경우 해당 부분은 임의로 구현하고 클래스 자체의 논리를 테스트  
- 클래스의 독립된 동작을 확인할 수 있으므로 구현된 논리에 대한 확신을 얻을 수 있음  
- 단위 테스트의 주요 기능  
	- 단일코드 : 전체 모듈이 아닌 작은 코드에 대해서만 초점  
	- 신속성 : 빠르게 실행되고 신속하게 작성, 복잡하지 않아야 함  
	- 의존성 제거 : 테스트할 코드를 변경시킬 수 있는 의존성이 없어야 함  
	- 모든 코드 경로 확인 : 해당 코드에 대해서는 모든 부분을 포함  
	- 한 가지만 확인 : 한 번에 여러 개를 확인하기보다는 단 하나만을 확인  
- 전체 기능보다 테스트할 코드에만 초점을 맞추다보니 명확하고 간결한 테스트 가능  

#### 종단간(end-to-end) 테스트  

- 전체 시스템을 함께 테스트  
- 코드의 각 라인에 대해 신경쓰지 않으며 코드 커버리지도 신경 쓰지 않음  
- 최종 사용자가 동작하는 것을 기존으로 테스트 케이스를 작성  
- 의존성 위에서 동작하는 방식에 중점  
- 단위 테스트의 다음 단계로 종단간 테스트를 검토  
- 사용자에게는 해당 코드가 웹서비스나 DB호출 또는 다른 코드들과 어떻게 동작하는지가 중요
- 종단간 테스트의 단점  
	- 느림 : 시스템 전체를 테스트하기 때문에 단위 테스트와 비교하여 상대적으로 느리고 작성하는데 오래 걸림
	- 복잡 : 사용자 관점에서 전체적인 테스트에 중점으 두고 있기 때문에 단위 테스트와 비교해 복잡

### 테스트 구조  

- 준비(Arrange) : 단위 테스트의 초기 상태를 작성, 테스트를 하기 위해 필요한 설정(클래스 초기화, 데이터 설정)
- 작동(Act) : 실제로 행동하고 시험을 치르는 단계, 클래스의 함수를 호출하거나 일부 프로퍼티를 변경
- 확인(Assert) : 검증 단계, act 단계에서 코드의 상태를 변경해야 한다면 assert 단계에서 상태 변경이 예상대로 되었는지 확인

#### 모의(Mocking)

- 의존성을 임의로 구현하여 독립된 코드를 테스트하는 개념
- 대부분의 코드는 어떤 식으로든 외부 코드와 관련이 있기 때문에 단위 테스트에서 중요한 개념
- 코드와 코드로직에 대해 의존성에 영향을 받지 않고 테스트하고자 한다면 의존성을 모의할 필요가 있음  
- 모의는 의존성에 더미 객체를 생성, 더미 객체를 코드에 전달

---

## 테스트 도구  

- 자스민(Jasmine)과 카르마(Karma) 
- Angular CLI를 사용하면 자동으로 의존성을 추가 
- 테스트 작성을 위해 필요한 초기 설정, 샘플 테스트도 생성 

### 자스민(Jasmine) 

테스트 케이스를 작성하고 관리하는 여러 기능을 제공  

- describe()
	- 테스트 집합의 컨테이너 역할
	- 자스민은 describe 함수를 테스트 케이스의 루트로 인식
	-케이스 실행의 시작지점
- beforeEach()
	- 테스트 케이스의 준비(arrange) 역할
	- 테스트 케이스를 설정하는 필요한 공통 코드를 작성
- it()
	- 코드에서 작동(act)하는 부분
	- describe 함수 내에 여러개의 it 함수가 있음
	- 별도의 단위 테스트로 동작
- expert()
	- 확인(assert)하는 부분
	- 테스트 케이스의 유효성을 검사
	- 확인을 도와주는 매칭 함수를 가지고 있음
		- toBe : assert에서 기대하는 값
		- toContain : 반환 값에 특정 값이 포함되어 있는지 확인
		- toBeLossThan, toBeGreaterThan : 값의 범위를 확인

### 카르마(Karma) 

- 정확한 테스트 케이스를 실행하는 역할
- 테스크 케이스를 식별한 다음 실행하고 실행 결과를 보여주는 커맨드 창 도구
- 브라우저를 실행 한 다음 브라우저에서 테스트 케이스를 실행하는 도구
- 브라우저 타입을 선택, 실행할 테스트 케이스를 선택하는 등의 여러가지 작업이 가능
- 카르마 CLI 패키지가 함께 제공  

### 자스민과 카르마 설치 및 설정  

- package.json 파일에 다음과 같이 설정되어 있음  

{% highlight json %}
"devDependencies": {
	"@angular-devkit/build-angular": "~0.13.0",
	"@angular/cli": "~7.3.5",
	"@angular/compiler-cli": "~7.2.0",
	"@angular/language-service": "~7.2.0",
	"@types/node": "~8.9.4",
	"@types/jasmine": "~2.8.8",
	"@types/jasminewd2": "~2.0.3",
	"codelyzer": "~4.5.0",
	"jasmine-core": "~2.99.1",
	"jasmine-spec-reporter": "~4.2.1",
	"karma": "~4.0.0",
	"karma-chrome-launcher": "~2.2.0",
	"karma-coverage-istanbul-reporter": "~2.0.1",
	"karma-jasmine": "~1.1.2",
	"karma-jasmine-html-reporter": "^0.2.2",
	"protractor": "~5.4.0",
	"ts-node": "~7.0.0",
	"tslint": "~5.11.0",
	"typescript": "~3.2.2"
}
{% endhighlight %}

#### 설치

**카르마 CLI**  

npm 전역 범위에 카르마 CLI를 설치 

> npm i -g karma-cli 

**다른 dev 의존성** 

카르마 CLI 외에도 karma, karmachrome-launcher, karma-jasmine, jasmine-core와 같은 의존성을 설치 

> npm i karma karmachrome-launcher karma-jasmine jasmine-core 

package.json에 있는 devDependencies 섹션의 모든 의존성을 설치 

**자스민 타이핑** 

테스트 케이스도 Typescript로 작성하기 위해 자스민 Typescript 타이핑을 설치 

> npm i @types/jasmine 

#### 설정 

- package.json을 확인하여 모든 의존성이 잘 설치되었는지 확인
- 루트 폴더에 Karma.conf.js 라는 파일 생성

{% highlight typescript %}
module.exports = function (config) {
  config.set({
    basePath: '',
    frameworks: ['jasmine', '@angular-devkit/build-angular'],
    plugins: [
      require('karma-jasmine'),
      require('karma-chrome-launcher'),
      require('karma-jasmine-html-reporter'),
      require('karma-coverage-istanbul-reporter'),
      require('@angular-devkit/build-angular/plugins/karma')
    ],
    client: {
      clearContext: false // leave Jasmine Spec Runner output visible in browser
    },
    coverageIstanbulReporter: {
      dir: require('path').join(__dirname, '../coverage/SampleTrelloApplication'),
      reports: ['html', 'lcovonly', 'text-summary'],
      fixWebpackSourcePaths: true
    },
    reporters: ['progress', 'kjhtml'],
    port: 9876,
    colors: true,
    logLevel: config.LOG_INFO,
    autoWatch: true,
    browsers: ['Chrome'],
    singleRun: false,
    restartOnFileChange: true
  });
};
{% endhighlight %}

- basePath : 카르마가 테스트 케이스를 찾기 시작할 기본 경로를 정의
- frameworks : 사용할 프레임워크 지정
- plugins : 실행을 위해 필요한 의존성의 배열
- reporters : 테스트 케이스의 실행 진행률을 어떻게 볼지 설정
- autoWatch : 파일을 감시하다가 변경사항이 생기면 테스트를 다시 실행할지 여부
- browsers : 어떤 브라우져에서 실행되야 하는지 설정

---

## 테스트 프로젝트의 폴더와 파일 구조

- 테스트 파일은 일반적으로 spec.ts 확장자를 부여
- homepage.component.ts => homepage.component.spec.ts
- Angular CLI를 사용하면 동일한 명명 규칙을 사용하여 컴포넌트 또는 서비스를 생성할 때 자동으로 테스트 파일을 만들어 줌  

---

## 파이프용 테스트 작성하기

- 가장 쉬운 예제 중 하나
- 파이프가 외부 의존성을 가지지 않아서 모의 코드 걱정 없이 로직을 쉽게 테스트할 수 있음
- 이 예제에서는 하나의 파이프함수(사용자정의 정렬)가 있으므로 이 파이프에 대한 테스트 케이스 작성

### 새로운 파일 생성 

- 사용자정의 정렬 코드가 있는 폴더에 테스트 파일 생성(customsort_pipe.spec.ts)
- 파이프 함수의 논리
	- task 배열과 sort boolean 프로퍼티, 두 개의 파라미터를 기반으로 함
	- sort boolean 프로퍼티를 기반으로 task 배열을 정렬할지 여부를 결정
	- 정렬은 task 프로퍼티를 기반으로 함

- 단위 테스트 케이스의 목록
	- sort 프로퍼티를 true로 하고 파이프 함수를 호출 했을 때의 task 배열 확인
	- sort 프로퍼티를 false로 하고 파이프 함수를 호출 했을 때의 task 배열 확인
	- 빈 task 배열을 넘겼을 때의 결과 확인
	- 빈 title을 가진 task가 task 배열에 포함된 경우 결과 확인
	- 동일한 title을 가진 task가 2개 이상 task 배열에 포함되었을 경우의 결과 확인

처음 두 개에 중점을 두어 테스트 케이스 작성 

### 테스트 케이스 작성 

- 테스트 케이스의 의존성을 가져오는 것이 첫 번째 단계
- 모든 테스트 케이스는 해당 컴포넌트/파이프/서비스와 모델 데이터에 대한 의존성을 가짐
- 이번 경우 사용자정의 파이프 클래스와 task 객체 

{% highlight typescript %}
import { CustomSort} from './custom-sort.pipe';
import { Task } from '../model/task';

describe('사용자 정의 정렬', () => {
	let pipe: CustomSort;
	let tasks: Task[];
	beforeEach(() => {
		pipe = new CustomSort();
		tasks = [{ title: 'Basic', id: 0, subtask: [], taskheaderId: '1' },
		{ title: 'Advance', id: 0, subtask: [], taskheaderId: '1' },
		{ title: 'Complex', id: 0, subtask: [], taskheaderId: '1' }];
	});
	it('task 배열을 정렬함', () => {
		const expectedTask: Task[] = [
			{ title: 'Advance', id: 0, subtask: [], taskheaderId: '1' },
			{ title: 'Basic', id: 0, subtask: [], taskheaderId: '1' },
			{ title: 'Complex', id: 0, subtask: [], taskheaderId: '1' }]
		expect(pipe.transform(tasks, true)).toEqual(expectedTask);
	});
	it('task 배열을 정렬하지 않음', () => {
		const expectedTask: Task[] = [
			{ title: 'Basic', id: 0, subtask: [], taskheaderId: '1' },
			{ title: 'Advance', id: 0, subtask: [], taskheaderId: '1' },
			{ title: 'Complex', id: 0, subtask: [], taskheaderId: '1' }]
		expect(pipe.transform(tasks, false)).toEqual(expectedTask);
	});
});
{% endhighlight %}

#### 함수 설명  

- describe 함수는 두 개의 파라미터를 사용
	- 첫번째는 description으로 문자열로 테스트 케이스를 정의하는 설명
	- 두번재는 자스민이 호출할 콜백 함수

#### beforeEach 함수 

- 사용자정의 sort 객체를 초기화, transform 함수에 전달될 task 배열을 만듦
- it 함수가 호출되기 전에 호출
- 여러 테스트 케이스를 실행할 때 이전 테스트 케이스의 데이터가 다음 테스트 케이스의 데이터에 영향을 미치지 않도록 함

#### it 함수 

- beforeEach 함수에서 만든 task 배열을 전달하고 배열이 제목별로 정렬되도록 함
- 예상되는 출력 배열을 정의한 다음 toEqual을 사용해 transform 함수에서 반환된 값과 비교

---

## 테스트 케이스 실행

- Angular CLI를 사용하면 터미널에서 아래 명령을 실행하면 카르마가 브라우저를 실행하고 애플리케이션의 모든 테스트 케이스를 실행

> ng test

---

## 서비스 클래스를 위한 테스트 케이스 작성

- 다른 시스템에서 데이터를 가져오는 모든 프로젝트는 서비스 클래스를 가지고 있음
- 서비스 클래스는 웹서비스를 호출하기 위해 HTTP를 사용
- 따라서 서비스 클래스에서 단위 테스트를 작성하려면 HTTP와 서비스가 필요로 하는 다른 외부 의존성을 모의해야 함

### 새로운 파일 생성

- service 폴더에 trello.service.spec.ts 파일 생성
- 트렐로 서비스는 HttpClient GET 메서드를 사용하여 JSON 파일에서 보드 데이터를 가져오는 기능
- 프로미스를 사용하여 응답을 가져오고 성공 또는 실패를 프로미스에 반환
- 테스트에 필요한 두가지 흐름
	- public 변수가 이미 보드에 있는지 확인
	- 보드가 없을 때 HTTP를 호출
- 테스트 케이스
	- 보드 객체가 없는 경우 HTTP 호출을 통해 데이터를 가져오는지 확인
	- 이미 보드 객체가 있는 경우 해당 객체를 반환하는지 확인

### 테스트 케이스 작성

{% highlight typescript linenos %}
import { TrelloService } from './trello.service';
import { of } from 'rxjs';
import { Board } from '../model/board';

describe('Trello HTTP Service', () => {
	let trelloService: TrelloService;
	let mockHTTP;
	let fakeBoards: Board[];

	beforeEach(() => {
		mockHTTP = jasmine.createSpyObj('mockHTTP', ['get']);
		trelloService = new TrelloService(mockHTTP);
	});

	it('보드 undefined 확인', () => {
		mockHTTP.get.and.returnValue(of(fakeBoards));
		trelloService.getBoardsWithPromises().then(boards => this.boards = boards);
		expect(fakeBoards).toBeDefined();
	});

	it('보드 defined 확인', () => {
		fakeBoards = new Array();
		mockHTTP.get.and.returnValue(of(fakeBoards));
		trelloService.getBoardsWithPromises().then(boards => this.boards = boards);
		expect(fakeBoards).toBeDefined();
	});

	it('보드 가져오기', () => {
		trelloService.Boards = new Array();
		trelloService.Boards.push({
			id: 0,
			title: 'Test Board',
			task: []
		});
		mockHTTP.get.and.returnValue(of(trelloService.Boards));
		trelloService.getBoardsWithPromises().then(boards => {
			fakeBoards = boards;
			expect(fakeBoards).toBeDefined();
			expect(fakeBoards[0].title).toEqual('Test Board');
		});
	});
});
{% endhighlight %}

#### describe 함수

{% highlight typescript %}
describe('Trello HTTP Service', () => {
	let trelloService: TrelloService;
	let mockHTTP;
	let fakeBoards: Board[];
});
{% endhighlight %}

- 트렐로 서비스를 위한 fakeBoards라는 보드 배열을 만듦
- fakeBoards는 트렐로 서비스의 getBoardsWithPromises에서 반환 하는 값
- 트렐로 서비스 클래스 의존성(HttpClient)을 모의
- 프로퍼티를 선언만 하고 초기화는 beforeEach 함수 내에서 수행

#### beforeEach 함수

{% highlight typescript linenos %}
beforeEach(() => {
	mockHTTP = jasmine.createSpyObj('mockHTTP', ['get']);
	trelloService = new TrelloService(mockHTTP);
});
{% endhighlight %}

- HttpClient 객체를 모의 하여 트렐로 서비스 클래스의 인트턴스를 만듦
- 2행
	- HttpClient 객체를 모의하려면 내부 메서드를 모의하는 방법이 필요
	- 자스민에서 제공하는 createSpyObj 함수를 사용
	- 기본적으로 정의하는 티입의 더미 객체를 생성 또는 더미 함수 배열을 제공하여 메서드로 사용할 수 있게 함
	- mockHTTP를 사용하면 get 메서드를 찾을 수 있음
- 3행 : 트렐로 서비스 클래스의 인스턴스를 만들고 mockHTTP 객체를 전달

#### it 함수

- json 객체가 없는지 확인
- 보드 객체가 undefined 인 경우 HTTP 호출

{% highlight typescript linenos %}
it('보드 undefined 확인', () => {
	mockHTTP.get.and.returnValue(of(fakeBoards));
	trelloService.getBoardsWithPromises().then(boards => this.boards = boards);
	expect(fakeBoards).toBeUndefined();
});
{% endhighlight %}

- 2열
	- 자스민에서 get 함수를 호출하면 Observable 형태의 보드 배열 타입을 반환한다고 알려줌
	- it 함수에서 getBoardsWithPromises 함수를 호출하면 자스민은 HTTP 호출에서 보드 배열의 Observable을 반환하는지 확인
- 4열
	- 아직 배열을 초기화 하지 않았기 때문에 반환값은 undefined
	- expect 문에서 테스트

- fakeBoards 객체를 초기화 했다면 반환 값은 undefined가 아닌 해당 값을 받음
- 그럼 expect문은 undefined가 아닌 보드 배열 객체를 받음
- 자스민을 사용하면 객체를 모의할 뿐만 아니라 단위 테스트에서 사용하는 반환 값 또한 모의된 객체로 가져올 수 있음

{% highlight typescript %}
it('보드 defined 확인', () => {
	fakeBoards = new Array();
	mockHTTP.get.and.returnValue(of(fakeBoards));
	trelloService.getBoardsWithPromises().then(boards => this.boards = boards);
	expect(fakeBoards).toBeDefined();
});
{% endhighlight %}

- 보드 객체를 트렐로 서비스의 프로퍼티에 할당
- 예상되는 동작은 getBoardsWithPromises 함수의 else 부분

{% highlight typescript %}
it('보드 가져오기', () => {
	trelloService.Boards = new Array();
	trelloService.Boards.push({
		id: 0,
		title: 'Test Board',
		task: []
	});
	mockHTTP.get.and.returnValue(of(trelloService.Boards));
	trelloService.getBoardsWithPromises().then(boards => {
		fakeBoards = boards;
		expect(fakeBoards).toBeDefined();
		expect(fakeBoards[0].title).toEqual('Test Board');
	});
});
{% endhighlight %}

- 새 보드 객체 배열을 만들고 이를 트렐로 서비스의 보드 객체에 할당
- 함수 호출
- expect문을 통해 프로미스 함수 내에서 올바른 객체를 받았는지 확인
- expect문을 then 안에 있는 것은 프로미스가 성공적으로 수행된 뒤에만 expect로 값을 비교하려고 했기 때문
- expect문이 then 밖에 있는다면 결과를 받기 전에 실행됨으로 언제나 실패하게 됨

---

## 독립된 컴포넌트의 테스트 케이스 작성

- 서비스나 파이프와 비교해 컴포넌트의 고유한 점은 사용자 인터페이스와 상호 작용한다는 점
- 컴포넌트를 테스트할 때는 다음과 같은 두 가지 전력 사용 가능
	- 테스트 컴포넌트를 격리
		- 서비스나 파이프와 비슷한 방식으로 컴포넌트를 테스트
		- 컴포넌트의 템플릿 부분을 고려하지 않고 테스트 하지도 않음
		- 컴포넌트에 있는 논리를 테스트하려는 경우에 유용
		- 컴포넌트에 대한 포괄적인 검증을 못할 수도 있음
	- 종단간 컴포넌트 테스트
		- 템플릿과 함께 컴포넌트를 테스트
		- 컴포넌트 논리에서 발생하는 변경 사항이 템플릿에 미치는 영향을 테스트할 수 있음
- 이번 섹션은 첫 번째 방식으로 테스트

### 새로운 파일 생성

- board 폴더에 테스트 파일 생성
- 보드 컴포넌트는 새 작업 추가, 보드 제목 수정, 기존 작업 수정과 같은 많은 기능이 있음
- 새 작업 추가 기능에 중점을 두고 테스트 케이스 작성

### 테스트 케이스 작성

- addtask 메서드는 이미 존재하는 작업의 전체 길이를 가져와 1씩 증가시켜서 새로운 작업에 대한 id로 부여
- 그 다음 이 새 작업을 보드 객체의 기존 작업 목록에 추가
- 두 가지 테스트를 작성
	- 보드 객체에 첫 번째 작업을 추가
	- 이미 존재하는 목록에 작업을 추가
- 격리된 테스트 케이스를 생성할 것이기 때문에 객체를 생성하려는 클래스만 import

{% highlight typescript %}
import { BoardComponent } from './board.component';
import { Board } from '../model/board'
{% endhighlight %}

### 테스트 케이스 구현 

- 보드 컴포넌트의 의존성 목록
	- HTML 엘리먼트를 참조하는 엘리먼트 참조
	- ActivatedRoute로 라우팅 값 가져오기
	- TrelloService 서비스로 보드 객체 가져오고 업데이트 하기
- 보드 컴포넌트를 효과적으로 테스트 하려면 이 모든 것을 테스트

- before 함수에서 보드 컴포넌트의 인스턴트를 만들고 모의 종속성을 코드에 전달

{% highlight typescript %}
describe('BoardComponent', () => {
	let boardComponent: BoardComponent;
	let mockElementRef, mockRoute, mockTrelloService;

	beforeEach(() => {
		boardComponent = new BoardComponent(mockElementRef, mockRoute, mockTrelloService);
	});
});
{% endhighlight %}

- 두 가지 테스트 케이스 

{% highlight typescript linenos %}
it('기존 작업에 새 작업 추가 테스트', () => {
	boardComponent.addtaskText = 'Test task';
	boardComponent.board = new Board();
	boardComponent.board.id = 1;
	boardComponent.board.title = '보드 1';
	boardComponent.board.task = new Array();
	boardComponent.board.task.push({
		id: 1,
		title: '작업1',
		subtask: [],
		taskheaderId: '1'
	});
	boardComponent.addtask();
	expect(boardComponent.board.task.length).toBe(2);
});

it('첫 번째 작업 추가 테스트', () => {
	boardComponent.addtaskText = '더미';
	boardComponent.board = new Board();
	boardComponent.board.id = 1;
	boardComponent.board.title = '보드 1';
	boardComponent.board.task = new Array();
	boardComponent.addtask();
	expect(boardComponent.board.task.length).toBe(1);
	expect(boardComponent.board.task[0].id).toBe(1);
	expect(boardComponent.board.task[0].title).toBe('더미');
});
{% endhighlight %}

- 1행 : 첫 번째 테스트
	- 기존 보드 객체를 초기화, 새로운 작업으로 정의 후 addtask 함수에 전달
	- expect 함수는 작업 배열의 길이만 확인
- 17행 : 두 번째 테스트
	- 보드에 기존 작업이 없고 새로운 작업을 생성한다고 가정
	- 테스트 케이스의 길이가 1인 확인 하는 것을 제외하고는 첫 번째 케이스와 유사
	- 첫 번째 테스트 케이스르 작성하는 경우 코드에 예외가 발생하지 않는지 확인
	- 여러 값을 확인하기 위해 여러개의 expert문이 있음

---

## 통합 컴포넌트를 위한 테스트 케이스 작성

- 통합 테스트를 구현할 때 가장 복잡한 것은 설정 부분
- 컴포넌트와 의존성의 초기화를 함께 해야함
- 복잡한 통합 테스트의 몇 가지 이점
	- 바인팅 테스트 가능, 복잡한 바인딩이 있는 경우 매우 유용
	- 테스트를 작성하며 바로 문제를 식별
	- 통합 테스트를 통해 템플릿 관련 버그를 신속하고 효율적으로 식별하고 사전에 수정

### 통합테스트 설정 

- 컴포넌트 인스턴스가 필요하지 않음  
- 연관된 템플릿이 있고 컴포넌트를 로드하고 바인딩을 테스트 할 수 있도록 모듈에 포함된 컴포넌트가 필요
- homepage 컴포넌트에 대한 통합 테스트 케이스를 작성
- 테스트 케이스에 필요한 import

{% highlight typescript %}
import { async, ComponentFixture, TestBed } from '@angular/core/testing';
{% endhighlight %}

- Angular는 angular/core/testing 모듈에 특수한 라이브러리 제공
	- TestBed
		- Angular가 컴포넌트를 만드는데 사용
		- 템플릿을 생성하고 이를 각 컴포넌트와 연관지음
		- 테스트 중인 컴포넌트를 보유하고 있는 모듈을 생성
	- async
		- beforeEach 메서드에서 비동기 작업을 동기 작업으로 변환하는데 사용하는 도우미 함수
	- ComponentFixture
		- ComponentFixture는 테스트 함수에서 하용하는 타입
		- TestBed를 사용하여 컴포넌트를 만들면 ComponentFixture 타입의 컴포넌트를 반환

- 홈페이지 컴포넌트는 두 가지 서비스에 의존
	- 트랠로 서비스
	- 라우터 서비스

{% highlight typescript %}
constructor(public el: ElementRef, private _route: ActivatedRoute, private _trelloService: TrelloService) { }
{% endhighlight %}

- 다음과 같이 import를 완성

{% highlight typescript %}
import { async, ComponentFixture, TestBed } from '@angular/core/testing';
import { RouterModule } from '@angular/router';
import { HomepageComponent } from './homepage.component';
import { TrelloService } from './../service/trello.service';
{% endhighlight %}

### 테스트 케이스 작성

- 홈페이지 컴포넌트의 기능
	- ngOninit에서 사용 가능한 모든 보드를 가져와서 로컬 보드 개열 객체에 바인딩
	- 새로운 보드 기능을 추가
		- 작업은 없고 제목은 'New Board'로 설정

- 세 가지 테스트 케이스를 작성
	- 컴포넌트가 성공적으로 생성되었는지 확인
	- 두 개의 보드를 추가했을 때 컴포넌트에 보드가 잘 추가되고 템플릿에 표시가 되는지 확인
	- 새 보드를 만들고 UI에 잘 표시되는지 확인

- 통합 테스트에서는 바인딩이 잘 동작되는지 보기 위해 UI도 확인 가능
- 컴포넌트의 인스턴스를 얻기 위해 추가 작업 필요

#### describe 함수

{% highlight typescript linenos %}
describe('HomepageComponent', () => {
	let component: HomepageComponent;
	let fixture: ComponentFixture<HomepageComponent>;
});
{% endhighlight %}

- 2행 : 홈페이지 컴포넌트를 정의
- 3행
	- fixture 변수를 선언, 홈페이지 컴포넌트를 타입으로 전달
	- ComponentFixture는 기본적으로 컴포넌트의 래퍼(warpper)로 컴포넌트의 인스턴스를 직접 만든었다면 제공하지 않을 추가 기능을 제공
	- 변경 탐지나 컴포넌트 템플릿의 html 엘리먼트 접근

#### 첫 번째 beforeEach 함수

- beforeEach 함수는 컴포넌트와 모듈을 생성하는 장소, 또한 의존성과 동작을 정의
- 통합테스트의 경우 일반적으로 두 가지 beforeEach 함수가 있음
- 하나는 모듈을 초기화, 다른 하나는 컴포넌트를 만드는 것

{% highlight typescript linenos %}
import { APP_BASE_HREF } from '@angular/common';
import { of } from 'rxjs';
...
describe('HomepageComponent', () => {
	...
	beforeEach(async(() => {
		const mockTrelloService = { getBoardsWithPromises: () => of([]).toPromise() };
		TestBed.configureTestingModule({
			declarations: [HomepageComponent],
			imports: [RouterModule.forRoot([])],
			providers: [{ provide: APP_BASE_HREF, useValue: '/' },
			{ provide: TrelloService, useValue: mockTrelloService }]
		}).compileComponents();
	}));
	...
});
...
{% endhighlight %}

- 8행
	- TestBed 유틸리티의 함수를 사용하여 모듈을 생성
	- configureTestingModule 함수는 모듈을 생성하고 모둘에 대한 의존성을 정의
	- app.module 파일의 ngModule과 비슷한 모듈을 생성
	- 다음과 같은 프로퍼티를 가짐
		- declarations : 모듈과 관련된 컴포넌트를 정의
		- imports : 의존성이 있는 모듈을 가져오기 위한 import 구문, 라우트 객체만으로 컴포넌트를 만족시키므로 빈 라우트를 추가
		- providers : 서비스와 기본 href 링크와 같은 외부 의존성을 정의하는 공급자, 두개의 속성이 있음
			- provide : 어떤 의존성이 필요한지 알려줌
			- useValue : 이 의존성을 대체할 객체 또는 값
	- configureTestingModule 함수는 비동기 함수이지만 테스트 케이스가 실행되기 전에 모듈이 생성되었는지 확인하기 위해 동기적으로 실행
		- async 함수를 사용하면 비동기를 동기식 연산으로 변환
- 7행
	- 원래의 서비스를 대체할 mockTrelloService 객체를 생성
	- 이 서비스 클래스는 getBoardsWithPromises라는 하나의 메서드를 사용
	- 이 메서드는 보드 타입에 대한 프로미스를 반환
	- 홈페이지 컴포넌트가 mockTrelloService에서 이 메서드를 호출할 때 보드 타입의 프로미스를 반환했는지 확인하기 위해 반환 값으로 빈 배열을 할당
- 13행
	- compileComponents 함수는 템플릿이 있는 컴포넌트로 컴파일하고 해당 컴포넌트를 모듈에 추가

#### 두 번째 beforeEach 함수

- 컴포넌트를 만들고 인스턴스를 가져오는 것

{% highlight typescript %}
beforeEach(() => {
	fixture = TestBed.createComponent(HomepageComponent);
	component = fixture.componentInstance;
	fixture.detectChanges();
});
{% endhighlight %}

- 홈페이지 컴포넌트를 생성하고 이를 컴포넌트 fixture에 할당
- fixture에서 컴포넌트인스턴스를 component 변수에 할당
- 컴포넌트 fixture에서 detectChanges를 호출
	- 테스트 하는 동안에 컴포넌트의 UI를 변경하기 위한 코드를 사용했다면 항상 detectChanges 메서드를 호출

#### if 함수

- 첫 번재는 컴포넌트를 성공적으로 만들 수 있는지 확인

{% highlight typescript %}
it('컴포넌트 생성 확인', () => {
	expect(component).toBeTruthy();
});
{% endhighlight %}

- toBeTruthy 메서드를 사용하여 컴포넌트가 있는지 확인
- 통합 테스트를 작성할 때마다 항상 이 테스트 케이스를 실행
- 생성한 컴포넌트 인스턴스가 잘 생성되었고 누락된 의존성이 있는지 확인하는 역할

##### UI 보드에서 확인

- 컴포넌트에서 템플릿까지 종단간 테스트를 하는 방법
- 두 개의 보드가 있을 때 UI에 잘 표시되는지 컴포넌트 프로퍼티는 정상인지 확인

{% highlight typescript %}
it('두 개의 보드가 있는지 확인', () => {
	component.boards = new Array();
	component.boards.push(
		{
			id: 1,
			task: [],
			title: 'Board 1'
		},
		{
			id: 2,
			task: [],
			title: 'Board 2'
		}
	);
	fixture.detectChanges();
	const compiled = fixture.debugElement.nativeElement;
	const title = compiled.querySelectorAll('.title');
	expect(title[0].textContent).toContain('Board 1');
	expect(title[1].textContent).toContain('Board 2');
	expect(component.boards.length).toBe(2);
});
{% endhighlight %}

- 컴포넌트 객체에 보드 배열을 만들고 두 개의 보드를 생성
- 두 개의 보드를 UI에 반영하기 위해 detectChanges 메서드를 호출
	- detectChanges 메서드는 Angular를 호출하여 모든 프로퍼티에 대한 바인딩을 갱신
- 컴포넌트 fixture 객체의 함수를 사용하여 템플릿에서 엘리먼트를 가져옴
	- debugElement는 컴포넌트 템플릿과 연관된 루트 엘리먼트
	- nativeElement는 템플릿에 대한 핸들을 제공
- 템플릿에 대한 핸들을 확보하고 나면 표준 UI API를 호출하여 엘리먼트를 가져오고 그 값을 내부적으로 확인 가능 
	- html 엘리먼트가 title 클래스 프로퍼티를 가졌는지
	- 두 개의 보드를 잘 가져왔는지 확인
- 마지막 라인에서 컴포넌트 클래스의 보드 객체가 두 개의 보드를 가지고 있는지 확인

#### 신규 보드 생성 테스트

{% highlight typescript %}
it('새 보드 생성', () => {
	component.addBoard();
	fixture.detectChanges();
	expect(component.boards.length).toBe(1);
	const compiled = fixture.debugElement.nativeElement;
	const title = compiled.querySelectorAll('.title');
	expect(title.length).toBe(2);
	expect(title[0].textContent).toContain('New Board');
});
{% endhighlight %}

- 컴포넌트에서 addBoard 메서드를 호출한 다음 Angular가 바인딩을 새로 고치도록 하기 위해 detectChanges 메서드를 호출
- 정상적으로 완료 되면 expect 문으로 보드의 수가 한개인지 title이 있는 두 개의 엘리먼트가 있는지 확인
- 마지막 행에서 첫 번째 새로운 보드가 New Board라는 컨텐츠를 가졌는지 확인