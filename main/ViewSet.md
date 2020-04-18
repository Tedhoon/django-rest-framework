# ViewSet

mixin과 generic APIView를 상속받음

[소스코드](https://github.com/encode/django-rest-framework/blob/master/rest_framework/viewsets.py)


## ReadOnlyModelViewSet 
특정 레코드들을 읽을 수 있게 도와주는 ViewSet

```python
class ReadOnlyModelViewSet(mixins.RetrieveModelMixin,
                           mixins.ListModelMixin,
                           GenericViewSet):
    """
    A viewset that provides default `list()` and `retrieve()` actions.
    """
    pass
```