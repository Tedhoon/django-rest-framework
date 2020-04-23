# Authentication

인증 요청에는 `request.user`(유저), `request.auth`(권한)이 전달되며 인증 실패시 request.user = AnonymousUser, request.auth = None으로 setting된다.

위의 default값은 settings에서 물론 바꿔줄 수 있음

<br>


## APIView에서 Default로 세팅되어 있는 Authentication
```python
REST_FRAMEWORK = {
    'DEFAULT_AUTHENTICATION_CLASSES': [
        'rest_framework.authentication.BasicAuthentication',
        'rest_framework.authentication.SessionAuthentication',
    ]
}
```

<br>

## view, viewset에 직접 설정하기
```python
from rest_framework.authentication import SessionAuthentication, BasicAuthentication
from rest_framework.permissions import IsAuthenticated
from rest_framework.response import Response
from rest_framework.views import APIView

class ExampleView(APIView):
    authentication_classes = [SessionAuthentication, BasicAuthentication] 👈👈👈
    permission_classes = [IsAuthenticated]

    def get(self, request, format=None):
        content = {
            'user': unicode(request.user),  # `django.contrib.auth.User` instance.
            'auth': unicode(request.auth),  # None
        }
        return Response(content)



@api_view(['GET'])
@authentication_classes([SessionAuthentication, BasicAuthentication]) 👈👈👈
@permission_classes([IsAuthenticated])
def example_view(request, format=None):
    content = {
        'user': unicode(request.user),  # `django.contrib.auth.User` instance.
        'auth': unicode(request.auth),  # None
    }
    return Response(content)
```


<br>


## 인증 실패 시

- HTTP 401 Unauthorized
    - `WWW-Authenticate` header 포함
- HTTP 403 Permission Denied
    - `WWW-Authenticate` header 미포함
    - authentication은 성공했지만, permission 실패 시에도 raise

<br>
## 종류

- BasicAuthentication
    - HTTP 자체 기본인증 방식
    - HTTP 제어 헤더로 넘긴 ID, PW를 base64로 encoding
    - base64를 사용하기 때문에 굉장히 보안에 취약함(테스트에 적합)
    - SSL과 같이 쓰면 그나마 괜찮!

- TokenAuthentication
    - 유일한 token(key값)을 발급
    - token 헤더
    - SSL을 당연히 권장

- SessionAuthentication
    - auth.login()이 수행될 때마다 발급되는 session정보를 참조해서 인증

- RemoteUserAuthentication
    - User 정보가 다른 서비스에서 관리 될 때 사용

- Custom authentication
    - subclass로 BaseAuthentication를 만들고 `.authenticate(self, request)` 메서드를 오버라이딩 시키면 됨
    - 요 메서드가 인증성공 시 (user, auth)라는 두 튜플을 리턴하고, 실패시 None을 리턴 
<br>

## httpie 요청방법
```
http --auth id:password --form POST url content
```


<br>

## :bulb: 추후 업데이트 예정!
Django-rest-knox library 등..
뭔가 빈약한 설명이었기에 추후 계속 업데이트 하겠음!