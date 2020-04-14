## Serializer(직렬화)


JSON통신
기본적으로 json은 어떤 object를 str로 변환하여
전송하고
전송받은 값을
다시 받을 때 다시 object형식으로 변환하여 쓴다.

```python
ex_dict = {
    title : 'title',
    content : 'content'
}

json_dict = json.dumps(ex_dict) #json으로 변환(str)

tmp_dict = json.loads(json_dict) #json(str)에서 dic으로 변환
```


직렬화

drf의 Serializer/ModelSerializer

유효성 검사 해줌


serializer가 필요한 이유

API서버에 단순히 데이터를 갖다줘[GET]방식이라면 
JSON형식(str변환)으로 갖다 주면 되지만

데이터를 가공 및 처리해달라는 요청[POST]라면

