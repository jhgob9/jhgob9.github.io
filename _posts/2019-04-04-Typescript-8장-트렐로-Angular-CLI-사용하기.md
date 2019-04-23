---
title: "[TypeScript] 8장 트렐로 Angular CLI 사용하기"
date: 2019-04-04 10:00:00 +0900
categories: typescript
---

# 8장 트렐로 Angular CLI 사용하기

이번 장에서 다룰 내용 

- Angular CLI는 무엇이며 왜 사용해야 하는지 설명
- Angular CLI를 사용해서 애플리케이션을 만드는 방법을 살펴보기
- 컴포넌트, 서비스, 파이프, 모듈 및 라우팅을 생성하는 옵션을 살펴보기
- Angular CLI가 제공하는 린팅, 빌딩, 애플리케이션 구동 옵션에 대해 살펴보기
- Angular CLI의 테스트 기능에 대해 살펴보기

---

## Angular CLI 소개

- Angular CLI는 코드를 빌드하고 관리할 때 모범사례를 따르도록 도와주는 커맨드 라인 인터페이스
- Angular CLI는 업계의 모든 모범사례를 통합하고 효율적으로 애플리케이션을 개발할 수 있는 인터페이스를 제공
	- 권장되는 스타일 기이드에 맞춰 애플리케이션 생성
	- 개발과 상용 모등서 애플리케이션을 빌드하고 실행
	- 컴포넌트, 서비스, 파이프 각각에 대해서 테스트 통합
	- 린팅을 사용하여 애플리케이션이 디자인과 일관성이 있는지 확인

### Angular CLI 설정

- Angular CLI를 npm에 설치
- Angular CLI는 node 모듈이므로 CLI를 설치하려면 먼저 Node.js를 설치
- 다음 명령으로 node가 올바르게 설치 되었는지 확인

> node -v

- 노드가 설치되어 있다면 다음 명령으로 Angular CLI 설치

> npm install -g @angular/cli

- 다음 명령으로 Angular CLI의 버전 확인

> ng -v

### Angular CLI help 명령어

- 다음 코드로 help 실행

> ng --help

- 모든 옵션에 대한 설명이 나열됨
- 특정 명령어에 대한 옵션만 살펴보고 싶다면 다음 코드를 실행

> ng new --help

### Angular CLI로 애플리케이션 만들기

- 다음 명령으로 Angular CLI를 사용해서 Angular 애플리케이션 생성

> ng new trello-app --skip-install

- app 파일들을 포함하여 애플리케이션에 필요한 파일과 설정 파일을 생성
- --skip-install 플래그는 node.js 모듈 설치를 진행하지 않는 옵션

### 파일 개요

![]({{ "/assets/images/img18.png" | absolute_url }})

- app/src : 애플리케이션 레벨에서 사용되는 main.ts, style.css, tsconfig, typing 파일이 있음
- .editorconfig : 들여쓰기 등의 코드 구조와 관련된 설정
- .gitignore : 파일과 폴더를 git에 체크인 해야하는지 아닌지에 대한 규칙을 제공
- karma.conf.js : 테스트 케이스 프레임워크를 설정
- package.json : 애플리케이션을 실행하는 데 필요한 모든 의존성 목록이 있음
- tsconfig.json : Typescript 컴파일러 옵션 구성
- tslint.json : 린팅에 대한 규칙을 정의, 표준 코드 규칙 및 스타일 가이드를 적용하는데 도움
- angular.json : Angular CLI에서 애플리케이션 구성 요소를 생성할 때 따라야할 규칙을 설정
- app/src/index.html : 시작파일, 모든 컴포넌트가 로드되는 기본 파일
- app/src/main.ts : 애플리케이션의 진입점 역할, 부트스트랩 논리를 정의
- app/src/tsconfig : 로컬 Typescript 컴파일러 옵션을 정의하는데 사용되는 두개의 파일, tsconfig.app.json / tsconfig.spec.json

### ng new - 플래그와 커스터마이징

- `ng new --help`를 실행하여 애플리케이션을 생성할 때 설정할 수 있는 많은 플래그를 확인할 수 있음
	- dry run
		- 파일을 생성할지 또는 생성될 파일을 나열할지 여부를 설정
		- 실제로 파일을 만들지 않고 CLI로 만들 파일과 폴더를 확인하려는 경우에 유용
		- 여러 구성 옵션을 사용하여 필요한 것을 확인하려는 경우에 사용
	- skip install
		- 애플리케이션 파일은 생성하지만 패키지에 필요한 node.js 모듈은 다운로드 하지 않음
	- skip git
		- Angular CLI는 기본적으로 프로젝트의 로컬 Git을 초기화
		- 원하지 않으면 이 플래그의 값을 true로 설정
	- skip tests
		- 테스트와 관련된 파일을 생성하지 않음
	- style
		- 스타일 파일의 확장자를 정의
		- 기본적으로 css이며 sass, less로 변경 가능
	- prefix
		- 기본적으로 컴포넌트를 생성할 때 컴포넌트 선택자의 이름은 app-*
		- 상용 제품에서는 이 이름을 따르지 않기를 원할 수도 있음
	- routing
		- 컴포넌트를 탐색할 수 있도록 라우팅 로직이 필요할 때 설정
		- 라우팅에 필요한 관련 파일을 생성하고 기본 NgModule에 연결

