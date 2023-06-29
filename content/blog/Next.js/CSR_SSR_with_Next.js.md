---
title: '원티드 프리온보딩 챌린지 - CSR / SSR with Next.js 사전과제'
date: 2023-06-29 20:15
category: 'Next.js'
draft: false
---

### 원티드 프리온보딩 챌린지 - CSR / SSR with Next.js

#### Assignment) 개인 블로그에 아래 질문에 대한 포스팅을 하고 링크를 제출해주세요.

1. CSR(Client-side Rendering)이란 무엇이며, 그것의 장단점에 대하여 설명해주세요.

> CSR이란 클라이언트에서 웹 페이지에 필요한 소스 코드를 다운 받아 클라이언트에서 계산하여 화면을 렌더링하도록 구성된 방식입니다.

> 장점: 웹 페이지의 상호작용으로 인한 데이터 변경을 감지하여 빠르게 리렌더링 할 수 있어서 빠른 사용자 경험을 제공합니다.

> 단점: CSR 프레임워크 혹은 라이브러리를 다운 받고 모든 소스 코드를 다운 받기 때문에 초기 화면 렌더링 속도가 느릴 수 있습니다.  
>  클라이언트에서 동적으로 화면을 렌더링하기 때문에 SEO에 친화적이지 않아 검색 시 노출되지 않습니다.

2. SPA(Single Page Application)로 구성된 웹 앱에서 SSR(Server-side Rendering)이 필요한 이유에 대하여 설명해주세요.

> CSR의 단점인 SEO에 최약한 점을 해결할 수 있습니다.

> Data Fetching이 중첩된 컴포넌트의 경우 waterfall 현상이 일어나서 렌더링 속도가 느려지는데, 이를 Server-side Rendering을 통해 서버측에서 Data Fetching을 관리한다면 더 빠르게 렌더링 할 수 있습니다.

3. Next.js 프로젝트에서 `yarn start(or npm run start)` 스크립트를 실행했을 때 실행되는 코드를 Next.js Github 레포지토리에서 찾은 뒤, 해당 파일에 대한 간단한 설명을 첨부해주세요.

> `packages/next/src/bin/next.ts` 해당 파일이 실행됩니다. 해당 파일에서는 아래와 같은 일을 수행합니다:

- `react`, `react-dom`이 있는지 의존성 체크를 합니다

- args에 `--version`이 있다면 버전을 로그에 기록하고 종료합니다.
- `next --help` 명령일 때를 체크합니다.
- `--inspect`이 있다면 사용하지않는 flag라고 에러를 띄웁니다.
- 환경 변수를 설정합니다.
- pnp 버전을 체크합니다.
- `import { commands } from '../lib/commands'`에서 가져온 commands 에서 `commands[start]`를 실행합니다.
