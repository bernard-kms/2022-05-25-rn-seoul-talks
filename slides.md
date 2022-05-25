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
- 목표가 날카롭지 않으면 산으로 간다
- 뇌피셜, 기분으로 작업하지 않는다
- 정량적, 정성적 뭐든 좋다
  - 동영상 녹화
  - Firebase Performance Monitoring(같은 것)

---

# 불필요한 렌더링을 줄이자

- 잦은 컴포넌트 업데이트가 JS와 네이티브 모두에게 병목
- memoization, pure component, immutable data structures, ...
  - 👉 [https://reactjs.org/docs/optimizing-performance.html](https://reactjs.org/docs/optimizing-performance.html)

## 왜 이렇게 자주 렌더링될까?
- [why-did-you-render](https://github.com/welldone-software/why-did-you-render)
  - ...monkey patches React to notify you about potentially avoidable re-renders.

---

## 왜 이렇게 자주 렌더링될까: 값 비교

```tsx
const [user, setUser] = useState({ id: 1, name: '헤드위그', score: 10 });

useEffect(() => {
  someEffect(user.name);
}, [user]);

  setUser({ id: 1, name: '헤드위그', score: 11 });

return (
  <UserProfile user={user}/>
)
```
👇
```tsx
useEffect(() => {
  // some effect
}, [user.name]);

return (
  <UserProfile userName={user.name}/>
)
```

---

## 왜 이렇게 자주 렌더링될까

```tsx
import { unstable_batchedUpdates } from 'react';

const someCallback = useCallback(() => {
  unstable_batchedUpdates(() => {
    setA(a);
    setB(b);
    setC(c);
    // some heavy state updates...
  });
}, []);

return (
  <Component ...>
)
```
- React 18부터는 필요없다(=ReactNative 0.69)
---

# 워터폴 멈춰
홈 스크린의 모든 상태가 준비 될 때 까지 스플래시 스크린에서 대기

```tsx
import { unstable_batchedUpdates } from 'react';

useEffect(() => {
  (async () => {
    // some actions...
    hideSplashScreen();
  })();
}, []);

return (
  <App ...>
)
```

---

# 가능하다면 네이티브 구현
같은 구현이 네이티브 코드로도 존재한다면, 그것을 선택 (없다면 만들어도...)

- firebase-js-sdk 👉 react-native-firebase
- `useNativeDriver: true` in Animation
- `list.map(...)` 👉 FlatList, VirtualizedList

---
layout: image-right
image: '/react-native-firebase.png'
---

# ...네이티브 코드의 병목
서드파티 라이브러리, 혹은 우리가 짠 네이티브 코드

- `react-native-firebase`의 병목
- RN core 컴포넌트의 병목
  - e.g [react-native-fast-image](https://github.com/DylanVann/react-native-fast-image)

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

# 질문 & etc...

me@minsik.kim · GitHub @bernard-kms
