---
# 속성은 대쉬 내부에 작성하세요

# 영문으로 같은 내용 작성 시 id 같아야 함
lng_pair: id_kakao_emulation6
# 영문으로 작성하는 경우 영문제목
title: 네트워크 애뮬레이션 실습6 - 서브넷 마스크

# 저자 설정(생략 가능)
#author: initializer

# 카테고리와 태그 설정
category: 카카오클라우드스쿨
tags: [network, linux, GNS3, Wireshark, router]

# 섬네일 이미지
img: "https://conceptdraw.com/a1778c3/p1/preview/640/pict--router-cisco-routers---vector-stencils-library.png--diagram-flowchart-example.png"

# 댓글 비활성화 여부
comments_disable: false

# 작성 날짜
date: 2022-07-07 15:03:22 +0900

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

[카카오 클라우드 스쿨] 서브넷 마스크 실습

<!-- outline-end -->



# 네트워크 애뮬레이션 실습6 - 서브넷 마스크

* 자세한 내용은 동기님의 블로그를 참고하세요
  * https://velog.io/@ptah0414/22-07-08-TIL


1. 구성 <br> ![img_257.png](img_257.png)  <br> <br>
2. 인터넷  <br> ![img_255.png](img_255.png)  <br> <br>
3. HQ 라우터  <br> ![img_256.png](img_256.png)  <br> <br>
4. 연결  <br> ![img_258.png](img_258.png)  <br> <br>
5. 중간퀴즈
   * 우리회사는 내부적으로 사설주소를 사용한다. 전체 범위는 192.168.2.1/24를 사용하고 이를 적절히 서브넷팅하여 각 네트워크에 할당할 계획이다
   * DEV 팀은 100개의 IP가 필요하다
   * OPS 팀은 20개의 IP가 필요하다
   * 제주 지사에 있는 SALES팀은 15개의 IP가 필요하고
   * 본사와 지사를 연결하는 WAN 구간은 2개의 IP 주소가 필요하다
   * 적절한 네트워크 주소를 구하되, 아래의 조건을 만족해야 한다
     1. 서브넷팅은 가장 규모가 큰 팀부터 주소를 구해낸다
     2. 화면에 각 네트워크 주소와 기본 게이트웨이 주소를 작성해라
     3. 게이트웨이 주소는 각 네트워크의 첫 번째 주소를 사용한다.
     4. 단, 본사와 지사간에는 라우터간 연결이므로 여기에는 게이트웨이라는 표현을 사용하지 않는다.
   * 팁: VSLM을 사용한다  <br> <br>
6. 내피셜 답안
   1. DEV 팀: 128씩 나눠야 함. 192.168.2.0 ~ 192.168.2.127(/25,255.255.255.128) 192.168.2.0
   2. OPS 팀: 32씩 나눠야 함. 192.168.2.128 ~ 192.168.2.159(/27, 255.255.255.224) 192.168.2.128
   3. SALES팀: 16씩 나눠야 함. 192.168.2.160 ~ 192.168.2.175(/24, 255.255.255.240) 192.168.2.160
   4. 게이트웨이 주소: 4로 나눔(실사용2개). 192.168.2.176 ~ 192.168.2.179(/4, 255.255.255.252)  <br> <br>
7. 따라서 최종적인 모양은 이런 양상일 예정
   * ![img_266.png](img_266.png)
