---
title: "[Typescript] 3장 Sport News Combinator 기능추가"
date: 2019-03-08 10:40:00 +0900
categories: typescript
---

# 3장 Sport News Combinator 기능추가

이번 장에서 다룰 내용  

- Typescript의 함수 : 타입을 사용하여 TypeScript 함수 규칙을 유연하게 설정
- 클래스와 인터페이스의 대한 깊이 있는 고찰 : 생성자와 스코핑 및 멤버 선언과 같은 클래스의 기능 살펴보기, 인터페이스를 사용하면 보다 객체지향적인 코드 작성이 가능함
- SNC 기능추가 : 헤더와 풋터를 위해 몇가지 다른 컴포넌트를 추가하여 기본 애플리케이션을 개선
- Angular 템플릿과 모듈 : Angular의 템플릿과 데이터 바인딩이 어떻게 동작하는지 살펴보기, 애플리케이션에서 모듈을 정의하는 방법 살펴보기

---

## 함수

TypeScript 함수는 Javascript 함수에 더 이해하기 쉽고 오류가 없는 코드를 작성 할 수 있도록 몇가지 기능을 추가  

{% highlight typescript %}
function printFullName(firstName:string,lastName:string):string{
	return firstName + ' ' + lastName;
}
{% endhighlight %}

TypeScript에서는 주로 클래스 내부의 메서드를 작성하기 위해 함수를 사용함  
클래스 내부에서 사용하는 경우에는 function 키워드가 필요하지 않음  

### 함수의 타입

TypeScript 함수는 파타리터와 반환 값을 정의하기 위해 타입을 사용  
함수를 호출할 때 따라야 하는 규약을 만들 수 있음  
위의 예제에서 printFullName 이라는 함수는 firstName과 lastName 이라는 두개의 문자열 파라미터가 있음  

컴파일러는 전달된 값의 타입 일치 여부를 확인  
반환 값의 타입을 정의할 수도 있음  

#### 화살표 함수(Arrow Function)

뚱뚱한(fat) 화살표 또는 람다(lambda) 함수라도고 불리움  
es6에 새로 도입되었으며 간단한 익명 함수를 작성하게 해줌  
위의 코드를 화살표 함수로 바꿔보면 아래와 같음  

{% highlight typescript %}
(firstName:string,lastName:string) => firstName + ' ' + lastName;
{% endhighlight %}

=> 를 기준으로 왼쪽에는 함수의 파라미터, 오른쪽에는 함수 본문  
아래는 화살표 함수에서 파라미터를 관리 다양한 방법  

- 빈 파라미터 : 파라미터가 없을 경우 빈 괄호를 사용 {% highlight typescript %}
    () => console.log('Hello World!!');
{% endhighlight %}

- 단일 파라미터 : 전달할 파라미터가 하나일 경우 괄호로 묶을 필요가 없음
    {% highlight typescript %}
    id => console.log(id);
{% endhighlight %}

- 다중 파라미터 : 파라미터가 여러개 있는 경우 파라미터를 괄호로 묶어야 함 
    {% highlight typescript %}
    (firstName:string,lastName:string) => firstName + ' ' + lastName;
{% endhighlight %}