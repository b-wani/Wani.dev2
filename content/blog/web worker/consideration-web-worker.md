---
title: '웹 워커를 실무에 적용하면서 고민한 내용...'
date: 2022-12-14 23:16:38
category: 'Web Worker'
draft: false
---

실무에서 웹 워커를 적용하면서 고민했던 내용을 기록합니다.

실무 프로젝트에서는 `Vue 3` `Vuex` `Typescript` `Webpack 5` 환경으로 구성되어있다.

적용하면서 고민했던 주제는 두 가지인데 나열하자면 아래와 같다.

1. 토큰 관리는 어디서 해야할까?
2. Vuex를 이용한 상태관리패턴을 유지할 것인가?

그렇다면 이제 주제별로 고민한 내용을 살펴보자

## 1. 토큰 관리는 어디서 해야할까?

API 요청 시 토큰 인증이 필요한데 이를 위한 토큰 관리를 어느 스레드에서 할지 고민이었다.

### Case 1. Session Storage

![Session Storage](./images/session-storage.png)

- API 요청 시 필요한 권한 토큰을 Session Storage에서 관리하는 방식이다.
- Web Worker에서 API 요청을 할 때 마다 Access Token을 넘겨줘야한다.
- Access Token 권한 만료 동기화를 일치시킬 수 있다.
- 새로고침 시 Token을 유지시킬 수 있다.

### Case 2. Worker

![Worker](./images/worker.png)

- 토큰을 Web Worker에서 관리하는 방식이다.
- 토큰을 Web Worker에서 관리하기 때문에 보안상 유리하다.
- 새로고침 시 매번 Token API를 요청해야한다.

### Case 3. Session Storage + Worker

![Session Storage + Worker](./images/session-storage-plus-worker.png)

- 토큰을 Session Storage와 Web Worker 둘 다 갖고있는 방식이다.
- Web Worker에 매번 토큰을 넘길 필요가 없다.
- 토큰을 Session Storage에 저장하기 때문에 새로고침 시 토큰이 유지된다.
- 관리 주체가 두곳이어서 관리 포인트가 늘어난다.

### 결론

Case 1. Session Storage를 선택했다.

그 이유는

1. 새로고침 시 토큰이 유지된다.
2. 토큰을 워커에 매번 넘기는건 적은 비용이다.
3. 두 스레드간의 토큰이 일치하도록 유지된다.
4. 현재 프로젝트와 유사한 구조이다.

## 2. Vuex를 이용한 상태관리패턴을 유지할 것인가?

![Veux](./images//vuex.png)
프로젝트에서 Vuex를 사용하여 개발하고 있기 때문에 위와 같은 상태관리패턴을 적용하고 있는 상태이다. 하지만 웹 워커를 도입한다면 어떤 패턴을 적용해야할지 고민이었다.

### Case 1. 웹 워커, Vuex 분리

![웹 워커, Vuex 분리](./images/case1-pattern.png)

- 웹 워커와 Vuex를 분리한 패턴이다.
- fetch, 응답 데이터는 웹 워커에서 관리한다.
- Vuex에서는 서버 응답데이터를 제외한 State를 관리한다.

### Case 2. 웹 워커 + Vuex

![웹 워커 + Vuex](./images/webworker-plus-vuex.png)

- Vuex를 거쳐서 웹 워커를 동작시키는 방식이다.
- 기존 방식에 상태관리패턴에서 웹 워커만 추가된 패턴이다.
- 상태관리는 Vuex, 로직 처리는 웹 워커에서 수행한다.

### Case 3. 웹 워커에서 Vuex 만들기

![웹 워커에서 Vuex 만들기](./images/vuex-in-worker.png)

- 웹 워커 내부에 Vuex를 만들어서 관리하는 방식이다.
- [참고링크](https://logaretm.com/blog/vuex-off-main-thread/#nature-of-javascript)

### 결론

Case 2. Vuex를 거쳐서 웹 워커를 동작시키는 방식을 선택했다.

이유는

1. 코드에서 Vuex를 dispatch 했을 때 내부 동작은 모른채로 예상한 기대값을 받아서 처리하고 있다. 이러한 흐름을 봤을 때 dispatch의 내부 동작을 웹 워커 코드로 수정한다면 짜여진 코드를 수정할 일이 적다. 여전히 코드단에서는 dispatch의 내부 동작을 신경쓰지 않고 사용할 수 있다.
2. 웹 워커에서 처리한 데이터를 state를 저장하기 때문에 다양한 컴포넌트에서 접근이 가능하다.

## 마치며

![역할](./images/role.png)
최종 결과는 기존에 Vuex에서 담당하던 계산 로직, API 호출을 웹 워커에 위임한 모습이다.

프론트엔드 프레임워크를 사용하면서 웹 워커를 사용한 사례가 없어서 힘들었다. 그 밖에 타입스크립트, 웹팩5 부분도 고려하다보니 예상치 못한 에러가 많았다.  
다행히 책과 인터넷을 뒤져가며 많은 시행착오 끝에 고민했던 부분에 대해 잘 결정한 것 같다.
