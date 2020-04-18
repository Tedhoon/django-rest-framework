# Mixins

이전의 APIView에서 구현했던 ListView, DetailView 등 모두 비슷한 논리의 구조이기 때문에 
내부의 로직은 비슷하게 반복이 될 것

`불필요한 코드의 답습을 줄이고자` 필요한 기능들을 미리 만들어두고 상속 시킴

또한 APIView에서는 일일이 serializer를 호출시켰지만 Mixin에서는 내부적으로 호출하여 생략가능

> 코드가 깔-꼼 해짐 :sparkles:

<br>

## 사용방식

generics 안의 GenericAPIView를 사용하여 target queryset과 serializer_class를 정의해주고
mixins 안의 각 method들을 사용하여 기능을 적절히 뽑아다 쓰는 방식

<br>

## Mixin 활용

```python

# LISTVIEW
from snippets.models import Snippet
from snippets.serializers import SnippetSerializer
from rest_framework import mixins
from rest_framework import generics

class SnippetList(mixins.ListModelMixin,
                  mixins.CreateModelMixin,
                  generics.GenericAPIView):

    # GenericAPIView에서의 class변수들 2가지
    # GenericAPIView는 APIView를 상속받음
    queryset = Snippet.objects.all()
    serializer_class = SnippetSerializer


    # get 호출 시
    def get(self, request, *args, **kwargs):
        return self.list(request, *args, **kwargs)
        # ListModelMixin의 list() 메서드 리턴

    # post 호출 시
    def post(self, request, *args, **kwargs):
        return self.create(request, *args, **kwargs)
        # CreateModelMixin의 create() 메서드 리턴

        
        
# DETAILVIEW

class SnippetDetail(mixins.RetrieveModelMixin,
                    mixins.UpdateModelMixin,
                    mixins.DestroyModelMixin,
                    generics.GenericAPIView):
    queryset = Snippet.objects.all()
    serializer_class = SnippetSerializer

    def get(self, request, *args, **kwargs):
        return self.retrieve(request, *args, **kwargs)
        # list method대신 RetrieveModelMixin안의 retrieve() 메서드 리턴
        # 단순히 특정 pk값의 객체를 가져오는 함수

    def put(self, request, *args, **kwargs):
        return self.update(request, *args, **kwargs)
        # UpdateModelMixin의 update() 메서드 리턴

    def delete(self, request, *args, **kwargs):
        return self.destroy(request, *args, **kwargs)
        # DestroyModelMixin의 destroy() 메서드 리턴
```


<br>


## mixins를 포함한 generics의 APIVIEW

물론 이 또한 반복되는 답습이 있기 때문에 합쳐놓은 CBV가 존재한다.
> ViewSet과 지향하는 의미는 비슷하지만 더 원초적인 의미에서 합쳐놓은 친구


```python
from snippets.models import Snippet
from snippets.serializers import SnippetSerializer
from rest_framework import generics

# ListCreateAPIView 
class SnippetList(generics.ListCreateAPIView):
    queryset = Snippet.objects.all()
    serializer_class = SnippetSerializer

# RetrieveUpdateDestroyAPIView
# 직관적이다. retrieve, update, destroy를 지원한다.
class SnippetDetail(generics.RetrieveUpdateDestroyAPIView):
    queryset = Snippet.objects.all()
    serializer_class = SnippetSerializer
```