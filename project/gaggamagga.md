# :pushpin: 가까? 마까?
>제주도 맛집 추천 플랫폼  
>https://www.gaggamagga.shop  

</br>

## 1. 제작 기간 & 참여 인원
- 2022년 12월 01일 ~ 12월 29일
- 팀 프로젝트

</br>

## 2. 사용 기술
#### `Back-end`
  - Python 3.10.8
  - Django 4.1.3
  - DRF 3.14.0
  - Django simple JWT 5.2.2
#### `Database`
  - PostgreSQL 14.5
#### `Infra`
  - AWS EC2
  - AWS Route 53
  - AWS CloudFront
  - AWS S3
  - Docker 20.10.12
  - Docker Compose 2.11.2
  - Gunicorn
  - Nginx 1.23.2
  - Github Actions
#### `Front-end`
  - Vanilla JS
#### `Management`
  - Notion
  - Github
  - Slack

</br>

## 3. 핵심 기능
- 사용자 환경(회원가입, 로그인, 회원정보 관리, 팔로우, 비활성화, 아이디/비밀번호 찾기 등등)
- 맛집 후기(리뷰) 작성/수정/삭제, 조회수 카운트, 좋아요, 검색 기능
- 후기 댓글 작성/수정/삭제
- 후기 댓글의 대댓글 작성/수정/삭제 기능
- 유저간 댓글 알림 기능

<br>

## 4. ERD 설계
![image](https://user-images.githubusercontent.com/96408893/224054683-53982789-94e9-4478-8a9c-27191a0ac424.png)


<br>

## 5. API 설계 | [Swagger API Docs](https://www.back-gaggamagga.shop)
<details>
<summary style="font-size: 15px;"><b>USER API</b></summary>
<div markdown="1">
  
![user_api](https://user-images.githubusercontent.com/96408893/224063812-a5d74eea-0edf-4792-9bc6-24b4b51cee98.png)

</div>
</details>


<details>
<summary style="font-size: 15px;"><b>PLACE API</b></summary>
<div markdown="1">

![place_api](https://user-images.githubusercontent.com/96408893/224063863-bc0e91d1-1280-405d-b4cc-3c85ddb7091a.png)

</div>
</details>

<details>
<summary style="font-size: 15px;"><b>REVIEW API</b></summary> 
<div markdown="1">

![review_api](https://user-images.githubusercontent.com/96408893/224063910-8e237422-bf87-4244-91b2-83282f808f53.png)


</div>
</details>


<details>
<summary style="font-size: 15px;"><b>NOTIFICATION API</b></summary>
<div markdown="1">

![notification_api](https://user-images.githubusercontent.com/96408893/224063960-2fab70dd-381d-4a84-b43e-272056b00c07.png)


</div>
</details>
<br>

## 6. Architecture
![architecture](https://user-images.githubusercontent.com/96408893/224064354-f6062443-6500-4713-afaa-8e3e0b2ad339.png)

</br>

## 7. 트러블 슈팅
### 7.1. testcode 작성중 review 삭제 에러
- 문제: 테스트코드 review delete 부분 작성중 리뷰의 개수가 1개일 때 place.rating 을 계산 할 수 없는 에러 발생
~~~python
place.rating = (place.rating * review_cnt - review.rating_cnt) / (review_cnt - 1)
decimal.DivisionByZero: [<class 'decimal.DivisionByZero'>]
~~~

<details>
<summary><b>기존 코드</b></summary>
<div markdown="1">

~~~python
# reviews/views.py
place.rating = (place.rating * review_cnt - review.rating_cnt) / (review_cnt - 1)
~~~
</div>
</details>

- 해결
- 조건문을 이용해 리뷰의 개수가 1개일 때 place.rating = 0으로 설정

<details>
<summary><b>개선된 코드</b></summary>
<div markdown="1">

~~~python
# reviews/views.py
if review_cnt == 1:
  place.rating = 0
else:
  place.rating = (place.rating * review_cnt - review.rating_cnt) / (review_cnt - 1)
~~~

</div>
</details>
</br>

### 7.2. 외래키 참조 에러
- 문제: Profile이라는 모델이 따로 생성되어있어, User 모델과 연결되어있는 Review 리스트를 가져오려고 하니 에러 발생
~~~python
AttributeError: Got AttributeError when attempting to get a value for field `review_set` on serializer `PublicProfileSerializer`.
The serializer field might be named incorrectly and not match any attribute or key on the `Profile` instance.
~~~

![](https://velog.velcdn.com/images/2jiyeong0/post/92847691-9cbc-4f86-bb14-bdeb14358c3f/image.PNG)

- 해결
- source를 이용
- source='user.review_set'
- profile 관련 serializer에서도 내가 작성한 리뷰리스트를 조회

<details>
<summary><b>개선된 코드</b></summary>
<div markdown="1">

~~~python
# users/serializers.py
review_set = ReviewListSerializer(many=True, source='user.review_set')
~~~

</div>
</details>
</br>


## 8. 피드백 반영
### 8.1. 프로필의 사용자 팔로우 기능 중 자기 자신 팔로우 기능
- 피드백 내용: 사용자 피드백을 받는 기간중 페이지 점검을 하는데 자기 자신을 팔로우 한 사용자를 발견
- 문제: Vanilla JS를 이용해 구현한 프론트 부분에서 자기 자신은 팔로우 하지 못하게 설정해뒀으나 사용자가 임의로 코드를 변경해 자기 자신을 팔로우 가능하게 함

<details>
<summary><b>기존 코드</b></summary>
<div markdown="1">

~~~python
# users/views.py
def post(self, request, nickname):
        you = get_object_or_404(Profile, nickname=nickname)
        me = request.user.user_profile
        if me in you.followers.all():
            you.followers.remove(me)
            return Response({"message":"팔로우를 했습니다."}, status=status.HTTP_200_OK)
        else:
            you.followers.add(me)
            return Response({"message":"팔로우를 취소했습니다."}, status=status.HTTP_200_OK)
~~~
</div>
</details>

- 해결
- 백엔드 부분에 팔로우를 본인이 아닐 시에만 할 수 있게 조건을 추가 
- if me != you: 조건을 추가해 request.user 와 팔로우하려는 user가 동일할 시에는 error를 출력하게 설정

<details>
<summary><b>개선된 코드</b></summary>
<div markdown="1">

~~~python
# users/views.py
def post(self, request, nickname):
        you = get_object_or_404(Profile, nickname=nickname)
        me = request.user.user_profile
        if me != you:
            if me in you.followers.all():
                you.followers.remove(me)
                return Response({"message":"팔로우를 했습니다."}, status=status.HTTP_200_OK)
            else:
                you.followers.add(me)
                return Response({"message":"팔로우를 취소했습니다."}, status=status.HTTP_200_OK)
        return Response({"message":"본인은 팔로우 할 수 없습니다."}, status=status.HTTP_400_BAD_REQUEST)
~~~

</div>
</details>

<br>


## 9. 회고 / 느낀점 / 현황판 / 그 외 트러블 슈팅
>프로젝트 개발 회고 글: https://velog.io/@2jiyeong0/TIL22.12.29-파이널-프로젝트-최종발표-개인회고
<br>

>프로젝트 현황판 / 그 외 트러블 슈팅: https://bolder-starburst-a73.notion.site/060c8fd4af5845df8770441ef69bdaf5
