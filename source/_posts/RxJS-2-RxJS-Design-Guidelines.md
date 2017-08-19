---
title: RxJS - 2. RxJS Design Guidelines
date: 2017-08-17 13:55:46
categories: 
- javascript
tags: 
- rxjs
- reactiveX
---

UPDATE     : 2017.08.17 번역 시작
Translator : ByoengGiKim

[원본 출처 링크 : http://xgrommx.github.io/rx-book/content/guidelines/index.html](http://xgrommx.github.io/rx-book/content/guidelines/index.html)

# 2.RxJS Design Guidelines(RxJS 설계 가이드라인)

<img style="display: block; margin: 0 auto; clear: right;"
  src="https://raw.githubusercontent.com/Reactive-Extensions/RxJS/master/doc/designguidelines/images/984368.png"
  alt="RxJS Logo">

* [Introduction](introduction/index.html)
* [RxJS를 사용할때](when/index.html)
  * [비동기이고 이벤트 중심 연산을 조정하는 RxJS 사용하라 ](when/index.html#use-rxjs-for-orchestrating-asynchronous-and-event-based-computations)
  * [RxJS는 비동기적인 데이터의 시퀀스 다룰때 사용하라](when/index.html#use-rxjs-to-deal-with-asynchronous-sequences-of-data)
* [The RxJS contract](contract/index.html)
  * [Assume the RxJS Grammar](contract/index.html#assume-the-rxjs-grammar)
  * [Assume resources are cleaned up after an `onError` or `onCompleted` messages](contract/index.html#assume-resources-are-cleaned-up-after-an-onerror-or-oncompleted-message)
  * [Assume a best effort to stop all outstanding work on Unsubscribe](contract/index.html#assume-a-best-effort-to-stop-all-outstanding-work-on-unsubscribe)
* [RxJS 사용하는법](using/index.html)
* [Operator 구현](implementations/index.html)

