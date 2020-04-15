# Serializer(직렬화)
- serializer는 DB안의 정보, 즉 쿼리셋 또는 모델 인스턴스들을 json, dic, etc 형태로 직렬화(변환) 시켜주는 역할을 한다.
- 데이터를 직렬화 하여 json으로 던져줄 수 있는 고마운 친구
- json의 통신원리를 보면 필요성을 알 수 있음

<br>

## JSON 통신
기본적으로 json은 data를 str 형태로 변환하여 전송하고, <br>
전송받은 값을 다시 받을 때 str형에서 원래의 object형식으로 변환하여 통신을 함
> 굳이 이유를 따지자면 메모리의 전송용량 등등이 있다고한다.

- 예시 및 파이썬에서의 json 간단 활용법👇

    ```python
    ex_dict = {
        title : 'title',
        content : 'content'
    }

    json_dict = json.dumps(ex_dict) #json으로 변환(str)

    tmp_dict = json.loads(json_dict) #json(str)에서 dic으로 변환
    ```

<br>

## serializer가 필요한 이유
API서버에 단순히 데이터를 갖다줘[GET]방식이라면 JSON형식(str변환)으로 갖다 주면 되지만,<br>
데이터를 가공 및 처리해달라는 요청[POST]이라면 심화적인 변환이 필요하기 때문

<br>

## 간단한 serialization 예시
```python
# model instance들을 xml로 변환
from django.core import serializers
data = serializers.serialize('xml', SomeModel.objects.all(), fields=('name','size'))
```

## django의 ModelSerializer 기능
```python
from rest_framework import serializers

class MySerializer(serializers.ModelSerializer):
    class Meta:
        model = 모델
        fields = 필드들
        read_only_fields = ('field명') 
        # read_only_fields 또는 write_only_fields 지정 가능"
```

