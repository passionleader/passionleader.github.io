---
# 속성은 대쉬 내부에 작성하세요

# 영문으로 같은 내용 작성 시 id 같아야 함
lng_pair: id_kakao_architect
# 영문으로 작성하는 경우 영문제목
title: 클라우드 엔지니어가 필요한 지식들

# 저자 설정(생략 가능)
#author: initializer

# 카테고리와 태그 설정
category: 카카오클라우드스쿨
tags: [network, linux, GNS3, Wireshark]

# 섬네일 이미지
img: "https://docs.microsoft.com/ko-kr/azure/virtual-wan/media/virtual-wan-global-transit-network-architecture/figure1.png"

# 댓글 비활성화 여부
comments_disable: false

# 작성 날짜
date: 2022-07-05 10:03:22 +0900

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

[카카오 클라우드 스쿨] 엔지니어가 필요한 전반적인 지식들

<!-- outline-end -->



# 클라우드 엔지니어가 필요한 지식들
## 엔지니어가 필요한 전반적인 지식들
* 그러니까.. 내가 12월까지 공부할 것들  <br><br>


1. **네트워크 지식**(아주 중요함) <br><br>
2. **서버**
   * (리눅스 - 오픈소스 - 클라우드스택 - 오픈스택, KVM)
   * ![img_112.png](img_112.png) <br><br>
3. 서버 관리 툴
   * 엄청난 양의 물리적, 논리적인 서버를 자동화할 수 있는 툴이 필요함 <br><br>
   1. 스크립트
      * bash, python(os, mkdir 등 리눅스를 관리하기 위한 라이브러리), go, php 정도
        * ~~내가 쓸 DB 하나를 차근차근 설치한다~~
          * **데이터엔지니어는 서버 3000대를 동시에 일괄적으로 설치한다** <br><br>
   2. Ansible
      * ![img_113.png](img_113.png)
      * 환경 구성 자동화 도구
      * 명세서(명세파일)을 가지고 수많은 서버를 손쉽게 관리할 수 있다
        * ex)'대구의 웹 서버만 모두 재부팅 할 거다' 라는 명세서를 작성하면 손쉽게 수행할 수 있다
      * **멱등성**을 보장받을 수 있다 <br><br>
   3. Terraform
      * ![img_114.png](img_114.png)
      * 설치한 **인프라**를 한꺼번에 관리할 수 있다 <br><br>

   4. 컨테이너 오케스트레이션
      * 컨테이너와 마이크로서비스 아키텍처를 규모에 따라 관리할 프레임워크를 제공
        * 예를 들어 3000대의 서버에 "A 서버는 연결하고, B 서버는 시스템을 모니터링, C 서버는 컨테이너를 띄우고.. 등등 조율하는 기능
        * 도커 - SWARM(내장), K8S(SWARM은 기업용으로 쓰기에는 모자람) <br><br>
   5. 형상관리 프로그램
      * Github, Gitlab
        * 형상관리 프로그램
        * 버전 관리, 롤백 등을 도와줌 <br><br>
   6. Jenkins
      * ![img_115.png](img_115.png)
      * 소프트웨어 개발 시 지속적 통합 서비스를 제공하는 툴
      * gitlab 등에 commit될 경우, Jenkins가 이를 인지하고, 자동으로 배포한다
      * 서버가 동작 중, 롤백을 해야 하는 경우 서버 중단 없이 일부 컨테이너를 변경 가능하다
      * 프라모델 조립하듯이, 자신이 맡은 부분만 집중할 수 있다  <br><br>


* 즉, Jenkins 까지의 Pipeline을 구축하면 된다
  * 우리는 환경만 만들어 주면 된다

<br>

> 멱등성
* 사전적 의미: 연산을 여러 번 적용하더라도 결과가 달라지지 않는 성질
* 즉, 함수를 반복 수행한다거나 프로그램을 여러번 반복 설치해도 해도 결과가 달라지지 않는 성질을 보장해 주는 것
  * 멱등성을 보장받지 않으면 결과가 계속 달라지므로, 매번 다시 설치, 시작, 중지해야 한다
  * 멱등성이 보장되는 경우, 같은 행위를 반복하지 않아도 됨
  * 서버를 1000대 가까이 설치하는 입장에서 멱등성을 반드시 보장받아야 한다
* **즉, 서버의 규모가 클 경우, 멱등성을 보장 받는 Ansible을 사용하는것이 좋음**

<br>
<hr>

## 컨테이너를 활용한 서버 구성 예시 <br>
![img_69.png](img_69.png)
* 가상화가 아닌 격리를 사용한다
  * 하이퍼바이저에 비해 이미지 용량이 줄어들고, 시스템 리소스를 사용하며, HostOS가 불필요함

<br>

* 컨테이너를 사용한다면 이런 식으로 구성하게 될 것임
  * www.potal.com 사이트
  * /sports -> 스포츠 컨테이너 별도
  * /blogs  -> 블로그 컨테이너 별도
  * /news   -> 뉴스 컨테이너 별도
  * /webtoon-> 웹툰 컨테이너 별도

* 장점
  * 서버가 4개로 돌아가는 것과 비슷한 성능을 냄
  * 특정한 사이트(ex:스포츠)탭에 트래픽일 몰릴 경우 해당 컨테이너를 (오케스트레이션 툴이 명령때려서) 오토스케일링(스케일 아웃) 할 수 있다.
    * 참고
      * 스케일 인 아웃 - 서버의 개수
      * 스케일 다운 업 - 서버의 성능
  * 서버 하나가 죽어도 다른 서비스는 제공 가능함
