# ViewSet

mixin과 generic APIView를 상속받음

장식자를 통해서 CRUD가 아닌 다른 logic들을 구현하기에 용이👏

<br>

## 소스코드

#### 출처는 당연히 [여기](https://github.com/encode/django-rest-framework/blob/master/rest_framework/viewsets.py) 👈👈👈



```python
# viewset.py


# GenericViewSet
class GenericViewSet(ViewSetMixin, generics.GenericAPIView):
    # GenericAPIView를 상속받아서 queryset과 serialize_class를 정의해줄 수 있게했다.
    """
    The GenericViewSet class does not provide any actions by default,
    but does include the base set of generic view behavior, such as
    the `get_object` and `get_queryset` methods.
    """
    pass




## ReadOnlyModelViewSet 
class ReadOnlyModelViewSet(mixins.RetrieveModelMixin,
                           mixins.ListModelMixin,
                           GenericViewSet):
    
    # list(), retrieve() method를 가지고 있음

    pass

```

## VIewSet 활용

```python
from rest_framework import viewsets

# 특정 레코드들을 `읽을 수 있게` 도와주는 ReadOnlyModelViewSet
# ListView와 DetailView의 기능 둘 다 소화
class UserViewSet(viewsets.ReadOnlyModelViewSet):
  
    queryset = User.objects.all()
    serializer_class = UserSerializer




# 모든 기본 기능이 내장되어있는 ModelViewSet

from rest_framework.decorators import action
from rest_framework.response import Response
# custom API작성을 위해 import
class SnippetViewSet(viewsets.ModelViewSet):
    """
    This viewset automatically provides `list`, `create`, `retrieve`,
    `update` and `destroy` actions.

    Additionally we also provide an extra `highlight` action.
    """
    queryset = Snippet.objects.all()
    serializer_class = SnippetSerializer
    permission_classes = [permissions.IsAuthenticatedOrReadOnly,
                          IsOwnerOrReadOnly]
    # 권한 설정도 해줄 수 있음!🙌 (권한 설정 파트는 따로 작성합니당)


    @action(detail=True, renderer_classes=[renderers.StaticHTMLRenderer])
    def highlight(self, request, *args, **kwargs):
        snippet = self.get_object()
        return Response(snippet.highlighted)

    def perform_create(self, serializer):
        serializer.save(owner=self.request.user)
```

<br>


## action decorater

####  method 호출 방식
- GET(default)
- POST 👈 따로 지정해주어야함!

#### renderer_classes

rendering 시킬 Response의 형태(format)를 지정해줄 수 있음.
- [rest_framework/renderers.py](https://github.com/encode/django-rest-framework/blob/master/rest_framework/renderers.py) 👈👈👈 참조하세여!
    - renderers.JSONRenderer (default : 1)
    - renderers.BrowsableAPIRenderer (default : 2)
    - renderers.StaticHTMLRenderer , TemplateHTMLRenderer
    - etc...

- 다양하게 쓰이지는 않고 default값이 주로 쓰임! `(front관여 최소화)`

#### url

url은 default값으로 custom method의 이름으로 지정됨