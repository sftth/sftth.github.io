---
title: "React - 빌드 배포하기"
excerpt: React 빌드, 배포하기 
date: 2020-11-14 02:00:00 +0800
last_modified_at: 2020-11-14 02:00:00 +0800
header:
  teaser: /assets/images/01_teaser/home.jpg
  overlay_image: /assets/images/01_teaser/home.jpg
  overlay_filter: 0.5
  og_image: /assets/images/01_teaser/home.jpg
  caption: "Photo credit: [**Flickr***](https://live.staticflickr.com)"
categories: react
---

### 빌드

<details>
<summary>npm run build</summary>
<div markdown="1">
```sh 
npm run build

> reactspace@0.1.0 build
> react-scripts build

Creating an optimized production build...
Compiled successfully.

File sizes after gzip:

  41.68 KB (+497 B)  build/static/js/2.a0373692.chunk.js
  1.39 KB            build/static/js/3.e2cc48c3.chunk.js
  1.16 KB (+1 B)     build/static/js/runtime-main.6280fe6d.js
  851 B (+432 B)     build/static/js/main.31523a79.chunk.js
  72 B               build/static/css/main.bab0b925.chunk.css

The project was built assuming it is hosted at /.
You can control this with the homepage field in your package.json.

The build folder is ready to be deployed.
You may serve it with a static server:

  npm install -g serve
  serve -s build

Find out more about deployment here:

  https://cra.link/deployment
```
</div>
</details>

### 배포

```sh 
npx serve -s build

   ┌─────────────────────────────────────────────────────┐
   │                                                     │
   │   Serving!                                          │
   │                                                     │
   │   - Local:            http://localhost:5000         │
   │   - On Your Network:  http://192.168.219.105:5000   │
   │                                                     │
   │   Copied local address to clipboard!                │
   │                                                     │
   └─────────────────────────────────────────────────────┘
```