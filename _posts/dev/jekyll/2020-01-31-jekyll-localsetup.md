---  
title: 로컬 실행환경 구성
excerpt: Jekyll 실행 환경을 로컬에 구성하기  
date: 2020-01-21 11:00:00 +0800
last_modified_at: 2020-01-21 11:00:00 +0800
header:
  teaser: /assets/images/01_teaser/jekyll.jpeg
  overlay_image: /assets/images/01_teaser/jekyll.jpeg
  overlay_filter: 0.5
  og_image: /assets/images/01_teaser/jekyll.jpeg
  caption: "Photo credit: [**Flickr***](https://live.staticflickr.com)"
categories: jekyll  
---

Jekyll 작업의 어려움은 수정 작업 후 Github로 푸시하고 몇 분은 기다려야 
수정 사항 검증이 가능하다는 점입니다. 경미한 수정에도 커밋, 푸시를 
하지 않고서는 검증을 할 수 없고 이 때문에 형상 관리 측면에서 
수용하기 어려운 점이 있습니다. 그래서 Jekyll을 로컬에서 수행하는 방법이 필요합니다. <p>
이 포스팅은 Jekyll을 로컬에서 구동하여 수정 검증의 효율성을 제공하는 방법에
관한 것입니다. 

---
## Local Environment
작업 플랫폼은 Window10으로 한정합니다.
- Window10 (64bit)

## Install Ruby
먼저 Ruby를 설치 합니다. 설치 프로세스는 다음과 같습니다.

- [Ruby installer for windows](https://rubyinstaller.org/downloads/) 다운로드 <br>
   ![Jekyll](/assets/images/jekyll/jekyll-localsetup001.png)

- 인스톨 화면의 지시데로 설치 수행 <br>
   ![Jekyll](/assets/images/jekyll/jekyll-localsetup002.png)

- MSYS2 설치 수행 <br>
   ![Jekyll](/assets/images/jekyll/jekyll-localsetup006.png)

## Install bundler
Ruby 설치를 완료하면 gem 사용이 가능해집니다. gem 명령어를 통해 jekyll과 gem관리를 위한 bundler를
설치합니다.

- Window > Start Command Prompt with Ruby 선택 > 명령어 입력

```sh 
gem install bundler
```

## Install Jekyll
```sh 
gem install jekyll
```

## SSL Error
gem install jekyll bundler 수행 시 ssl 인증 오류 관련 에러가 발생할 수 있습니다.<br>
해결 방법에 대해 여러 가지 찾아 보았으나 명확한 답을 찾지 못하여 결국 아래와 같은 방법으로 https
요청을 http로 변경하여 처리 하였습니다.

```sh 
D:\>gem sources --remove https://rubygems.org/ #https://rubygems.org/를 cache에서 제거
https://rubygems.org/ removed from sources

D:\>gem sources -a http://rubygems.org/
https://rubygems.org is recommended for security over http://rubygems.org/ #http://rubygems.org/ 추가

Do you want to add this insecure source? [yn] y
https://rubygems.org/ added to sources
```
참고: [http://blim.co.kr/archives/687](http://blim.co.kr/archives/687)

## Dependency
설치가 완료되면 실행하고자 하는 jekyll 소스 디렉토리로 이동하여 의존성 다운로드를 수행합니다.

```sh 
D:\> cd /IDE/minimal-mistakes-jekyll #이동 디렉토리는 개인별로 상이 할 수 있음
D:\ minimal-mistakes-jekyll > bundle install
```
이 때 디렉토리 내에 있는 Gemfile을 이용하게 되는데 혹시 없으면 아래와 같이 내용을 체워 생성합니다. <br>
참고로 이때도 ssl 문제가 발생하면 source를 http://rubygems.org 로 바꿔줍니다.

```sh
source "https://rubygems.org"
gemspec
```

## Run
최종적으로 구동을 수행하여 http://localhost:4000으로 접속이 되면 완료된 것입니다.

```sh 
D:\  minimal-mistakes-jekyll> bundle exec jekyll serve & # --port xxxx 옵션 지정 가능
```
- 구동 화면

![jekyll](/assets/images/jekyll/jekyll-localsetup008.png)

참고: [simplehanlab run-on-local](https://simplehanlab.github.io/jekyll/minimal-mistakes/run-on-local/)





