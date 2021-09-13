## HTTP 다이제스트 인증

[HTTP 기본인증](https://https://github.com/devxb/devDictionary/blob/main/HTTP&HTTPS/HTTP%20%EA%B8%B0%EB%B3%B8%EC%9D%B8%EC%A6%9D.md) 의 문제점을 보완환 인증 프로토콜이 다이제스트 인증이다.        
그러나, 인증의 범위가 Authenticate, Authorization영역에 국한된다는점, 재전송 공격에 노출될수있다는점, 보안의 성능이 구현중심적인점 등... 여러 문제점이 있어서 현재(2021)는 거의 사용되지않고있으며, 완벽한 보안 HTTP 트랜잭션을 위해선, HTTPS를 사용해야한다.       
그럼에도 다이제스트 인증의 방식은 보안 트랜잭션을 구현하려는 이들에게는 여전히 유용하기 때문에, 다이제스트 인증의 과정에 대해서 간략히라도 알고 넘어가야 할것같다는 생각에 정리하게되었다.

---
### 다이제스트 인증의 특징

다이제스트인증은 다음과 같은 특징을 제공하여, HTTP인증의 문제점을 보완했다.
- 비밀번호를 네트워크를 통해 평문으로 보내지않는다.
- 구현하기에 따라, HTTP메시지 위 변조를 막는다.
- 인증체결을 가로채서 재현하지 못한다.

---
### 다이제스트 인증의 개요

다이제스트 인증의 개요는 다음과 같다.

1. 클라이언트가 서버에 보안영역에 대한 접근(자원)을 요구한다.

```HTTP/1.1
GET /realm/private/helloword.jpg HTTP/1.1
```

2. 서버는 클라이언트의 보안영역에 대한 권한을 확인한다.     

이때, 서버는 올바른 클라이언트인지 확인하기위해 아래의 값을 WWW-Authenticate 헤더에 담아 함께 보낸다.

- nonce라고 하는 서버에서 생성한 랜덤값을 클라이언트에 전달한다. 클라이언트는 자신의 아이디, 비밀번호, nonce, 영역을 요약하여 서버에 다시 보내야할 의무가있다.
- 클라이언트가 요청한 파일의 영역(realm)을 보낸다.
- digest인증에 사용할수있는 알고리즘을 보낸다.
- qop 보안 등급을 보낸다.

```HTTP/1.1
HTTP/1.1 401 Authorization Required

WWW-Authenticate: Digest realm="private" nonce="asdfjnajbqwbrhasdf" qop="auth"
```

3. 클라이언트는 서버가 보낸 WWW-Authenticate의 정보와 유저 private정보를 토대로 요약을 생성하여 서버로 보낸다.   

이때, 클라이언트는 WWW-Authenticate에 들어있는 모든정보를 Authorization헤더에 포함하여 보내야하며, 추가적으로 서버와 클라이언트가 nonce를 주고받은 횟수인 nc, 서버의 인증에 사용할 cnonce(안만들어도 된다.), 인증에 사용할 response를 만들어야한다.   
response는 다음과 같이 만들어진다.   
     
HA1 = MD5(아이디:realm:비밀번호)     
HA2 = MD5(method:uri)     
response = MD5(HA1:nonce:nc:qop:HA2)      
     
최종적으로 만들어지는 HTTP메시지는 다음과 같다.

``` HTTP/1.1
GET /realm/private/helloworld.jpg HTTP/1.1

Authorization: Digest,
realm="private",
nonce="asdfjnajbqwbrhasdf",
qop="auth",
uri="/realm/private/helloword.jpg",
nc="00000001",
response="fasjdfbasdhvfjqwebkfjbasdfkasdf"
```
   
4. 서버는 클라이언트가 보낸 Authorization의 무결성을 확인한다.
- response와 자신이 보낸 nonce값이 일치하다면 리소스접근을 허락한다. 
이때, 클라이언트가 보낸 인증정보를 함께 담아 보내며, 다음 요청에 사용될 nextnonce도 같이 보낸다.

```HTTP/1.1
HTTP/1.1 200 OK

Authorization-info: qop="auth",
nc="00000001",
nextnonce="bhjbq23jrhqwhjfasfhasdf"
```
