---
# 속성은 대쉬 내부에 작성하세요

# 영문으로 같은 내용 작성 시 id 같아야 함
lng_pair: id_kakao_emulation1
# 영문으로 작성하는 경우 영문제목
title: [네트워크]애뮬레이션 실습1 - 패킷 캡쳐

# 저자 설정(생략 가능)
#author: initializer

# 카테고리와 태그 설정
category: 카카오클라우드스쿨
tags: [network, GNS3, Wireshark]

# 섬네일 이미지
img: "https://www.gns3.com/assets/custom/gns3/images/logo-colour.png"

# 댓글 비활성화 여부
comments_disable: false

# 작성 날짜
date: 2022-07-04 17:03:22 +0900

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

[카카오 클라우드 스쿨] GNS3 및 Wireshark를 통한 네트워크 에뮬레이션

<!-- outline-end -->

# 네트워크 애뮬레이션 실습1 - 패킷 캡쳐
* 시뮬레이터: 시스템 자원을활용해서  구성
* 애물레이터: 시스템에 없는 자원을 가상으로 만들어서 구성
  * 애뮬레이터로 가상 라우터, 스위티 등을 만들어서 실습을 해 볼 예정!

<br>

* 서버에 원격접속하는 방법
   1. using GUI: vnc, spice, pcoip..
   2. using CLI: telnet, ssh (일반 PW 로그인뿐만 아니라, key-pair를 이용한 인증도 제공한다)

<br>

## 환경 구성

