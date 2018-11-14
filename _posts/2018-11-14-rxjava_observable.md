---
layout: post
title:  "Rx Java - Observable"
date:   2015-04-18 08:43:59
author: eunkyung
categories: Software
tags:	"RX Java"
cover:  "/assets/rxjava_image.png"
---


#### Observable
옵저버 패턴을 구현. 옵저버 패턴(대표적인 예 : onClick 메소드)은 객체의 상태 변화를 관찰하는 관찰자(옵저버) 목록을 객체에 등록한다. 그리고 상태 변화가 있을 때마다 메서드를 호출하여 객체가 직접 목록의 각 옵저버에게 변화를 알려준다. 라이프 사이클은 존재하지 않으며 보통 단일 함수를 통해 변화를 알림. 옵저버블 네이밍의 의미는 "현재는 관찰되지 않았지만 이론을 통해서 앞으로 관찰할 가능성을 의미한다."이다.

Observable 클래스는 상황에 맞게 세분화해 각각 Observable, Maybe, Flowable 클래스로 구분해 사용

1. Maybe 클래스 : reduce() 함수나 firstElement()함수와 같이 데이터가 발행될 수 있거나 혹은 발행되지 않고도 완료되는 경우를 의미.
2. Flowable 클래스 : Observable에서 데이터가 발행되는 속도가 구독자가 처리하는 속도보다 현저하게 빠른 경우 발생하는 배압(Back pressure)이슈에 대응하는 기능을 추가로 제공.

Rxjava의 Observable은 세가지의 알림을 구독자에게 전달한다.
```
1. onNext : Observable이 데이터의 발행을 알림. 기존의 옵저버 패턴과 동일.

2. onComplete : 모든 데이터의 발행을 완료했음을 알림. onComplete 이벤트는 단 한번만 발생. 발생한 후에는 더 이상 noNext 이벤트가 발생해선 안됨. 

3. onError : Observable에서 어떤 이유로 에러가 발생할 때 알림. onError 이벤트가 발생하면 이후에 onNext 및 onComplete 이벤트가 발생하지 않음. 즉, Observable의 실행을 종료.
```
Observable을 생성할 때는 직접 인스턴스를 만들지 않고 정적 팩토리 함수를 호출.

#### just()
인자로 넣은 데이터를 차례로 발행하려고 Observable을 생성. (실제 데이터 발행은 subscribe함수를 호출해야 시작)
한 개의 값을 넣을 수도 있고 인자로 여러개의 값(최대 10개)을 넣을 수도 있다. (단 , 타입은 모두 같아야함)
