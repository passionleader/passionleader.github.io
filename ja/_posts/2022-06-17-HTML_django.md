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

* a태그 (하단에 실습 있음)
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
<br>

* Div 태그
    <div align="center"> DIV: 특정 공간을 분할하는 박스태그</div>

<br>

* class 태그
  * 비슷한 친구들 끼리 묶어버릴 때 클래스를 사용함
  * 폰트, 기능 등등 한 번에 적용시킬 수 있음
  * ![img_6.png](img_6.png)

<br>

* list 태그
  * 목록을 표현할 때 사용함
  * ordered list(점)
  * unordered list(숫자)

<br>

* form 태그 (하단에 실습 있음)
  * 데이터를 전송하는 용도로 사용됨
    * action attribute
    * method attribute


<br>
<hr>

## 1-3. 태그 실습:  < a > 태그
>  어제 만든 django 프로젝트로 진행해 보자


1. ex02 라는 앱 생성하기
   * ![img.png](img.png)
2. 앱 추가하기(settings.py)
```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'ex01',
    'ex02'
]
```
3. 어제 만든 템플릿에 대해 앱 별로 디렉토리 구분하고 템플릿 생성하기
   * ![img_3.png](img_3.png)
4. (디렉토리가 수정되었으니 어제 만든 views는 따로 수정해야 함)
```python
...
    return render(request, 'ex01/page1.html', context)
...
    return render(request, 'ex01/input.html')
...
```
5. 오늘 만든 ex02 views 구성하기
```python
# Create your views here.
def func1(request):
    return render(request, "ex02/func1.html")
```
6. config/urls 구성하기
```python
import ex02.views
urlpatterns = [
    path('admin/', admin.site.urls),
    path('qwer/abcd', ex01.views.func1),
    path('qqqq', ex01.views.func2),
    path('getPost', ex01.views.getPost),
    path('ex02/func1', ex02.views.func1),
]
```
7. 템플릿(html)에 a태그 써 보기
```html
<!-- 아래 두개는 전혀 다른 페이지로 이동시킨다 -->
<a href="a">상대 경로</a>
<br>
<a href="/a">절대 경로</a>
```
8. 결과
   * a와 /a는 전혀 다르다는 것을 알면 OK
   * ![img_4.png](img_4.png) 상대 경로를 클릭했을 때
   * ![img_5.png](img_5.png) 절대 경로를 클릭했을 때


<br>
<hr>

## 1-4. 태그 실습:  < form > 태그
> form 태그를 써서 간단한 로그인 창을 만들어 보자
* get을 써도 되긴 하지만 post 방식을 써 볼것임

<br>

1. 방금 만들었던 ex2 앱의 views에서 formtag.html 페이지 등록하기
```python
def func1(request):
    return render(request, "ex02/func1.html")

def formtag(request):
    return render(request, "ex02/formtag.html")

def getdata(request):
    return HttpResponse()
```
2.  프레임 생성
   * ![img_8.png](img_8.png)
3. uri 등록
```python
    path('ex02/formtag', ex02.views.formtag),
    path('ex02/getdata', ex02.views.getdata),
```
4. formtag 프레임 작성
```html
> ID PW를 보내고 난 뒤, getdata uri(사실상 views의 getdata 함수)로 이동하도록 한다
> 마찬가지로 csrf_token 추가함
<body>
    <form action="./getdata" mothod="post">
        { % csrf_token% }
        <input type="text" name="userid">
        <input type="password" name="userpw">
        <input type="submit" value="로그인">
        <button>로그인버튼</button>
    </form>
</body>
```
5. 위 프레임의 name을 함수랑 맞춰 준다
```python
def getdata(request):
    userid = request.POST.get('userid', None)
    userpw = request.POST.get('userpw', None)
    print(userid, userpw)
    return HttpResponse("전송되었습니다")
```

<br>
<hr>

## 1-5. django에서 템플릿 관리 방법
> 우리가 한 것처럼 서버 위치에서 정적으로 파일을 사용하던가 <br>
> 혹은 동적으로 외부(클라우드)나 클라우드 등에서 파일을 가져올 수도 있음

<br>


1. 프로젝트에 static 이름의 디렉터리 생성

2. 경로설정
   * settings.py 내부 STATIC_URL을 찾아서 다음과 같이 설정한다
   * ![img_9.png](img_9.png)

3. static 폴더에 이미지 하나를 가져와 본다
   * ![img_10.png](img_10.png)
4. 아까 사용했던 formtag html에 다음을 추가해 본다
```html
<!DOCTYPE html>
{ % load static % }
...(중략)
<img src="/static/google.png" /> 이렇게 써도 되지만, 나중을 위해 템플릿 문법을 써보자
<img src="{ % static 'google.png' % }" /> 이렇게 하면 static 폴더에서 로드해 파일을 손쉽게 불러올 수 있다
```
5. 나중을 위해 템플릿 변수를 쓰도록 하자
