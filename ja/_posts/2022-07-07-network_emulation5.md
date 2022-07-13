---
# 속성은 대쉬 내부에 작성하세요

# 영문으로 같은 내용 작성 시 id 같아야 함
lng_pair: id_kakao_emulation5
# 영문으로 작성하는 경우 영문제목
title: (네트워크)애뮬레이션 실습5 - 터널링

# 저자 설정(생략 가능)
#author: initializer

# 카테고리와 태그 설정
category: 카카오클라우드스쿨
tags: [network, GNS3, router]

# 섬네일 이미지
img: "https://www.gns3.com/assets/custom/gns3/images/logo-colour.png"

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
  * ![img_224](https://user-images.githubusercontent.com/104918800/178200042-535d68ce-4fe3-4bcf-9764-be89919034a9.png)
  * ![img_225](https://user-images.githubusercontent.com/104918800/178200045-f4b8b371-3d10-47c2-801a-69963b84b0c8.png)
<br>

* NAT 구성 없이 클러스터 등의 구성이 가능하다. 꽤 편하고 좋음
* 단점:인터넷을 통하기 때문에 보안적인 대처가 필요함(암호화, 해싱, 인증)
  * -> GRE + IP security = GRE Over IPsec VPN (VPN의 종류임)
* **따라서 클라우드는 멀티 테넌시 환경이다**
* -> 멀티 테넌시 환경을 위해, 터널이 필요하다!!!!!!
* ![img_275](https://user-images.githubusercontent.com/104918800/178200236-6ca28db5-ed62-4f39-88f4-4d504f055d3e.png)
<br>

## 터널링 실습

서울
1. 앞쪽(서울본사)
   * ![img_226](https://user-images.githubusercontent.com/104918800/178200047-d8c77562-b3db-4171-b58a-4f8d0a410953.png)
2. 뒷쪽(서울인터넷)
   * ![img_227](https://user-images.githubusercontent.com/104918800/178200051-485ec622-8342-451b-b046-cf96a3975922.png)
3. 알지 못하는 모든 신호를 인터넷 쪽으로 보내겠다
   * ![img_228](https://user-images.githubusercontent.com/104918800/178200053-057bb587-926a-43b8-a16c-1acd9cab3ca4.png)

<br>

부산
1. 부산 인터넷쪽
   * ![img_229](https://user-images.githubusercontent.com/104918800/178200055-de0a1d13-2c9d-4de0-ad8d-38b85bf2cb29.png)
2. 부산 지사쪽
   * ![img_230](https://user-images.githubusercontent.com/104918800/178200058-8f27327c-4d0c-433c-bf57-3b67dc47749c.png)
3. 알지 못하는 모든 신호를 인터넷 쪽으로 보내겠다
   * ![img_231](https://user-images.githubusercontent.com/104918800/178200059-f2f64d5d-85b0-4f19-ae59-59795303705f.png)

<br>

ISP
1. 서울 쪽
   * ![img_232](https://user-images.githubusercontent.com/104918800/178200061-d2370da3-c562-48c7-9c24-73a6cec88f57.png)
2. 부산 쪽
   * ![img_233](https://user-images.githubusercontent.com/104918800/178200062-142d71a2-2de9-4bee-b8f5-a8eeb9ee8488.png)

<br>

인터넷 부분은 더 이상 할 일이 없다
* ![img_235](https://user-images.githubusercontent.com/104918800/178200064-94e65c73-2db2-4472-98ba-581ea1539f5d.png)

<br>

서울에서 부산으로 핑 보내보기
* ![img_234](https://user-images.githubusercontent.com/104918800/178200063-7ebbb916-4312-46a2-8961-ce57073a5628.png)

<br>

하지만, 서울, 부산 내부의 친구들은 인터넷이 안됨
(외부에 공개되어 있지 않음)
* ![img_236](https://user-images.githubusercontent.com/104918800/178200067-b6994e12-9490-43fb-a87c-b8d637780601.png)

<br>

서울
1. 터널 만들기
   * ![img_237](https://user-images.githubusercontent.com/104918800/178200071-db49bbbf-d7d4-485c-9ff7-b8288ec17091.png)
2. 터널의 IP주소 입력
   * ![img_238](https://user-images.githubusercontent.com/104918800/178200072-5d6fc6fd-4145-4157-9400-38d06cd7ef3f.png)
3. 어디서부터 어디까지 터넗인지 지정해 주면 됨: 출발지와 목적지 지정
   * ![img_240](https://user-images.githubusercontent.com/104918800/178200075-6cd20748-ba3f-4967-a581-f5456c5e7302.png)

<br>

부산도 동일하게
1. 만들고 IP주소 입력
   * ![img_241](https://user-images.githubusercontent.com/104918800/178200077-61bb26ea-f6e3-40fe-b19c-ebdd1999b74d.png)
2. 범위 지정
   * ![img_242](https://user-images.githubusercontent.com/104918800/178200079-54d923ac-1b83-459a-b7b4-e68868799dd4.png)

<br>

터널을 통해 핑이 감
* ![img_243](https://user-images.githubusercontent.com/104918800/178200080-21bd004c-dbe3-4355-b1ab-49a890a18fc5.png)
* ![img_244](https://user-images.githubusercontent.com/104918800/178200084-90184b4e-e8dd-4154-9abf-6f50e308d8e9.png)

<br>

라우팅 설정
* 서울 ip route 192.168.2.0 255.255.255.0 10.10.10.2
* 부산 ip route 192.168.1.0 255.255.255.0 10.10.10.1

<br>

가상 PC 실행
* 서울 (pc1)
  * ![img_245](https://user-images.githubusercontent.com/104918800/178200086-c13d762b-c3c9-4238-a271-b8902e6a46c8.png)
* 부산 (pc2)
  * ![img_246](https://user-images.githubusercontent.com/104918800/178200088-87a2b923-b2fd-4ba7-810d-b0c396d00e06.png)
* 서울에서 부산 PC로 핑이 감
  * ![img_247](https://user-images.githubusercontent.com/104918800/178200089-0652b1ab-bbb3-4e8a-a5d9-fac7951edbc0.png)

<br>

ICMP 확인해 보면
* ![img_248](https://user-images.githubusercontent.com/104918800/178200092-75718650-3c0a-4ba8-bed5-6f27d51ba043.png)

<br>



## 뜬금없지만, 쉘 스크립트로 핑 확인하는 프로그램 생성
* ![img_253](https://user-images.githubusercontent.com/104918800/178200189-dd481769-4c57-4cb2-807e-93e5a8e386ea.png)
* ![img_250](https://user-images.githubusercontent.com/104918800/178199990-a41ebca1-0fea-4035-a0f5-1f3391a809a0.png)
* ![img_251](https://user-images.githubusercontent.com/104918800/178200183-0ba18025-d8ca-422f-b0b8-7f84df9a9371.png)
* ![img_252](https://user-images.githubusercontent.com/104918800/178200187-0dfd4b1f-54c8-411c-8987-bc46ffb65b81.png)
* ![img_254](https://user-images.githubusercontent.com/104918800/178200191-a084e30e-6a6b-4d27-b5d7-2005458288af.png)



