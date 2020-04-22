# Pagination

- DRF 페이지네이션의 의의

> 단일 request만으로 처리하기에 많은 queryset들을 페이지네이션을 통해 요청리소스를 분할한다.

> 단일 reuqest를 여러 request로 나누어주는 느낌

<br>

## Pagination 종류
- `PageNumberPagination`
- LimitOffsetPagination
- CursorPagination
- `Customized Pagination`(커스텀)

‼ 보통 PageNumberPagination을 디펄트로 적용, 추가적인 설정이 필요한 부분만 customizing 시킴

<br>

## 기본 setting 이해하기
[rest_framework/generics.py](https://github.com/encode/django-rest-framework/blob/master/rest_framework/generics.py)의 GenericAPIView는 pagination을 지원하는데 소스코드를 들여다보면,
```python
class GenericAPIView(views.APIView):
    ...
    pagination_class = api_settings.DEFAULT_PAGINATION_CLASS
```
[rest_framework/settings.py](https://github.com/encode/django-rest-framework/blob/master/rest_framework/settings.py)의 Default값을 참조하는 것을 알 수 있음.

또한,

[rest_framework/pagination.py](https://github.com/encode/django-rest-framework/blob/master/rest_framework/pagination.py)에 들어가보면 구현되어있는 pagination class들의 page_size는 api_settings.Default값으로 설정되어있음.

따라서 [rest_framework/settings.py](https://github.com/encode/django-rest-framework/blob/master/rest_framework/settings.py)의 내용을 우리의 django project settings에 overwriting 시켜줌으로써 Pagination class의 Default값과 Page size의 Default값을 설정하여 쓸 수 있습니당.

👇👇👇

### Overwriting 하는 방법
> PagiNumberPagination을 자주 사용할 것이기 때문에 이를 기준으로 작성했습니다!

```python
# 우리의 settings.py
REST_FRAMEWORK = {
    'DEFAULT_PAGINATION_CLASS': 'rest_framework.pagination.PageNumberPagination',
    'PAGE_SIZE': 100
}
```
> 당연히 다른 settings 값들도 위와 같이 key값을 참조하여 쓸 수 있겠습니다! 🤓

<br>


## CustomPagination
> 사실 그냥 class overwriting이다!

예시)
```python
...
from rest_framework import pagination

# pagination.py 하나 만들어서 분리하면 좋겠져?
class CumtomPagination(pagination.PageNumberPagination):
    page_size = 5


class MyViewSet(viewsets.ModelViewSet):
    queryset = Model.objects.all().order_by('-id')
    serializer_class = MySerializer
    pagination_class = CustomPagination
```
🙌 요 상황은 전역의 pagination관련 설정이 되어있을 때 살짝 커스텀하는 경우!

만약 전역설정을 안했을 시 pagination은 모두 None으로 설정되어있기 때문에 기본 CBV들에 바로 pagination 설정을 해줘도 되겠습니당.



<br>

## 페이지네이션 국룰! 🐱‍🐉
> 페이지네이션 수행 전에는 반드시 정!렬! 해줄 것!(당연한 말 괜히 강조)

