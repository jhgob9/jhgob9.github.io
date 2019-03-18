---
title: "[TypeScript] 5장 두번째 애플리케이션 - 트렐로"
date: 2019-03-18 10:40:00 +0900
categories: typescript
---

# 5장 두번째 애플리케이션 - 트렐로

이번 장에서 다룰 내용  

- 예제 트렐로 애플리케이션 소개
- Typescript 네임스페이스와 모듈
- Typescript 제네릭
- Angular 고급 컴포넌트
- Angular 생명주기 훅

---

## 트렐로 예제 애플리케이션 소개

실제 트렐로(일정관리와 협업을 지원하는 개발도구)의 축소 버전  

### 애플리케이션 개요

- 작업리스트가 있는 보드를 만들고
- 보드마다 새로운 작업과 하위 작업을 추가 할 수 있음  

### 기술 개요

- 컴포넌트를 추가하고 컴포넌트 간 데이터를 공유하는 방법을 실습  
- 싱글톤 서비스를 사용하여 컴포넌트 사이에 공통으로 사용되는 데이터를 생성  

---

## Typescript 네임스페이스와 모듈

- Namespace(네임스페이스) : 내부모듈
- Module(모듈) : 외부모듈

### 유니버셜 네임스페이스

- Javascript는 모든 클래스와 함수를 window 네임스페이스에 추가(window는 현재 창과 관련된 모든 함수, 클래스를 포함하는 유니버셜 네임스페이스)
- 이러한 함수, 클래스는 웹애플리케이션의 모든 곳에서 접근이 가능
- 애플리케이션 규모가 커지면 하나의 네임스페이스 아래 모든 함수와 클래스를 사용하는 것이 좋지 않음  
- 유니버셜 네임스페이스가 모든 함수와 클래스를 갖게 되면 충돌이 일어날 수 있음
- 같은 이름을 가진 클래스나 함수를 선언 할 수 없음  

{% highlight javascript %}
/* 자바스크립트 */
function getDate(){
	return true;
}
function getDate(){ //getDate 함수를 덮어 씀
	return false;
}
window.getDate(); // false가 출력됨
{% endhighlight %}

![]({{ "/assets/images/namespace1.png" | absolute_url }})

- 이런 문제를 해결하기 위해서 컨테이너(네임스페이스와 모듈)가 있음
- 컨테이너가 있으면 각 클래스나 함수가 컨테이너 안에서 캡슐화 되고 컨테이너 이름만 유니버셜 네임스페이스에 노출됨 

### Typescript 네임스페이스

- namespace 라는 키워드를 제공하여 관련 함수, 클래스와 인터페이스를 하나의 namespace 아래 캡슐화

{% highlight typescript %}
namespace WebSeriveResponse{}
{% endhighlight %}

- 네임스페이스 외부의 모든 사용자는 다음과 같은 점 표기법으로 접근 가능

> {% raw %}<<네임스페이스이름>>.<<함수/클래스 이름>>{% endraw %}

- 네임스페이스 아래 정의된 모든 함수와 클래스는 네임스페이스 외부에는 표시 안됨  

![]({{ "/assets/images/namespace2.png" | absolute_url }})

- 14행에서 WebServiceResponse에 WebResponse 속성이 없다는 Typescript 경고가 뜸
- 10행에서는 네임스페이스 내부이기 때문에 WebResponse 클래스에 엑세스 가능

#### export 키워드

- export 키워드를 사용하여 네임스페이스 내부의 특정 멤버를 외부에 공개할 수 있음  

{% highlight typescript %}
namespace WebServiceResponse{
	export class WebResponse{
		getResponse(){
			return 'Success'
		}
		sendResponse() {
			return '200 OK'
		}
	}
	let localResponse = new WebResponse();
	localResponse.getResponse();
	localResponse.sendResponse();
}
let response = new WebServiceResponse.WebResponse();
response.getResponse();
response.sendResponse();
{% endhighlight %}

#### 중첩(nested) 네임스페이스

- 네임스페이스 안에 또 다시 네임스페이스를 추가하여 중첩된 캡슐화를 지원
- 외부에 노출되는 중첩 네임스페이스에도 export 키워드가 붙어야 함  
- 특정 기능 테스트나 하위 기능 테스트에 유용

{% highlight typescript %}
namespace WebServiceResponse{
	let url: string;
	export namespace ServiceResponse{ // 중첩 네임스페이스
		export class WebResponse {
			getResponse() {
				return 'Success'
			}
			sendResponse() {
				return '200 OK'
			}
		}
	}
	let localResponse = new ServiceResponse.WebResponse();
	localResponse.getResponse();
	localResponse.sendResponse();
}
let response = new WebServiceResponse.ServiceResponse.WebResponse(); // 외부네임스페이스.내부네임스페이스.내부클래스
response.getResponse();
response.sendResponse();
{% endhighlight %}

### Typescript 모듈

- 모듈 장점 : 모듈로더를 사용할 수 있음, 비동기 동작을 제공, 결과적으로 애플리케이션의 속도를 높일 수 있음
- 사용가능한 모듈로더가 몇가지 있으며 구문과 관리 방법이 각각 다름

#### 정의

- 모든 파일이 별도의 모듈이고 파일 이름이 모듈 이름
- 파일을 생성하여 모듈을 만들 수 있음
- 명시적으로 정의되지 않는 한 모듈 내부의 모든 컨텐츠는 캡슐화 되어 외부에서 접근 불가
- 다음과 같은 코드로 service.ts 라는 파일을 만들면 service라는 모듈이 만들어지고 다른 모듈에서 이 모듈을 참조할 수 있음  

{% highlight typescript %}
interface iBoardService{
	url:string;
	getBoardInformation();
}
class BoardService{
	url:string;
	getBoardInformation(){
		return '사용가능한 보드 없음';
	}
}
let boardService = new BoardService();
boardService.getBoardInformation();
{% endhighlight %}

#### 모듈 export

- 모듈의 멤버를 공개하려면 export 키워드를 사용  
- export를 사용하여 노출 시킬 멤버를 명시적으로 정의

{% highlight typescript %}
export class BoardService{
	url:string;
	getBoardInformation(){
		return '사용가능한 보드 없음';
	}
}
{% endhighlight %}

#### 모듈 import

- export 한 모듈을 사용하려면 명시적으로 다른 모듈에서 import 해야 함

{% highlight typescript %}
{% raw %}
import { BoardService } from './module/service';
{% endraw %}
{% endhighlight }

- 필요한 모듈만 선택적으로 import 가능 
- import를 하면 로컬멤버와 마찬가지로 사용 가능  

{% highlight typescript %}
{% raw %}
import { BoardService } from './module/service';
let board = new BoardService();
board.getBoardInformation();
{% endraw %}
{% endhighlight }

- import 하는 멤버를 편리하게 사용할 수 있도록 멤버의 이름을 변경 할 수 있음

{% highlight typescript %}
{% raw %}
import { BoardService as Service } from './module/service';
let board = new Service();
board.getBoardInformation();
{% endraw %}
{% endhighlight }

- import 할 멤버가 모듈의 export된 모든 멤버일 경우에는 별표를 사용하여 한번에 가져올 수도 있음 

{% highlight typescript %}
import * as Service from './module/service';
let board = new Service.BoardService();
board.getBoardInformation();
let interface: Service.iBoardService;
{% endhighlight }