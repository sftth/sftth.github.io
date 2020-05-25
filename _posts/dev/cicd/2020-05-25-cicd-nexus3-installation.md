---
title: "CICD - Nexus 3 Installation"
date: 2020-05-25 00:00:00 +0800

categories: cicd
tags:
  - nexus 3
  - repository
sidebar:
  title: "Development"
  nav: "dev-sidebar"
---

Window 10 플랫폼에서 Nexus 3 설치 및 기동에 관한 가이드 입니다. <br>

### 1. Download

아래 순서에 따라 웹사이트에서 설치 파일을 다운로드 받습니다. <br>

#### 1.1 Web Site
 - sonatype website for download NXRM3 [NXRM 3 Download 링크](https://help.sonatype.com/repomanager3/download/download-archives---repository-manager-3)

#### 1.2 zip file
 - Windows archive http://download.sonatype.com/nexus/3/nexus-3.23.0-03-win64.zip

### 2. Install

아래 순서에 따라 설치 압축해제 및 기동 합니다. <br>

####   2.1 unzip 
- 다운로드 받은 설치 파일을 c:\sw에 압축 해제 합니다.
- 압축을 해제 하면 아래 디렉토리가 생성 됩니다.

```xml 
C:\sw\nexus-3.23.0-03-win64\nexus-3.23.0-03\bin
```

####   2.2 cmd

- cmd 창을 열어서 아래 경로로 이동합니다.
- 이동 경로: C:\sw\nexus-3.23.0-03-win64\nexus-3.23.0-03\bin
- 아래 명령어를 입력합니다. 

```xml 
nexus.exe /run
```