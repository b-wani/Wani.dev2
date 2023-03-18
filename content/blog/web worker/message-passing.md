---
title: '웹 워커 통신 방법 - 메시지 패싱'
date: 2022-11-01 19:51:14
category: 'Web Worker'
draft: false
---

지난 글에서는 웹워커의 종류와 특징들을 찍먹하는 내용을 작성했는데요 이번에는 웹워커가 실제로 메인 쓰레드와 어떻게 데이터를 주고 받는지에 대해 작성하겠습니다. 제가 처음 웹워커를 개발했을 때 저만의 규칙을 만들어서 메인 쓰레드와 웹워커 간의 메시지 패싱을 구현했는데 구조도 별로고 다른 사람이 보기에도 어렵더라구요. 무엇보다 찝찝한 건 저만의 규칙이라는 것이 제일 컸습니다. 그래서 정형화된 패턴을 가지고 적용하면 어떨까 생각이 들어 찾아보고 정리해봤습니다.

## RPC 패턴

관련된 책을 읽던 중 RPC 패턴을 발견했습니다! RPC(Remote Procedure Call) 패턴이란 함수와 인자로 된 표현 양식을 직렬화하여 외부에 전달하여 실행시키는 방법이라고 하는데요, 아래와 같은 구조로 소통한다고 합니다.

> squareSum(4) → square_sum|num:4

예시를 보니까 어지럽네요; 다행히 이를 확장시켜 JSON-RPC 라는 메시지 통신 방식이 존재합니다. 요청과 응답을 객체에 담아 우리에게 익숙한 JSON 형식으로 표현하는 방식인데요 예시를 JSON-RPC 버전으로 바꾸면 아래와 같습니다.

> squareSum(4) → { jsonrpc: ‘2.0’, method: ‘square_sum’, params: [4], id: 1 }

위와같이 표현한다면 id를 기준으로 요청과 응답이 어떻게 연결되는 지 명확하게 알 수 있습니다!  
하지만 새롭게 추가된 jsonrpc 필드는 뜬금없이 무엇일까요? 해당 필드는 JSON-RPC 버전을 가리키는데 네트워크 설정 시 활용되는 값입니다. \*Web3.js 에서도 JSON-RPC 형식을 따릅니다.  
웹 워커의 경우 Structured Clone Algorithm 을 사용하여 깊은 복사된 객체를 넘겨주기 때문에 JSON 직렬화/역직렬화 과정을 거칠 필요가 없습니다. 따라서 jsonrpc 필드는 브라우저의 웹 워커에서는 큰 의미가 없다고 판단하여 구성하지 않았습니다.

## 명령 분배 패턴

JSON-RPC 패턴으로 요청하지만 응답을 받는 주체에서는 어떤 코드를 실행할지 막막합니다.  
명령 분배 패턴을 적용한다면, 직렬화된 명령어를 받아서 실행해야 할 함수를 찾을 수 있습니다.

```javascript
// 명령 분배 패턴 예시
const commands = {
  squareSum(max) {},
  fibonacci(limit) {},
}

const dispatch = (method, args) => {
  if (commands.hasOwnProperty(method)) {
    return commands[method](...args)
  }
  throw new TypeError(`Command ${method} not defined!`)
}
```

- 워커가 수행할 명령들을 정의해놓은 commands 라는 객체로 만들어서 관리합니다.
- `hasOwnProperty` 를 사용해 method 속성을 찾으러 **proto** 객체까지 탐색하는 일을 막아줍니다.

## 구현

그렇다면 이제 JSON-RPC 패턴과 명령 분배 패턴을 이용하여 간단하게 구현해보겠습니다.

### Folder structure

```text
project
|- index.html.   // 브라우저에서 워커 실행을 위한 html 파일입니다.
|- main.js.      // 프로그램의 엔트리 파일입니다.
|- worker.js.    // 워커 코드를 작성한 파일입니다.
|- rpc-worker.js // RpcWorker 클래스를 정의한 파일입니다.
|- commands.js.  // 워커가 처리할 명령을 작성한 파일입니다.
```

### index.html

```html
<html>
  <head>
    <meta charset="UTF-8" />
    <title>Worker Patterns</title>
    <script src="main.js" type="module"></script>
  </head>
</html>
```

- 브라우저에서 실행하기 위해서 html 파일을 만듭니다.

### commands.js

```javascript
const sleep = ms => new Promise(res => setTimeout(res, ms))

const commands = {
  async square_sum(max) {
    await sleep(Math.random() * 5000)
    let sum = 0
    for (let i = 0; i < max; i++) {
      sum += Math.sqrt(i)
    }
    return sum
  },
  async fibonacci(limit) {
    await sleep(Math.random() * 1000)
    let prev = 1n,
      next = 0n,
      swap
    while (limit) {
      swap = prev
      prev = prev + next
      next = swap
      limit--
    }
    return String(next)
  },
  async bad() {
    await sleep(Math.random() * 3000)
    throw new Error('oh no')
  },
}

export default commands
```

