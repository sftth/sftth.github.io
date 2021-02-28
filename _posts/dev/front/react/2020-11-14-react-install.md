---
title: "React - 환경구축"
excerpt: React 개발 환경 구축   
date: 2020-11-14 00:00:00 +0800
last_modified_at: 2020-11-14 00:00:00 +0800
header:
  teaser: /assets/images/01_teaser/home.jpg
  overlay_image: /assets/images/01_teaser/home.jpg
  overlay_filter: 0.5
  og_image: /assets/images/01_teaser/home.jpg
  caption: "Photo credit: [**Flickr***](https://live.staticflickr.com)"
categories: react
---

## react를 경험해 볼 수 있는 방법

- Online Playgrounds
- Add React to a Website
- Create a New React App

이 중에서 `Create a New React App` 시도 

## npm 설치

### npm 정의 
node.js로 되어 있는 프로그램을 편하게 설치할 수 있도록 제공하는 앱 보관소

### npm 설치 
[nodejs.org](https://nodejs.org/en/) 방문 후 LTS 또는 Current 버전 다운로드 및 설치

<img src="/assets/images/react/react-npm.png" width="600" height="584" alt="npm-installation">

### npm 설치 확인 
terminal에서 npm 버전 확인하여 정상 설치 여부 확인

```sh 
$ npm -v
```

## 개발환경 구축

react 개발 환경은 여러 방법이 있겠지만 이 번에는 간편하게 툴체인을 활용할 수 있는 create-react-app을 통해 구축


**참고** 
[새로운 React 앱 만들기](https://ko.reactjs.org/docs/create-a-new-react-app.html)
[Getting Started](https://create-react-app.dev/docs/getting-started)
{: .notice--info}

### create-react-app 설치  

```sh 
$ sudo npm install -g create-react-app
```

<img src="/assets/images/react/react-cra.png" width="600" height="584" alt="npm-installation">

### 필요한 파일 생성

react용 root 디렉토리 생성 > 아래 명령어를 통해 필요한 파일 생성 

```sh 
$ mkdir reactspace
$ cd reactspace
$ create-react-app .
```

### react 기동

```sh 
npm run start

> reactspace@0.1.0 start
> react-scripts start

ℹ ｢wds｣: Project is running at http://192.168.219.105/
ℹ ｢wds｣: webpack output is served from 
ℹ ｢wds｣: Content not from webpack is served from /Users/summit/IDE/reactspace/public
ℹ ｢wds｣: 404s will fallback to /
Starting the development server...
Compiled successfully!

You can now view reactspace in the browser.

  Local:            http://localhost:3000
  On Your Network:  http://192.168.219.105:3000

Note that the development build is not optimized.
To create a production build, use npm run build.
```
