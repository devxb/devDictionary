## HTTP 기본인증

목차
- HTTP 기본인증
- HTTP 기본인증의 결함
---
웹 애플리케이션은 사용자가 특정 리소스에 접근하려할때, 비밀번호와같은 개인정보를 요구함으로써 인증을 시도한다.

클라이언트와 서버가 인증을 하는 일련의 방법에대해서 알아보자.

1. 클라이언트는 서버에 리소스를 요청한다.
```HTTP
GET /helloworld/hello.jpg HTTP/1.0
```

2. 해당 리소스 영역에 대해 인증이 필요하다면, 서버는 인증을 요청한다.
```HTTP
HTTP/1.0 401 Authorization Required

WWW-Authenticate: Basic realm="private"
```
WWW-Authenticate 헤더의 필드의미는 다음과 같다.
Basic : 비밀번호를 암호화할 알고리즘을 의미한다.
- Basic의 경우 base-64 인코딩을 의미하며, 이 외에도 bearer, digest등이 있다.

realm : 클라이언트가 요청한 리소스 영역이다.

3. 클라이언트는 서버의 인증요구에 응답한다. 
```HTTP
GET /helloworld/hello.jpg HTTP/1.0

Authorization: Basic AjnafjsdnASdjnsf132
```
Authorization 헤더의 필드를 채우는 과정은 다음과 같다.
- 사용자에게 ID와 비밀번호를 입력받는다. 
- 사용자 ID와 비밀번호를 콜론으로 잇는다. ID:PASSWORD
- base-64로 인코딩한다. BASE64ENC(ID:PASSWORD) -> asdfhbhasdbfasdf
- 서버에게 인가 요청을 보낸다.

4. 서버는 클라이언트의 요청에 응답한다.
```HTTP
HTTP/1.0 200 OK

Content-type: image/jpg
Content-Length: ...
...

...
```
---
HTTP기본인증의 결함

HTTP기본인증은 비밀번호가 의도치않게 다른 사용자에게 노출되는것은 막아주지만, 악의적인 공격으로부터 개인정보를 보호해주지는 못한다.
- base-64인코딩은 인코딩 절차를 반대로 수행해서 어렵지 않게 디코딩할수있다. 즉, base-64방식으로 인코딩된 사용자 ID, PASSWORD는 악의적인 공격자에게 사실상 비밀번호 그대로 보내는 것과 다름이 없다.

- 악의적인 공격자가 중간에 비밀번호를 캡처해 원 서버에 그대로 보내더라도 기본인증은 이러한 공격을 막기위한 어떠한 일도 하지 않는다.

- 사용자가 가짜서버에 연결되어있는데도 서버에 개인정보를 보낸다면, 서버는 이 정보를 저장해뒀다가, 나중에 사용할수도 있다.
