---
extends: _layouts.post
section: content
title: Jigsaw를 사용하여 블로그 만들기
date: 2022-09-01
modify_date: 2022-10-18
description: This is your first blog post.
cover_image: /assets/img/post-cover-image-1.png
---

현재 이 웹사이트는 Jigsaw로 만들었다.

### 왜 많은 정적 사이트 생성기(Static Site Generator)중에 Jigsaw를 골랐는가?

[Jamstack.com](https://jamstack.com/generators)에 보면 정적 사이트를 만들 수 있는 여러가지 프로젝트가
있다.
업무 외의 상황에서도 PHP 숙련도를 높히기 위해 PHP기반의 [Jigsaw](https://jigsaw.tighten.com)를 사용해서 블로그를 시작하게 되었다. 
 
그뿐만 아니라 라라벨 Blade 구문으로 레이아웃과 포스팅을 작성할 수 있고, Md(Mark Down) 문법으로 포스팅을 작성 할 수 있기 때문에 후에 정적 사이트 생성기 프로젝트를 바꾸게 되더라도 수월하게 마이그레이션 할 수 있을거라 생각한다.

*참고: [Jigsaw를 사용하여 만든 사이트들](https://builtwithjigsaw.com/)

### Jigsaw를 사용하여 Github Page로 배포하기

Jigsaw에 관한 설명은 [여기](https://jigsaw.tighten.com/docs/installation/)에 전반적으로 설명이 되어있으나, 헤멨던 부분을 정리하고자 한다.

#### 1. 로컬 개발 환경 생성

빠르게 로컬에서 띄워보기:
```shell
# 로컬 경로 생성 
mkdir my-jigsaw-blog

# Jigsaw 설치
cd my-jigsaw-blog
composer require tightenco/jigsaw

# 스타터 탬플릿 스캐폴딩(공식문서에는 다른 탬플릿 존재)
vendor/bin/jigsaw init blog

# 로컬환경에서 실행
npm run watch
```
#### 2. Github Page로 배포하기

최상위 경로에 ".github/workflows/main.yml" 파일을 생성한 후 아래의 내용을 넣고
배포가 이루어졌을때 깃헙 페이지 내에서 페이지를 빌드하도록 한다.


```yml
name: Build & Publish

on:
  push:
    branches:
      - master

jobs:
  build-site:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v2
    - name: Install Composer Dependencies
      run: composer install --no-ansi --no-interaction --no-scripts --no-suggest --no-progress --prefer-dist
    - name: Install NPM Dependencies
      run: npm install
    - name: Build Site
      run: npm run prod
    - name: Stage Files
      run: git add -f build_production
    - name: Commit files
      run: |
        git config --local user.email "actions@github.com"
        git config --local user.name "GitHub Actions"
        git commit -m "Build for deploy"
    - name: Publish
      run: |
        git subtree split --prefix build_production -b gh-pages
        git push -f origin gh-pages:gh-pages
```