- 워커가 수행할 명령들을 정의한 commands 객체를 정의합니다.
- 비동기 함수를 만들기 위해 sleep 이라는 함수를 통해 commands 의 명령을 호출하도록 합니다.

### worker.js

```javascript
import commands from './commands.js'

self.onmessage = async msg => {
  const { method, params, id } = msg.data
  let data

  if (commands.hasOwnProperty(method)) {
    try {
      const result = await commands[method](...params)
      data = { id, result }
    } catch (err) {
      data = { id, error: { code: -32000, message: err.message } }
    }
  } else {
    data = {
      id,
      error: {
        code: -32601,
        message: `method ${method} not found`,
      },
    }
  }

  postMessage(data)
}
```

- 미리 작성한 commands.js 파일을 import 합니다. 이때 worker는 type: ‘module’ 옵션이어야 합니다.
- 메인 스레드는 워커에게 JSON-RPC 구조로 메시지를 전달해줍니다.
- method 로 commands 에 등록되었는지 확인 후 파라미터를 전달하여 실행합니다. 실행한 결과는 메인 스레드로 전달합니다.
- 에러 코드 -32000은 실행 중 에러가 발생했을 때를 의미합니다.
- 에러 코드 -32601은 명령어가 존재하지 않음을 의미합니다.

### main.js

```jsx
import { RpcWorker } from './rpc-worker.js'

const worker = new RpcWorker('worker.js')

Promise.allSettled([
  worker.exec('square_sum', 1_000_000),
  worker.exec('fibonacci', 1_000),
  worker.exec('fake_method'),
  worker.exec('bad'),
]).then(([square_sum, fibonacci, fake, bad]) => {
  console.log('square sum: ' + square_sum.value)
  console.log('fibonacci: ' + fibonacci.value)
  console.log('fake: ' + fake.reason.message)
  console.log('bad: ' + bad.reason.message)
})
```

- RpcWorker 인스턴스를 생성하고 워커에 4개의 일을 줍니다.
- Promise.allSettled 함수를 사용한 이유는 성공과 실패 결과를 확인하기 위해서입니다.

### rpc-worker.js

```jsx
export class RpcWorker {
  constructor(path) {
    this.next_comand_id = 0
    this.in_flight_commands = new Map()
    this.worker = new Worker(path, { type: 'module' })
    this.worker.onmessage = this.onMessageHandler.bind(this)
  }

  onMessageHandler(msg) {
    const { result, error, id } = msg.data
    const { resolve, reject } = this.in_flight_commands.get(id)

    this.in_flight_commands.delete(id)

    if (error) {
      reject(error)
    } else {
      resolve(result)
    }
  }

  exec(method, ...args) {
    const id = ++this.next_comand_id
    let resolve, reject
    const promise = new Promise((res, rej) => {
      resolve = res
      reject = rej
    })
    this.in_flight_commands.set(id, { resolve, reject })
    this.worker.postMessage({ method, params: args, id })
    return promise
  }
}
```

- RpcWorker 클래스는 메인 스레드와 워커 스레드 간의 상호 작용을 도와주는 매개체 라고 생각하면 됩니다.
- path 값을 토대로 Worker 를 생성합니다.
- onMessageHandler 메서드를 통해서 워커에게 전달받은 데이터를 처리합니다. in_flight_commands 맵을 이용해서 전달 받은 데이터를 id 값으로 요청 주체와 매핑 시켜줍니다.
- exec 메서드는 워커에게 일을 시킬때 사용합니다. 변수 resolve, reject 를 promise 객체의 resolve, reject 에 참조시키고 고유한 id 값을 이용해서 해당 resolve, reject 를 저장합니다. exec 함수가 끝나도 클로저 때문에 in_flight_commands 에 resolve, reject 가 남아있습니다. promise 객체는 pending 상태가 되었다가 워커가 일이 끝난다면 resolve, rejec t에 올바른 결과값이나 에러 메시지가 담겨지게 됩니다.
- next_comand_id 를 단순히 숫자의 증가로 구현하였는데 이는 오버플로우가 발생한다면 문제의 소지가 있습니다. 실무에서는 uid 라이브러리를 적용할 생각합니다.

## 마치며

멀티스레드 기반 자바스크립트 책을 참고하여 JSON-RPC 패턴과 명령 분배 패턴을 이용해서 간단한 구현을 해봤는데요 저는 무엇보다도 exec 메서드에서 클로저를 활용하여 워커의 비동기 처리를 관리하는 부분이 인상 깊었습니다. 앞으로 여기서 적용한 두 패턴을 이용하여 실무에 적용 후 내용을 공유하려합니다.
