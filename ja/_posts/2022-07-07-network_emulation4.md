---
# 속성은 대쉬 내부에 작성하세요

# 영문으로 같은 내용 작성 시 id 같아야 함
lng_pair: id_kakao_emulation4
# 영문으로 작성하는 경우 영문제목
title: [네트워크]애뮬레이션 실습4 - 외부에서 내부망 접속

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
date: 2022-07-07 10:03:22 +0900

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

[카카오 클라우드 스쿨] 외부에서 내부망 접속

<!-- outline-end -->


# 네트워크 애뮬레이션 실습4 - 외부에서 내부망 접속

<br>


## 외부에서 내부망으로 간단한 접속테스트(Linux - httpd)

1 . 다음과 같이 설정한다
  * ![img_203](https://user-images.githubusercontent.com/104918800/178200000-35e57360-0c73-4e90-86bc-8e22b3d20cc6.png)

<br>

2 . 외부 내부 IP 설정

```commandline
SSK#conf t

SSK(config)#interface fa0/0
SSK(config-if)#ip add 192.168.1.105 255.255.255.0
SSK(config-if)#no sh

SSK(config-if)#do ping 192.168.1.199
.!!!!

SSK(config-if)#do show arp
Protocol  Address          Age (min)  Hardware Addr   Type   Interface
Internet  192.168.1.105           -   cc01.88d4.0000  ARPA   FastEthernet0/0
Internet  192.168.1.199           0   cc01.14a8.0000  ARPA   FastEthernet0/0

SSK(config)#interface fa0/1
SSK(config-if)#ip add 192.168.105.105 255.255.255.0
SSK(config-if)#no sh

SSK(config-if)#do ping 192.168.199.199
.....
```

<br>

3 . 라우팅 테이블에 강사님 라우터 부분 추가해서 핑 던져보기
* ![img_204](https://user-images.githubusercontent.com/104918800/178200001-6fbab7e6-30b9-4d1f-80f3-45394a2e9c59.png)
* 라우터랑 직접 연결되지 않은 부분을 연결할 수 있도록 하는 것임
* ![img_205](https://user-images.githubusercontent.com/104918800/178200003-47798554-3c5d-4d22-a72d-ca8be2ff6c9e.png)

<br>

4 . 클라우드 2 부분은 리눅스임, VMnet3으로 설정
* ![img_210](https://user-images.githubusercontent.com/104918800/178200009-841d5d3e-3a96-49af-85aa-042fd88f3702.png)
<br>

5 . VM웨어의 vNIC 설정, 리눅스와 연결시키기 위해 IP주소를 다음과 같이 변경
* ![img_209](https://user-images.githubusercontent.com/104918800/178200008-6c4a2a53-f996-45e0-b226-6036d6f09e32.png)
* ![img_211](https://user-images.githubusercontent.com/104918800/178200011-bbef2932-783c-4012-a3f8-f0a55d1be0f5.png)
<br>

6 . 리눅스 내의 네트워크 설정
* ![img_215](https://user-images.githubusercontent.com/104918800/178200019-ff741ea0-d18d-4791-b70a-110a02b73adc.png)
<br>

7 . 다음 명령어를 사용해서 httpd페이지 생성, httpd서비스 실행
* ![img_212](https://user-images.githubusercontent.com/104918800/178200013-ed37724c-2129-4cea-a052-d8e8be8ccc91.png)
* ![img_213](https://user-images.githubusercontent.com/104918800/178200016-f242377c-5364-4553-887e-64f953955d66.png)
<br>

8 . 방화벽 종료
* ![img_214](https://user-images.githubusercontent.com/104918800/178200017-5c2f25d9-a083-4951-a170-c145039b72f3.png)
<br>

9 . 외부에서 주소 입력시 접속가능
* ![img_216](https://user-images.githubusercontent.com/104918800/178200021-3a92a388-33db-408b-aa58-6b735415803e.png)
<br>
<br>
<hr>

## 외부에서 PAT로 구성된 내부망으로 접속하기 (Linux - ssh)

오늘 할 것 <br>  ![img_217](https://user-images.githubusercontent.com/104918800/178200022-3297149a-330b-4703-9b0e-70c559d48ed0.png) <br>
* 외부에서 http://라우터fa0/0주소:8001 접속시 본사 내부의 웹서버가 보여야 한다
* 외부에서 ssh -l root -p 라우터의fa0/0주소 로 연결하면 리눅스로 ssh 연결되어야 한다
  * step1: 각 인터페이스에 IP주소를 입력해서 동일 네트워크간 통신 가능여부 확인
  * step2: 서로 다른 네트워크(본사-지사 or 지사-지사)간의 통신이 가능하도록 static route 작성하기
  * step3: 본사에서는 인터넷으로의 연결을 위해 default route 조성하기
  * step4: 공인 주소가 하나이므로 이를 이용한 내부 네트워크의 인터넷 사용을 위한 동적 PAT 구성
  * step5: 리눅스 서버로의 웹, SSH 연결을 위한 정적 PAT 구성하기

<br>

* 실습을 위한 기본 명령어
  1. 인터페이스별 IP, 동작상태 확인을 위한 명령
     * do show ip int br
  2. 라우팅 테이블 확인
     * do show ip route
  3. 천체 구성정보 확인하기
     * R1(config)# do show run
     * R1# show run
  4. 잘못된 IP주소 수정 방법
     * 한번 더 다시 입력하거나
     * no ip add 로 초기화 시킴
  5. 잘못된 route 수정 방법
     * 이건 한번 더 입력하면 덮어쓰는게 아닌 동일 목적지에 대한 경로가 하나 더 생겨버림
     * no ip route (잘못된 route) 로 삭제한 다음 다시 입력해야 한다

<br>

**HQ(본사) 설정**
<br>

1. 일반 라우팅 (본사용)
  * int fa0/0
  * in add 192.168.1.105 255.255.255.0
  * no sh
  * int fa0/1
  * int add 192.168.105.2 255.255.255.0
  * no sh

2. 지사와의 통신을 위한 정적 라우팅
   * ip route 192.168.116.0 255.255.255.0 192.168.1.116
   * ip route 192.168.123.0 255.255.255.0 192.168.1.123

3. 본사에서 인터넷으로의 통신을 위한 정적 라우팅
   * ip route 0.0.0.0 0.0.0.0 192.168.1.1

4. 사설 구간에서 인터넷으로의 통신을 위한 동적 PAT
   * access-list 1 permit any
   * ip nat inside source list 1 int fa0/0 overload

5. 라우터 외부
    * int fa0/0
    * ip nat outside

6. 라우터 내부
    * int fa0/1
    * ip nat inside

7. 외부에서 웹서버(사설 192.168.105.105:80)로 접속하기 위한 정적 PAT 구성 필요
   1. 외부에서 192.168.1.105:8001 :-> 192.168.105.105:80
      * ip nat inside source static tcp 192.168.105.105 80 int fa0/0 8001
   2. 외부에서 ssh fa0/0:20022 :-> 192.168.105.105:22
        * ip nat inside source static tcp 192.168.105.105 22 int fa0/0 20022

<br>

**BR1(지사1) 설정**
<br>

1. 일반 라우팅(자기 지사용)
  * int fa0/0
  * int add 192.168.1.116 255.255.255.0
  * no sh
  * int fa0/1
  * ip add 192.168.116.2 255.255.255.0
  * no sh

2.  다른지사 본사와 통신의 위한 정적 라우팅
  * ip route 192.168.105.0 255.255.255.0 192.168.1.105
  * ip route 192.168.123.0 255.255.255.0 192.168.1.123

<br>

**BR2(지사2) 설정**
<br>


1. 일반 라우팅(자기 지사용)
  * int fa0/0
  * int add 192.168.1.123 255.255.255.0
  * no sh
  * int fa0/1
  * ip add 192.168.123.2 255.255.255.0
  * no sh

2. 다른지사 본사와 통신의 위한 정적 라우팅
  * ip route 192.168.105.0 255.255.255.0 192.168.1.105
  * ip route 192.168.123.0 255.255.255.0 192.168.1.116

<br>

**접속해 보자**
<br>

* 외부에서 http://본사라우터fa0/0:8001
* ssh -l root -p 20022 본사라우터fa0/0주소
* 지사확인은 위에서 ssh 연결이 된 이후 해당 터미널에서 아래와 같이 웹접속을 확인할 수 있다.
  * curl http://지사리눅스서버주소


<br>
<hr>


## 구조
* ![img_274](https://user-images.githubusercontent.com/104918800/178200235-b0b997e8-a1a0-474a-b8ec-0830af8298d4.png)
