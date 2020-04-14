## REST (API)란😎
> Restful API가 풀네임!
- `REST(REpresentational State Transfer)란`, "웹에 존재하는 모든 자원(이미지, 동영상, DB 자원)에 고유한 URI를 부여해 활용"하는 것으로, 자원을 정의하고 자원에 대한 주소를 지정하는 방법론을 의미한다고 한다. 따라서 RESTful API는 REST 특징을 지키면서 API를 제공하는 것을 의미한다.

- 한 줄로 `자원을 이름으로 구분해서 상태를 전송하는 방법`

- REST architecture를 따르는 API를 Restful하다, 또는 Rest API라고 한다!

<br>

### REST architecture

1. Server - Client
2. STATELESS : 클라이언트의 이전 상태를 기록하지 않는 상태 (ex : http)
3. Cache
4. Uniform Interface
5. Layered System
6. Code-On-Demand

<br>

### 왜써야할까❔ 
1. 백엔드와 프론트엔드의 완전한 분리
    - Http method와 Uri를 적절히 사용하여 백엔드를 표현해줄 수 있기 때문에 프론트엔드와의 협업 시 해당사항만 제공하면 된다. 
    > 쉽게말해서 진짜 백엔드 api만 보내놓고 놀 수 있음

2. 정보(API)의 송수신의 자유도 급격하게 상승
    - Rest API 백엔드 서버를 만들어두면 서버에서 가공하는 API를 수많은 서비스에서 활용이 가능해진다.
    - 즉 코드의 재활용성이 현저하게 늘어남
    
3. 수많은 네트워크들의 독립적 발전에 도움
    - REST architecture(설계방법)의 표준을 통해 개선 및 변동이 있어도 하위호환을 깨트리지 않을 수 있음
    - REST 표준 설계조건이 있음(약속) 

<br>

### 디자인 원칙

1. URL 디자인 원칙 : 자원에 대한 처리를 주소에 나타내지않는다(HTTP method는 내부적으로 처리하도록), 어떤 자원인지 주소에 명확하게 나타냄
    - ```bash
        # url에 행위를 표현하지 말라는 말임!, 밑의 예처럼 뒤죽박죽 될 가능성 있음 
        POST /users/2/delete
        ```
2. 소문자를 사용한다 : 대소문자에 차이를 두기 때문에 소문자로 통일하자

3. 밑줄을 사용하지 않고 `하이푼(-)`을 사용하자!

4. 자원에 대한 행위는 HTTP메소드로 표현!
> 모르면 👇👇👇

<br>

### HTTP method
|Method|role|
|---|---|
|GET|해당 리소스를 조회|
|POST|해당 리소스를 생성|
|PUT|해당 리소스 대체|
|DELETE|해당 리소스 삭제|
|PATCH|리소스의 `일부`를 수정|

- PUT vs PATCH?
    > 보통 설명을 둘 다 "수정"으로 표현하곤 하는데 확실히 짚고 넘어갑시당
```bash
# 기본 json 형식 api를 {id:1, title:'hello'}라고 가정하자

PUT을 이용하여 이 API를 수정하고자 할 때는 리소스 전체를 주어야한다!
왜냐하면 일부수정이 아닌 `대체`하는 것이기 때문

PUT /body/1
{ id: 1, title: 'helloworld' }

# 만약 id를 적어주지 않고 요청을 보낼 시 id값은 Null이 채워지는 대참사
```
```bash
# 이와 다르게 PATCH요청을 보낼 시 

PATCH /body/1
{ title 'helloworld' }

# 일부 리소스만 대체하기 때문에 깰꼼!
```


<br>

### backend 설계 예시 with Http method
|URL|METHOD|Description|Params|Return|
|---|---|---|---|---|
|/api/posts|`GET`|전체 글을 조회합니다.||[...{Post}]|
|/api/posts|`POST`|새로운 글을 작성합니다.|{title:'',content:''}|{Post}|
|/api/posts/[id]|`GET`|특정 id의 글을 조회합니다.||{Post}|
|/api/posts/[id]|`DELETE`|특정 id의 글을 삭제합니다.|||

