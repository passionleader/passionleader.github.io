---
# 속성은 대쉬 내부에 작성하세요

# 영문으로 같은 내용 작성 시 id 같아야 함
lng_pair: id_kakao_emulation3
# 영문으로 작성하는 경우 영문제목
title: 네트워크 애뮬레이션 실습3 - 본사와 지사의 라우터 설정

# 저자 설정(생략 가능)
#author: initializer

# 카테고리와 태그 설정
category: 카카오클라우드스쿨
tags: [network, linux, GNS3, Wireshark, router]

# 섬네일 이미지
img: "https://www.gns3.com/assets/custom/gns3/images/logo-colour.png"

# 댓글 비활성화 여부
comments_disable: false

# 작성 날짜
date: 2022-07-06 12:03:22 +0900

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

# 네트워크 애뮬레이션 실습3 - 본사와 지사의 라우터 설정
* 오늘의 실습: 본사, 지사를 구성하며 회사 네트워크를 구성해 보자
  * 지사에서 본사 인터넷에 접속해 보자
  * ![img_127](https://user-images.githubusercontent.com/104918800/178190062-70e79e01-0488-48e8-9b93-1d83f50fd18d.png)

<br>
<hr>

## 1. 네트워크 구축하기
1. GNS 실행 <br> <br>
2. 다음과 같이 구성한다
   * 오늘은 linux를 쓰지 않고 VPC를 사용할 것임
   * ![img_121](https://user-images.githubusercontent.com/104918800/178190050-97526a99-813d-4993-9a60-8345a109c05f.png) <br> <br>
3. 본사의 라우터 slot에 랜카드를 추가한다
   * ![img_122](https://user-images.githubusercontent.com/104918800/178190051-196791e3-7da0-4cf5-8647-6b65739aa3ad.png) <br> <br>
4. cloud에 인터넷을 연결한다
   * ![img_123](https://user-images.githubusercontent.com/104918800/178190053-4fda013b-a477-4fc3-911f-65aec4b95dba.png) <br> <br>
5. 다음과 같이 연결한다
   * ![img_124](https://user-images.githubusercontent.com/104918800/178190056-29b9c878-bb9b-4397-98d0-7b42b39ddac0.png) <br> <br>
6. 전체 실행하지 말고, HQ 라우터, BR 라우터만 우클릭으로 실행시킨다
   * ![img_125](https://user-images.githubusercontent.com/104918800/178190058-a3d2467e-4e2d-4c58-b8d9-8c2f90fa72b3.png) <br> <br>
7. (GNS3가 CPU 자원을 너무 많이 잡아먹는다면 라우터에서 다음을 수행한다)
   * ![img_126](https://user-images.githubusercontent.com/104918800/178190061-2762cef1-3b07-497e-95fb-ee8db5ebb443.png) <br> <br>

<br>
<hr>

## 2. 라우터 설정하기
1. 인터넷을 연결하려면 NAT도 구성해야 한다
   * ![img_128](https://user-images.githubusercontent.com/104918800/178190063-3d6bd83d-76ef-4570-894b-5366ef71bfe7.png) <br> <br>
2. 본사(NAT-HQ)의 지사연결부분 구성하기(뒷단으로 나가는 부분)
   * ![img_133](https://user-images.githubusercontent.com/104918800/178190069-d22350f6-6f14-4ae7-8711-910965204916.png) <br> <br>
3. 본사의 내부망 구성하기
   * ![img_134](https://user-images.githubusercontent.com/104918800/178190072-aa0d5728-1486-47a8-8cf8-2f9fd208af3c.png) <br> <br>
4. 본사 라우터 현황 확인
   * ![img_135](https://user-images.githubusercontent.com/104918800/178190077-98eb9392-2e48-4b38-8096-fff7d157ad91.png) <br> <br>
5. 지사의 본사연결부분 구성하기(본사의 뒷단으로부터 받음)
   * ![img_136](https://user-images.githubusercontent.com/104918800/178190079-9f107b86-cd29-4cac-8fb5-2c42ab5aeff5.png) <br> <br>
6. 지사 내부망 구성하기
   * ![img_137](https://user-images.githubusercontent.com/104918800/178190080-86d6439e-49bb-4a4a-8150-347ea3dfbf3e.png) <br> <br>

<br>
<hr>


## 3. 라우터 인바운드 설정(들어오는 부분)
1. 두 개의 라우터는 10번대 주소를 서로 알고 있다.
   * 10.10.10.1 에서 10.10.10.2에서 핑을 보낼 수 있다
   * ![img_138](https://user-images.githubusercontent.com/104918800/178190081-b79a3a22-da02-4b0e-a1f5-3368a41629f4.png)
   * ![img_139](https://user-images.githubusercontent.com/104918800/178190084-40542377-2258-46bb-b31d-981ec8dab8bc.png) <br> <br>
2. 하지만 본사의 100번대에서 지사의 10번대로 보낼 수 있을까?
   * Ping은 가지만, 지사의 Router는 어디로 반환할 지 모르게 된다
   * 따라서 핑은 가지만, 오지는 않는다
   * ![img_140](https://user-images.githubusercontent.com/104918800/178190086-f44ec929-5b51-4873-855b-c1fb805996c9.png)
   * ![img_141](https://user-images.githubusercontent.com/104918800/178190087-e9a1aebb-111b-49c5-8a54-ffb64d31ffa2.png) <br> <br>
3. 그렇다면 본사의 10번대에서 지사의 200번대로 보낼 수 있을까?
   * 이번에는 Ping도 안 간다 (어디있는지 모른다)
   * ![img_142](https://user-images.githubusercontent.com/104918800/178190088-d8beeb1c-1e86-409d-963d-6889173a26d5.png)
   * ![img_144](https://user-images.githubusercontent.com/104918800/178190091-1088af60-9e1b-40f9-88f4-71cedd4a3436.png) <br> <br>
4. 따라서 본사의 라우팅 테이블에 추가하면
   * 라우팅 테이블 추가, 10.2 뒤에 200.0이 있다는 내용
   * ![img_145](https://user-images.githubusercontent.com/104918800/178190092-5aa1dc74-b3be-402f-9b5d-cb43ef5469b7.png) <br> <br>
5. 지사에 라우팅 테이블에도 추가한다
   * 라우팅 테이블 추가. 10.1 뒤애 100.0이 있다
   * ![img_147](https://user-images.githubusercontent.com/104918800/178190094-16bf613e-08d2-4843-bc86-b83fd29989fa.png) <br> <br>
6. 이제 아주 잘 되는 모습이 확인된다
   * 기존 <br> ![img_146](https://user-images.githubusercontent.com/104918800/178190093-2478bedc-47fe-4b54-b207-541da2229d42.png)
   * 수정 <br> ![img_149](https://user-images.githubusercontent.com/104918800/178190096-34e2083e-6769-48bd-a00a-12bf220d2f40.png) <br> <br>

* **지금까지 한 것은 단방향이었음!**, 아직 인터넷에 접속할 수는 없다

<br>
<hr>


## 4. 라우터 아웃바운드 설정하기 (나가는 부분)
1. IP Route 확인하기
  * ![img_150](https://user-images.githubusercontent.com/104918800/178190098-d44d64c6-3b32-4082-b802-ad855202384b.png)
  * 10.10.10.x IP는 fa0/1로 가는 것을 확인할 수 있음 <br> <br>
2. 인터넷으로 나가기 위한 디폴트 라우트 설정
  * ![img_159](https://user-images.githubusercontent.com/104918800/178190117-6add11bc-1075-4c2b-bb4a-e08e29d0e8b1.png)
  * ![img_157](https://user-images.githubusercontent.com/104918800/178190114-e1d1a0de-869d-4876-905d-9ab949e6950a.png)
  * 추가설명(예시)
    * ip route 1.1.1.0 255.255.255.0 2.2.2.2
      * 0000 0001. 0000 0001. 0000 0001. 0000 0000
      * 1111 1111. 1111 1111. 1111 1111. 0000 0000
      * 모든 IP 주소를 2.2.2.2로 보내겠다 (디폴트 포트 설정)
        ip route 1.1.0.0 255.255.0.0 2.2.2.2   1.1.0.0 ~ 1.1.255.255
        ip route 1.0.0.0 255.  0.0.0 2.2.2.2   1.0.0.0 ~ 1.255.255.255.255
        ip route 0.0.0.0   0.  0.0.0 2.2.2.2   0.0.0.0 ~ 255.255.255.255  : default route
    * 라우터와 같이 패킷처리 장비: 목적지 주소에 부합하는 네트워크 경로가 2개 이상인 경우, 더욱 자세한 주소를 찾아간다 -> longestmatch rules <br> <br>

2. fa0/0에 나가는 연결 설정해 주기
   * ![img_151](https://user-images.githubusercontent.com/104918800/178190099-d566c970-cd65-4a73-a78e-5aaae10f5d4b.png) <br> <br>

3. 구글에 핑 보냄
   * ![img_160](https://user-images.githubusercontent.com/104918800/178190120-aba41b5a-8ef0-4397-b16e-397c2c647928.png) <br> <br>

4. pc에 DNS서버인 8.8.8.8 추가 (지사에도 동일하게 적용)
   * ![img_152](https://user-images.githubusercontent.com/104918800/178190100-ec79e7a8-2ec7-4b9c-a21d-ab20bca7135b.png) <br> <br>

5. 글자를 DNS 서버(8.8.8.8)에게 물어봐서 접속하게끔 한다 (지사에도 동일하게 적용)
   * ![img_153](https://user-images.githubusercontent.com/104918800/178190104-ee0ddba0-7d91-4c18-a1cf-63250bfa6b0a.png) <br> <br>

6. DNS 네임으로 핑 보낼 수 있음
   * ![img_155](https://user-images.githubusercontent.com/104918800/178190108-2d150d43-553b-478d-87bb-69bdcc2156d7.png)
   * 이제 HQ에서는 인터넷에 접속할 수 있지만
   * 아직 BR에서는 인터넷에 접속할 수 없다 -> 회사 내부 사설 IP들을 매핑시켜야 함 <br> <br>

7. 사설 주소와 공인 주소를 매핑하기
   * any:0.0.0.0 줄임말임, 즉 내부에 있는 모든 사설 IP를 묶어낸다
   * ![img_161](https://user-images.githubusercontent.com/104918800/178190121-817bb21b-9d60-4679-b39d-8cba5645bec3.png) <br> <br>

8. 다음과 같이 구성하여 지사와 본사의 인터넷 연결하기
   * ![img_162](https://user-images.githubusercontent.com/104918800/178190124-0cb330f7-584f-4235-a5b9-b9f2eed76833.png) <br> <br>

9. PC를 실행하여 내부망 테스트
   * ![img_164](https://user-images.githubusercontent.com/104918800/178190127-5f493328-ff13-4138-ae77-50f7279a3e23.png)
   * ![img_165](https://user-images.githubusercontent.com/104918800/178190128-bf8f027f-c821-4233-a610-91538e05bd15.png) <br> <br>

10. PC에 DNS 주소 이용하도록 설정
    * ![img_166](https://user-images.githubusercontent.com/104918800/178190130-c827078c-6d46-444a-b623-fae4cb18c84c.png) <br> <br>

11. google에 핑 해보기
    * ![img_168](https://user-images.githubusercontent.com/104918800/178190135-009ccee4-3dcf-4226-a86d-8e1576603424.png) <br> <br>

<br>
<hr>


## 5. 연습문제
* 우리 회사는 웹서비스 제공을 위해 공인주소를 구매하였다.
* 211.183.3.0/24를 사용할 수 있고, 라우터의 HQ의 fa0/0에는 211.183.3.99를 할당한다.
* ISP 주소는 211.183.3.2로 고정되어 있다. 나머지 주소는 내부에서 자유롭게 사용할 수 있다
<br>

* 이 중, 211.183.3.100은 본사와 지사에 있는 PC들이 동적 PAT를 통하여 인터넷과 연결이 가능하도록 할 것이고,
* 211.183.3.101은 본사 내부에 있는 웹서버 10.10.100.100과 1:1로 정적 NAT를 구성하여 웹서비스를 구성하여 웹서비스를 제공할 계획이다
<br>

* 요약: ![img_169](https://user-images.githubusercontent.com/104918800/178190139-0f991987-78e2-4ad7-9cc3-5474edd67e23.png)
<br>

1. 클라우드(인터넷) 구성 다음과 같이 변경한다
   * ![img_170](https://user-images.githubusercontent.com/104918800/178190142-24462aaf-9d00-4caa-91a4-25452cb9e7b1.png) <br><br>
2. HQ의 fa0/0 설정하기
   * ![img_185](https://user-images.githubusercontent.com/104918800/178190178-dff9a950-62a2-4487-a8f5-5622690cca91.png) <br><br>
3. HQ의 아웃바운드 정적라우팅 재설정하기(기존의 ISP 지우고 새 ISP로 변경)
   * ![img_184](https://user-images.githubusercontent.com/104918800/178190177-b9ee7406-81ff-452e-b225-7da4ded41e25.png)
   * ![img_183](https://user-images.githubusercontent.com/104918800/178190172-0ad2343c-e881-47ed-afa5-d58618dcc649.png)
   * ![img_182](https://user-images.githubusercontent.com/104918800/178190169-8a27e294-cdb3-48b4-be87-3ce56a1c8927.png) <br><br>
4. HQ의 NAT 재설정하기(지우고 다시생성), 구글 핑하면 잘될것임
   * ![img_178](https://user-images.githubusercontent.com/104918800/178190163-ad858989-ca6a-4730-ac62-3b37837cb3e9.png)
   * ![img_177](https://user-images.githubusercontent.com/104918800/178190162-ce3dce31-522c-4f68-9969-3d3804f8ca33.png)
   * ![img_176](https://user-images.githubusercontent.com/104918800/178190160-b6218861-7889-4beb-b630-fddc5dcae96c.png) <br><br>
5. NAT 풀 생성, 범위 지정
   * ![img_186](https://user-images.githubusercontent.com/104918800/178190179-8a9ef3cd-cb39-431a-9cca-b7c167ba3684.png) <br><br>
6. NAT 정적매핑
   * ![img_187](https://user-images.githubusercontent.com/104918800/178190180-4d6178f3-f4da-49f7-a3c5-c101e5320889.png) <br><br>
7. ip nat inside source list 1 pool PRI overload
   * 아까 만든 PRI라는 pool을 사용
   * overload: 여러 개의 사설 ip가 하나의 ip를 공유할 것임 <br><br>
8. 라우터 하단 가상 PC에서 연결 시도
   * PC1> ping 8.8.8.8
   * PC2> ping 8.8.8.8  <br><br>



