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

# 포트 853 분석 보고서

## 1. 개요
포트 **853**은 **DNS over TLS (DoT, DNS over Transport Layer Security)** 프로토콜에서 사용되는 표준 포트입니다. DoT는 DNS 질의 및 응답을 암호화하여 **보안성과 개인정보 보호를 강화**하는 역할을 합니다.

본 보고서에서는 포트 853의 개요, 특징, 보안성, 장점 및 단점을 분석합니다.

---

## 2. 포트 853 및 DNS over TLS(DoT)의 개념

### 2.1 DNS over TLS (DoT)란?
기존 DNS(포트 53 기반)는 평문(Plaintext)으로 작동하여 **중간자 공격(Man-in-the-Middle, MITM)**에 취약했습니다.  
DNS over TLS는 **TLS(Transport Layer Security) 암호화 계층을 추가**하여 이러한 보안 취약점을 해결합니다.

### 2.2 포트 853의 역할
- **DoT의 표준 포트**로 사용됨 (RFC 7858에 정의됨).
- 클라이언트와 DNS 리졸버 간의 **TLS 세션을 통한 암호화된 DNS 요청** 처리.
- 일반적인 **포트 53 DNS 요청보다 보안성이 높음**.

---

## 3. 포트 853의 주요 특징

| 항목 | 설명 |
|------|------|
| **프로토콜** | TCP 기반 |
| **보안 방식** | TLS 암호화 적용 |
| **RFC 표준** | RFC 7858 (DNS over TLS) |
| **기본 사용 사례** | DNS 질의/응답 보안 강화 |
| **대체 프로토콜** | DNS over HTTPS (DoH, 포트 443) |

---

## 4. 포트 853의 장점 및 단점

### 4.1 장점
✅ **보안 강화**  
- DNS 요청이 **암호화**되어 MITM 공격 및 스누핑 방지.  

✅ **프라이버시 보호**  
- ISP나 해커가 사용자의 DNS 요청을 감청할 수 없음.  

✅ **무결성 보장**  
- TLS를 통해 **데이터 변조 방지** 기능 제공.  

✅ **표준화된 보안 프로토콜**  
- **RFC 7858**에 의해 공식 표준으로 정의됨.  

---

### 4.2 단점
❌ **오버헤드 증가**  
- TLS 핸드셰이크 및 암호화 처리로 인해 일반 DNS(포트 53)보다 성능 저하 가능.  

❌ **방화벽 차단 가능성**  
- 일부 네트워크 방화벽에서 포트 853을 차단하여 DoT 사용 불가능.  

❌ **UDP 미지원**  
- 일반 DNS(포트 53)는 UDP 기반이지만, DoT는 TCP만 사용하여 네트워크 부하 증가 가능.  

---

## 5. 포트 853과 관련된 보안 고려사항

### 5.1 DoT 보안 최적화 방안
- 최신 TLS 버전(예: TLS 1.3) 사용.
- **공개된 인증서(공개키) 검증**을 통한 신뢰성 확보.
- DoT 서버와의 **엄격한 검증 및 핀닝(Pinning) 적용**.

### 5.2 포트 차단 우회 방법
- DoT를 차단하는 네트워크에서는 **DNS over HTTPS(DoH, 포트 443)** 사용 가능.
- VPN 또는 프록시를 통해 우회 가능.

---

## 6. 포트 853 vs. 포트 53 vs. 포트 443 비교

| 포트 번호 | 프로토콜 | 암호화 | 주요 용도 |
|----------|---------|--------|----------|
| **53**  | DNS     | ❌ (평문) | 전통적인 DNS 질의/응답 |
| **853** | DoT     | ✅ (TLS 암호화) | 보안 강화된 DNS over TLS |
| **443** | DoH     | ✅ (HTTPS 기반) | DNS over HTTPS |

- **DoT(포트 853)과 DoH(포트 443)** 모두 암호화를 지원하지만, DoH는 HTTPS 트래픽과 섞여 방화벽에서 차단하기 어려움.  
- **DoT는 순수한 DNS 트래픽을 보호**하는 데 초점이 맞춰져 있음.  

---

## 7. 결론
포트 853은 **DNS 보안 강화를 위해 설계된 포트**로, DNS 요청을 TLS 암호화를 통해 보호하는 역할을 합니다. 기존 DNS(포트 53)의 보안 취약점을 보완하지만, 성능 오버헤드 및 방화벽 차단 문제를 고려해야 합니다.

만약 DoT가 차단된 환경이라면, **포트 443을 사용하는 DoH(DNS over HTTPS) 대안**을 고려할 수 있습니다.

---

## 8. 참고 자료
- [RFC 7858 - Specification for DNS over TLS](https://datatracker.ietf.org/doc/html/rfc7858)
- [Cloudflare - DNS over TLS](https://developers.cloudflare.com/1.1.1.1/encryption/dns-over-tls/)
- [Google Public DNS over TLS](https://developers.google.com/speed/public-dns/docs/dns-over-tls)

