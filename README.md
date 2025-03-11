# 853port
853 port number DNS over TLS

## DNS Domain Name System의 문제점
인터넷상에서의 통신의 대부분은 웹브라우져를 통해서 이루어진다.

보안 향상을 위해 Google, Firefox같은 브라우져 제공업체가 평문통신을하는 HTTP 에서 암호통신을하는 HTTPS로 전환을 권장하고 최근 대부분의 서비스가 HTTPS 대응을 하여 도청, 변조등의 문제를 해결하고있다

그러나 HTTPS통신을 하기전 DNS에 의한 도메인 확인은 암호화 되어있지 않기때문에 접속하려는 호스트명을 도청하여 위조된 response를 받을 가능성이있다.

## 그것을 방지하기위한 수단이 DNS over HTTPS이다.

DNS에 통신하는 내용을 json형식으로 맵핑한 테이터를 HTTPS프로토콜로 통신하는 수단

지금까지 DNS서버의 UDP포트(53)로 이루어지던 DNS 도메인확인을 TCP포트(443)로 HTTPS(HTTP/2 over TLS)통신으로 이루어지는 프로토콜이다.

RFC8484로 표준화 되어있다.

클라이언트가 요청할때에는 GET, POST메소드가 사용된다.

RFC로부터 인용한 request 예제

DNS over HTTPS외에도 DNS를 암호화된 경로로 안전하게 확인하는 방법으로 DNS over TLS가 있다 RFC7858, RFC8310로 표준화 되어있다.

두 통신의 가장 큰 차이점은 사용하는 포트이다. DNS over HTTPS는 일반적인 HTTPS통신과 같은 443포트를 사용하지만 DNS over TLS는 853포트를 사용한다.  
도메인 확인을 위해 DNS over HTTPS를 사용하고 Web사이트에 접속하기위해 HTTPS를 사용하면 도청, 변조등의 문제를 해결할 수 있는가?
       
현재 도청, 변조는 해결이 가능하지만 HTTPS접속시 SNI라는 기능을 이용하여 아래와 같은 행위가 가능하다    
    
클라이언트가 어느 도메인의 웹사이트에 접속하였는지 확인     
클라이언트가 접속하려는 도메인을 파악하여 강제적으로 차단     
HTTPS로 handshake를 할때 어느 도메인에 접속하고싶은지 서버에게 평문으로 전송하는 SNI라는 기능때문이다.    
       
평문을 사용하여 접속하는 이유는 어느IP주소에 2개 이상의  도메인을 할당하고 있고 각각 다른 증명서를 사용 할 경우 handshake시에 서버는 클라이언트에게 어느 도메인에 대한 요구인지 정보를 전달받지 않으면 요청에 맞는 서버증명서를 선택 할 수 없기때문이다     
       
이 문제의 해결책이 TLS1.3의 확장으로 제안된 Encrypted SNI이다.       
      
server_name을 송신할때 서버로부터 공개키를 DNS로취득한 후 암호화하여 서버에 송신한다. 이때 DNS over HTTPS 또는 DNS over TLS를 이용한다.    
[DNS 서버 점검](https://dnscheck.nic.or.kr/guideline/GUIDE_CHK_MAN_01.html)
