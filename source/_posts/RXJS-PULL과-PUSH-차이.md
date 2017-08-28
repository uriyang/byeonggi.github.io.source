---
title: RXJS - PULL과 PUSH 차이
date: 2017-08-22 23:29:09
tags:
---
# RXJS에서의 Pull 와 Push


UPDATE     : 2017.08.17 번역 시작
Translator : ByoengGiKim
[출처(일부 내용): http://reactivex.io/rxjs/manual/overview.html#observable](http://reactivex.io/rxjs/manual/overview.html#observable)

---

| | Single | Multiple |
| --- | --- | --- |
| **Pull** | [`Function`](https://developer.mozilla.org/en-US/docs/Glossary/Function) | [`Iterator`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Iteration_protocols) |
| **Push** | [`Promise`](https://developer.mozilla.org/en-US/docs/Mozilla/JavaScript_code_modules/Promise.jsm/Promise) | [`Observable`](../class/es6/Observable.js~Observable.html) |


*Pull* and *Push* are two different protocols that describe how a data *Producer* can communicate with a data *Consumer*.

*Pull* 와 *Push*는 어떻게 데이터*공급자*와 데이터*생산자* 서로 작용할지를 표현하는 서로 다른 규약이다.

**What is Pull?** In Pull systems, the Consumer determines when it receives data from the data Producer. The Producer itself is unaware of when the data will be delivered to the Consumer.
**Pull은 무엇인가?** Pull 체계에서, 소비자는 데이터 생산자로부터 데이터를 받을때를 결정한다. 공급자 자체는 언제 데이터가 소비자에게 전달되지를 알지 못한다.

Every JavaScript Function is a Pull system. The function is a Producer of data, and the code that calls the function is consuming it by "pulling" out a *single* return value from its call.
모든 자바스트립트를 Pull 시스템입니다. 함수는 데이터의 공급자이고, 그리고 함수가 호출하는 코드는 *single*리턴 값을 "pulling" 함으로써 소비하고 있다.

ES2015 introduced [generator functions and iterators](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/function*) (`function*`), another type of Pull system. Code that calls `iterator.next()` is the Consumer, "pulling" out *multiple* values from the iterator (the Producer).

ES2015는 Pull 시스템의 또다른 타입 [제너레이터 함수 그리고  iterators](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/function*) (`function*`)을 도입했다. `iterator.next()` 호출하는 코드는 소비자이고, iterator(생산자)로부터 *multiple* 값들을 "pulling" 하면서

| | Producer | Consumer |
| --- | --- | --- |
| **Pull** | **Passive:** 요청될때 데이터를 생산한다.| **Active:** 데이터가 요청될때를 결정한다.|
| **Push** | **Active:** 자신의 페이스대로 데이터를 생산한다. | **Passive:** 받은 데이터에 받응한다.|

**What is Push?** In Push systems, the Producer determines when to send data to the Consumer. The Consumer is unaware of when it will receive that data.

**무엇이 Push인가?** Push systems에서 , 공급자는 언제 소비자에게 데이터를 보낼지를 결정한다. 소비자는 언제 데이터가 받게 될지를 모른다.


Promises are the most common type of Push system in JavaScript today. A Promise (the Producer) delivers a resolved value to registered callbacks (the Consumers), but unlike functions, it is the Promise which is in charge of determining precisely when that value is "pushed" to the callbacks.

Promises는 오늘날 자바스크립에서 가장 일반적인 Push 시스템 유형이다. Promise(생산자)는 등록된 콜백에게 resolved 값을 전달하지만, 이것은 함수와 같지는 않다.
, 프로미스는 값이 콜백으로 "pushed"될때를 정확하게 결정하는 부분에 있다.




RxJS introduces Observables, a new Push system for JavaScript. An Observable is a Producer of multiple values, "pushing" them to Observers (Consumers).

자바스크립트 위한 새로운 Push system인 RxJS는  Observables 도입했다. Observers(소비자)에게 pushing 하는 Observable는 다중 값 공급자이다.

- A **Function** is a lazily evaluated computation that synchronously returns a single value on invocation.

- A **Function**은  동기적으로 실행에 대한 단일 값으로 리턴된 느리게 측정되는 연산이다.

- A **generator** is a lazily evaluated computation that synchronously returns zero to (potentially) infinite values on iteration.

- A **generator**는 동기적으로 iteration에 대한 0에서 (잠재적으로)무한한 값들을 리턴하는 느긋하게 측정되는 연산이다.

- A **Promise** is a computation that may (or may not) eventually return a single value.

- **Promise**는 결국 단일값으로 리턴되거나 그렇지 않은 연산이다.

- An **Observable** is a lazily evaluated computation that can synchronously or asynchronously return zero to (potentially) infinite values from the time it's invoked onwards.

- **Observable**은 동기적이거나 또는 비동기적으로 O 에서 (잠재적으로) 시간에 따라서 계속 발생하는 무한한 값들을 리턴할수 있는 느긋하게 측정할수 있는 연산이다. 

---


# 요약 

1. Pull - 소비자는 데이터를 생산자로부터 언제 받을지를 결정한다.
    - 소비자가 데이터가 필요하면 소비자에게 가져오는 방식
    
2. Push - 생산자는 데이터를 공급할 시점을 결정하고 소비자에게 데이터를 전달한다.
    - 생산자가 데이터를 소비자에게 넘겨주는 방식 