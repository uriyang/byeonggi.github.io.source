---
title: RxJS - 2. RxJS Design Guidelines(2.2 When to use RxJS)
date: 2017-08-17 14:49:57
categories: 
- javascript
- rxjs
tags: 
- rxjs
- reactiveX
---

# When to use RxJS(RxJS를 사용할때는)

### Use RxJS for orchestrating asynchronous and event-based computations(비동기적이고 이벤트 중심적 연산을 하려고 할때 사용하라)

Code that deals with more than one event or asynchronous computation gets complicated quickly as it needs to build a state-machine to deal with ordering issues.
Next to this, the code needs to deal with successful and failure termination of each separate computation. This leads to code that doesn’t follow normal control-flow, is hard to understand and hard to maintain.
한가지 이상의 이벤트 또는 비동기적인 연산을 다루는 코드에서 빠르게 복잡하게 되기 때문에 순차적인 이슈를 다를 때 상태-머신을 만드는 것이 필요하다. 
이것 다음에는, 코드는  성공과 실패의 종료에 대해서 각각 분리된 연산으로 다루는 것이 필요한다. 이것은 보통의 컨트롤 흐름으로 가지 않도록 하고, 이것을 이해하는것도 어렵고 유지하는것도 어렵다. 


RxJS makes these computations first-class citizens. This provides a model that allows for readable and composable APIs to deal with these asynchronous computations.
RxJS 그러한 연산들을 일급 객체로 만든다. 이는 비공기 연산시 다루는 읽기가능하고 구성가능한 API들을 다룰수 있는 모델을 제공한다. 

#### Sample ####

```js
var input = document.getElementById('input');
var dictionarySuggest = Rx.Observable.fromEvent(input, 'keyup')
  .map(() => input.value)
  .filter(text => !!text)
  .distinctUntilChanged()
  .throttle(250)
  .flatMapLatest(searchWikipedia)
  .subscribe(
    results => {
      list = [];
      list.concat(results.map(createItem));
    },
    err => logError(err)
  );
```

This sample models a common UI paradigm of receiving completion suggestions while the user is typing input.
이 예제는 사용자가 타이핑 입력을 할때 , 받아오는 완료 암시에 공통적인 UI 다이어그램을 모델링했다.

RxJS creates an observable sequence that models an existing `keyup` event on the input.
RxJS 이 `Keyup` 이벤트의 입력이 존재를 나타내는  observable sequence를 만든다.

It then places several filters and projections on top of the event to make the event only fire if a unique value has come through. (The `keyup` event fires for every key stroke, so also if the user presses left or right arrow, moving the cursor but not changing the input text).



Next it makes sure the event only gets fired after 250 milliseconds of activity by using the [`throttle`](../../observable/observable_instance_methods/throttle.html) operator. (If the user is still typing characters, this saves a potentially expensive lookup that will be ignored immediately).

In traditionally written programs, this throttling would introduce separate callbacks through a timer. This timer could potentially throw exceptions (certain timers have a maximum amount of operations in flight).

Once the user input has been filtered down it is time to perform the dictionary lookup. As this is usually an expensive operation (e.g. a request to a server on the other side of the world), this operation is itself asynchronous as well.

The [`flatMap`/`selectMany`](../../observable/observable_instance_methods/flatmap.html) operator allows for easy combining of multiple asynchronous operations. It doesn’t only combine success values; it also tracks any exceptions that happen in each individual operation.

In traditionally written programs, this would introduce separate callbacks and a place for exceptions occurring.

If the user types a new character while the dictionary operation is still in progress, we do not want to see the results of that operation anymore. The user has typed more characters leading to a more specific word, seeing old results would be confusing.

The `flatMapLatest` operation makes sure that the dictionary operation is ignored once a new `keyup` has been detected.

Finally we subscribe to the resulting observable sequence. Only at this time our execution plan will be used. We pass two functions to the `subscribe` call:
마지막으로 우리는 observable sequence의 결과를 구독한다. 
1. Receives the result from our computation. 연산의 결과를 받아라.
2. Receives exceptions in case of a failure occurring anywhere along the computation. 연산시에 어떤 곳에서도 실패가 발생한 케이스의 에러를 받아라.

#### When to ignore this guideline(이 가이드라인에서 넘어가도 될때는) ####

If the application/library in question has very few asynchronous/event-based operations or has very few places where these operations need to be composed, the cost of depending on RxJS (redistributing the library as well as the learning curve) might outweigh the cost of manually coding these operations.

