# :pushpin: 경매를 시작할까?!
>머신러닝을 활용해 유화를 생성하고 이를 이용해 경매에 참여하는 서비스

</br>

## 1. 제작 기간 & 참여 인원
- 2022년 11월 22일 ~ 11월 28일
- 팀 프로젝트(5명/팀장)

</br>

## 2. 사용 기술
#### `Back-end`
  - Python 3.10.8
  - Django 4.1.3
  - DRF 3.14.0
  - Django simple JWT 5.2.2
#### `Database`
  - SQLite
#### `Front-end`
  - Vanilla JS
#### `Management`
  - Notion
  - Github
  - Slack

</br>

## 3. 핵심 기능
- 사용자 환경(회원가입, 로그인, 회원정보 관리 등)
- 유화 작품 생성, 수정, 삭제 기능 구현(사진 업로드, 유화 스타일 선택/적용 등)
- 나의 유화 작품 경매 등록, 삭제 기능 구현
- 포인트 적립, 사용 기능 구현
- 댓글 생성, 수정, 삭제 기능 구현

<br>

## 4. ERD 설계
![](https://velog.velcdn.com/images/2jiyeong0/post/dedcd2bc-7d6b-4180-8a1e-9c601ba3824a/image.PNG)


<br>

## 5. API 설계 
<details>
<summary style="font-size: 15px;"><b>USER API</b></summary>
<div markdown="1">
  
![](https://velog.velcdn.com/images/2jiyeong0/post/92eb30bc-8912-41a7-ac6c-421b7529516e/image.PNG)
![](https://velog.velcdn.com/images/2jiyeong0/post/3da0abd4-f471-49de-837c-0ea943e4d84a/image.PNG)
![](https://velog.velcdn.com/images/2jiyeong0/post/2e7f401a-9bf3-441f-8673-ad247206e25b/image.PNG)
![](https://velog.velcdn.com/images/2jiyeong0/post/082c396a-22e9-4b87-a5a1-92dbb0c9dc5a/image.PNG)

</div>
</details>


<details>
<summary style="font-size: 15px;"><b>PAINTING API</b></summary>
<div markdown="1">

![](https://velog.velcdn.com/images/2jiyeong0/post/12e91050-4a0a-47e9-87e4-7429f434d365/image.PNG)
![](https://velog.velcdn.com/images/2jiyeong0/post/c38fd430-4717-4c4b-aab4-1f212005cfd8/image.PNG)
![](https://velog.velcdn.com/images/2jiyeong0/post/907240da-5975-49e8-ba18-81741050605f/image.PNG)

</div>
</details>

<details>
<summary style="font-size: 15px;"><b>AUCTION API</b></summary> 
<div markdown="1">

![](https://velog.velcdn.com/images/2jiyeong0/post/2606a5ec-f30a-44e5-9cd5-f826e224fdaa/image.PNG)
![](https://velog.velcdn.com/images/2jiyeong0/post/9e2851bb-ab76-4b5d-b4b1-04fb7a27e2a5/image.PNG)
![](https://velog.velcdn.com/images/2jiyeong0/post/582b41dd-dd2b-4487-9036-521f4d8034df/image.PNG)
![](https://velog.velcdn.com/images/2jiyeong0/post/843c718b-a02b-47f8-a5ff-441ae860dbe2/image.PNG)



</div>
</details>

<br>


## 6. 트러블 슈팅
### 6.1. DRF serializers 부분업데이트(partial=True) 에러
- 문제: 기본적으로 serializers는 모든 필수 필드에 대한 값을 전달해야 하며 그렇지 않으면 유효성 검사 오류가 발생한다.

<details>
<summary><b>기존 코드</b></summary>
<div markdown="1">

~~~python
# users/views.py
user_serializer = UserSerializer(data=request.data)
~~~
</div>
</details>

- 해결
- partial=True 옵션을 통해 부분 업데이트가 허용
- 모델에는 더 많은 필드 값이 있지만 partial=True로 인해 해당 필드만 업데이트를 할 수 있게 해준다.

<details>
<summary><b>개선된 코드</b></summary>
<div markdown="1">

~~~python
# users/views.py
# partial을 True로 설정할 경우 required field에 대한 validation을 수행하지 않는다.
# 주로 일부 필드를 update 할 때 사용된다.
user_serializer = UserSerializer(data=request.data, partial=True)
~~~

</div>
</details>
</br>

### 6.2. migrate 에러
- 문제: 객체 수정할 때, 중복 변수 재수정 후 migrta 진행시 에러 발생
~~~python
django.db.utils.OperationalError: no such column
~~~

- 해결
- 기존에 존재하는 정보들을 어떻게 수정 할 지 하지 않았기 때문에 에러 발생
 1. model field 옵션에 defalt='' 추가
 2. model field 옵션에 null='True' 추가
 3. model field 옵션에 blank='True' 추가

</br>



## 7. 회고 / 느낀점 / 현황판 / 그 외 트러블 슈팅
>프로젝트 개발 회고 글: https://velog.io/@2jiyeong0/TIL22.11.28
<br>

>프로젝트 현황판 / 그 외 트러블 슈팅: https://bolder-starburst-a73.notion.site/c4e68e7765b64fd3bef2fb0071fb8996