### 앱 커스터마이징

> ng new trello-app --skip-git true --style sass --prefix trello --roution true --dry-run

- 라우팅 가능
- 컴포넌트 선택자는 troll
- sass 사용
- 실제 생성은 하지 않음

### Angular CLI 파일

- Angular CLI의 모든 설정은 angular.json에 저장
- [angular.json 파일 보기](){:target="_blank"}
	- sourceRoot
		- 코드를 어디에 위치 시킬지 설정
		- 기본적으로 src 지만 원하는 곳으로 변경 가능
	- outputPath
		- 빌드파일을 저장할 폴더 이름을 설정
		- ng bulid 명령을 실행하면 Angular CLI가 빌드하고 여기 설정된 경로에 파일을 저장
		- 이 폴더의 파일을 사용하여 배포
	- assets
		- 애플리케이션과 함께 제공하려는 리소스를 나열
	- prefix
		- ng new 에서 사용한 값
		- 여기 설정된 이름으로 컴포넌트 선택자 앞에 접두어를 붙임
	- styles
		- ng new 명령에서 지정한 스타일 확장자
	- fileReplacements
		- 환경 파일의 경로를 지정
		- replace와 with에 대한 경로를 반드시 지정

---

## 애플리케이션용 파일 만들기

- 애플리케이션 생성 후 다음 단계는 필요한 컴포넌트, 서비스, 파이프 같은 기능을 추가하는 것
- Angular CLI는 쉽게 기능을 생성할 수 있는 명령을 제공

> ng generate <<기능이름>> <<파일이름>> <<옵션>>

- 기능 이름 : 컴포넌트, 서비스, 파이프 등등
- 옵션은 필수는 아님, 지정하지 않으면 기본값을 사용

### 컴포넌트 만들기

- Angular 에서 가장 일반적으로 필요한 것은 컴포넌트
- 다음 명령을 통해 컴포넌트 생성

> ng generate component homepage

- 아래와 같이 별칭으로 줄여서 사용 가능

> ng g c homepage

- 아래와 같이 파일을 생성함

> CREATE src/app/homepage/homepage.component.sass  
> CREATE src/app/homepage/homepage.component.html  
> CREATE src/app/homepage/homepage.component.spec.ts  
> CREATE src/app/homepage/homepage.component.ts  
> UPDATE src/app/app.module.ts  

#### Angular CLI의 컴포넌트 옵션

[컴포넌트 옵션 상세보기](https://angular.io/cli/generate#component){:target="_blank"}

- flat
	- 플래그를 true로 설정하면 Angular CLI는 폴더를 생성하지않고 상위 폴더 자체에 파일을 생성
	- 권장되는 방법은 항상 각 컴포넌트의 폴더를 만드는 것
	- 기본값은 true
- inlineTemplate(-t)
	- 컴포넌트에 대한 별도의 템플릿 파일을 생성하지 않음
	- ts 파일에 템플릿이 정의된 컴포너트가 생성됨
- inlineStyle(-s)
	- 컴포넌트에 별도의 스타일 파일을 생성하지 않음
- skipTests
	- 테스트 파일 생성 여부
	- 기본값은 생성(권장)

### 서비스 생성

> ng generate service trello

> ng g s trello

- 관련 파일이 별도의 폴더가 아니라 루트 폴더에 생성
- 아래와 같이 파일을 생성함

> CREATE src/app/trello.service.spec.ts  
> CREATE src/app/trello.service.ts  


#### Angular CLI의 서비스 옵션

[컴포넌트 옵션 상세보기](https://angular.io/cli/generate#service){:target="_blank"}

- flat
	- 컴포넌트와 같은 기능
	- 기본값은 false
	- 별도의 폴더에 서비스를 생성하고 싶을 경우 true로 변경

### 파이프 생성

> ng generate pipe custome-sort

> ng g pipe custome-sort

- flat 플래그를 지정하지 않으면 상위 폴더에 파이프를 생성
- 모든 파이프를 모아두는 별도의 폴더가 있을 경우 해당 폴더를 지정
- 아래와 같이 파일 생성

> CREATE src/app/custome-sort.pipe.spec.ts  
> CREATE src/app/custome-sort.pipe.ts  
> UPDATE src/app/app.module.ts  

- 별도의 폴더를 지정할 경우

> ng g pipe pipes/custome-sort  
> CREATE src/app/pipes/custome-sort.pipe.spec.ts  
> CREATE src/app/pipes/custome-sort.pipe.ts  
> UPDATE src/app/app.module.ts  

### 모듈 생성

- 대규모 어플리케이션에서는 여러 모듈이 필요
- 모듈을 사용하면 코드가 보다 조직화되고 재사용성이 높아짐
