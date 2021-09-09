# HTTP란?
(참고: HTTP 완벽가이드 웹은 어떻게 동작하는가)

이 글에서는 HTTP의 특징과 클라이언트와 서버가 요청,응답하는 방식에 대해 적겠다.

1. HTTP의 간단한 구조
2. HTTP의 특징
3. 서버와 클라이언트가 HTTP로 통신하는 과정

---
### HTTP의 구조

HTTP는 시작줄, 헤더, 본문으로 구성된다.

 - 시작줄 
 -- 시작줄에는 사용 프로토콜의 정보와 버전, 서버 상태메시지, 요청의 경우 메서드(GET, POST, PUT, DELETE...)와, 서버의 주소가 있다.

- 헤더
-- HTTP헤더는 애플리케이션에 따라 자유롭게 수정될수있다.
-- 헤더에 올수있는내용은 다음과 같은것들이 있다.
-- Accept : 클라이언트가 받아들일 수 있는 파일을 나타낸다. MIME 형식으로 작성해야한다. ex) image/gif
-- Content-type : 본문(엔터티 본문)의 파일타입을 나타낸다. ex) text/html
-- Date : 서버가 응답을 만들어 낸 시각이다.
-- Connection : 서버와 클라이언트의 통신 상태를 결정한다.
-- Cache-Control : 캐시를 컨트롤 하기 위해 사용한다.
-- 이 외에도 많은 헤더들이 있다.

- 본문(엔터티 본문)
-- 본문은 HTTP프로토콜이 전달하고자 하는 내용을 담고있으며, 메서드에 따라 없을수도 있다.

최종적으로 HTTP프로토콜은 다음과 같은 형태로 만들어진다.
```HTTP
HTTP/1.1 200 OK

Content-type: text/html
Host: www.github.com
Accept: text/html
Connection: keep-alive

<!DOCTYPE html>
<html>
	<head>
		<title> Hello World! </title>
	</head>
</html>
```

---
### HTTP의 특징

- HTTP는 TCP 위에서 돌아가는 프로토콜이다.
-- TCP/IP프로노콜은 서로를 확인하기 위해 일련의 hand-shake 과정을 거친다. 이 과정에서 많은 시간손실이 발생하고, 이를 해결하기위해 keep-alive와 파이프라인 커넥션이 고안되었다.

- HTTP는 비연결성, 무상태성을 갖고있다.
-- 클라이언트와 서버는 일련의 트랜잭션을 마친후 연결 해제된다. 
-- 하지만, 현대의 애플리케이션은 사용자의 상태를 유지하고, 그 정보를 토대로 더 나은 경험을 제공하려고한다. 이를 위해 고안된것이 session과 cookie다.
---
### 서버와 클라이언트가 HTTP로 통신하는 과정

1. 클라이언트가 주소창에 서버의 주소를 입력한다.

2. 클라이언트가 입력한 주소 (github.com)를 프록시 서버, 캐시, 쿠키등에서 도메인과 매칭되는 IP를 찾고 없다면 DNS서버에서 검색한다.

3. 서버의 IP와 TCP Connection을 맺는다.

4.  일련의 트랜잭션을 거친후, TCP/IP프로토콜 연결을 해제한다.
-- 단, Connection이 keep-alive라면, 연결을 살려둘수도있다. + HTTP 1.1 부터는 Connection이 close가 아니라면 연결을 끊지않는것을 중점으로 한다.
