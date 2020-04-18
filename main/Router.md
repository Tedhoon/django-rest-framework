# Router

> 한가지 함수 또는 class에 여러가지 http요청에 대응하는 각각의 기능을 가지고있기 때문에 url등록 시 묶어서(binding) 등록해줘야합니다 ✔

> 좀더 명확하게는 우리가 url_patterns에 써왔던 path함수의 두 번째인자에 묶어서 넣어주면 됨

## as_view() 파헤치기

as_view() 는 딕셔너리형태의 인자를 받음

```python
# as_view의 형태
as_view({'http_method':'처리할 함수'})
```

```python
# 적용
TempViewSet.as_view({
    'get' : 'retrieve',
    'put' : 'update',
    'patch' : 'partial_update'
    ...
})

        👇👇👇

# 딕셔너리가 담긴 as_view를 객체에 넣어줍니다.
router_path = TempViewSet.as_view({
    'get' : 'retrieve',
    'put' : 'update',
    'patch' : 'partial_update'
    ...
})

        👇👇👇

path의 두번 째 인자에 넣어줍니다.
```

```python

from rest_framework.routers import DefaultRouter

router = DefaultRouter()
router.register('url_prefix',)

```



## 명시적으로 Routing해주기 (Router 사용X)

```python
# urls.py

from snippets.views import SnippetViewSet, UserViewSet, api_root
from rest_framework import renderers

# 객체에 담아서 일일이 binding해주는 과정
snippet_list = SnippetViewSet.as_view({
    'get': 'list',
    'post': 'create'
})
snippet_detail = SnippetViewSet.as_view({
    'get': 'retrieve',
    'put': 'update',
    'patch': 'partial_update',
    'delete': 'destroy'
})
snippet_highlight = SnippetViewSet.as_view({
    'get': 'highlight'
}, renderer_classes=[renderers.StaticHTMLRenderer])
user_list = UserViewSet.as_view({
    'get': 'list'
})
user_detail = UserViewSet.as_view({
    'get': 'retrieve'
})


# 바인딩 된 객체를 넣어줍니당.
urlpatterns = format_suffix_patterns([
    path('', api_root), # 기본화면
    path('snippets/', snippet_list, name='snippet-list'),
    path('snippets/<int:pk>/', snippet_detail, name='snippet-detail'),
    path('snippets/<int:pk>/highlight/', snippet_highlight, name='snippet-highlight'),
    path('users/', user_list, name='user-list'),
    path('users/<int:pk>/', user_detail, name='user-detail')
])

```

## Router를 사용한 라우팅

🚫 ViewSet 만 사용가능(한 것 같다)

rest_framwork에 내장되어있는 router중 DefaultRouter는 이러한 관례적인 라우팅을 미리 묶어놓았기 때문에 쉽게 등록가능
```python


```