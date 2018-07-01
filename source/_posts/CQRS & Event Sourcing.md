---
title: CQRS & Event Sourcing
date: 2018-07-01 14:24:00
tags: 
- CQRS 
- EventSourcing 
- 이벤트소싱
---

# 1. CQRS & Event Sourcing


## 1-1 EventSourcing

>  Capture all changes to an application state as a sequence of events.(어플리케이션의 모든 상태 변화를 순서에 따라 이벤트로 보관한다)

1.  전통적인 데이터 저장 방법에서 비지니스가 증가할수록 이력 테이블은 증가하고 복잡해짐

- 전통적인 방식 

![전통적인 방식의 저장](https://cdn-images-1.medium.com/max/800/1*ne9wMAjAXRD50_U-HgdrHQ.png)

- 이벤트 소싱 방식
![이벤트 소싱 저장 방식](https://cdn-images-1.medium.com/max/800/1*0l2GybnXcJljJR1bzFUxTg.png)

2. 모든 상태 변화를 *Event* 로 관리
    - Event의 특징 Immutable(불변적),Append Only ( No! UPDATE / DELETE ) 
    - Event의 구성
      - 이벤트 식별자 (Aggregate Id)
      - 이벤트 타입
      - 버전
      - 발생시간
      - 내용(payload)
3. 모든 Event는 순서대로 영구 저장소에 저장
  ![이벤트저장](이벤트저장.png)

4. 이벤트 조회시에 Snapshot의 사용
    - 매번 현재 상태를 알기위해서 이벤트를 Replay 해야하는데 항상 모든 이벤트를 조회 활수는 없어서 스냇샵을 둠
    - Snapshot
      - 이벤트 저장시 생성
      - 시간(time) or 이벤트 발생 개수를 기준으로 생성 
      - Snapshot은 EventStore와 분리된 저장소에 저장 ( 성능상 주로 in Memory 저장소를 사용함)
    - Snapshot 구성
      - 이벤트 식별자 ( Aggregate Id )
      - 이벤트 version ( replay된 마지막 Ver.)
      - Replay된 도메인 객체


## 1-2.Command and Query Responsibility Segregation (CQRS) pattern(명령과 조회의 책임 분리) 의 개념 

- Commands -> Writes(쓰기)
- Queries -> Read(읽기)
- 상태변경을 처리하는 Command Model와 데이터 조회 Query Model을 분리 구현
- 이벤트 소싱에선 사실상 필수 

>  => 이벤트 소싱 간단하게 이해하고 , 추후에 CQRS 공부해야함 이해 못함


### - 출처 및 자료

> [spring springcamp2017 - 심천보님 발표 자료 참고](https://github.com/jaceshim/springcamp2017)
> [Rachel 님 블로깅 자료](https://medium.com/@mjspring/%EC%9D%B4%EB%B2%A4%ED%8A%B8-%EC%86%8C%EC%8B%B1-event-sourcing-%EA%B0%9C%EB%85%90-50029f50f78c)
> [MS문서-cqrs](https://docs.microsoft.com/en-us/azure/architecture/patterns/cqrs#when-to-use-this-pattern)