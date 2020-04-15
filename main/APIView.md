# APIView
> class 기반의 APIVIEW

> 만들어진 API기반 CBV중 가장 기본이 되는 친구다.

## Source Code
> 들어가서 어떻게 만들어졌는지 확인해보자

[요기](https://github.com/encode/django-rest-framework/blob/8cba4f87ca8e785d1a8c022a7a8ea9649e049c11/rest_framework/views.py#L115) 👈👈👈
```python
class APIView(View):

    ...

    @classmethod
    def as_view(cls, **initkwargs):
        ...      
        # Note: session based authentication is explicitly CSRF validated,
        # all other authentication is CSRF exempt.
        return csrf_exempt(view)
    # as_view()메서드로 crsf_exempt를 자동으로 처리해주는 걸 알 수 있음

    ...

    # 당차게 들어갔지만 뭔가 건질건 안보였다. 흑😂
    # 추후 계속 들여다 보다가 추가하겠음
```

## APIVIEW 활용
APIVIEW를 상속받은 후에 원하는 http 요청 및 응답 method를 오버라이딩 시켜주면 됩니다.

- 가장 가벼운 친구이기 때문에 커스텀이 자유롭다는 장점!

- func based view보다는 method구분에 용이하네용

```python

from snippets.models import Snippet
from snippets.serializers import SnippetSerializer
from django.http import Http404
from rest_framework.views import APIView
from rest_framework.response import Response
from rest_framework import status

# ListView
class SnippetList(APIView):
    """
    List all snippets, or create a new snippet.
    """
    # 단순히 원하는 method들을 써주면됩니당.
    # 본 예시는 get,post http 응답을 넣어줬네여.

    def get(self, request, format=None):
        snippets = Snippet.objects.all()
        serializer = SnippetSerializer(snippets, many=True)
        return Response(serializer.data)

    def post(self, request, format=None):
        serializer = SnippetSerializer(data=request.data)
        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data, status=status.HTTP_201_CREATED)
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)



# DetailView
class SnippetDetail(APIView):
    """
    Retrieve, update or delete a snippet instance.
    """
    # get_object_or_404와 같음
    def get_object(self, pk):
        try:
            return Snippet.objects.get(pk=pk)
        except Snippet.DoesNotExist:
            raise Http404

    def get(self, request, pk, format=None):
        snippet = self.get_object(pk)
        serializer = SnippetSerializer(snippet)
        return Response(serializer.data)

    def put(self, request, pk, format=None):
        snippet = self.get_object(pk)
        serializer = SnippetSerializer(snippet, data=request.data)
        if serializer.is_valid():
        # serializer에 기본 유효성 검사가 내장되어있음
            serializer.save()
            return Response(serializer.data)
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)

    def delete(self, request, pk, format=None):
        snippet = self.get_object(pk)
        snippet.delete()
        return Response(status=status.HTTP_204_NO_CONTENT)
```