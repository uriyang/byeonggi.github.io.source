---
title: RxJS - Introduction
date: 2017-08-17 12:59:33
categories: 
- javascript
tags: 
- rxjs
- reactiveX
---

UPDATE     : 2017.08.17
Translator : ByoengGiKim

[원본 출처 링크 : http://xgrommx.github.io/rx-book/index.html](http://xgrommx.github.io/rx-book/index.html)


# 1.RxJS v4.0

Reactive Extensions (Rx) is a library for composing asynchronous and event-based programs using observable sequences and LINQ-style query operators.
리액티브 확장(Rx)는 Observable 시퀀스들과 LINQ-스타일 쿼리 연산자들을 사용하여 비동기적이고 결합과 이벤트 지향 프로그램에 대한 라이브러리이다.

Data sequences can take many forms, such as a stream of data from a file or web service, web services requests, system notifications, or a series of events such as user input.
데이터 시퀀스는 파일 , 웹서비스, 웹서비스 리퀘스트, 시스템알람, 또는 연속적인 사용자의 입력 같은 스트림과 같은 많은 형태를 취한다.

Reactive Extensions represents all these data sequences as observable sequences. An application can subscribe to these observable sequences to receive asynchronous notifications as new data arrive.

Reactive Extensions(Rx)은 observable 시퀀스들 같이 모든 데이터 시퀀스를 나타낸다. 어플리케이션은 새로운 데이터가 들어올때마다 비동기적인 알람을 받기 하기 위해서
observable sequences을 구독할수 있다.

RxJS has no dependencies which complements and interoperates smoothly with both synchronous data streams such as iterable objects in JavaScript and single-value asynchronous computations such as Promises as the following diagram shows:

다음 다이어그램과 같이 프로미스와 같은 하나의 single-value 비동기적인 연산과 자바스크립에서 iterable objects와 같은 동기적인 데이터 스트림을 가지면서 상호작용과 보완해주면서 RxJS는 동작한다. 

<center>
<table style="display: table">
    <thead>
        <tr>
            <th></th>
            <th>Single return value</th>
            <th>Mutiple return values</th>
        </tr>
    </thead>
    <tbody>
        <tr>
          <td>Pull/Synchronous/Interactive</td>
          <td>Object</td>
          <td>Iterables (Array | Set | Map | Object)</td>
        </tr>
        <tr>
          <td>Push/Asynchronous/Reactive</td>
          <td>Promise</td>
          <td>Observable</td>
        </tr>
    </tbody>
</table>
</center>

To put it more concretely, if you know how to program against Arrays using the Array#extras, then you already know how to use RxJS!

<center>
<h3>Example code showing how similar high-order functions can be applied to an Array and an Observable</h3>

<table style="display: table">
    <thead>
        <tr>
            <th style="text-align:center;" colspan="2">Iterable</th>
            <th style="text-align:center;" colspan="2">Observable</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td colspan="2">
                <pre>

```javascript
getDataFromLocalMemory()
    .filter (s => s != null)
    .map(s => `${s} transformed`)
    .forEach(s => console.log(`next => ${s}`))
```
                </pre>
            </td>
            <td colspan="2">
                <pre>
```javascript
getDataFromNetwork()
    .filter (s => s != null)
    .map(s => `${s} transformed`)
    .subscribe(s => console.log(`next => ${s}`))
```
                </pre>
            </td>
        </tr>
    </tbody>
</table>
</center>
