# Filter & Search

:bulb: 먼저 filter와 search를 확실히 구분해보자!

|filter|search|
|---|---|
|`Request` 걸러서 보내기|`Response` 걸러서 받기|
|내가 작성한 글만 보여줘|전달받은 queryset(response)을 column에 대해 걸러서 받음|

<br>

### 참조용 request :rocket:

self.request : 보낸 request

Self.request.user : 보낸 request의 user

Self.request.GET / self.request.query_param : GET 방식

Self.request.POST : POST 방식

<br>

## Fliter 예제
```python
# 단순히 CBV 메서드 오버라이딩
...
class MyViewSet(viewsets.ModelViewSet):
    queryset = Models.objects.all()
    serializer_class = MySerializer

    def get_queryset(self):
        qs = super().get_queryset() # 기존 쿼리셋가져오기
        qs = qs.filter(...)
        return qs
```

### Setting filter backends
전역으로 filter backend 설정하기! 
```python
REST_FRAMEWORK = {
    'DEFAULT_FILTER_BACKENDS': ['django_filters.rest_framework.DjangoFilterBackend']
}
```

<br>

## Search 예제
```python
...
# 만들어진 SearchFilter 사용해보기!
from rest_framework.filters import SearchFilter

class MyViewSet(viewsets.ModelViewSet):
    queryset = Models.objects.all()
    serializer_class = MySerializer

    # import한 filter_backend 지정
    filter_backends = [SearchFilter] 

    # 어떤 칼럼을 기반으로 검색을 할건지
    search_fields = ('title', ...)
```
### search URL
```
http://www.myurl.com/temp?search=내용
```
### search_fields 활용법
- '^' Starts-with search.
- '=' Exact matches.
- '@' Full-text search. (Postgresql만 지원...갓끼리🐘)
- '$' Regex search.
```python
# ex)
search_fields = ['=username', '=email']
```