# Media 다루기

### Url을 이용해서 filename 받기
```python
# serializers.py
...
class MediaSerializer(serializers.ModelSerializer):
    file = serializers.FileField(use_url=True)
    image = serializers.ImageField(use_url=True)
```
### file upload 받기
```python
from rest_framework.parser import MultiPartParser, FromParser ...
from rest_framework.response import Response
from rest_framework import status

class ExampleViewSet(viesets.ModelViewSet):
    ...
    parser_classes = (MultiPartParser, FromParser)
    # 다양한 형식을 미디어 파일들을 받을수 있게 도와줌 


    # create()오버라이딩
    def post(self, request,*arg, **kwargs):
        serializer = MediaSerializer(data=request.data)
	    if serializer.is_valid():
            serializer.save()
            return Response(serializer.data, status=HTTP_201_CREATED)
        else:
            return Response(serializer.error, status=HTTP_400_BAD_REQUEST)
```