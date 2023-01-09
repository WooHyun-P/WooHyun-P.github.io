참조
* https://medium.com/naver-cloud-platform/nbp-%EA%B8%B0%EC%88%A0-%EA%B2%BD%ED%97%98-%EC%8B%9C%EB%8C%80%EC%9D%98-%ED%9D%90%EB%A6%84-grpc-%EA%B9%8A%EA%B2%8C-%ED%8C%8C%EA%B3%A0%EB%93%A4%EA%B8%B0-1-39e97cb3460
* https://chacha95.github.io/2020-06-15-gRPC1/

---

#### proto file
* (Naming...)
  * field tag (= field number)
  * proto2 vs proto3
  * 참조
    - https://medium.com/naver-cloud-platform/nbp-%EA%B8%B0%EC%88%A0-%EA%B2%BD%ED%97%98-%EC%8B%9C%EB%8C%80%EC%9D%98-%ED%9D%90%EB%A6%84-grpc-%EA%B9%8A%EA%B2%8C-%ED%8C%8C%EA%B3%A0%EB%93%A4%EA%B8%B0-2-b01d390a7190
    
    
---
# Protocol Buffer
```
구조화된 데이터를 직렬화 하기 위해 사용되는 Google의 오픈소스 메커니즘이다. 기본적으로 GRPC는 Protocol Buffer를 사용한다.

JSON 과 같은 다른 직렬화 방식을 사용할 수도 있다.

Protocol Buffer로 작업시,

proto 파일에서 직렬화 하려는 데이터 구조를 정의

프로토콜 버퍼 데이터는 message로 구성된다.

각 메시지는 feild 라는 일련의 name-value pair를 포함하는 정보의 작은 논리적 레코드다.

protoc 라는 컴파일러를 통해 .proto 파일을 컴파일 한다.

proto 정의로 부터 원하는 프로그래밍 언어로 데이터에 접근하게 해주는 data access class를 생성해준다.

각 feild를 위한 간단한 접근자를 제공하며, raw byte로 부터 혹은 raw byte로 전체 구조를 serializa/parse 하는 method를 제공한다.

예) C# 으로 예를 들면, (아래 코드는 https://github.com/grpc/grpc-dotnet gRPC 오픈소스 example 을 참조)

Greeter.proto 파일 작성
proto
syntax = "proto3";

package greet;

// The greeting service definition.
service Greeter {
  // Sends a greeting
  rpc SayHello (HelloRequest) returns (HelloReply);
}

// The request message containing the user's name.
message HelloRequest {
  string name = 1;
}

// The response message containing the greetings
message HelloReply {
  string message = 1;
}

작성한 .proto 파일을 protoc 컴파일러로 컴파일

 여기서 원하는 언어의 컴파일러로 컴파일 한다. (또는 Application 단에서 빌드시 자동으로 컴파일이 된다. vs나 android studio 등)

자세한 사용법은 (https://jeong-pro.tistory.com/190)  해당 글을 참조

proto를 컴파일 하면  했을 때, Greeter 이라는 class가 생성된다. 이제 Application 내에서 Person class를 사용할 수 있다.

이 Greeter 클래스를 사용해서 Greeter Protocol Buffer 메시지를 '채우고', '직렬화' 하고, '검색' 할 수 있다.

## Protocol Buffer version

일반적으로 proto2 버전을 사용할 수 있지만, GRPC는 proto3을 사용하는 것을 권장한다.

이유는, GRPC가 지원하는 모든 언어를 사용할 수 있으며, proto3과 proto2의 통신 호환성 문제를 피할 수 있기 때문이다.
```