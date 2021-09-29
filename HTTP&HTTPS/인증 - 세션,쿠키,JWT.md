## 인증 - 세션, 쿠키, JWT

서버는 편의성을 위해 인증된 사용자 정보를 저장한다.   
서버가 사용자 인증 정보를 저장하는 방법인 쿠키, 세션, JWT에 대해서 알아보겠다.   
목차는 다음과 같다.     
- 쿠키
- 세션
- JWT
- JWT refresh token

### 쿠키
쿠키는 클라이언트측에 저장되는 정보다.     
사용자 인증정보를 클라이언트측에 쿠키로 저장 해놓으면, 클라이언트는 다음 HTTP요청마다 쿠키를 함께 보내게된다. 이 방법은 아래와 같은 단점이 있어 중요한 보안 트랜잭션에서는 사용하면 안된다.     

- 쿠키정보가 탈취당하면 사용자 개인정보가 그대로 노출될수있다.      
(이것을 막기위해 HTTP only태그와 https 프로토콜일때만 쿠키를 보내도록 secure태그를 사용하지만, 그래도 안심할수는 없다.)
- 클라이언트가 쿠키를 임의로 수정할경우, 인증 트랜잭션에 예측할수없는 결함이 생길수있다.

### 세션
세션은 서버측에 저장되는 정보다. 일반적으로, 사용자의 중요정보는 모두 서버의 세션에 저장하고, 클라이언트에는 해당 인증 세션의 ID값만 전달한다. 클라이언트는 매 요청마다 자신의 인증 ID값을 같이 보내고 서버는 해당  ID에 맞는 인증 세션이 있는지 확인하는 방식으로 동작한다.
사용자 인증정보가 서버측에 저장되고, ID값이 탈취당한다고 해도 ID값 그 자체로는 사용자의 개인정보를 알수 없어서 보안측면에서 쿠키보다 더 유리하다. 하지만, 사용자가 많고, 저장해야할 인증정보가 많다면, 서버 비용을 많이 사용해야 한다는 문제점이 있다.

### JWT
JWT는 JSON Web Token의 줄임말로 말 그대로, JSON 형태로 이루어진 웹 토큰이다.     
JWT는 사용자의 인증정보를 Token형태로 만들어서, HTTP로 주고받는다.     
JWT의 구조를 우선 알아보자.     
```JSON
{
 "alg": "HS256",
 "typ": "JWT"
}
{
 "sub": "1234567890",
 "name": "John Doe",
 "iat": 1516239022
}
HMACSHA256(
  base64UrlEncode(header) + "." +
  base64UrlEncode(payload), 256bit-secretKey
)
```
위 JSON을 인코딩하면 아래와 같은 결과가 나온다.
```JWT
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c
```
(출처 : jwt.io)     
"Header"     
위 코드에는 alg와 typ이 있다. 암호 알고리즘과 토큰타입을 나타내는 부분이다.     
alg의 경우 어떤 암호 알고리즘을 통해 암호화할지 선택한다. HS256과 HS348등이 있다.     
     
"Body(payload)"     
사용자의 데이터를 담는 부분이다. 위 코드에는 sub, name, iat가 들어있다.     
인증에 관한 특별한 의미는 없다.     

"Verify Signature"     
사용자의 서명을 담는 부분이다. 이 구간에 있는 secret-key값을 기준으로 header와 body부분을 인코딩한다.   
  
- 인코딩된 JWT토큰은 다음과 같은 형태가 된다.
HMACSHA256(base64UrlEncode(header).base64UrlEncode(body),secret-key)     

위 처럼 JWT는 인코딩에 사용된 secret-key를 알지 못하는 이상, 절대 디코딩 할 수 없는게 특징이다.

### JWT refresh token
JWT에는 사용자의 정보가 그대로 담겨있기 때문에, 토큰 탈취에 항상 주의해야한다. 또한, 한번 제공된 JWT토큰을 계속해서 사용 할 경우, 실시간으로 변경되는 데이터가 반영되지 않는 문제점이 발생할 수 있다.     
이러한 문제점을 해결하기 위해서, JWT토큰과 새로운 JWT를 발급받는 JWT refresh token을 함께 발급하며, JWT토큰에는 expires주기를 짧게 설정하여 토큰이 만료될때마다 JWT refresh token을 사용하여 새롭게 발급받는 방식으로 사용한다.     
     
JWT refresh token은 세션, DB와 같이 클라이언트의 접속을 차단할 수 있는 곳에 저장하여 사용한다. 
