---
title: RxJS - 1. Why RxJS?
date: 2017-08-17 14:39:10
tags:
---

UPDATE     : 2017.08.17 번역 시작
Translator : ByoengGiKim

## Why RxJS?(왜 RxJS인가?) ##

<!-- toc -->

One question you may ask yourself, is why RxJS?  What about Promises?  Promises are good for solving asynchronous operations such as querying a service with an [XMLHttpRequest](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest), where the expected behavior is one value and then completion.  The Reactive Extensions for JavaScript unifies both the world of Promises, callbacks as well as evented data such as DOM Input, Web Workers, Web Sockets.  Once we have unified these concepts, this enables rich composition.
당신 스스로에게 왜 RxJS?라는 질문을 할지도 모른다. 프로미스는 어떤 예측된 동작 하나의 갑이나 그러고 나서 완료될시에 XMLHttpRequest 가지고 서비스를 질의하는 것과 같은 비동기적인 연산을 해결하는데 아주 좋다. 자바스크립트를 위해서 Reactive Extensions는 각각 프로미스, 콜백 그리고 Dom 입력, 웹 워커들, 웹소켓들들의 이벤트 데이터까지고 통합했다. 우리가 그러한 개념들로 통합되다면, 이것은 풍부한 구성을 가능하도록 한다.



To give you an idea about rich composition, we can create an autocompletion service which takes the user input from a text input and then query a service, making sure not to flood the service with calls for every key stroke, but instead allow to go at a more natural pace.

당신에게 풍부한 구성에 대한 아이디어들 주기위해서, 우리는 사용자가 텍스트를 입력하고 나면 서비스를 질의하고 자동완성 서비스를 만들수 있다, 모든 키 입력에 호출이 부하를 주기 않도록 해야하고, 하지만 대신에 더 자연스런 속도로 되어야만 한다.

First, we'll reference the JavaScript files, including jQuery, although RxJS has no dependencies on jQuery...
첫번째로,RxJS는 jQuery에 의존은 없지만 jQuery를 포함한JavaScript 파일을 참고할것이다, 

```html
<script src="http://code.jquery.com/jquery.js"></script>
<script src="rx.lite.js"></script>
```
Next, we'll get the user input from an input, listening to the keyup event by using the [`Rx.Observable.fromEvent`](content/observable/observable_methods/fromevent.html) method.  This will either use the event binding from [jQuery](http://jquery.com), [Zepto](http://zeptojs.com/), [AngularJS](https://angularjs.org/) and [Ember.js](http://emberjs.com/) if available, and if not, falls back to the native event binding.  This gives you consistent ways of thinking of events depending on your framework, so there are no surprises.

다음에는, 우리는 키 이벤트를 감지하는  [`Rx.Observable.fromEvent`](content/observable/observable_methods/fromevent.html) 메서드를 사용하여 사용자의 입력을 받을 것이다. 이벤트 바인딩은 [jQuery](http://jquery.com), [Zepto](http://zeptojs.com/), [AngularJS](https://angularjs.org/) 그리고 [Ember.js](http://emberjs.com/) 등을 쓸수있다면 사용하거나 그게 안된다면 네이티브 이벤트 바인딩에 의하여 사용할것이다. 이 방식은 우리의 체계상 이벤트에 바인딩하는 일관된 방법으로 생각되기 때문에, 크게 놀랄 것은 없다.

```javascript
var $input = $('#input'),
    $results = $('#results');
/* 오직 각각의 Keyup으로부터 값을 얻는다 */
var keyups = Rx.Observable.fromEvent($input, 'keyup')
    .map(e => e.target.value)
    .filter(text => text.length > 2);
/* 지금 500ms 동안에 입력을 throttle/debounce 한다 */
var throttled = keyups.throttle(500 /* ms */);
/* 지금 오로지 변경된 값만 얻기 때문에 , 우리는 화살표를 제거하거나 다른 문자열들을 컨트롤한다. */
var distinct = throttled.distinctUntilChanged();
```

Now, let's query Wikipedia!  In RxJS, we can instantly bind to any [Promises A+](https://github.com/promises-aplus/promises-spec) implementation through the [`Rx.Observable.fromPromise`](content/observable/observable_methods/frompromise.html) method or by just directly returning it, and we wrap it for you.
지금, Wikipedia로 검색해보자! RxJS 로, 우리는 즉시 [`Rx.Observable.fromPromise`](content/observable/observable_methods/frompromise.html) 통한 [Promises A+](https://github.com/promises-aplus/promises-spec) 구현 [`Rx.Observable.fromPromise`] 이나 이것을 직접적으로 리턴해서 바인딩할수있고, 그리고 우리는 이것을 감쌀것이다.



```javascript
function searchWikipedia (term) {
    return $.ajax({
        url: 'http://en.wikipedia.org/w/api.php',
        dataType: 'jsonp',
        data: {
            action: 'opensearch',
            format: 'json',
            search: term
        }
    }).promise();
}
```

Once that is created, now we can tie together the distinct throttled input and then query the service.  In this case, we'll call [`flatMapLatest`](content/observable/observable_instance_methods/flatmaplatest.html) to get the value and ensure that we're not introducing any out of order sequence calls.

한번 이것이 생성되면,지금  distinct의 입력을 throttled 되게하고, 서비스를 질의하도록 묶을수있다. 이 경우에서는, 우리는 [`flatMapLatest`](content/observable/observable_instance_methods/flatmaplatest.html)를 호출할것이다.

```javascript
var suggestions = distinct.flatMapLatest(searchWikipedia);
```

Finally, we call the subscribe method on our observable sequence to start pulling data.
마지막으로, 우리는 observable sequence 가 데이터를 끌어오록 하깅 위해서 subscribe 메서드를 호출했다.

```javascript
suggestions.subscribe( data => {
    var res = data[1];
    /* 바인딩과 같이 데이터를 가지고 어떻 작업을 해라 */
    $results.empty();
    $.each(res, (_, value) => $('<li>' + value + '</li>').appendTo($results));
    }, error => {
    /* 에러를 핸들링한다. */
    $results.empty();
    $('<li>Error: ' + error + '</li>').appendTo($results);
});
```