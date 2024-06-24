# 🍪 Django, Vue, GraphQL을 이용한 블로그 프로젝트 
> 블로그는 _create, read, update, delete_ 작업을 포함한다. 이 프로젝트에서는 Django로 CRUD 작업을 수행하고 블로그 데이터에 대한 **GraphQL API**를 제공하는데 중점을 둔다. Vue는 반응형 데이터 바인딩과 관리하기 쉬운 컴포넌트로 인해 동적 인터페이스를 쉽게 생성 할 수 있다. 또한 GraphQL API 데이터를 처리하므로 Vue Apollo 플러그인을 활용 할 수 있다. 

> 🌵 목표
1. Django 모델을 GraphQL API로 변환
2. Django 서버와 Vue 애플리케이션 동시 실행
3. Django 관리자로 블로그 게시물 관리
4. Vue에서 GraphGL API를 사용하여 브라우저에서 데이터 표시

작성자는 여러개의 게시물 작성 가능하다.
게시물에는 여러개의 태그가 있을 수 있다.

### 🍄 Project Overview

⚒️ 💻 environment & tools : macOS, Python IDLE, terminal

* Backend - Djanggo
---(GraphQL)---
* Frontend - Vue.js 3


## Step 1. 개발환경 구축
```
yeryeongseo@seoyelyeong-ui-MacBookPro Documents % mkdir dvg/
yeryeongseo@seoyelyeong-ui-MacBookPro Documents % cd dvg/
```
Django-Vue-GraphQL의 약어인 **dvg/**라는 새 프로젝트 디렉토리를 생성했다. 이는 프로젝트 코드를 구성한 디렉토리이다. 

```
yeryeongseo@seoyelyeong-ui-MacBookPro dvg % mkdir back_end/
yeryeongseo@seoyelyeong-ui-MacBookPro dvg % cd back_end/
```
프론트엔드 및 백엔트 코드를 완전히 분리 할 것이므로 프로젝트 디렉토리 내에 백엔드 디렉토리를 생성했다. 

### Django 설치

```
yeryeongseo@seoyelyeong-ui-MacBookPro back_end % python3 -m venv venv
yeryeongseo@seoyelyeong-ui-MacBookPro back_end % source venv/bin/activate
(venv) yeryeongseo@seoyelyeong-ui-MacBookPro back_end % 

```
파이썬의 내장 venv 모듈을 사용하여 venv라는 이름의 가상 환경을 만들고 활성화한다. 프롬포트 앞의 (venv)는 가상환경이 성공적으로 활성화되었음을 나타낸다. 

이제 back_end/디렉토리에 파일을 생성하고 첫 번째 요구사항을 정의해본다.

```
(venv) yeryeongseo@seoyelyeong-ui-MacBookPro back_end % echo "Django==5.0.2" > requirements.txt
(venv) yeryeongseo@seoyelyeong-ui-MacBookPro back_end % python -m pip install -r requirements.txt
```

![](https://velog.velcdn.com/images/yeryoong/post/ee4acdc9-7910-49bd-81f4-ef8838f48a5c/image.png)

이제 Django 프로젝트를 생성 할 준비가 되었다.

### Django 프로젝트 생성
```
(venv) yeryeongseo@seoyelyeong-ui-MacBookPro back_end % django-admin startproject backend .
```
이때 마지막 점(.)을 빠뜨리지 않도록 주의해야한다. 점을 추가하면 장고가 백엔드 프로젝트에 중첩된 프로젝트 디렉토리를 만들지 않는다. 

현재 디렉토리의 구조는 다음과 같다.
![](https://velog.velcdn.com/images/yeryoong/post/12741d1b-1a16-454f-9cea-0a483e0024b9/image.png)

```
dvg/
└── back_end/
    ├── manage.py
    ├── requirements.txt
    └── backend/
        ├── __init__.py
        ├── asgi.py
        ├── settings.py
        ├── urls.py
        └── wsgi.py

```

### Django 마이그레이션 실행

특정 내용을 추가하기 전에 Django의 초기 마이그레이션을 실행해야한다. 

```
(venv) yeryeongseo@seoyelyeong-ui-MacBookPro back_end % python3 manage.py migrate
```
![](https://velog.velcdn.com/images/yeryoong/post/67eeb10b-591c-49fe-ae67-8dcdc50c5bbd/image.png)

> 위의 그림처럼 마이그레이션을 실행하면 각각의 마이그레이션에 대해 OK가 표시된 긴 목록이 나타난다. Django 마이그레이션을 실행하면 db.sqlite3 이라는 SQLite 파일이 생성된다. 이 파일은 프로젝트의 데이터베이스다. 즉 프로젝트의 데이터를 저장하며, 이 작업 중 생성하는 모든 블로그 게시물은 이 파일에 저장된다.

### Super User 생성
> 이제 데이터베이스가 준비되었으므로 슈퍼유저를 생성 할 수 있다. 이 슈퍼유저로 나중에 Django 관리자 인터페이스에 로그인 할 수 있다. createsuperuser 관리 명령을 사용해 하나를 생성해본다.

```
(venv) yeryeongseo@seoyelyeong-ui-MacBookPro back_end % python3 manage.py createsuperuser

```
![](https://velog.velcdn.com/images/yeryoong/post/563da0f3-be98-4561-b846-b214009aecae/image.png)

> createsuperuser 관리 명령을 실행하면 사용자 이름, 이메일, 비밀번호를 설정하는 프롬포트가 표시된다. 이 필드에 본인의 데이터를 입력하고 기억해야한다. 이 인증정보는 Django 관리자 인터페이스에 로그인하는데 필요하다. 
이제 Django 애플리케이션이 준비되었으니 장고 개발 서버를 시작하고 브라우저에서 확인해보자. 기본적으로 포트 8000번에서 runserver 관리 명령을 사용하여 서버를 시작한다. 

```
(venv) yeryeongseo@seoyelyeong-ui-MacBookPro back_end % python3 manage.py runserver
```
![](https://velog.velcdn.com/images/yeryoong/post/bc2f3599-6411-43c4-ba26-b24d52c1160c/image.png) ![](https://velog.velcdn.com/images/yeryoong/post/2bfd1546-4374-4f52-b9c9-55601fcb5cf2/image.png)

> 이제 브라우저에서 http://localhost:8000 을 방문하면 Django 스플래시 페이지가 표시된다. 또한 http://localhost:8000/admin 을 방문하여 로그인 폼을 볼 수 있다. 슈퍼 유저를 위해 사용자 이름과 비밀번호로 장고 관리자에 로그인한다. 

![](https://velog.velcdn.com/images/yeryoong/post/1a8bce8e-5267-42e7-89f5-aaa151a75a30/image.png) (로그인 전 화면) ![](https://velog.velcdn.com/images/yeryoong/post/b64579a5-dd12-4416-ba1d-84ea7f706ffe/image.png)

(로그인 후  화면)

> 로그인 성공 시 장고 관리자 인터페이스로 이동한다. 현재는 비어있는상태이다. 

## Step2. Django 관리자 생성
> 장고 프로젝트의 기초가 마련되었다. 블로그의 핵심 로직을 만들어보자. 이번 단계에서는 블로그 콘텐츠를 작성하고 관리하기위한 데이터 모델과 관리 구성을 만든다.

#### Django 블로그 애플리케이션 생성

> 블로그의 기능들을 별도의 Django 애플리케이션으로 분리해야한다. startapp 관리명령을 사용하여 애플리케이션을 생성하자.

```
(venv) yeryeongseo@seoyelyeong-ui-MacBookPro back_end % python3 manage.py startapp blog
```
![](https://velog.velcdn.com/images/yeryoong/post/c0cd70c8-ff97-44e5-8c84-2e7a0709cf8d/image.png)

> 이제 blog/ 디렉토리가 생성되었고, backend/폴더에 있는 파일과 유사한 여러 파일이 포함되었다.
이제 backend/ 프로젝트 앱에서 일부 변경 및 추가를 수행해야한다. 

#### Django 블로그 애플리케이션 활성화

>장고 애플리케이션을 생성하면 프로젝트에서 기본적으로 사용 할 수 없다. 프로젝트가 새로운 애플리케이션을 알 수 있도록 설치된 애플리케이션 목록에 추가해야한다. backend/settings.py 에서 INSTALLED_APPS 변수를 업데이트하자.

![](https://velog.velcdn.com/images/yeryoong/post/c95b9ea7-b45b-477b-ac63-e36275a067d5/image.png)

> vi편집기를 이용하여 'blog.apps.BlogConfig'를 추가해주었고, 이를 통해 Django가 해당 앱을 알게된다. 앱의 설정 클래스를 명시적으로 지정하면 INSTALLED_APPS가 해당 앱에 대한 데이터 모델 및 URL 패턴과 같은 정보를 검색하는데 도움이 된다.

#### Django 블로그 데이터 모델 생성

>이제 장고는 블로그 앱을 인식 할 수 있으므로 데이터 모델을 만들 수 있다. 세 가지 모델을 만들어볼것이다.
* Profile : 블로그 사용자에 대한 추가 정보 저장
* Tag : 블로그 게시물을 그룹화하는 카테고리
* Post : 각 블로그 게시물의 콘텐츠 및 메타데이터 저장

각 모델을 blog/models.py에 추가할것이다. 모든 모델은 Django의 models.Model 클래스를 상속한다. 

#### Profile 모델

> 프로필 모델은 사용자 정보를 포함하는 세가지 필드를 가진다. 
* user - 해당 프로필이 연관된 장고 사용자와의 1:1 연결
* website - 사용자에 대해 더 자세히 알아볼 수 있는 선택적인 URL
* bio - 사용자에 대한 짧은 텍스트로 선택적

blog/models.py를 열고 settings 모듈을 가져오는 import문을 추가하자.
![](https://velog.velcdn.com/images/yeryoong/post/cf314b68-2de9-4d03-950b-e605319538f6/image.png)

.str()메소드는 만든 프로필 객체가 관리자 사이트에서 사용자 이름을 표시하게한다. 

#### Tag 모델
> 블로그 게시물을 작성 할 때 해당 게시물에 태그를 추가 할 수 있다. 이러한 태그를 사용하면 독자가 이후에 특정 태그로 게시물을 필터링 할 수 있다. Tag 모델에는 name 이라는 하나의 필드만 있으며, 이 필드는 태그의 짧고 고유한 이름을 저장한다. 

![](https://velog.velcdn.com/images/yeryoong/post/3e4811e9-7ac4-4c3e-97a7-d3f75e764b69/image.png)

#### Post 모델
> 블로그의 핵심인 게시물 모델이다. Post 모델은 실제 블로그 게시물을 나타내며 작성한 내용과 추가한 메타 정보를 포함하며 다음과 같은 여러 필드가 있다.

![](https://velog.velcdn.com/images/yeryoong/post/b64b5106-046a-4838-8d0e-6cfbb3e0f06d/image.png)

> 일반적으로 가장 최근 게시물이 먼저 표시되므로 정렬도 게시된 날짜를 기준으로 가장 최근것이 먼저 표시되도록 설정한다. 이제 Post 모델도 만들어보자.

![](https://velog.velcdn.com/images/yeryoong/post/3fa2fb32-9d7f-416b-bc72-3e4099af0dd6/image.png)

> author(작성자)에 대한 on_delete=models.PROTECT 인수는 게시물이 있는 작성자를 실수로 삭제하지 않도록한다. Tag에 대한 ManyToManyField 관계를 사용하면 게시물을 영구적으로, 여러 태그와 연결 할 수 있다. 또한 각 태그는 여러 게시물에 연결 될 수 있다. 

#### 모델 관리자 구성 생성
> 모델이 설정되었으니 장고에게 이 모델들이 어떻게 관리자 인터페이스에 표시되어야하는지 알려주어야한다. blog/admin.py에서 Django의 admin 모듈과 모델들을 가져온다.

![](https://velog.velcdn.com/images/yeryoong/post/ce9b7eb5-172b-4147-955b-48ee2b3aeb48/image.png)

#### 프로펠과 태그에 대한 모델 관리자 클래스 생성 및 등록
프로필과 태그 모델에 대한 관리자 클래스를 생성하고 등록한다. 단순히 모델을 지정하면된다. 
![](https://velog.velcdn.com/images/yeryoong/post/a1b0a536-67be-400c-be58-2800a194643e/image.png)

#### 게시물에 대한 모델 관리자 클래스 생성 및 설정
> 게시물은 많은 정보를 포함하므로 인터페이스를 혼잡하게 만들지 않으면서 특정 정보만 표시하는것이 도움된다. 이를 위해 게시물에 대한 관리자 클래스를 생성한다. 이 클래스에는 특정 변수들이 사용되며, 이를 통해 게시물의 상세 정보를 사용자 정의 할 수 있다.

![](https://velog.velcdn.com/images/yeryoong/post/36a51c0d-12b2-4e1f-900f-defc3bf98b2e/image.png)![](https://velog.velcdn.com/images/yeryoong/post/5bcd3c65-1705-4f69-9844-a9666ed71bb2/image.png)

* list_display : 각 게시물에 대한 정보만을 표시한다.이를 통해 게시물 목록이 보다 간결히 표시되고 사용자가 필요한 정보를 쉽게 확인 할 수 있다.
* list_filter : 게시물 목록을 게시된 게시물 또는 게시되지 않은 게시물, 게시 날자에 따라 필터링 할 수 있도록한다. 이를 통해 특정 기준에 따라 게시물을 신속하게 찾을 수 있다. 
* list_editable : ID를 제외한 모든 표시된 필드를 편집 할 수 있도록 허용한다. 이를 통해 관리자가 게시물 목록 페이지에서 직접 필요한 정보를 수정 할 수 있다.
* search_fields : 제목, 부제목, 슬러그 및 본문을 사용하여 게시물을 검색 할 수 있도록 허용한다. 이를 통해 사용자는 특정 키워드나 문구를 포함하는 게시물을 빠르게 찾을 수 있다.
* prepopulated_fields : 슬러그 필드를 제목과 부제목을 사용하여 미리 채운다. 이를 통해 슬러그 필드를 자동으로 채우고 사용자가 수동으로 입력하는 번거로움을 줄일 수 있다.
* date_hierarchy : 모든 게시물의 게시 날짜를 사용하여 브라우저에서 탐색 가능 한 날짜 계층을 생성한다. 이를 통해 사용자는 특정 날짜 범위에 해당하는 게시물을 빠르게 찾을 수 있다.
* save_on_top : 목록 상단에 변경 사항을 저장하는 버튼을 표시한다. 이를 통해 사용자는 목록의 다른 부분으로 이동하지 않고도 변경사항을 즉시 저장 할 수 있다.

#### 모델 마이그레이션 생성 및 적용
> Django는 블로그 내용을 관리하고 유지 할 수 있는 모든 정보를 가지고있지만 이러한 변경 사항을 지원하는 데이트베이스를 업데이트해야한다. 이제 모델들에 대한 마이그레이션을 생성하고 실행한다. makemigrations 명령을 사용하여 마이그레이션을 생성하고 migrate 명령을 사용하여 이를 실행한다.

```
(venv) yeryeongseo@seoyelyeong-ui-MacBookPro back_end % python3 manage.py makemigrations
(venv) yeryeongseo@seoyelyeong-ui-MacBookPro back_end % python3 manage.py migrate
```
![](https://velog.velcdn.com/images/yeryoong/post/9eb6e11b-119f-4555-897b-12bebf264b97/image.png)

이제 중지했던 장고 서버를 다시 시작해 관리자 페이지에 다시 접속한다. ![](https://velog.velcdn.com/images/yeryoong/post/586396c5-d7b7-414c-88a0-6c7d8a8b9da2/image.png)
태그, 프로필 및 게시물 목록에 대한 링크와 추가/편집이 가능한 링크가 있다. 이제 직접 게시물을 추가하고 편집하여 관리자 인터페이스가 어떻게 반응하는지 보자.

## Step 3. Graphene-Django 설정
> GraphQL을 사용하면 필요한 데이터만 검색 할 수 있으므로 RESTful API에서 일반적인 대용량 응답과 비교하여 사용 할 수 있다. 이는 데이터를 투영하는데 유연성을 제공하므로 GraphQL API를 제공하는 서비스의 로직은 변경하지않고 새로운 방식으로 데이터를 검색 할 수 있다. 지금까지의 작업물을 GraphQL API에 통합하기위해 Graphene-Django를 사용할것이다. 

#### Graphene-Django 설치
먼저 프로젝트의 requirements 파일에 추가한다.![](https://velog.velcdn.com/images/yeryoong/post/48c089de-0a85-492e-b6ef-d9266a5a5ca2/image.png)
그런 다음 업데이트된 requirments 파일을 사용하여 설치한다.
```
(venv) yeryeongseo@seoyelyeong-ui-MacBookPro back_end % python3 -m pip install -r requirements.txt
```
![](https://velog.velcdn.com/images/yeryoong/post/94a36b8c-be93-43a8-8ced-622064fe6efa/image.png)

> 설치가 잘 된것을 확인 할 수 있다. 이제 settings.py의 INSTALLED_APPS 변수에 "graphene_django"를 추가하여 프로젝트의 settings.py 모듈에서 장고가 이를 찾을 수 있도록 한다. 

![](https://velog.velcdn.com/images/yeryoong/post/e16ea25d-a6ac-47d9-b4ab-b0b7e0c895e8/image.png)
> 위와 같이 추가해주었고, Graphene-Django를 작동시키기위해 몇 가지 구성을 수행한다. settings.py를 업데이트하여 프로젝트가 GraphGL 정보를 찾을 위치를 알 수 있도록 하고, GraphQL API 및 GraphiQL(탐색 가능한 GraphQL 인터페이스)을 제공하기 위해 URL 패턴을 추가한다. GRAPHENE 설정은 프로젝트가 GraphQL 스키마를 찾을 특정 위치를 구성한다. settings.py에서 스키마를 blog.shcema.schema Python 경로로 지정하면된다.

#### GraphQL 및 GraphiQL을 위한 URL 패턴 추가
이제 backend/urls.py에 새 URL 패턴을 추가해야한다. URL을 Graphene-Django의 GraphQLView로 지정한다. 
![](https://velog.velcdn.com/images/yeryoong/post/e948f977-4dfc-49f1-bda7-c4ec1d2402d0/image.png)

graphiql = True 인수는 Graphene-Django에게 GraphiQL 인터페이스를 사용 할 수 있게한다.

#### GraphQL 스키마생성
스키마는 각각 특정 Django 모델과 중요한 유형의 쿼리를 해결하는 방법을 지정하는 몇 가지 클래스로 구성된다. 

![](https://velog.velcdn.com/images/yeryoong/post/11dcb376-177f-426d-ac0b-9df2cfb92233/image.png)
back_end/blog/shcema.py 모듈을 생성하여 Graphene-Django의 DjangoObjectType, 블로그 모델 및 get_user_model()을 가져온다.

![](https://velog.velcdn.com/images/yeryoong/post/6bc00adc-c3a8-4d26-8ebf-b71a2c5a5655/image.png)
이제 각 모델 및 사용자 모델에 대한 해당 클래스를 만든다. 각 클래스는 GraphQL 유형을 나타내므로 이름이 Type으로 끝나야한다. 

![](https://velog.velcdn.com/images/yeryoong/post/8311108c-10f9-46b6-bd26-41e239635100/image.png)

이제 다시 서버에 연결하고, 블로그에 직접 글을 등록해보자..

![](https://velog.velcdn.com/images/yeryoong/post/a1aeeee6-a74a-41f3-8a3c-423547414427/image.png)

세 개의 예시 포스트를 등록해보았다.


![](https://velog.velcdn.com/images/yeryoong/post/554870d6-c536-41d1-b977-9f47e60b95e3/image.png)

#### Graphene-Django 설치하기
프로젝트의 requirements 파일에 Graphene-Django를 추가해야한다. 
```
(venv) yeryeongseo@seoyelyeong-ui-MacBookPro backend % vi requirements.txt
```
```
```
코드를 입력하세요
``` 
(venv) yeryeongseo@seoyelyeong-ui-MacBookPro backend % cat requirements.txt
Django==5.0.2
graphene-django==3.2.0
```

![](https://velog.velcdn.com/images/yeryoong/post/8c53eb60-db95-4081-be97-1943c368e11f/image.png)

이제 프로젝트의 settings.py 모듈에 INSTALLED_APPS 변수에 "graphene_django"를 추가하여 Django가 그것을 찾을 수 있도록한다.

#### Graphene-Django 구성하기
settings.py를 업데이트하여 프로젝트가 GraphQL 스키마를 찾는 위치를 알 수 있도록 한다.

```
back_end/backend/settings.py
# ...

GRAPHENE = {
  "SCHEMA": "blog.schema.schema",
}

```
### 이제 GraphQL 과 GraphiQL을 위한 URL 패턴을 추가하기
장고가 GraphQL 엔트포인트와 GraphiQL 인터페이스를 제공하도록하기위해 새로운 URL 패턴을 'backend/urls.py'에 추가한다. 이 URL 패턴은 Graphene-Django의 'GraphQLView'를 가리킨다.

```
# backend/urls.py
from django.contrib import admin
from django.urls import path
from django.views.decorators.csrf import csrf_exempt
from graphene_django.views import GraphQLView

urlpatterns = [
    path("admin/", admin.site.urls),
    path("graphql/", csrf_exempt(GraphQLView.as_view(graphiql=True))),
]

```

### GraphQL 스키마 생성하기
스키마는 각 Django 모델과 관련된 여러 클래스와 프론트엔드에서 필요한 몇 가지 중요한 쿼리 유형을 해결하는 방법을 지정하는 클래스로 구성된다.
* 'blog/' 디렉토리에 'schema.py' 모듈을 새로 만든다.
* 'DjangoObjectType', 블로그 모델, 장고 사용자 모델에 접근 할 수 있는 'get_user_model()'을 가져온다.

```
# back_end/blog/schema.py
from django.contrib.auth import get_user_model
from graphene_django import DjangoObjectType
from blog import models

```

이제 각 모델과 사용자 모델에 해당하는 클래스를 만든다. 각 클래스는 GraphQL 타입을 나타내므로 이름끝에 'Type'을 붙인다. 
```
# back_end/blog/schema.py
class UserType(DjangoObjectType):
    class Meta:
        model = get_user_model()

class AuthorType(DjangoObjectType):
    class Meta:
        model = models.Profile

class PostType(DjangoObjectType):
    class Meta:
        model = models.Post

class TagType(DjangoObjectType):
    class Meta:
        model = models.Tag

```
추가적으로 'graphene.ObjectType'을 상속하는 'Query' 클래스를 만들어야한다. 이 클래스는 생성한 모든 타입 클래스를 하나로 모아준다. 'graphene'을 가져와서 쿼리할 모델을 지정하는 메소드를 추가한다.

```
# back_end/blog/schema.py
import graphene
from django.contrib.auth import get_user_model
from graphene_django import DjangoObjectType
from blog import models

class Query(graphene.ObjectType):
    all_posts = graphene.List(PostType)
    author_by_username = graphene.Field(AuthorType, username=graphene.String())
    post_by_slug = graphene.Field(PostType, slug=graphene.String())
    posts_by_author = graphene.List(PostType, username=graphene.String())
    posts_by_tag = graphene.List(PostType, tag=graphene.String())

    def resolve_all_posts(root, info):
        return models.Post.objects.prefetch_related("tags").select_related("author").all()

    def resolve_author_by_username(root, info, username):
        return models.Profile.objects.select_related("user").get(user__username=username)

    def resolve_post_by_slug(root, info, slug):
        return models.Post.objects.prefetch_related("tags").select_related("author").get(slug=slug)

    def resolve_posts_by_author(root, info, username):
        return models.Post.objects.prefetch_related("tags").select_related("author").filter(author__user__username=username)

    def resolve_posts_by_tag(root, info, tag):
        return models.Post.objects.prefetch_related("tags").select_related("author").filter(tags__name__iexact=tag)

```
'Query'클래스는 'graphene.List' 또는 'graphene.Field' 유형의 여러 속성으로 구성된다. 단일 항목을 반환하는 쿼리에는 'graphene.Field'를 사용하고, 여러 항목을 반환하는 쿼리에는 'graphene.List'를 사용한다.

이제 모든 타입과 resolver를 가진 스키마가 완성되었다. 
```
# back_end/blog/schema.py
schema = graphene.Schema(query=Query)

```

이제 서버를 실행하여 GraphQL 인터페이스에 접속하자.

```
python manage.py runserver

```

브라우저를 열고 localhost:8000/graphql 주소로 이동하면 다음과 같은 화면이 뜬다.


![](https://velog.velcdn.com/images/yeryoong/post/0a28e1e0-4689-4455-9fdc-59e19a909877/image.png)

여기에서 GraphiQL 인터페이스를 사용하여 GraphQL 쿼리를 실행 할 수 있다. 또한 저장된 데이터에 접근하고 다양한 쿼리를 테스트 할 수 있다.

#### GraphiQL 활성화 확인

이제 화면 좌측 상단의 Docs 섹션을 확장하고 'query:Query'를 클릭한다. 이제 다음 쿼리를 입력해보자! 다음 쿼리는 생성한 모든 게시물의 리스트를 반환한다.
```
{
  allPosts {
    title
    author {
      user {
        username
      }
    }
    tags {
      name
    }
  }
}

```
위의 쿼리를 입력 후 play 버튼을 누르면 게시물 목록을 반환받는다. 
![](https://velog.velcdn.com/images/yeryoong/post/1238dddc-a09e-4dff-8076-24eadd9b8086/image.png)

## Step 4. django-cors-headers 설정
백엔드와 프론트엔드가 로컬에서 다른 포트에서 실행이 되기때문에, 프로덕션 환경에서 완전히 별개의 도메인에서 실행 될 수 있기 때문에 교차 출처 리소스 공유(Cross-origin resource sharing, CORS)를 처리해야한다. 이를 처리하지 않으면 프론트엔드에서 백엔드로의 요청이 브라우저에 의해 차단된다. django-cors-headers 프로젝트를 사용하면 CORS를 쉽게 처리 할 수 있다. 이 프로젝트를 사용하여 Django가 다른 출처에서 오는 요청에도 응답하도록 설정하면 프론트엔드가 GraphQL API와 원활하게 통신 할 수 있다. 

> 교차 출처 리소스 공유(CORS)란?
추가 HTTP 헤더를 사용하여 한 출처에서 실행중인 웹 애플리케이션이 다른 출처의 선택한 자원에 접근 할 수 있는 권한을 부여하도록 브라우저에 알려주는 체제이다. 웹 애플리케이션은 리소스가 자신의 출처(도메인, 프로토콜, 포트)와 다를 때 교차 출처 HTTP 요청을 실행한다.

### django-cors-headers 설치
'django-cors-headers'를 requirements 파일에 추가한다.
![](https://velog.velcdn.com/images/yeryoong/post/a57ed6ba-7236-4369-9a65-8873ac4eefc0/image.png)

이제 업데이트된 requirements 파일을 사용하여 패키지를 설치한다. 
```
yeryeongseo@seoyelyeong-ui-MacBookPro backend % python3 -m pip install -r requirements.txt 
```
이제 프로젝트의 'settings.py' 모듈에서 'INSTALLED_APPS' 리스트에 'corsheaders'를 추가한다.

![](https://velog.velcdn.com/images/yeryoong/post/2b35bc8e-4b0b-40be-b194-f6edb7f82af5/image.png)

저장하기전에 'MIDDLEWARE'리스트의 맨 앞에 "corsheaders.middleware.CorsMiddleware" 을 추가한다. django-cors-headers 문서에서는 미들웨어 리스트의 가능한 앞 쪽에 이 미들웨어를 두는것을 권장한다고한다. 최소한 Django의 'CommonMiddleware'보다 앞에 두어야한다.
![](https://velog.velcdn.com/images/yeryoong/post/6d67dd71-c3a6-4d73-86b9-294fbd0e2e54/image.png)

이제 django-cors-headers 를 설치해보자
```
yeryeongseo@seoyelyeong-ui-MacBookPro back_end % pip3 install django-cors-headers
```

특정 도메인에서만 요청을 허용하려면 settings.py 에 다음을 추가해준다.
```
CORS_ALLOWED_ORIGINS = [
    "http://localhost:5173",
]

```

이제 Django 백엔드가 CORS 설정을 통해 프론트엔드에서의 요청을 허용 할 수 있다.

## Step 5. Vue.js 설정

### Node.js 설치
[공식홈페이지](https://nodejs.org/en)에서 다운받아준다.

### 설치 확인
![](https://velog.velcdn.com/images/yeryoong/post/69fc26b3-5110-413c-8452-dd5fd3c9e178/image.png)

설치가 확인되었다면 Vue 프로젝트를 생성할 디렉토리 'dvg'로 이동해 생성한다.

![](https://velog.velcdn.com/images/yeryoong/post/71be2d8c-113a-4794-a51c-00939b9d3474/image.png)

> Project name(프로젝트 이름): front_end
Add TypeScript?(TypeScript를 추가하시겠습니까?): No
Add JSX Support?(JSX 지원을 추가하시겠습니까?): No
Add Vue Router for Single Page Application development?(단일 페이지 애플리케이션 개발을 위해 Vue Router를 추가하시겠습니까?): Yes
Add Pinia for state management?(상태 관리를 위해 Pinia를 추가하시겠습니까?): No
Add Vitest for Unit Testing?(단위 테스트를 위해 Vitest를 추가하시겠습니까?): No
Add an End-to-End Testing Solution?(종단 간 테스트 솔루션을 추가하시겠습니까?): No
Add ESLint for code quality?(코드 품질을 위해 ESLint를 추가하시겠습니까?): No
Add Vue DevTools 7 extension for debugging?(디버깅을 위해 Vue DevTools 7 확장을 추가하시겠습니까?): Yes(그냥 Yes해봄)

이제 백엔드 폴더, 프론트엔드 폴더가 만들어진걸 확인 할 수 있다. ![](https://velog.velcdn.com/images/yeryoong/post/01a7b102-9e2a-4cb4-b643-80945370259d/image.png)

front_end/ 폴더로 이동하여 프로젝트의 모든 종속 항목을 설치한다. 

![](https://velog.velcdn.com/images/yeryoong/post/6138cac6-8940-4ecf-b973-8942ff4d15e9/image.png)

프로젝트 구조가 마련되고 모든 종속 항목이 설치되었으므로 Vite 개발 서버를 실행 할 수 있다. 

### Vite란..![](https://velog.velcdn.com/images/yeryoong/post/62291a04-a78a-4020-a425-b187f775d0c9/image.png)

```
$ npm run dev
```

![](https://velog.velcdn.com/images/yeryoong/post/fbac8380-8ab7-4a55-9fee-b76c6f80202e/image.png)

기본적으로 Vite 서버는 5173 포트에서 실행된다. 이제 Django 애플리케이션은 http://localhost:8000 에서 실행되고 Vue 애플리케이션은 http://localhost:5173 에서 실행된다. http://localhost:5173를 방문해보자.![](https://velog.velcdn.com/images/yeryoong/post/39fa1477-8335-4d2e-aaa1-e4bd7d5373fd/image.png)

## Step 6. 기본 뷰와 컴포넌트 생성
이제 Vue가 실행되므로 컴포넌트를 생성 할 수 있다. 컴포넌트는 프론트엔드의 빌딩 블록이다.
처음으로 생성할 컴포넌트는 Posts와 Authors를 볼 수 있는 뷰이다. 첫 번째 뷰를 생성하기 전에 정리작업이 필요하다. 

#### 기존 뷰 제거
src/views/ 디렉토리에 기본 뷰 두 개가 있다. 이 두 뷰를 제거한다. 
```
(venv) yeryeongseo@seoyelyeong-ui-MacBookPro views % pwd
/Users/yeryeongseo/Documents/dvg/front_end/src/views
(venv) yeryeongseo@seoyelyeong-ui-MacBookPro views % ls
AboutView.vue	HomeView.vue
(venv) yeryeongseo@seoyelyeong-ui-MacBookPro views % rm -rf AboutView.vue
(venv) yeryeongseo@seoyelyeong-ui-MacBookPro views % rm -rf HomeView.vue 
(venv) yeryeongseo@seoyelyeong-ui-MacBookPro views % ls
(venv) yeryeongseo@seoyelyeong-ui-MacBookPro views % 
```

#### 뷰 추가
블로그에는 네 가지 뷰가 필요한데 먼저 AuthorView.vue를 생성한다. 이 파일은 Vue Single-File Component(SFC)이다. 이는 컴포넌트의 템플릿, 로직 및 스타일을 하나의 파일로 묶어낸 특수한 파일 형식이다. 
```
<!-- front_end/src/views/AuthorView.vue -->
<script setup>
import PostList from "../components/PostList.vue";
</script>

<template>
  <h2>Author</h2>
  <PostList />
</template>

<style scoped>
  h2 {
    color: red;
  }
</style>

```

vi 편집기로 추가하였다. 일반적으로 `<script>` 블록으로 시작하며 여기서 SFC가 사용하는 모든 컴포넌트를 가져온다. 위의 코드에서는 나중에 생성하러 PostList 컴포넌트를 가져온다. HTML은 `<template>` 블록 내에 있어야하고, 렌더링하려는 컴포넌트는 사용자 정의 HTML 태그를 사용한다. `<style>`내의 CSS는 스코프가 지정된다. 이는 SFC 내에서 정의된 스타일이 동일 파일의 요소에만 영향을 미친다는것을 의미한다. 기본적으로 자식 컴포넌트는 이 스타일을 상속하지 않는다.
  
이제 views/ 폴더에 AllPostsView.vue, PostsByTagView.vue, PostView.vue 를 생성한다.
  
  ---
```
// AllPostView.vue
<script setup>
import PostList from "../components/PostList.vue";
</script>

<template>
  <h2>Recent Posts</h2>
  <PostList />
</template>

<style scoped>
  h2 {
    color: blue;
  }
</style>
```
  ---
```
//PostsByTagView.vue
<script setup>
import PostList from "../components/PostList.vue";
</script>

<template>
  <h2>Posts by Tag</h2>
  <PostList />
</template>

<style scoped>
  h2 {
    color: green;
  }
</style>
```
  ---
```
//PostView.vue
<script setup>
import AuthorLink from "../components/AuthorLink.vue";
</script>

<template>
  <h2>Post</h2>
  <AuthorLink />
</template>

<style scoped>
  h2 {
    color: orange;
  }
</style>
```

#### 기존 컴포넌트 제거
다음 경로의 몇 개의 파일과 'icons/' 폴더를 삭제한다.
src/components/
```
(venv) yeryeongseo@seoyelyeong-ui-MacBookPro components % ls
HelloWorld.vue	TheWelcome.vue	WelcomeItem.vue	icons
```
#### 컴포넌트 생성
블로그에 필요한 두 개의 컴포넌트를 생성한다. 먼저 작가의 링크를 표시할 컴포넌트를 생성한다.
'components/' 폴더에 'AuthorLink.vue' 파일을 생성한다.
```
<!-- front_end/src/components/AuthorLink.vue -->
<template>
  <h3>Author Link</h3>
</template>

```
다음으로 같은 폴더에 'PostList.vue' 파일을 생성한다.
```
<!-- front_end/src/components/PostList.vue -->
<script setup>
import AuthorLink from "./AuthorLink.vue";
</script>

<template>
  <h3>Posts List</h3>
  <AuthorLink />
</template>

```
PostList에서는 AuthorLink 도 렌더링한다. 필요한곳에 컴포넌트를 가져와서 사용 할 수 있다. 

#### 라우트 설정
현재 http://localhost:5173 에 방문하면 오류가 발생할것이다. 잘못된 라우트를 수정하고 새 뷰에 대한 라우트를 설정하려면 'src/router/' 폴더의 기존 'index.js' 파일을 열고 아래 코드로 내용을 대체한다.
```
import { createRouter, createWebHistory } from "vue-router";
import AuthorView from "../views/AuthorView.vue";
import AllPostsView from "../views/AllPostsView.vue";
import PostView from "../views/PostView.vue";
import PostsByTagView from "../views/PostsByTagView.vue";

const router = createRouter({
  history: createWebHistory(import.meta.env.BASE_URL),
  routes: [
    {
      path: "/",
      name: "posts",
      component: AllPostsView,
    },
    {
      path: "/author",
      name: "author",
      component: AuthorView,
    },
    {
      path: "/post",
      name: "post",
      component: PostView,
    },
    {
      path: "/tag",
      name: "tag",
      component: PostsByTagView,
    },
  ],
});

export default router;

```
이제 라우트가 존재하는 뷰를 가리키므로 Vite 서버 출력에 있는 모든 오류가 사라질것이다. 다른 페이지로 편리하게 이동하려면 'App.vue' 파일에 라우트 링크를 추가한다. 'App.vue' 파일은 Vue 앱이 찾는 기본 컴포넌트이다. 'src/' 폴더에 위치해있다. 'App.vue' 파일의 기존 내용을 아래 내용으로 대체한다.
```
<script setup>
import { RouterLink, RouterView } from "vue-router";
</script>

<template>
  <header>
    <div class="wrapper">
      <h1>My Blog 🐾</h1>
      <nav>
        <RouterLink to="/">Posts</RouterLink>
        <RouterLink :to="{ name: 'author' }">Author</RouterLink>
        <RouterLink :to="{ name: 'post' }">Post</RouterLink>
        <RouterLink :to="{ name: 'tag' }">Tag</RouterLink>
      </nav>
    </div>
  </header>

  <RouterView />
</template>

<style scoped>
  h1 {
    text-align: center;
    font-weight: bold;
    margin-bottom: 1rem;
  }

  header {
    border-bottom: 1px solid #ccc;
    margin-bottom: 1rem;
  }

  nav {
    text-align: center;
    margin: 1rem 0;
  }

  nav a {
    padding: 0.5rem;
  }
</style>
```

이제 http://localhost:5173/ 에 접속해보면 다음과 같은 화면이 뜬다.
![](https://velog.velcdn.com/images/yeryoong/post/be4f97cb-b559-4c1f-9658-dbce4f6a1960/image.png)

#### Vue 컴포넌트 업데이트
이제 작은 컴포넌트부터 업데이트하고 이후에는 뷰 컴포넌트를 계속 업데이트할것이다. 
> AuthorLink: 포스트와 포스트 목록에서 사용되는 주어진 작성자의 페이지로 연결 제공
PostList: 전체 포스트, 작성자 및 태그별 포스트에서 사용되는 블로그 포스트 목록 렌더링
AllPostsView: 모든 포스트 목록 추가
PostsByTagView: 주어진 태그와 연관된 포스트 목록 추가
AuthorView: 작성자에 대한 정보와 그들이 작성한 포스트 목록 표시
PostView: 주어진 포스트에 대한 메타데이터와 콘텐츠 표시
App: 네비게이션에서 일부 링크를 제거

AuthorLink 컴포넌트는 GraphQL API에 대한 작가 데이터와 일치하는 구조의 author prop을 받아들여야한다. Props는 컴포넌트에서 사용 할 수 있는 사용자 정의 속성이다. 이 컴포넌트는 작가의 성과 이름이 제공된 경우 해당 정보를 보여주고, 그렇제 않으면 작가의 이름을 보여주어야한다. 

front_end/src/componenets/AuthorLink.vue 파일의 기존 내용을 아래의 코드로 대체한다.
```
<script setup>
import { computed } from "vue";
import { RouterLink } from "vue-router";

const props = defineProps(["author"]);
const firstName = props.author.user.firstName;
const lastName = props.author.user.lastName;
const displayName = computed(() => {
  if (firstName && lastName) {
    return `${firstName} ${lastName}`;
  } else {
    return `${props.author.user.username}`;
  }
});
</script>

<template>
  <RouterLink
    :to="{ name: 'author', params: { username: author.user.username } }"
  >
    {{ displayName }}
  </RouterLink>
</template>

```
이 컴포넌트는 GraphQL과 직접적으로 작동하지 않고 다른 컴포넌트에서 제공하는 작가 객체를 전달받는다. 작가 객체와 작업하기 위해 5번째 줄에서 defineProps()로 prop을 정의한다. defineProps() 함수는 변수에 저장 할 수 있는 객체를 반환한다. 

#### 게시물 목록 준비
props를 문자열 목록으로 정의 할 수 있지만, object 구문을 사용하여 제공된 props의 정보를 더 세밀하게 설정할 수 있다. PostList 컴포넌트에서는 object 구문을 사용하여 더 복잡한 props를 선언한다. PostList 컴포넌트는 GraphQL API에서 게시물에 관한 데이터와 일치하는 구조를 가진 posts prop을 받아들인다. 컴포넌트는 몇가지 기능을 표시해야한다. 

- 게시물의 제목 (게시물 페이지로의 링크)
showAuthor가 true로 설정된 경우 AuthorLink를 사용하여 해당 게시물의 작가에 대한 링크 src/components/ 디렉토리의 PostList.vue 파일을 열고 다음과 같이 파일을 업데이트한다.
```
<script setup>
import AuthorLink from "./AuthorLink.vue";
import { RouterLink } from "vue-router";

const props = defineProps({
  posts: {
    type: Array,
    required: true,
  },
  showAuthor: {
    type: Boolean,
    required: false,
    default: true,
  },
});
</script>

<template>
  <ol class="post-list">
    <li class="post" v-for="post in posts" :key="post.slug">
      <RouterLink :to="{ name: 'post', params: { slug: post.slug } }">
        {{ post.title }}
      </RouterLink>
      <span v-if="showAuthor"> by <AuthorLink :author="post.author" /> </span>
    </li>
  </ol>
</template>

```
PostList가 정리되었으니 AllPostView를 업데이트 할 차례이다. 이 컴포넌트킄 블로그의 모든 포스트 목록을 표시해야한다. src/views/ 디렉토리의 AllPosts.vue 컴포넌트를 열고 아래의 내용으로 업데이트한다.
```
<script setup>
import PostList from "../components/PostList.vue";

const { result, loading, error } = {
  error: { message: "아직 GraphQL API에 연결되지 않았습니다." },
};
</script>

<template>
  <h2>최근 게시물</h2>
  <div v-if="loading">로딩 중...</div>
  <div v-else-if="error" class="warn"></div>
  <PostList v-else :posts="result.allPosts" />
</template>

<style scoped>
  h2 {
    color: blue;
  }
</style>

```

이제 주어진 태그에 대한 게시물을 표시하기위해 src/views/ 경로의 PostsByTagView.vue 파일을 업데이트한다.
```
<script setup>
import PostList from "../components/PostList.vue";
import { useRoute } from "vue-router";

const route = useRoute();
const tag = route.params.tag;
const { result, loading, error } = {
  error: { message: "아직 GraphQL API에 연결되지 않았습니다." },
};
</script>

<template>
  <div v-if="loading">로딩 중...</div>
  <div v-else-if="error" class="warn">{{ error.message }}</div>
  <section v-else :set="tagPosts = result.postsByTag">
    <h2>"{{ tag }}" 태그가 지정된 게시물</h2>
    <PostList v-if="tagPosts.length > 0" :posts="tagPosts" />
    <p v-else>이 태그에 대한 게시물을 찾을 수 없습니다</p>
  </section>
</template>

<style scoped>
  h2 {
    color: orange;
  }
</style>

```

글쓴이에 대한 정보를 표시하기위해 작가의 프로필 페이지로 이동하게하는 AuthorView 컴포넌트에는 다음 정보가 표시되어야한다. 
- 글쓴이의 사용자 이름과 제목
- 글쓴이의 풀네임과 부제목
- 글쓴이의 웹사이트 링크
- 글쓴이의 전기
- 글쓴이가 작성한 포스트 목록, showAuthor를 false로 설정

그리고 src/views/ 디렉토리의 AuthorView.vue 파일을 다음과 같이 변경한다.
```
<script setup>
import PostList from "../components/PostList.vue";

const { result, loading, error } = {
  error: { message: "아직 GraphQL API에 연결되지 않았습니다." },
};
</script>

<template>
  <div v-if="loading">로딩 중...</div>
  <div v-else-if="error">{{ error.message }}</div>
  <section v-else :set="author = result.authorByUsername">
    <h2>{{ author.user.username }}</h2>
    <template v-if="author.user.firstName && author.user.lastName">
      <h3>{{ author.user.firstName }} {{ author.user.lastName }}</h3>
    </template>
    <p v-if="author.bio">
      {{ author.bio }}
      <template v-if="author.website">
        {{ author.user.username }}에 대해 자세히 알아보세요: 
        <a :href="author.website">웹사이트</a>.
      </template>
    </p>
    <h3>게시물</h3>
    <PostList
      v-if="author.postSet"
      :posts="author.postSet"
      :showAuthor="false"
    />
    <p v-else>작가가 아직 포스트를 작성하지 않았습니다.</p>
  </section>
</template>

<style scoped>
  h2 {
    color: red;
  }
</style>

```
PostView 컴포넌트는 포스트의 모든 정보를 표시하는 역할을 한다. 이 컴포넌트는 다음과 같은 포스트 정보를 표시해야한다. 
- 제목과 부제목
- 글쓴이
- 게시일
- 메타 설명
- 본문 
- 연관된 태그 목록
src/view/ 디렉토리의 PostView.vue 파일을 다음과 같이 업데이트하자.
```
<script setup>
import AuthorLink from "../components/AuthorLink.vue";

const dateFormatter = new Intl.DateTimeFormat("en-US", { dateStyle: "full" });
const displayableDate = (date) => dateFormatter.format(new Date(date));
const { result, loading, error } = {
  error: { message: "No connection to the GraphQL API yet." },
};
</script>

<template>
  <div v-if="loading">Loading...</div>
  <div v-else-if="error" class="warn">{{ error.message }}</div>
  <section v-else :set="post = result.postBySlug">
    <h2>{{ post.title }}</h2>
    <h3>{{ post.subtitle }}</h3>
    <p>{{ post.metaDescription }}</p>
    <aside>
      Published on {{ displayableDate(post.publishDate) }}<br />
      Written by <AuthorLink :author="post.author" />
      <h4>Tags</h4>
      <ul>
        <li v-for="tag in post.tags" :key="tag.name">
          <RouterLink :to="{ name: 'tag', params: { tag: tag.name } }">
            {{ tag.name }}
          </RouterLink>
        </li>
      </ul>
    </aside>
    <article>{{ post.body }}</article>
  </section>
</template>

<!-- ... -->

```

다음번엔 App 컴포넌트와 라우트를 다루어볼것이다!

