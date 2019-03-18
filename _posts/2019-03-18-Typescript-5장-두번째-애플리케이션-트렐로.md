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

