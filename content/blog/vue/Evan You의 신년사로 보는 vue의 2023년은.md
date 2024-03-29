---
title: 'Evan You의 신년사로 보는 Vue의 2023년은?'
date: 2023-01-25 00:31
category: 'Vue'
draft: false
---

얼마 전 유튜브 채널 조승연의 탐구생활에 [`세계정상들의 신년사로 보는 2023년 생존법`](https://youtu.be/-S2wiyZ72M4)을 봤다.  
신년사를 통해서 각 나라의 목표를 알 수 있는 좋은 영상이었는데 문득 vue의 2023년을 알고싶다면 에반 유의 신년사를 보면 되지 않을까 생각이 들어서 찾아보게 되었다.  
해당 글은 나의 어줍짢은 번역 실력과 주관이 담긴 글이다. 정확한 정보를 보기 위해서는 [원문](https://blog.vuejs.org/posts/2022-year-in-review.html)을 보는 것을 추천한다.

# 2022년 리뷰

새해 복 많이 받으세요 Vue 커뮤니티 여러분! 2023년을 앞두고, 2022년에 해온 일들을 정리하고 다가오는 2023년에는 무엇을 기대할지 이야기하고 싶습니다.

---

## 2022년 요약

2022년 2월, 우리는 [Vue의 기본(default) 버전을 3.x 버전으로 바꿨습니다](https://blog.vuejs.org/posts/vue-3-as-the-new-default.html). 이 전환은 버전 3에 대한 프레임워크의 모든 공식적인 부분의 준비 상태를 표시했으며, 최신 모범 사례에 대한 가이드를 제공하는 공식 문서의 대대적인 개정을 진행했습니다.

우리는 여전히 Vue 3 생태계로 이동하기 위한 전환기에 있습니다. 전환기가 끝난 후에, 우리는 개발 도구들에 투자함으로써 Vue 개발 경험을 더 개선하고자 집중했습니다. 우리 팀원들은 [Vite](https://vitejs.dev/) 개발에 적극적으로 참여해왔으며, [Volar 1.0](https://blog.vuejs.org/posts/volar-1.0.html)을 통해 Vue의 IDE와 타입스크립트 지원을 크게 개선했습니다.

2022년에 걸쳐, 우리는 Vue 3의 NPM 사용이 **약 200%** 성장한 것을 봤습니다. 커뮤니티 측면에서 Vue 3 생태계는 이제 생산성을 높이는데 도움이 되는 훌륭한 솔루션이 될 때가 됐습니다. [Nuxt 3](https://nuxt.com/) 와 [Vuetify 3](https://vuetifyjs.com/en/) 둘 다 2022년 11월에 안정화 상태에 도달했으며 [NativeScript for Vue 3](https://github.com/nativescript-vue/nativescript-vue)는 최근에 베타 버전이 출시되었습니다.

Vue 3가 기본 버전임에도, 많은 사용자들은 마이그레이션 비용 때문에 Vue 2에 머물러야하는 것을 알고 있습니다. Vue 2 사용자가 프레임워크의 발전으로 이익을 얻을 수 있도록 Vue 2의 소스 코드를 타입스크립트로 옮기기로 결정하고 Vue 3의 핵심 기능 중 일부를 Vue 2.7에 반영했습니다. 또한 Vite, Vue Devtools와 Volar 모두 Vue 2와 Vue 3를 동시에 지원하도록 했습니다.

## 2023년에 기대되는 것

### 더 작게, 더 자주 마이너(Minor) 버전을 릴리즈

Vue 2의 마지막 마이너 릴리즈(2.7)이 출시됨에 따라, 2023년에는 Vue 3 코어의 신규 기능들이 본격화될 것으로 예상합니다. 작업할 기능들은 꽤 많습니다!

그 중 하나는 릴리즈 주기를 개선하는 것입니다. Vue는 [semver](https://semver.org/)를 따르기 때문에, 마이너 버전에 신규 기능을 포함해야 합니다. 과거에는 많은 기능을 포함하여 불규칙적으로 마이너 버전을 릴리즈를 하는 "big minor" 방식을 사용했습니다. 이로 인해 복잡도가 높은 기능 개발로 인해 복잡도가 낮은 기능의 반영이 늦어지는(block) 결과가 나타났습니다. 2023년에는 더 많은 기능을 더 빨리 사용할 수 있도록 볼륨은 작지만 더 자주 마이너 버전을 릴리즈하고자 합니다.

이 말은 즉, 3.3 버전에 들어가는 기능들이 조정 될 것임을 의미합니다. 원래 Suspense 와 Reactivity Transform 기능을 3.3 버전에서 실험 상태(experimental status)로 마칠 계획이었지만, 두 기능에 대해 RFC 토론을 더 할 필요를 느꼈습니다. 이 일로인해 복잡도가 낮은 기능의 반영을 늦춰지는 일도 생기지 않도록 하기 위함도 있습니다. 현재 3.3 버전의 목표는 RFC 토론이 필요없이 명확하게 제안되거나 계획된 기능들로 출시되는 것입니다. 예를 들어 `<script setup>` 매크로에서 외부에서 가져온 타입을 지원하는 기능 같은 것들입니다.

이와 동시에 아래와 같은 일들도 진행할 예정입니다.

1. Suspense 와 Reactivity Transform의 준비 상황을 좀 더 평가합니다.
2. 사용자가 제안한 뛰어난 RFC와 기능 요청을 살펴보는 데 더 시간을 투자합니다.
3. SSR lazy hydration 처럼 3.4 버전 이후에 도입할 기능에 대해 RFC를 게시합니다.

이번 달 말에 더 자세한 내용을 기대해주세요.

다른 주목할 사항으로는 가까운 미래에는 큰 변경사항이 없다는 점입니다. v2에서 v3로 전환하는 동안에 사용자가 직면한 문제들을 알고있기 때문에 앞으로는 더 나은 Vue의 장기적인 업그레이드 관리(upgrade story)를 하고자 합니다.

### Vapor Mode

Vapor Mode는 [Solid](https://www.solidjs.com/)에서 영감을 받아 실험해 온 대안적인 컴파일 전략입니다. 같은 Vue SFC(Single-File Components)가 주어지면 Vapor Mode에서는 성능이 더 좋은 자바스크립트 결과를 컴파일하고, 메모리도 더 적게 사용합니다. 그리고 현재 가상 돔(DOM) 기반의 결과와 비교하여 더 적은 런타임 지원 코드를 요구하게 됩니다.
아직 초기 단계이지만, 고수준(high level)의 요점을 정리하자면:

- Vapor Mode는 성능이 가장 중요한 사례를 위한 것입니다. 옵션으로 제공되며 기존 코드에는 영향을 주지 않습니다.
- 적어도, Vapor 컴포넌트 하위 트리를 기존 Vue 3 앱에 포함할 수 있을 것입니다. 이상적으로는 컴포넌트 레벨에서 세분화된 선택을 취할 수 있기를 희망합니다. 같은 앱에서 Vapor 컴포넌트와 non-Vpor 컴포넌트를 자유롭게 혼합하는 것입니다.
- Vapor 컴포넌트로만 앱을 빌드하면 가상 돔을 번들에서 삭제할 수 있습니다. 그로인해 기본 런타임 크기가 크게 줄어듭니다.
- 최고의 성능을 내기 위해 Vapor Mode는 Vue 기능의 일부(subset)만 지원할 것입니다. 특히, Vapor Mode 컴포넌트는 오직 컴포지션 API와 `<script setup>`만을 지원할 것입니다. 하지만, Vapor 와 non-VAPOR 컴포넌트를 지원되는 기능에서는 둘 다 똑같이 작동될 것입니다.

연말에 더 많은 진전이 있다면 더 자세히 공유하겠습니다.

### 컨퍼런스

2023년에는 이미 많은 Vue 컨퍼런스가 준비되어 있습니다:

- [Vue.js Amsterdam](https://vuejs.amsterdam) - Feb 9-10, Amsterdam, The Netherlands
- [Vue.js Live](https://vuejslive.com) - May 12 & 15th, London, UK
- [VueConf US](https://us.vuejs.org/) - May 24-26th, New Orleans, USA
- VueFes Japan - October 28th, Tokyo, Japan (info TBA)

### Vue 2의 EOL(End of Life)까지 1년

리마인드 하자면, 오늘은 Vue 2 지원이 끝날 때까지 정확히 1년입니다. 이 부분에 대한 의미를 설명하는 페이지와 Vue 2 지원이 끝난 후에도 사용하는 사용자들을 위한 옵션을 개략적으로 만들었습니다: [Vue 2의 EOL 상세 정보와 추가 지원](https://v2.vuejs.org/lts/)

---

## 내 생각

2022년 Vue를 사용하는 사용자 입장에서 나도 리뷰를 한번 해보겠다.

Vue를 이용해서 밥벌어 먹는 입장에서 정말 기깔나게 잘 사용하고 싶은 욕심을 갖고있다. 하지만 국내 유명한 개발 컨퍼런스를 보면 항상 리액트 기반으로 설명해서 많은 영감을 얻지 못했다. 리액트의 철학과 뷰의 철학은 다르기 때문이다. 이로인해 아쉬운 마음이 있었다. 특히 Vue 3를 사용하는 입장에서 안그래도 작은 Vue를 Vue 2 와 3로 나누게 되니 참고할 만한 자료가 더더욱 없는 현실이었다. 그렇지만 더 잘 사용하기 위한 노력을 멈출 수 없기에 여러 시도를 하며 정답을 찾아가는 중이다.

에반 유의 신년사를 번역하면서 느낀 점은

1. Vue 2에서 Vue 3의 마이그레이션은 이제 불가피하다. 물론 철밥통이 되어 Vue 2를 계속 이어갈순 있겠지만 새로운 기능을 활용하려면 Vue 3로 마이그레이션은 이제는 필수가 된 것 같다. 발빠르게 Vue 3를 접목한 내 상황이 다행이라 느껴진다.
2. `<script setup>`에서 외부 타입을 지원하는 기능이 곧 추가된다는 점이 사용자 입장에서 상당히 좋은 소식이었다. 외부에서 타입을 가져올 수 없어서 다시 타입을 재선언했었는데 불편함이 사라질 것 같다.
3. Vapor Mode에 힘주어 말을한 것을 보니 올해 핵심 기능이 될 것 같다. 에반 유는 보통 영감을 받아서 더 좋은 결과물을 만들어내는데 해당 기능은 Solid에서 영감을 받았다고 한다. 요새 뉴페로 뜨는 Solid를 넘어서는 성능을 보여줄지 관심 포인트이다.