만약  어플리케이션/라이브러리에 대한 질문중 너무 다중 메세지를 가지는 연산자들이 가진다면, RxJS에 대한 의존에 대한 비용(러닝 커브만큼 라이브러리의 재분배까지도)이
수동적으로 그러한 연산자 코딩의 비용이 클지도 모른다. 


### Use RxJS to deal with asynchronous sequences of data(비동기적인 데이터의 시퀀스들을 다룰때 사용하라)

Several other libraries exist to aid asynchronous operations in the JavaScript ecosystem. Even though these libraries are powerful, they usually work best on operations that return a single message. They usually do not support operations that produce multiple messages over the lifetime of the operation.

자바스크립트 생태계에서  몇몇 또다른 라이브러리들은 비동기 연산들을 지원하기 위해서 존재한다. 비록 그러한 라이브러리들이 강력할지라도, 보통 단일 메세지를 리턴하는 데에만 잘 돌아간다. 보통 그러한 라이브러리들은 연산자의 라이클 사이클 넘어서 다중 메세지를 제공하는 연산자를 제공하지는 않는다.

RxJS follows the following grammar: `onNext`* (`onCompleted`|`onError`)?. This allows for multiple messages to come in over time. This makes RxJS suitable for both operations that produce a single message, as well as operations that produce multiple messages.

RxJS 다음 문법을 따른다: `onNext`* (`onCompleted`|`onError`)?. 이것은 다중 메세지가 시간을 초과하여 오도록 허용한다. 이점은 RxJS가 단일 메세지를 제공하는 연산자, 다중 메세지를 공급하는 연산자까지도 제공하도록 한다.


```js

var fs = require('fs');
var Rx = require('rx');

// 읽기/쓰기 에 대한 스트림의 구현부 
function readAsync(fd, chunkSize) { /* impl */ }
function appendAsync(fd, buffer) { /* impl */ }
function encrypt(buffer) { /* impl */}

// 64k 블록 단위로 비동기적으로 읽는 방식으로 4기가 파일을 열어라
var inFile = fs.openSync('4GBfile.txt', 'r+');
var outFile = fs.openSync('Encrypted.txt', 'w+');

readAsync(inFile, 2 << 15)
  .map(encrypt)
  .flatMap(data => appendAsync(outFile, data))
  .subscribe(
    () => {},
    err => {
      console.log('An error occurred while encrypting the file: %s', err.message);
      fs.closeSync(inFile);
      fs.closeSync(outFile);
    },
    () => {
      console.log('Successfully encrypted the file.');
      fs.closeSync(inFile);
      fs.closeSync(outFile);
    }
  );

```

In this sample, a 4 GB file is read in its entirety, encrypted and saved out to another file.

이 샘플에서, 암호화되고 다른 파일로 저장된 4기가 파일 전체를 읽는다. 

Reading the whole file into memory, encrypting it and writing out the whole file would be an expensive operation.

메모리에 전체 파일을 읽는것, 이것을 암호화하고, 전체 파일로 쓰기는 것은 고비용의 연산이다.

Instead, we rely on the fact that RxJS can produce many messages.

대신에, 우리는 RxJS는 많은 메세지를 준다는 사실에 집중해야한다.

We read the file asynchronously in blocks of 64K. This produces an observable sequence of byte arrays. We then encrypt each block separately (for this sample we assume the encryption operation can work on separate parts of the file). Once the block is encrypted, it is immediately sent further down the pipeline to be saved to the other file.  The `writeAsync` operation is an asynchronous operation that can process multiple messages.

우리는 64K의 블록씩 비동기적으로 파일을 읽는다. 이것은 바이트 배열들의 observable 시퀀스를 준다. 그러고 나서 우리는 각각 블록을 개별적으로 암호화한다(이 샘플에서 우리는 암호화 연산은 파일에서 개별적인 부분들로 일어남을 가정한다.) 블록에서 암호화가 일어나면, 이는 즉시 빠르게 다른 파일의 저장되는 파이프라인으로 전달한다.
`writeAsync` 연산자는 다중 메세지를 처리할수 있는 비동기적인 연산이다.


#### When to ignore this guideline(이 가이드라인에서 넘어가도 될때는) ####

If the application/library in question has very few operations with multiple messages, the cost of depending on RxJS (redistributing the library as well as the learning curve) might outweigh the cost of manually coding these operations.

만약  어플리케이션/라이브러리에 대한 질문중 너무 다중 메세지를 가지는 연산자들이 가진다면, RxJS에 대한 의존에 대한 비용(러닝 커브만큼 라이브러리의 재분배까지도)이
수동적으로 그러한 연산자 코딩의 비용이 클지도 모른다. 
