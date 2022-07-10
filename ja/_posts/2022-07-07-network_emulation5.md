---
# 속성은 대쉬 내부에 작성하세요

# 영문으로 같은 내용 작성 시 id 같아야 함
lng_pair: id_kakao_emulation5
# 영문으로 작성하는 경우 영문제목
title: 네트워크 애뮬레이션 실습5 - 터널링

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
date: 2022-07-07 13:03:22 +0900

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

[카카오 클라우드 스쿨] 터널링 실습

<!-- outline-end -->




# 네트워크 애뮬레이션 실습5 - 터널링

## 터널링
* NAT을 사용하지 않고 사설네트워크 구축
  * **터널링이 필요!**
  * ![img_224.png](img_224.png)
  * ![img_225.png](img_225.png)
<br>

* NAT 구성 없이 클러스터 등의 구성이 가능하다. 꽤 편하고 좋음
* 단점:인터넷을 통하기 때문에 보안적인 대처가 필요함(암호화, 해싱, 인증)
  * -> GRE + IP security = GRE Over IPsec VPN (VPN의 종류임)
* **따라서 클라우드는 멀티 테넌시 환경이다**
* -> 멀티 테넌시 환경을 위해, 터널이 필요하다!!!!!!
* ![img_275.png](img_275.png)
<br>

## 터널링 실습

서울
1. 앞쪽(서울본사)
   * ![img_226.png](img_226.png)
2. 뒷쪽(서울인터넷)
   * ![img_227.png](img_227.png)
3. 알지 못하는 모든 신호를 인터넷 쪽으로 보내겠다
   * ![img_228.png](img_228.png)

<br>

부산
1. 부산 인터넷쪽
   * ![img_229.png](img_229.png)
2. 부산 지사쪽
   * ![img_230.png](img_230.png)
3. 알지 못하는 모든 신호를 인터넷 쪽으로 보내겠다
   * ![img_231.png](img_231.png)

<br>

ISP
1. 서울 쪽
   * ![img_232.png](img_232.png)
2. 부산 쪽
   * ![img_233.png](img_233.png)

<br>

인터넷 부분은 더 이상 할 일이 없다
* ![img_235.png](img_235.png)

<br>

서울에서 부산으로 핑 보내보기
* ![img_234.png](img_234.png)

<br>

하지만, 서울, 부산 내부의 친구들은 인터넷이 안됨
(외부에 공개되어 있지 않음)
* ![img_236.png](img_236.png)

<br>

서울
1. 터널 만들기
   * ![img_237.png](img_237.png)
2. 터널의 IP주소 입력
   * ![img_238.png](img_238.png)
3. 어디서부터 어디까지 터넗인지 지정해 주면 됨: 출발지와 목적지 지정
   * ![img_240.png](img_240.png)

<br>

부산도 동일하게
1. 만들고 IP주소 입력
   * ![img_241.png](img_241.png)
2. 범위 지정
   * ![img_242.png](img_242.png)

<br>

터널을 통해 핑이 감
* ![img_243.png](img_243.png)
* ![img_244.png](img_244.png)

<br>

라우팅 설정
* 서울 ip route 192.168.2.0 255.255.255.0 10.10.10.2
* 부산 ip route 192.168.1.0 255.255.255.0 10.10.10.1

<br>

가상 PC 실행
* 서울 (pc1)
  * ![img_245.png](img_245.png)
* 부산 (pc2)
  * ![img_246.png](img_246.png)
* 서울에서 부산 PC로 핑이 감
  * ![img_247.png](img_247.png)

<br>

ICMP 확인해 보면
* ![img_248.png](img_248.png)

<br>



## 뜬금없지만, 쉘 스크립트로 핑 확인하는 프로그램 생성
* ![img_253.png](img_253.png)
* ![img_250.png](img_250.png)
* ![img_251.png](img_251.png)
* ![img_252.png](img_252.png)
* ![img_254.png](img_254.png)



