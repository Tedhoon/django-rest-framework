## httpie

`CLI로 request, response를(http호출) 테스트 해볼 수 있는 Test용 라이브러리`

### Install

```bash
pip install --upgrade httpie
```

### 형식
```bash
http [flags] [method] URL [item[item]]
```

> 인자사용 방식
- POST, PUT..etc 방식 => =
- GET 방식 => ==

```bash
# Json형식)
http --json POST 대상주소 GET인자 = 값 POST인자 == 값

# HTML form 형식
http --form POST 대상주소 GET인자 = 값 POST인자 == 값
```

### 예시
```bash
http --form post "http~" title="title" body="body"
```


### Http Response code 

> http 응답상태코드로 request에 대한 response를 개괄적으로 알아 볼 수 있습니당

> 갠적으로 중요하다고 생각 한 것에만 `강조`하였음

> API 서버를 만들 때 응답코드도 고려하여야 하므로 알고있어야 한다고 생각!


- <strong>100 Continue(정보응답)</strong><br>
현재까지의 상태가 괜찮으며 클라이언트가 계속해서 요청을 하거나 이미 요청을 완료한 경우에는 무시해도 되는 것을 알려줌
사실 100번대 응답은 잘 쓰이지 않음😅

    - 101 Switching Protocol<br>
    이 코드는 클라이언트가 보낸 Upgrade 요청 헤더에 대한 응답에 들어가며 서버에서 프로토콜을 변경할 것임을 알려줍니다.

    - `102` Processing (WebDAV)<br>
    이 코드는 서버가 요청을 수신하였으며 이를 처리하고 있지만, 아직 제대로 된 응답을 알려줄 수 없음을 알려줍니다.
    
    - 103 Early Hints<br>
    이 상태 코드는 주로 Link 헤더와 함께 사용되어 서버가 응답을 준비하는 동안 사용자 에이전트가(user agent) 사전 로딩(preloading)을 시작할 수 있도록 한다.


- <strong>200 OK(성공응답)</strong><br>
성공의 의미는 HTTP 메소드에 따라 달라짐

    |메소드|의미|
    |---|---|
    |GET|리소스를 불러와서 메시지 바디에 전송되었습니다.|
    |HEAD|개체 해더가 메시지 바디에 있습니다.|
    |PUT 또는 POST|수행 결과에 대한 리소스가 메시지 바디에 전송되었습니다.|
    |TRACE|메시지 바디는 서버에서 수신한 요청 메시지를 포함하고 있습니다.|
    <br>

    - `201` Created<br>
요청이 성공적이었으며 그 결과로 새로운 리소스가 생성되었습니다. 이 응답은 일반적으로 POST 요청 또는 일부 PUT 요청 이후에 따라옵니다.

    - 202 Accepted<br>
요청을 수신하였지만 그에 응하여 행동할 수 없습니다. 이 응답은 요청 처리에 대한 결과를 이후에 HTTP로 비동기 응답을 보내는 것에 대해서 명확하게 명시하지 않습니다. 이것은 다른 프로세스에서 처리 또는 서버가 요청을 다루고 있거나 배치 프로세스를 하고 있는 경우를 위해 만들어졌습니다.

    - 203 Non-Authoritative Information<br>
이 응답 코드는 돌려받은 메타 정보 세트가 오리진 서버의 것과 일치하지 않지만 로컬이나 서드 파티 복사본에서 모아졌음을 의미합니다. 이러한 조건에서는 이 응답이 아니라     - 200 OK 응답을 반드시 우선됩니다.

    - 204 No Content<br>
요청에 대해서 보내줄 수 있는 콘텐츠가 없지만, 헤더는 의미있을 수 있습니다. 사용자-에이전트는 리소스가 캐시된 헤더를 새로운 것으로 업데이트 할 수 있습니다.

    - 205 Reset Content<br>
이 응답 코드는 요청을 완수한 이후에 사용자 에이전트에게 이 요청을 보낸 문서 뷰를 리셋하라고 알려줍니다.

    - 206 Partial Content<br>
이 응답 코드는 클라이언트에서 복수의 스트림을 분할 다운로드를 하고자 범위 헤더를 전송했기 때문에 사용됩니다.

    - 207 Multi-Status (WebDAV)<br>
멀티-상태 응답은 여러 리소스가 여러 상태 코드인 상황이 적절한 경우에 해당되는 정보를 전달합니다.

    - 208 Multi-Status (WebDAV)<br>
DAV에서 사용됩니다: propstat(property와 status의 합성어) 응답 속성으로 동일 컬렉션으로 바인드된 복수의 내부 멤버를 반복적으로 열거하는 것을 피하기 위해 사용됩니다.

    - 226 IM Used (HTTP Delta encoding)<br>
서버가 GET 요청에 대한 리소스의 의무를 다 했고, 그리고 응답이 하나 또는 그 이상의 인스턴스 조작이 현재 인스턴스에 적용이 되었음을 알려줍니다.


<!-- - 300 f -->