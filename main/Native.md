# Native Code
> 본 챕터에서는 DRF에서 제공하는 CBV를 사용하지 않고 함수형으로 API를 다루는 로직들을 구현해보려고 합니다.

> 또한 커스텀에 유용한 import list들에 대해서도 정리해보겠음 🤗


## Request 와 Response 형식
- Request
    ```python
    request.POST 
    # form data를 다룰 때만 이렇게 받을 수 있음
    # 'POST' method 일때 쓸 수 있음
    request.data
    # 나머지 data 통용
    # 'POST', 'PUT', 'PATCH' method에서 작동
    ```

- Response
    ```python
    from rest_framework.response import Response

    return Response(data=' ~ ' , status= ' ~ ') 
    # 이런 형식으로 보내주면 됩니당
    ```

## Useful import List

- status : http 상태 응답에 사용
    ```python
    from rest_framework import status

    return Response(serializer.data, status=status.HTTP_201_CREATED)
    ```
    [status_code_list](https://github.com/encode/django-rest-framework/blob/master/rest_framework/status.py) 👈 status.요놈!

- @api_view (decorater) : 함수형 api_view로직을 짤 때 필요!
    ```python
    from rest_framework.decorators import api_view

    @api_view(['GET','POST','PUT','DELETE'])
    def my_function_api_view(requset):
        ...
    ```
- csrf_extemp (decorater) : crsf토큰 비활성화
    ```python
    from django.views.decorators.csrf import csrf_exempt

    @csrf_exempt
    def test(request):
    ```
    front단에서 token을 던지지 못하는 상황, 또는 테스트 케이스에서 사용할 법하다.


<br>

## 구현 예시
```python
from rest_framework import status
from rest_framework.decorators import api_view
from rest_framework.response import Response
from snippets.models import Snippet
from snippets.serializers import SnippetSerializer


@api_view(['GET', 'POST'])
def snippet_list(request):
    """
    List all code snippets, or create a new snippet.
    """
    if request.method == 'GET':
        snippets = Snippet.objects.all()
        serializer = SnippetSerializer(snippets, many=True) 
        # 2개 이상 인스턴스를 던져주고 싶을 때 many_True는 필수!
        return Response(serializer.data)

    elif request.method == 'POST':
        serializer = SnippetSerializer(data=request.data)
        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data, status=status.HTTP_201_CREATED)
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)


@api_view(['GET', 'PUT', 'DELETE'])
def snippet_detail(request, pk):
    """
    Retrieve, update or delete a code snippet.
    """
    try:
        snippet = Snippet.objects.get(pk=pk)
    except Snippet.DoesNotExist:
        return Response(status=status.HTTP_404_NOT_FOUND)

    if request.method == 'GET':
        serializer = SnippetSerializer(snippet)
        return Response(serializer.data)

    elif request.method == 'PUT':
        serializer = SnippetSerializer(snippet, data=request.data)
        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data)
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)

    elif request.method == 'DELETE':
        snippet.delete()
        return Response(status=status.HTTP_204_NO_CONTENT)
```
