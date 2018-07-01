---
title: Protractor 와 e2e TEST 
date: 2018-06-30 19:24:00
tags: 
- BDD 
- Protractor 
- Jasmine 
- TDD 
- e2e TEST 
- Angular
---

# Protractor 와 e2e TEST 


## 1-1 Unit Testing

단위 테스트는 어플리케이션에서 가장 작은 단위로 쪼개서, 어플리케이션의 다른 부분과 분리하여 테스트하는 것이다. 

다른 부분과 연계하는 테스트는 아니며, 주로 함수, 메서드 단위 또는 독립적으로 한가지 기능만을 테스트한다.


## 1-2. e2e 테스트의 개념 
소프트웨어 테스트는 테스트의 규모(레벨)에 따라 유닛 테스트, 통합 테스트, 시스템 테스트, 인수 테스트 이렇게 4가지로 분류한다.
여기에서 E2E 테스트는 시스템 테스트에 속한다.

**E2E(End-to-End) 테스트**는 전체 시스템이 제대로 작동하는지 확인 하기 위한 테스트로 시나리오 테스트, 기능 테스트, 통합 테스트, GUI 테스트를 하는데 사용한다.

API와의 연동도 테스트 항목에 포함되기 때문에 일반적으로 목(Mock)이나 스텁(Stub)과 같은 테스트 더블을 사용하지 않으며 최대한 실제 시스템을 사용하는 **사용자 관점**에서 시뮬레이션 한다.

그래서 테스트 속도가 서비스 규모에 따라 상당히 느릴 수 있기 때문에 유닛 테스트나 기능 테스트를 위한 일반적인 테스트 자동화와 시스템 테스트를 위한 **E2E 테스트 자동화**를 함께 구성한다.

## 2. Protractor
Protractor는 E2E 테스트 자동화를 위해서 만들어진 e2e test framework이다.
Selenium 은 브라우저 자동화 프레임워크이다. Selenium은 Selenium Server, WebDriver APIs , WebDriver browser drivers 을 포함하고 있다. 이 포함된 도구는 유저들의 인터렉션을 시뮬레이션한다.
Protractor은 Selenium의 자동화된 시뮬레이트 시스템과 같이 동작한다.
![Protractor](https://www.protractortest.org/img/components.png)


Protractor와 Selenium이 같이 동작할시에, 다음에 유의하라.
Protractor는 WebDriverJS로 Selenium의 WebDriver API로 랩핑하고 있 다.
WebDriver 커맨드는 비동기적이다. WebDriver 커맨트는 컨트롤 플로우에 스케줄되고 promises 로 리턴된다, primitive 타입의 값을 주지는 않는다.


test scripts  command를  the Selenium Server에 보낸다. 명령어는 다시 브라우져의 드라이버와 통신한다.

![프로세스 통신](https://www.protractortest.org/img/processes.png)

## 3. Protractor style guide


## 3-1. Page objects

Page object는 어플리케이션의 페이지의 요소에 대한 정보를 캡슐화 함으로써 
깔끔한 테스트를 작성하는데 도움을 준다.

Page object는 여러 테스트에서 재활용할수있고 , 만약 당신이 어플리케이션이 변경되면, Page object도 변경되어야한다.

- Encapsulate information about the elements on the page under test
- They can be reused across multiple tests
- Decouple the test logic from implementation details

## 3-2. Locators 

> locator 는 어떻게 DOM 엘리멘트를 찾을지를  Protractor에게 전달하는것이다.

```js
by.css('.myclass') // Find an element using a css selector.
by.id('myid') // Find an element with the given id.
by.name('field_name') // Find an element using an input name selector.
// Find an element with a certain ng-model.
// Note that at the moment, this is only supported for AngularJS apps.
by.model('name')
// Find an element bound to the given variable.
// Note that at the moment, this is only supported for AngularJS apps.
by.binding('bindingname')
```

> 리스트로 되어 있는 엘리멘트를 찾을시에는 By를 사용한다.

```js
element(by.css('some-css'));
element(by.model('item.name'));
element(by.binding('item.name'));
$('my-css'); // 축약형
element(by.css('my-css'));
```

## 3-3. Actions 
```js
var el = element(locator);
el.click(); //  클릭 이벤트를 발생시킨다.
el.sendKeys('my text'); // 키입력값을 준다.
el.clear(); // 모든 Text 내용을 지운다.
el.getAttribute('value'); // 해당 엘리멘트의 속성을 가져온다.
// 모든 액션 함수는 Promise를 리턴하여, 비동기적으로 동작할수있다.
var el = element(locator);
el.getText().then(function(text) {
  console.log(text);
});
```
## 3-4. Finding Multiple Elements(여러개의 엘리멘트를 찾는법)


```js
element.all(by.css('.selector')).then(function(elements) {
  // elements is an array of ElementFinders.
});
```

element.all() 는 몇가지의 helper 함수들을 가진다.
```js
element.all(locator).count(); // Number of elements.
element.all(locator).get(index); // Get by index (starting at 0).
element.all(locator).first(); // First and last.
element.all(locator).last();
```
locator을 CSS Selectors 사용시에는  , 아래와 같이 단축해서 사용할수 있다.
```js
$$('.selector');
// Is the same as:
element.all(by.css('.selector'));
```

## 3-5. Finding Sub-Elements( 하위 엘리멘트를 찾는법)

```js
element(by.css('some-css'));      // 한개의 엘리멘트
element.all(by.css('some-css')); // 엘리멘트 리스트
element(by.css('some-css')).element(by.tagName('tag-within-css')); // 하위의 한개의 엘리멘트 
element(by.css('some-css')).all(by.tagName('tag-within-css')); // 하위의 여러개의 엘리멘트 리스트
element.all(by.css('some-css')).first().element(by.tagName('tag-within-css'));
element.all(by.css('some-css')).get(index).element(by.tagName('tag-within-css'));
element.all(by.css('some-css')).first().all(by.tagName('tag-within-css'));
```

#### 지원 대상 Protractor

- [Protractor 지원 브라우져 리스트 ](https://www.protractortest.org/#/browser-support)