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


# 데이터 송수신(Model 미사용)
## 4-1. (클라이언트 > 서버) 데이터를 전송하는 코드를 짜 보자
일단 HTTP 프로토콜(get/post)만 이용해서 데이터를 받아 볼 것임 (아직은 모델을 사용하지는 않음)
<br> 우선 **get 방식**으로 클라이언트가 전송한 데이터를 전달받아 보자
1. View 코드(함수)를 다음과 같이 수정한다
```python
# Create your views here.
def func1(request):
    numVar1 = request.GET.get('num1', None)  # get은 값을 가져오겠다는 함수
    print(numVar1)  # 클라로부터 받아온 것을 서버 터미널로 출력한다
    return render(request, 'page1.html')
