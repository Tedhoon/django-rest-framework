# Permission

authetication기반으로 request.user와 request.auth를 이용하여 판단한다.

설정 방법은 역시 2가지가 있음
- settings.py에서 디펄트값 전역설정
- view, viewset마다 설정
```python
# CBV 예시
from rest_framework.permissions import IsAuthenticatedOrReadOnly ...

# APIView를 가지고 있어야함!
class MyViewSet(viewsets.ModelViewSet):
    ...
    permission_classes = [IsAuthenticatedOrReadOnly]
    ...

# fbv 예시
@api_view(['GET'])
@permission_classes([IsAuthenticatedOrReadOnly])
def example_api_view(reqeust, format=None):
    ...
```

## Permission class
drf에서 제공되는 permission class들
[소스코드 염탐](https://github.com/encode/django-rest-framework/blob/master/rest_framework/permissions.py) 👈👈👈
- AllowAny
    - 인증된 요청, 비인증 요청 모두 허용
    - `Default 값`

- IsAuthenticated
    - 인증된 요청에 대해서만 View호출 허용

- IsAdminUser
    - `is_staff == True`에서 허용

- IsAuthenticatedOrReadOnly
    - 비인증 요청은 읽기만 허용
    - 다른말로 비인증 요청은 '안전한 http method만 허용'(get, head, options 정도)





## 개발시 tip
> drf 페이지에서 로그인폼 만들어주기
```python
# urls.py

from rest_framework import urls

urlpatterns = [
    ...
    path('api-auth'/, include('rest_framework.urls')),
]

# 참고로 BasicAuthentication에서는 안돌아갑니당
```

## request.user
Permission이 적용되면 request에 user가 담김
