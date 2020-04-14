# What is DRF
<strong>Django 안에서 RESTful API 서버를 쉽게 구축할 수 있도록 돕는 오픈 소스 라이브러리</strong>

### install
```bash
pip install djangorestframework
```

### 기본세팅 

```python
# settings.py

INSTALLED_APP = [
    ...
    'rest_framework',
]
```

### 기본 serializer 설정

```python
# serializer.py
import model
from rest_framework import serializers

class MySerializer(serializers.ModelSerializer):
    class Meta:
        model = # 직렬화 시킬 모델
        field = # 직렬화 시킬 필드
```
- tip : django에서 만들어진 ModelSerializer은 유효성검사도 해줍니당


### 기본 url 라우팅
```python
# urls.py

from rest_framework.router import DefaultRouter
from views.py import MyView


router = DefaultRouter()
router.register('myurl',MyView)

urlpatterns = [
    path('',include(router.urls)),
]
```
