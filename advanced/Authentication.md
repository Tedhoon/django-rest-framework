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
    - django.middleware를 참조하기 때문에 외부서비스에서 사용불가

- RemoteUserAuthentication
    - User 정보가 다른 서비스에서 관리 될 때 사용

- Custom authentication
    - subclass로 BaseAuthentication를 만들고 `.authenticate(self, request)` 메서드를 오버라이딩 시키면 됨
    - 요 메서드가 인증성공 시 (user, auth)라는 두 튜플을 리턴하고, 실패시 None을 리턴 
<br>


## TokenAuthentication
> 가장 중요한 TokenAuthentication!

> Mobile Client에 적합

#### Token을 생성해보자! [참조](https://github.com/encode/django-rest-framework/tree/master/rest_framework/authtoken) 👈
```python
# authtoken/models.py

class Token(models.Model):
    key = models.CharField(_("Key"), max_length=40, primary_key=True)
    user = models.OneToOneField(
        settings.AUTH_USER_MODEL, related_name='auth_token',
        on_delete=models.CASCADE, verbose_name=_("User")
    ) 
    # user column으로 OneToOneField()로 구현되어있음!
    # settings.AUTH_USER_MODEL과 연결되어있음!!

    created = models.DateTimeField(_("Created"), auto_now_add=True)

    class Meta:
        abstract = 'rest_framework.authtoken' not in settings.INSTALLED_APPS
        verbose_name = _("Token")
        verbose_name_plural = _("Tokens")

    def save(self, *args, **kwargs):
        if not self.key:
            self.key = self.generate_key()
        return super().save(*args, **kwargs)

    def generate_key(self):
        return binascii.hexlify(os.urandom(20)).decode()

    def __str__(self):
        return self.key
```

1. installed_app에 추가후 migrate수행
```python
INSTALLED_APPS = [
    ...
    'rest_framework.authtoken',
    ...
]
```
2. Token 발급 ✨
    - [rest_framework/authtoken/views.py](https://github.com/encode/django-rest-framework/blob/master/rest_framework/authtoken/views.py)의 ObtainAuthToken을 이용한 생성 방법
    
    - CLI를 이용한 방법
        ```bash
        $ python manage.py drf_create_token <username>
        $ python manage.py drf_create_token -r <username> # 재발급

        # token 발급시 token이 있으면 기존의 token이 똑같이 발급되므로 새로운 token을 발급받으려면 옵션을 지정해주어야함!
        ```

    - :star: user생성시 signal과 receiver를 이용하여 자동으로 생성해주는 방법
        ```python
        from django.conf import settings
        from django.db.models.signals import post_save # db에 저장(save()메서드) 직후에 특정 동작 수행명령
        from django.dispatch import receiver
        from rest_framework.authtoken.models import Token

        @receiver(post_save, sender=settings.AUTH_USER_MODEL)
        def create_auth_token(sender, instance=None, create=False, **kwargs):
            if created:
                Token.objects.create(user=instance)
        ```


3. Token 획득
    - 토큰을 획득할 수 있는 url지정 후
    ```python
    # urls.py
    from rest_framework.authtoken.views import obtain_auth_token

    urlpatterns += [
        path('api-auth/', include('rest_framework.urls')), 
        path('api-token-auth/', obtain_auth_token), 👈👈👈
        # 참고로 obtain_auth_token = ObtainAuthToken.as_view()
    ]
    ```
    - 해당 url에 POST요청 🤷‍♂️



4. 발급받은 토큰을 API요청에 담기!

    Authorization HTTP header에 token을 같이 담아줍니다! 👇👇👇
    ```shell
    Authorization: Token 9944b09199c62bcf9418ad846dd0e4bbdfc6ee4b
    ```
    json형식은
    ```js
    { 'token' : '9944b09199c62bcf9418ad846dd0e4bbdfc6ee4b' }
    ```

    > 인증 성공시
    - request.user = Django의 User instance
    - request.auth = rest_framework.authtoken.models.BasicToken

<br>

## httpie 요청방법
- basicauthentication
    ```bash
    $ http --auth id:password --form POST url content
    ```
- tokenauthentication
    ```bash
    $ http POST 주소/api-token-auth/ id="" pw=""
    > 발급한 토큰확인
    
    $ http POST 주소/~/ "Authorization: Token 13weqwejkwqe125312~~~" content
    > 토큰을 같이 보내주기
    ```

<br>

## :bulb: 추후 업데이트 예정!
Django-rest-knox library 등..
중요한 친구들은 추후 계속 업데이트 하겠음!