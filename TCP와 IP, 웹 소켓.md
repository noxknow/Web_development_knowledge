## IP (Internet Protocol)

- 전 세계 수억대의 컴퓨터가 인터넷을 하기 위해서는 서로의 정체를 알 수 있도록 부여한 특별한 주소, 이 주소를 IP주소라고 한다.
- IP주소는 4바이트로 이루어진 주소번호를 사용한다. ex)192.168.100.100
- 숫자로 된 인터넷 주소는 식별하기 어렵기에 인간이 식별하기 쉬운 도메인 이름으로 변환시켜주는 Domain Name서비스를 사용한다. ex) www.naver.com
- Internet Protocol의 줄임말로, 인터넷에서 컴퓨터의 위치를 찾아서 데이터를 전송하기 위해 지켜야 할 규약이다.

## TCP (Transmission Control Protocol)

- 두 호스트가 교환하는 데이터와 승인메세지의 형식을 정의
- 서버와 클라이언트간의 데이터를 신뢰성있게 전달하기 위해 만들어진 규약
- TCP는 데이터 패킷에 일련의 번호를 부여하여 데이터 손실을 찾아서 교정하고, 순서를 재조합하여 클라이언트에게 전달할 수 있게 해준다. 이는 왜 중요하냐면, 데이터는 네트워크 선로를 통해 전달되는 과정에서 손실되거나 순서가 뒤바뀌어서 전달될수 있기 때문이다.
- 복잡해서 신뢰성이 높다.
- 인터넷은 패킷을 이용해서 정보를 전달하므로, 이 패킷을 전달하는 규약도 필요하다.
- 패킷: 데이터를 일정한 크기로 자른 단위로 인터넷에서 정보를 전달하는 단위이다. 나누어진 패킷이 순서대로 도착한다는 보장이 없으므로 규칙이 필요하다.
- TCP는 전달받은 패킷을 재조립하고, 패킷에 손상이 있거나 손실된 패킷이 있다면 재전송을 요청하는 패킷을 전송하여 재전송받는다.

***즉 IP는 데이터가 찾아갈 주소, TCP는 찾아온 데이터가 형식에 맞게 왔는지 확인하는 필터.***

![image](https://github.com/noxknow/Web_development_knowledge/assets/122594223/08f9c886-6bfb-4170-a74b-ba953c777a67)

cf) [https://velog.io/@wjddnjswjd12/TCPIP-net-Socket-모듈-등등-공부-기록](https://velog.io/@wjddnjswjd12/TCPIP-net-Socket-%EB%AA%A8%EB%93%88-%EB%93%B1%EB%93%B1-%EA%B3%B5%EB%B6%80-%EA%B8%B0%EB%A1%9D)

클라이언트로 부터 특정 주소로 요청이 들어오면 DNS상에서 IP주소를 받아온다 **⇒** HTTP 계층에서 HTTP메시지를 작성한다 **⇒** TCP계층에서 HTTP 메시지를 패킷으로 분해한다 **⇒** IP계층에서 전송 위치를 확인 **⇒** 네트워크를 통하여 전송한다 **⇒** 그 이후는 역순으로 진행하여 처리된다

## **소켓 통신을 위한 기본 모듈: net**

```jsx
const net=require('net');
```

- net.Server : 소켓서버
- net.Socket : 소켓

`server 메서드 :`

- server.listen(); : 가장 중요한 클라이언트 접속 대기
- server.close() : 추가 접속을 받지 않는다
- server.getConnections(callback) : 연결 갯수
- server.address() : 서버주소

`server 이벤트 **:**`

- listening : 포트 바인딩, 접속 가능한 상태 이벤트. server.listen메소드가 호출 되었을 경우
- connection : 클라이언트 접속 이벤트
- close : 서버 닫기(연결된 소켓이 없을 때만 발생)
- error : 에러

`net.Socket 이벤트 :`

- connect : 원격 소켓 연결 이벤트
- data : 읽을 수 있는 데이터 도착
- end : 원격 호스트의 소켓 종료
- timeout : 제한 시간 지남
- error : 에러

`net.Socket 메서드 :`

- connect(options[,connectListener]) : 연결
- write(data[,encoding][,callback] : 데이터 쓰기
- end([data][,encoding]) : 연결 종료 신호 보내기
- setKeepAlive([enable][,initialDelay]) : 연결 유지
- remoteAddress, remotePort : 원격 호스트 주소와 포트

## Web-socket

서버와 데이터를 주고 받는 방법에는 Http 요청이 있다. 이때 지속적으로 값을 받아야 하는 경우 어떻게 해야 할까?? **ex) 실시간 채팅, 실시간 게임**

이때 해결책으로는 2가지가 있다.

1. Server-sent Event (Http 통신을 종료안하고 계속 유지)
    - 단점은 server가 일방적으로 지속적인 데이터 수신만 한다.
2. Web-socket
    - Web-socket의 경우 이런 단점을 해결할 수 있다. 양방향 통신이 가능하기 때문에 자주 사용한다.
