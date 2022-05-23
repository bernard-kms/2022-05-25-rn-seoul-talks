---
# see https://github.com/slidevjs/themes/tree/main/packages/theme-apple-basic
theme: apple-basic
layout: intro
---

## 2년간의 RN앱 퍼포먼스 개선 케이스스터디

feat. 마이루틴

<div class="absolute bottom-10">
  <span class="font-500">
    김민식, 2022-05-25, react-native-seoul
  </span>
</div>

<!--
The last comment block of each slide will be treated as slide notes. It will be visible and editable in Presenter Mode along with the slide. [Read more in the docs](https://sli.dev/guide/syntax.html#notes)
-->

---
layout: intro-image-right
image: '/app-intro.jpg'
---

# 소개
## 마이루틴
- 하루를 관리할 수 있는 습관/루틴 앱
- 멀티플랫폼을 위해 데이터베이스에 정보 저장
- Firebase를 이용하여 (거의)모든 로직이 앱에 존재

## 김민식
- 이 앱을 2년간 만들어옴

---
layout: image-right
image: '/firebase-performance.png'
---

# 목표 정하기
- 목표가 날카롭지 않으면 작업 예측 😵
- 뇌피셜, 기분으로 작업 🙅
- 정량적, 정성적 뭐든 👌
  - 동영상 녹화
  - Firebase Performance Monitoring(같은 것)

e.g. <span class="font-700">"우리가 정의한 콜드 시작시간 지표가 유저평균 1초 이내가 되도록 한다"</span>

---

# 불필요한 렌더링을 줄이자

- 잦은 컴포넌트 리렌더링이 병목현상을 일으킨다
- 사실 React의 지식을 그대로 적용할 수 있다: memoization, pure component, immutable data structures
  - 👉 [https://reactjs.org/docs/optimizing-performance.html](https://reactjs.org/docs/optimizing-performance.html)

## 왜 이렇게 자주 렌더링될까?
- [why-did-you-render](https://github.com/welldone-software/why-did-you-render)
  - ...monkey patches React to notify you about potentially avoidable re-renders.
- 레퍼런스 비교가 아닌 값 비교

---

# 상태 업데이트가 모두 끝나기 전까지 렌더링을 막는다
한번의 렌더링으로 최종 상태를 그리도록
- e.g <span class="font-700">"홈 스크린을 렌더링하는 데 필요한 모든 상태가 준비 될 때 까지 스플래시 스크린에서 대기"</span>
- `unstable_batchedUpdates()` from React

```tsx
useEffect(() => {
  (async () => {
    setLoading(true);
    // some async state updates...
    unstable_batchedUpdates(() => {
      // some heady state updates...
    });
    setLoading(false);
  })();
}, []);

if (loading) {
  <Loading />
}

return (
  <Component ...>
)
```
---
layout: image-right
image: '/react-native-firebase.png'
---

# 네이티브 코드의 병목
서드파티 라이브러리, 혹은 우리가 짠 네이티브 코드

- `react-native-firebase`의 병목
- RN core 컴포넌트의 병목

---
layout: image-right
image: '/new-arch.png'
---
# The New Architecture
[https://reactnative.dev/architecture/overview](https://reactnative.dev/architecture/overview)
- New Renderer: <span class="font-700">Fabric</span>
- New NativeModules: <span class="font-700">TurboModules</span>

## 기존 아키텍처의 병목 제거
 - React concurrent (React 18)
 - less serialization
 - view flattening
 - lazy init

---
layout: center
class: text-center
---

# Learn More

[Documentations](https://sli.dev) · [GitHub](https://github.com/slidevjs/slidev) · [Showcases](https://sli.dev/showcases.html)
