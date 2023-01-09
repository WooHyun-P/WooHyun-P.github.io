# 등장배경
## Server-Client Model
## IPC (Inter-Process Communication)
IPC(프로세스 간 통신)란, 프로세스 사이에 서로 데이터를 주고받는 행위 또는 그에 대한 방법이나 경로를 뜻한다. (출처: [위키백과](https://ko.wikipedia.org/wiki/%ED%94%84%EB%A1%9C%EC%84%B8%EC%8A%A4_%EA%B0%84_%ED%86%B5%EC%8B%A0)
IPC 기법에는 PIPE, Message, Shared, Memory Map, Socket, RPC 등 여러가지가 있는데 대표적인 예로 몇가지 살펴보겠다.
### 1. Socket
양쪽 컴퓨터에서 정한 port 를 통해 서로 데이터를 주고받는 방식으로, ISO 7 Layer 구조에서 Transport Layer(L4) 의 TCP 또는 UDP 를 이용하기 위한 수단이다. 
목적지와의 통신이 컴퓨터 내부가 아니라 온라인 범위에서 이뤄지기 때문에 네티워크 간 통신이라고 구분하기도 하지만, 실질적으로는 local 컴퓨터의 Process 와 원격지 컴퓨터의 Process 가 IPC 통신하는 것이다.
소켓은 일련의 통신 과정을 직접 구현하기 때문에 통신 관련 장애 처리는 개발자가 모두 처리해야 한다. 서비스의 규모가 커질수록 데이터의 종류와 데이터의 양이 엄청나게 커질텐데, 이에 따른 data formatting 을 하는 것도 점점 어려워지게 된다.
### 2. RPC
소켓의 어려움을 극복하고자 등장한 기술이 RPC(원격 프로시적 호출)다.
네트워크 통신을 위해 고려해야 하는 작업들을 모두 처리하기 힘드므로 통신이나 call 방식에 신경쓰지 않고 원격지의 자원을 내 자원인 것처럼 사용할 수 있다.
원격 프로시저 호출(RPC)은 별도의 원격 제어를 위한 코딩 없이 다른 주소 공간에 있는 Function 이나 Procedure 를 같은 주소에서 호출하는 것 처럼 실행할 수 있게 하는 Process 간의 **_통신 기술_** 이다. 
즉, RRC 를 이용하면 local 또는 원격지에 있는 Function(또는 procedure) 을 동일한 code 로 호출할 수 있다. (상세한건 아래에서 설명)
IDL(Interface Definition Language)기반으로 다양한 언어를 가진 환경에서 쉽게 확장 가능하며, Inteface 협업에도 용이하다.
* 지원 언어 : C++, Java, Python, Ruby, Node.js, C#, Go, PHP, Objective-C 등
#### Stub (스텁)
Server 와 Client 는 서로 다른 주소공간을 사용하므로, 함수 호출에 사용된 매개변수를 꼭 변환해줘야 한다. 이유는, 메모리 매개변수에 대한 pointer 가 다른 데이터를 가리키게 될 수도 있기 때문이다. Stub 은 이러한 변환을 담당한다.
* Client Stub
  * client stub 은 함수 호출에 사용된 파라미터의 변환 및 함수 실행 후 서버에서 전달된 결과의 반환을 담당한다.
* Server Stub
  * server stub 은 클라이언트가 전달한 매개변수의 역변환 및 함수실행 결과 반환을 담당한다.
#### Stub 을 이용한 RPC 통신 과정
1. IDL(Interface Definition Language)을 사용하여 호출 규약 정의.
    a. 함수명, 인자, 반환값에 대한 데이터형이 정의된 IDL 파일을 rpcgen 으로 컴파일 하면 stub code 가 자동으로 생성된다.
2. stub code 에 명시된 함수는 원시코드의 형태로, 상세 기능은 서버에서 구현하게 된다.
    a. 만들어진 stub code 는 client 및 server 에 함께 빌드된다.
3. client 에서 stub 에 정의된 함수를 사용한다.
4. client stub 은 RPC runtime 을 통해 함수를 호출한다.
5. server 는 수신된 procedure 호출에 대한 처리 후 결과값을 반환한다.
6. client 는 server 의 결과값을 받아서 호출한 함수를 마치 자신의 local 에 있는 것처럼 사용할 수 있다.
### 3. REST
REST 는 HTTP/1.1 기반으로 URI 를 통해 모든 자원을 명시하고 Http Method 를 통해 처리하는 **아키텍처** 이다. REST 는 일종의 스타일이며, 표준이 아니기 때문에 parameter 와 응답 값이 명시적이지 않다. 또한 Http Method 의 형태가 제한적이기 때문에 세부 기능 구현에 제약이 있다. 웹 데이터 전달 format 으로는 XML, JSON 을 많이 사용한다. (아래 IDL 에서 상세히 설명)

---
---

# gRPC
gRPC 는 google 사에서 개발한 오픈소스 RPC(Remote Procedure Call) **프레임워크** 이다.
이전까지는 RPC 기능은 지원하지 않고, 메시지(Json 등)를 Serialize 할 수 있는 프레임워크인 PB(Protocol Buffer)만을 제공해왔는데, PB 기반 Serializer 에 HTTP/2 를 결합하여 RPC 프레임워크를 탄생시켰다.
REST 와 비교했을 때 가장 큰 특징은 **HTTP/2 를 사용한다는 것**과 **PB 로 데이터를 전달**한다는 점이다. 때문에 Proto file 만 배포하면 환경과 프로그램 언어에 구애받지 않고 서로 간의 데이터 통신이 가능하다.

>gRPC 의 구조
>출처: https://grpc.io/docs/what-is-grpc/introduction/
>![](https://velog.velcdn.com/images/dkqnwlnd/post/081725cd-a183-4b10-8e42-000414b7691d/image.png)


## HTTP 2.0 의 사용
### HTTP 1.1
HTTP/1.1 은 기본적으로 client 로 부터 request 가 있어야만 server 가 response 하는 구조로 매 요청마다 connection 을 생성해야 한다.
cookie 등 많은 meta 정보들을 저장하는 무거운 Header 가 request 마다 중복 전달되기 때문에 비효율적이고 속도가 느리다.
### HTTP 2.0
HTTP/2 는 하나의 connection 으로 동시에 여러 개의 메시지를 주고 받으며, Response 는 순서에 상관 없이 stream 으로 주고 받는다. (리소스간 우선순위를 설정해 client 가 먼저 필요한 리소스부터 보내줄 수도 있다.)
또한 Header 를 압축(Header Compression)하여 중복 제거 후 전달하기 때문에 또한 HTTP/1.1 에 비해 훨씬 효율적이다.
필요시 client 의 request 없이도 server 가 resource 를 전달할 수도 있기 때문에 client request 를 최소화 할 수 있다.
![출처:https://chacha95.github.io/2020-06-15-gRPC1/](https://velog.velcdn.com/images/dkqnwlnd/post/a2bf7c6b-c8a4-486f-8789-6f6644177f5f/image.png)

## IDL
### XML
  * XML 은 html 과 같이 tag 기반 언어이며, 미리 정의된 태그가 없어 높은 확장성을 가진 이기종간 전송 표준이었다.
  * 복잡하고 비효율적인 데이터 구조때문에 속도가 느리다는 단점이 있다.
### JSON
  * Key-Value 구조 기반으로 XML 의 데이터 구조에 비해 효율적인 면에서 좋다.
  * 제공되는 자료형의 한계로 parsing 후 추가 형변환이 필요한 경우가 많으며, string 기반이기 때문에 별도의 Serialization 이 필요하다.
### Protocol Buffer(Proto)
  * protocol buffer 는 XML 의 문제점을 개선하기 위해 제안된 IDL 이며 구조화된 데이터를 직렬화하는데 있어서 XML 보다 많은 장점들을 가지고 있다.
  * 대표적으로 XML 에 비해 간편하고 파일 크기도 작고 속도도 빠르며 가독성도 좋다.
    Protocol Buffer 는 google 사에서 개발한 **구조화된 데이터를 직렬화**(Serialization) 하는 기법이다.
#### 직렬화(Serialization)
데이터 표현을 byte 단위로 변환하는 작업을 의미한다. 같은 정보를 저장해도 text 기반의 json 은 **82 byte** 가 소요되는 반면, 직렬화된 Protocol Buffer 는 필드 번호, 필드 유형 등을 1 byte 로 받아서 식별하고, 주어진 length 만큼 읽도록 하기 때문에 **33 byte** 가 소요된다.
```json
{
  "userName": "Martin",
  "favoriteNumber": 1234,
  "interests": ["prigramming", "hacking"]
}
```
```proto
message Person {
  required string user_name = 1;
  optional int64 favorite_number
}
```
![출처:https://martin.kleppmann.com/2012/12/05/schema-evolution-in-avro-protocol-buffers-thrift.html](https://velog.velcdn.com/images/dkqnwlnd/post/f5bc1120-e334-4cfa-acd2-32177d607c5e/image.png)

## stub
gRPC 클라이언트 stub 은 3가지 종류가 있다. 사용하는 stub 에 따라 client 수신 처리 방식이 다르다.
### 사용 가능한 RPC
|               | Unary | Server Streaming | Client Streaming | BiStreaming |
| :-----------: | :---: | :--------------: | :--------------: | :---------: |
| Blocking Stub |   O   |        O         |                  |             |
|  Async Stub   |   O   |        O         |        O         |      O      |
|  Future Stub  |   O   |                  |                  |             |
### Blocking Stub
동기적으로 통신하는 방법으로, server 에서 응답이 올 때까지 대기한다.
### AsyncStub(Stub)
비동기 통신하는 방법으로, server 의 응답과 상관없이 StreamObserver 객체가 대신 받아서 처리한다.
### FutureStub
비동기 통신하는 방법으로, server 의 응답과 상관없이 ListenableFuture 로 래핑된 객체를 반환한다. 이후 server 에서 응답이 오면 ListenableFuture 객체를 통해 전달받은 메시지를 언래핑 할 수 있다.


## Life Cycle
### Unary RPC (단일 호출) 1:1 방식
Client 가 Server 에게 단일 요청을 보내고 단일 응답을 받는 방식이다.
### Client Streaming RPC N:1 방식
Client 가 Server 에게 단일 요청을 보내고 Server 는 Client Stream 형태의 메시지를 응답한다.
Client 는 더 이상 메시지가 없을 때까지 반환된 스트림에서 onNext 를 통해 읽는다.
### Server Streaming RPC 1:N 방식
Client 가 Server 에게 Stream 형태의 메시지를 보내고 Server 는 단일 응답을 전달한다.
### BiStreaming(양방향) N:M 방식
Client 와 Server 모두 Stream 형태의 메시지를 주고 받는 방식이다.
두 stream 은 모두 독립적으로 동작하기 때문에 Client/Server 의 요청/응답 순서에 상관없이 메시지 read, write 가 가능하다.
Server 는 응답을 작성하기 전에 모든 Client 의 메시지를 수신할 때까지 wait 하거나, ping-pong 을 수행할 수 있다.
Server 가 요청을 수신한 다음에 응답을 보내면, Client 는 응답을 기반으로 하는 다른 요청을 보낼 수 있다.

## Deadline / Timeout
Client 측에서는 RPC 가 종료되기 전에 RPC 가 완료될 때까지 대기하는 시간을 지정할 수 있다.
Server 측에서는 특정 RPC 가 timeout 되었는 지, 또는 RPC 가 종료되기 까지 남은 시간을 확인할 수 있다.

## RPC termination
gRPC 는 RPC 특성상 자신의 지역 내에서 함수를 호출하는 것처럼 호출한다고 배웠다. 그리고 gRPC 에서는 Client 측과 Server 측 모두 요청/응답에 대해 독립적이다. 때문에 Client 에서는 완료된 RPC 를 가질 수 있지만, Server 에서는 해당 RPC 에 대해 완료되지 못했거나 실패했을 수도 있다.
또한 Client 가 모든 Request 를 보내기 전에 Server 가 RPC 종료를 결정할 수도 있다. (때문에 이에 대핸 부분을 잘 설계해야 하는 듯 하다.)

## RPC Cancel
Client 및 Server 는 언제든 RPC 를 cancel 할 수 있다. 취소하는 즉시 해당 RPC 는 종료된다.
>RPC 를 취소하면 즉시 종료되며, 취소하기 전에 변경한 사항은 rollback 되지 않는다.

## Metadata
Metadata 는 key-value pair 의 list 형태의 특정 RPC 호출에 대한 정보이다.
key 는 대소문자를 구분하지 않는다.
metadata 를 통해 Client 는 Server 에게 호출과 관련된 정보를 제공하거나 Server 가 Client 에게 정보를 제공할 수도 있다.


## Channel
Channel 은 지정된 Host 및 Port 의 gRPC 서버에 대한 연결을 제공하고, Client Stub 을 생성할 때 사용된다.
gRPC 에서는 보통 하나의 connection 을 열어놓은 상태에서 주기적으로 메시지를 보내는 방식인데, 여기서의 connection 을 channel 이라고 한다. 
(매우 드물기는 하지만 Throughput 을 늘리기 위해 connection pool 을 이용해 multi-channel 을 열 수도 있다. 하지만 공식 가이드에서는 1개의 channel 을 사용하도록 권장하고 있다.)
gRPC 에서 Channel 을 close 하는 방법은 언어에 따라 다르다.


---
---
---
### 참조
- https://grpc.io/docs/what-is-grpc/introduction/
- https://medium.com/naver-cloud-platform/nbp-%EA%B8%B0%EC%88%A0-%EA%B2%BD%ED%97%98-%EC%8B%9C%EB%8C%80%EC%9D%98-%ED%9D%90%EB%A6%84-grpc-%EA%B9%8A%EA%B2%8C-%ED%8C%8C%EA%B3%A0%EB%93%A4%EA%B8%B0-1-39e97cb3460