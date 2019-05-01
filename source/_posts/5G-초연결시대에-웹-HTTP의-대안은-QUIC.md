---
title: 5G 초연결시대에 웹 HTTP의 대안은 QUIC?
date: 2019-04-30 01:04:34
categories:
  - WEB
tags:
 - QUIC
 - HTTP/3
 
---

## QUIC 소개 
QUIC (Quick UDP Internet Connection)
구글에서 QUIC라는 프로토콜 설계하였고 2013년 6월 공개 했다.

국제 인터넷 표준화 기구 (IETF)에 QUIC 프로토콜 표준화를 제안하였다.

Google-QUIC은 오로지 HTTP만 전송했으나 IETF QUIC 워킹 그룹을 통해 IETF-QUIC 은 TLS 1.3의 암호화 보안을 적용해 개선하였다.

HTTP-over-QUIC은 2018년 11월에 HTTP/3로 개명하였고 QUIC 버전 1의 최종 명세 2019년 7월 발표 예정이다.

## QUIC 프로토콜에 대해
기능 설명에 앞서서 QUIC를 이해하기 위해서는 TCP와 UDP에 대한 이해가 필요하다.

#### TCP란?
전송 제어 프로토콜
(Transmission Control Protocol, TCP)

![출처: https://hpbn.co/transport-layer-security-tls/#tls-handshake](https://user-images.githubusercontent.com/28129081/56914109-35453700-6aee-11e9-82a7-6f2b0ea32418.png)

TCP는 요청마다 3-way Handshake 와 SSL과 같은 암호화가 추가 된다면 TLS handshake 로 인한 RTT(Round Trip Time) 증가한다.
또한 HOL(head of line) 블로킹 문제 등이 있다.

##### HOL(Head Of Line) 블로킹
![출처: https://hpbn.co/building-blocks-of-tcp/#head-of-line-blocking](https://user-images.githubusercontent.com/28129081/56914303-b8ff2380-6aee-11e9-98f0-f0c45d256a0d.png)
 위 그림을 보면 P1을 받아오지 못해 재요청을 하여 데이터를 받아오는것을 볼 수 있다. 데이터를 다시 요청함으로써 신뢰성을 높이지만 반대로 통신이 지연되는 것을 알 수 있다.
이는 TCP 통신의 장점이자 단점으로 보이는 문제라 볼수 있는데 이를 Head Of Line Blocking이라 한다.

#### UDP 란?
사용자 데이터그램 프로토콜
(User Datagram Protocol, UDP)

![UDP](https://user-images.githubusercontent.com/28129081/56914420-067b9080-6aef-11e9-8752-c69a8028374d.png)
TCP처럼 3way-handshake가 없으며 RTT(Round Trip Time) 적어 속도가 빠른 반면에 데이터의 손실 가능성이 있고 그로 인해 신뢰성이 떨어진다고 볼 수 있다.

때문에 유튜브 영상 또는 게임에서 캐릭터 이동 등 데이터가 손실이 있더라도 괜찮은(?) 상황에서 많이 사용된다고 한다.

P2P통신에서도 UDP를 많이 쓴다고 하는데 중개서버를 활용한 UDP 홀펀칭이라는 기법을 사용한다고 한다.

시간이 된다면 추후 UDP 홀펀칭 기법도 스터디하여 소개할 수 있도록 하겠다.

여기까지 간단하게 TCP와 UDP에 대한 이해는 넘어가고 본격적으로 QUIC에 대해 알아보자.

### QUIC 프로토콜
![출처: https://http3-explained.haxx.se/en/the-protocol.html](https://user-images.githubusercontent.com/28129081/56917902-a4735900-6af7-11e9-8617-3dc7547eda7a.JPG)

위 그림과 같이 QUIC은 UDP 기반 위에서 구현한 전송 프로토콜이다. UDP는 데이터 신뢰성을 보장하지 않지만 <span style="color:red">**QUIC 계층추가**</span>를 통해 TCP와 같은 <span style="color:red">**신뢰성을 제공**</span>한다

HTTP2의 <span style="color:red">**Multiplexing**</span> 과 같이 하나의 연결로 다수의 병렬 스트림으로 데이터를 동시 전송하며 UDP 기반이지만 그와 다르게 TCP와 같은 <span style="color:red">**순서에 맞는 전송처리**</span>를 한다.

HTTP2에서는 여러 스트림중에 하나의 스트림이 패킷을 잃을 경우 복구가 되지 않았지만 QUIC은 다른 스트림이 전송 중에 잃은 <span style="color:red">**스트림의 패킷을 재전송**</span>을 하여 완료가 가능하다고 한다.

0-RTT와 1-RTT설정을 지원하여 기존의 TCP와는 다른 빠른 핸드쉐이크를 지원한다. TLS 1.3 전송 보안을 지원하는데 핸드쉐이크시 더 적은 RTT를 위해 사용한다.
TLS 1.3은 RTT를 개선하였고 보안을 강화하기 위해서 TLS 1.2에서 지원하던 보안에 취약했던 기능들을 삭제를 했다고 한다.

HTTP/2에서 **HPACK**은 순차적인 스트림 전달에 의존하기 때문에 QUIC에 맞게 스트림을 순서 없이 전달 가능하게 수정하였는데 이를 <span style="color:red">**QPACK**</span>이라고 한다.

최종적으로 TCP와 TCP+TLS 그리고 QUIC을 비교하면 아래그림과 같다.
![출처: https://blog.chromium.org/2015/04/a-quic-update-on-googles-experimental.html](https://user-images.githubusercontent.com/28129081/56918649-8a3a7a80-6af9-11e9-8b3b-f52d33ad229f.png)
이렇게 HTTP3는 UDP기반을 통해 RTT를 0에 가깝게 줄여 속도향상을 가져왔다. 그리고 QUIC+TLS1.3 계층을 통해 TCP의 장점인 데이터의 신뢰성까지 보완했다고 볼 수 있다.
곧 오는 2019년 7월에 예정에 맞추어 QUIC 버전 1 이 정식으로 공개되고 나면 다시 한번 웹 시장은 5G 시대에 발 맞추어 기업들의 속도 경쟁에 요동칠 것으로 보인다. 


조금 더 들어가 다음으로는 HTTP/2를 소개하도록 하겠다.
## HTTP/2 소개
구글에서 2009년 중반에 SPDY 발표 하였고 HTTP/2 프로토콜의 출발점으로 SPDY 사양이 채택되어 2015년 5월에 RFC 7540(HTTP/2) 및 RFC 7541(HPACK) 발행되었다.

#### 바이너리 프레이밍 계층
![출처: https://hpbn.co/http2/#request-and-response-multiplexing](https://user-images.githubusercontent.com/28129081/56918877-22386400-6afa-11e9-97a9-4de7c79cdb9d.PNG)
위 그림의 오른쪽을 보면 이전 버전인 HTTP/1.1 에서는 헤더와 메시지의 그림이 하나로 묶여있던것을 볼수 있는데 HTTP/2 에서는 **헤더 프레임**과 **데이터 프레임**으로 나뉜것을 볼수 있다. 이들은 바이너리 형식으로 인코딩되어 처리가 된다.

프레임이라는 단어 및 그림을 계속 연상하면서 가보자.

#### 멀티플렉싱
![출처: https://hpbn.co/http2/#request-and-response-multiplexing](https://user-images.githubusercontent.com/28129081/56919415-7e4fb800-6afb-11e9-9f09-d7fa1de4a5fe.PNG)

위 그림은 스트림들이 어떻게 처리가 되는지를 보여준다. 스트림 단위로 병렬로 처리되고 있음을 볼 수 있다. 
이는 기존 TCP의 문제인 HOL(Head Of Line) Blocking 문제로 인한 시간지연을 제거했다고 볼 수 있다.

#### 서버푸시
![출처: https://hpbn.co/http2/#request-and-response-multiplexing](https://user-images.githubusercontent.com/28129081/56924418-d1c80300-6b07-11e9-9b5f-4718594c5c90.PNG)
위 그림은 오른쪽 클라이언트가 왼쪽 서버에 stream1에 해당하는 frame1을 전송했다. 그리고 클라이언트는 계속해서 stream1의 frame2를 보내는 중이었다.
그런데 하나의 요청으로 서버는 추가적인 모든 데이터를 클라이언트에 푸시를 할 수가 있다.

이를 HTTP/2의 강력한 기능 중 하나인 **서버푸시**라 한다.

#### 헤더압축
![출처: https://hpbn.co/http2/#request-and-response-multiplexing](https://user-images.githubusercontent.com/28129081/56924693-84986100-6b08-11e9-95d1-f1d1116486dc.PNG)
이번 그림은 헤더압축을 나타내는 그림이다. request1과 request 2를 잘 보면 헤더정보가 url path만 다르고 같은 값들로 채워져 있다. 
request1에 해당하는 Sream1을 전달한 후에 request 2 의 헤더정보를 담고 있는 Sream2의 중복되는 헤더 정보를 제외하고 다른Stream으로 서버에 요청을 할 것이다.
**중복된 헤더정보를 제거**함으로써 페이지 로드 시간이 상당히 감소했다고 한다.

지금까지 HTTP/3 과 HTTP/2 에 대한 개념과 기능에 대해 알아보았다.
정리하자면 아래와 같다.
* 첫째, HTTP/3는 <span style="color:red">**스트림의 패킷 재전송**</span>을 통한 복구 완료가 가능하다는 점.
* 둘째, HPACK 의 순차적인 스트림 전달을 순서 없이 전달 가능하게 하여 **병렬처리 성능을 개선**하여 나온 <span style="color:red">**QPACK**</span>.
* 마지막으로 셋째, UDP기반위의 <span style="color:red">**QUIC 프로토콜**</span>을 통해 암호화 보안(TLS 1.3)을 적용했음에도 이론상 <span style="color:red">**0-RTT**</span>라는 것이 HTTP/3의 장점이라고 말하고 있다.

병렬처리 성능 및 속도, 보안 향상, 데이터의 신뢰성 등. 어느것 하나 빠짐없어 보이는 장점들을 보며 곧 다가올 2019년 7월 발표 예정일이 기대가 된다.


<br/>
<div style="font-size:14px;">
참고자료
[HTTP2]
https://developers.google.com/web/fundamentals/performance/http2/
https://hpbn.co/http2/#request-and-response-multiplexing
[HTTP/3 explained]
저자 Daniel Stenberg - curl 개발자 (https://http3-explained.haxx.se/en/)
</div>