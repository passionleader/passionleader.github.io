---
# 속성은 대쉬 내부에 작성하세요

# 영문으로 같은 내용 작성 시 id 같아야 함
lng_pair: id_kakao_django
# 영문으로 작성하는 경우 영문제목
title: HTML 기초지식과 django

# 저자 설정(생략 가능)
#author: initializer

# 카테고리와 태그 설정
category: html
tags: [카카오클라우드스쿨]

# 섬네일 이미지
img: "https://cdn.pixabay.com/photo/2018/05/18/15/30/web-design-3411373__340.jpg"

# 댓글 비활성화 여부
comments_disable: false

# 작성 날짜
date: 2022-06-17 10:00:10 +0900

# image_lazy_loader_posts = false 혹은 image_viewer_posts = false인 경우에만 사용하세요
#image_viewer_on: true
#image_lazy_loader_on: true

# 블로그내 검색 혹은 검색 엔진 검색에서 예외할건가
#on_site_search_exclude: true
#search_engine_exclude: true

# to disable this page, simply set published: false or delete this file
#published: false
---

<!-- outline-start -->

[카카오 클라우드 스쿨] HTML 기초지식과 django

<!-- outline-end -->


# HTML 기초지식과 django
> Django를 쓰려면 기본적인 HTML의 구성을 알아야 함

## 1-1. HTML 파일의 구성

```html
<html>
	<!-- html의 주석 -->

    <head>
        <title>01_html의 기본개념.html</title>
        <style>
            /* CSS */
        </style>
    </head>

    <br />  <!-- 태그 바로 닫아버리기 -->

    <body>
	</body>
	<script>
		// JavaScript
	</script>
</html>
```

* 기본 구성
  * head
    * title: 탭 제목
    * style: css가 여기에 오는데, 따로 파일로 빼 두는 것이 나을 것
  * body
    * 실질적으로 화면에 보여지는 것
    * 구글링으로 검색해서 가져다 쓰도록 하자
  * script
    * 하단에 자바스크립트 코드를 작성한다 (보통 다른 파일로 빼 두는게 일반적임)

<br>
<hr>

## 1-2. 자주 쓰이는 태그
* p태그
  * 문단을 구분하는 태그, 기능이 없어 보이지만 p 태그를 많이 사용하자
  * 관련이 있는 문단끼리 묶을 수 있음
  * 특정 문단의 디자인,기능, 폰트를 수정할 때 원하는 부분만 적용시킬 수 있음

<br>

* a태그
  * 하이퍼링크 역할, 다른 페이지/위치를
  * <a href=http://www.naver.com 과 같이 하이퍼링크를 걸어 두거나
  * <a href=awdicnaow.jpg 과 같이 이미지를 걸어 둘 수 있다
    * <a href=www.naver.com 처럼 http를 생략하면 파일로 연결함(404오류)
    * <a href=www.naver.com>예시</a>

<br>

> 절대경로와 상대경로
* 절대경로: 최상단 경로부터 해당 경로까지 전부 적어 주는것
  * 윈도우의 경우 C, 리눅스의 경우 /(root)
  * 도메인의 경우 https://comic.naver.com/bestChallenge/list?titleId=779079 사이트라면 ...naver.com/이 최상단 경로
* 상대경로: 내가 위치한 경로 기준으로만
```html

    <a href=/hello/world> 절대경로 </a>

    <a href=./hello/world> 상대경로</a>

```
