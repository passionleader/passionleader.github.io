---
# 속성은 대쉬 내부에 작성하세요

# 영문으로 같은 내용 작성 시 id 같아야 함
lng_pair: id_kakao_django
# 영문으로 작성하는 경우 영문제목
title: djagno 실습 - 프론트엔드 가져와서 운영해 보기

# 저자 설정(생략 가능)
#author: initializer

# 카테고리와 태그 설정
category: django
tags: [카카오클라우드스쿨]

# 섬네일 이미지
img: "https://cdn.pixabay.com/photo/2018/05/18/15/30/web-design-3411373__340.jpg"

# 댓글 비활성화 여부
comments_disable: false

# 작성 날짜
date: 2022-06-17 10:02:10 +0900

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

[카카오 클라우드 스쿨] djagno 실습 - 프론트엔드 가져와서 운영해 보기

<!-- outline-end -->


# djagno 실습 - 프론트엔드 가져와서 운영해 보기
> 목표: Bootstrap 웹사이트를 장고에서 돌려 보자

<br>


 1 . 장고 프로젝트 새로 만들기

```bash
django-admin startproject config .
```

2 . 앱을 하나 만들기

```bash
python ./manage.py startapp frontapp
```

3 . 템플릿을 다운 받아서 asset, css, js 폴더를  static 내부에 올린다
   * 필요에 따라 하위 디렉토리를 만든다
   * ![img_13.png](img_13.png)

4 . html 파일은 templates 폴더를 만들어 집어 넣는다
   * 필요에 따라 하위 디렉토리를 만든다
   * ![img_12.png](img_12.png)

5 . settings.py에서 사용할 app, static 리소스 경로, templates 경로를 설정한다

```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'frontapp',
]
...(중략)

TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [BASE_DIR/"templates"],
        'APP_DIRS': True,
        ...(중략)  }]

STATIC_URL = 'static/'
STATICFILES_DIRS = [
    BASE_DIR / 'static',
]
```

6 . 페이지만 띄울 정도의  views 함수 구성하기

```python
def index(request):
    return render(request, "index.html")
```

7 . URI 연결시키기

```python
import frontapp.views
# 최상단에서 바로 볼 수 있도록 URI 적용
urlpatterns = [
    path('admin/', admin.site.urls),
    path('', frontapp.views.index),
]
```

8 . templates폴더 내 html 파일에서 css 경로 설정

```html
다음과 같이 해도 되지만
<link href="../../static/frontapp/css/styles.css" rel="stylesheet" />
다음과 같이아까 배운대로 하는 것이 신상에 좋을 것임
<!DOCTYPE html>
{ % load static % }
..(중략)
<head>
    <link href="{ % static 'frontapp/css/styles.css' % }" rel="stylesheet" />
..(중략)
```

9 . 마찬가지로 이미지 (assets) 의 경로도 다 바꾸어 주어야 한다

```html
<img class="masthead-avatar mb-5" src="../../static/frontapp/assets/img/avataaars.svg" alt="..." />
혹은
<img class="masthead-avatar mb-5" src="{ % static 'frontapp/assets/img/avataaars.svg' % }" alt="..." />
특수문자 실수 없도록 조심한다
```

10 그러면 성공적으로 CSS가 적용된 페이지를 확인할 수 있다
   * ![img_14.png](img_14.png)