1. wireshark 설치 <br><br>
2. GNS3 설치 <br><br>
  * 시뮬레이터가 아닌 애뮬레이터라서 서버에 연결해서 사용할수도 있다
  * 아래와 같이 생긴 라우터 등을 모의로 구성해 볼 수 있다 <br> ![img_51](https://user-images.githubusercontent.com/104918800/177148542-3981e300-97d4-4a04-87ba-b6fdf0510be9.png)
  * ![img_47](https://user-images.githubusercontent.com/104918800/177148532-18e389ff-0f1d-4f81-9f79-da5269f9ad74.png) <br><br>
3. 설정
   * 파일 삽입
      * ![img_48](https://user-images.githubusercontent.com/104918800/177148535-e6444b18-c6e5-45d2-98f1-feac46846c7b.png)
   * general 설정
     * ![img_49](https://user-images.githubusercontent.com/104918800/177148539-7e31d168-b7ff-4b93-a204-66362722d508.png)
   * 이미지 삽입
     * ![img_50](https://user-images.githubusercontent.com/104918800/177148540-2101837a-6792-4b69-9bd8-470b29f8bac4.png)
   * next로 끝내기 <br><br>
4. GNS3 세팅하기
   * 드래그 앤 드롭 형식
     * ![img_52](https://user-images.githubusercontent.com/104918800/177148544-e3d00e2b-2e3d-490c-86f0-6085392a15a9.png)
   * cloud 1에 대해서 다음과 같이 configure한다
     * ![img_53](https://user-images.githubusercontent.com/104918800/177148547-9e9eab34-da59-4124-8021-338d5fa82f0f.png)
   * cloud 2에 대해서 다음과 같이 configure한다
     * ![img_54](https://user-images.githubusercontent.com/104918800/177148548-abc896dc-b2d1-433b-8695-b875289756f3.png)
   * cloud1에 케이블을 설치한다
     * ![img_55](https://user-images.githubusercontent.com/104918800/177148551-80ffee65-259f-4e0d-baf5-e8bc381d5b78.png)
   * cloud2에 케이블을 설치한다
     * ![img_56](https://user-images.githubusercontent.com/104918800/177148388-54617570-8ec1-4578-96ec-80164d0f83eb.png)
   * vmware에서 settings
     * ![img_57](https://user-images.githubusercontent.com/104918800/177148394-114df336-11d8-40b5-8d76-e925fff3302a.png)
   * 네트워크 설정 변경(VMnet1)
     * ![img_58](https://user-images.githubusercontent.com/104918800/177148395-1e1f9ee8-c2c0-4b57-aa35-07a6983a8b73.png)
     * 랜선을 빼서 다른 곳의 랜선으로 연결한 것과 같음
     * IP는 바뀔 것임
   * 리눅스 세팅에서 DHCP 할당으로 변경
     * ![img_59](https://user-images.githubusercontent.com/104918800/177148397-07264e76-aaeb-471b-9420-b4516d85b104.png)
     * 그리고 인터넷을 off/on을 하면 됨
     * 독립적이지만 사설 네트워트고, 인터넷과 연결되지 않은 네트워크에 연결 됨
     * ![img_60](https://user-images.githubusercontent.com/104918800/177148401-fcb853fe-f0dc-4e11-8801-1da0fbabb503.png)
   * 이제 포트포워딩을 진행해서 인터넷에 연결시킬 것이다 <br><br>
5. GNU 사용하기
   * 라우터 CPU 최적화 진행해 보기
     * **여기서 라우터는 윈도우 안에서 돌아가는 가상의 라우터, 노트북과는 별개임**
     * CPU가 라우터를 최적화 하지 않아서 CPU 사용량이 엄청 많음
     * ![img_62](https://user-images.githubusercontent.com/104918800/177148408-b983c1d6-9ec9-48cd-9d33-a0353f43578f.png)
     * 저것을 누르면 최적화가 실행된다 - CPU 사용량이 확 줄음
   * RUN(실행)시킨 뒤, 콘솔창을 띄운다
     * ![img_61](https://user-images.githubusercontent.com/104918800/177148403-c2640e25-76ed-428b-b964-8565c915275b.png)
     * ![img_63](https://user-images.githubusercontent.com/104918800/177148410-b9ecf130-7f7e-4d2d-b94c-3695c57ae84f.png)
     * 마지막으로 no sh
     * 강사님 주소로 do ping을 진행하면 핑이 잘 전달된다
   * 디폴트 라우트를 설정한다
     * ip route 0.0.0.0 0.0.0.0 192.168.1.1
     * 인터넷에 있는 내가 모르는 모든 네트워크는 192.168.1.1(게이트웨이 즉 공유기)로 보내버리라
     * 구글로 do ping을 진행하면 잘 간다
     * ![img_65](https://user-images.githubusercontent.com/104918800/177148416-f2a56f78-4b97-407b-a60b-b8873f558f11.png) <br> 여기서 ...!! 점은 arp table을 찾는 것임 <br><br>
6. 보안상 취약한 텔넷 구성하고 연결을 해보자
   * 왜 SSH를 써야하는지 알게 될 것
   * 유저생성
     * username user1 password user1
   * 가상의 터미널을 21개 만들자(21개의 라우터 동시에 들어올 수 있게 함)
     * line vty 0 20
   * 계정을 통해 로그인할 수 있도록 한다
     * login local
     * enable password test123
   * telnet을 통해 서버에 접속을 해 보자
     * C:\Program Files\GNS3\putty.exe 실행
     * telnet 선택 후 강사님 IP (192.168.1.199) 엔터!
     * en 엔터
     * 우분투 비번 엔터
   * 로그인 성공함
     * ![img_66](https://user-images.githubusercontent.com/104918800/177148420-4863f4cc-d359-4aa8-bcb7-472651ffd660.png)
   * 중간에(강사님의 vm으로 가던 중, 강사님의 PC에서) wireshark를 통해 데이터를 볼 수 있다
     * 텔넷으로 접속할 경우 중간에서 누가 빼서 볼 수가 있다
     * 따라서 **http 및 telnet는 절대 쓰면 안된다**

<br>
<hr>

## 오늘의 과제
* GOOGLE에 key-pair를 이용한 ssh 연결을 찾아 보자
* 이는 모든 클라우드 환경에서 사용된다 (패스워드를 이용하지 않음)
* SSH: 아주 중요함. 클라우드상 인스턴스로 접속할 때 아주 중요하다
