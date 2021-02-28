---
title: "React - JS & CSS Coding"
excerpt: React JS, CSS 코딩하기
date: 2020-11-14 03:00:00 +0800
last_modified_at: 2020-11-14 03:00:00 +0800
header:
  teaser: /assets/images/01_teaser/home.jpg
  overlay_image: /assets/images/01_teaser/home.jpg
  overlay_filter: 0.5
  og_image: /assets/images/01_teaser/home.jpg
  caption: "Photo credit: [**Flickr***](https://live.staticflickr.com)"
categories: react
---

## JS 파일 코딩

### public 디렉토리

index.html 이 있는 곳 
index.html의 아래 태그에 지금 부터 작업 할 파일이 위치 함

```html
...
<div id="root"></div>
...
```

### src 디렉토리

index.js 파일이 핵심
index.js 파일의 아래 스크립트는 index.html의 `<div id="root">` 에 매핑

```js
...
document.getElementById('root')
...
```

아래 스크립트를 통해 src/App.js 파일은 index.js에 포함 됨

```js 
...
import App from './App';
...
ReactDOM.render(
  <React.StrictMode>
    <App />
  </React.StrictMode>,
  document.getElementById('root')
);
```

## CSS 파일 코딩

App.js에 아래 스크립트를 통해 App.css가 포함 됨

```js 
...
import './App.css';
... 
```


**참고** 
[Intellij에서 React 개발하기](https://www.jetbrains.com/help/idea/react.html)
{: .notice--info}
