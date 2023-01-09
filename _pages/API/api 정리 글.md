API

- https://cloud.google.com/endpoints/docs/openapi/when-why-api-key?hl=ko

# API 키를 사용하는 이유 및 조건

## API 키는 프로젝트용, 인증은 사용자용

Cloud Endpoints는 API 키와 인증 체계(Firebase 또는 Auth0)를 모두 처리합니다. 다음은 API 키와 인증의 주된 차이점입니다.

- API 키는 호출하는 프로젝트, 즉 API를 호출하는 앱이나 사이트를 식별합니다.
- 인증 토큰은 사용자, 즉 앱이나 사이트를 사용하고 있는 사람을 식별합니다.

## API 키는 프로젝트 승인 제공

가장 적절한 체계를 결정하려면 API 키와 인증에서 무엇을 제공하는지 이해하는 것이 중요합니다.

API 키는 다음을 제공합니다.

- **프로젝트 식별** — API를 호출하는 애플리케이션이나 프로젝트를 식별합니다.
- **프로젝트 승인** — 호출하는 애플리케이션에 API를 호출하는 액세스 권한이 부여되었고 프로젝트에서 API가 사용 설정되었는지 여부를 확인합니다.

API 키는 인증 토큰만큼 안전하지 않지만([API 키의 보안](https://cloud.google.com/endpoints/docs/openapi/when-why-api-key?hl=ko#security_of_api_keys) 참조), API를 호출하는 애플리케이션이나 프로젝트를 식별합니다. 이러한 키는 호출을 수행하는 프로젝트에서 생성되며 특정 IP 주소 범위, Android 앱, iOS 앱과 같은 환경에서 키 사용을 제한할 수 있습니다.

호출하는 프로젝트를 식별하면 API 키를 사용하여 사용 정보를 해당 프로젝트와 연결할 수 있습니다. [Extensible Service Proxy(ESP)](https://cloud.google.com/endpoints/docs/openapi/glossary?hl=ko#extensible_service_proxy)는 API 키를 사용하여 액세스 권한을 부여받지 못했거나 API에서 사용 설정되지 않은 프로젝트의 호출을 거부할 수 있습니다.

## 사용자 인증

이와 달리 인증 체계는 일반적으로 다음 2가지 목적에 사용됩니다.

- **사용자 인증** - 호출하는 사용자의 신원을 안전하게 확인합니다.
- **사용자 승인** - 사용자에게 이 요청을 위한 액세스 권한이 필요한지 여부를 확인합니다.

인증 체계는 호출하는 사용자를 식별하는 안전한 방법을 제공합니다. 또한 Endpoints는 인증 토큰에 API를 호출할 수 있는 권한이 있는지 확인합니다. 이 인증 내용을 토대로 API 서버는 요청 승인을 결정합니다.

호출 중인 사용자를 식별하려면 [사용자 인증](https://cloud.google.com/endpoints/docs/openapi/authenticating-users?hl=ko)을 참조하세요.

API 키는 호출하는 프로젝트를 식별하지만 호출하는 사용자는 식별하지 않습니다. 예를 들어 API를 호출하는 애플리케이션을 만든 경우 API 키는 호출하는 애플리케이션을 식별하지만 애플리케이션 사용자는 식별하지 못합니다.

API를 호출할 수 있는 프로젝트나 서비스를 보다 안전하게 제한하는 방법이 필요한 경우 [서비스 간 인증](https://cloud.google.com/endpoints/docs/openapi/service-account-authentication?hl=ko)을 참조하세요.

## API 키의 보안

API 키는 일반적으로 안전하다고 간주되지 않습니다. 대개 클라이언트가 API 키에 액세스할 수 있으므로 API 키를 쉽게 도난당할 수 있습니다. API 키는 만료되지 않으므로 도난당하면 프로젝트 소유자가 키를 취소하거나 다시 생성하지 않는 한 무한정 사용 가능합니다. API 키에 제한을 설정하여 이를 완화할 수 있지만 더 좋은 승인 방법이 있습니다.

예를 보려면 [사용자 인증](https://cloud.google.com/endpoints/docs/openapi/authenticating-users?hl=ko)을 참조하세요.

## API 키 사용 조건

API에서 해당 메소드의 일부 또는 모두 API 키가 필요하도록 제한할 수 있습니다. 이는 다음과 같은 경우 유용합니다.

- 익명 트래픽을 차단하려 합니다. 문제를 디버그하거나 애플리케이션 사용량을 표시하기 위해 애플리케이션 개발자가 API 제작자와 협력해야 하는 경우, API 키로 API 제작자의 애플리케이션 트래픽을 식별합니다.
- API 호출 수를 제어하려고 합니다.
- API 트래픽의 사용 패턴을 식별하려 합니다. [API 및 서비스](http://console.developers.google.com/?hl=ko)에서 애플리케이션 사용량을 볼 수 있습니다.
- API 키를 기준으로 로그를 필터링하려고 합니다.

API 키를 다음 용도로 사용할 수 없습니다.

- 개별 사용자 식별 - API 키는 사용자를 식별하지 않고 프로젝트를 식별합니다.
- 안전한 승인
- 프로젝트 제작자 식별

[서비스 인프라](https://cloud.google.com/service-infrastructure/docs/overview?hl=ko)는 API 키로 프로젝트를 직접 찾을 수 있는 방법은 제공하지 않습니다.

$.ajax 예제

```
$.ajax({
    url:'/study/tmp/test.php', //request 보낼 서버의 경로
    type:'post', // 메소드(get, post, put 등)
    dataType: "jsonp",
    jsonpCallback: "myCallback",
    async: false, //동기: false, 비동기(기본값): ture
    data:{'id':'admin'}, //보낼 데이터,
	timeout: 2*60*60*1000, //2 hours,
    success: function(data) {
        //서버로부터 정상적으로 응답이 왔을 때 실행
    },
    error: function(err) {
        //서버로부터 응답이 정상적으로 처리되지 못햇을 때 실행
    }
});
 $.ajax({

     url : "호출주소", // 자신의 도메인 파일 호출 할 것 (cross domain 기본지원 안함 - " cross-domain 호출하기 편 " 볼것)
     type : "GET" , // GET or POST
     async : true , // 기본 비동기(true) 동기로 할꺼면 false
     cache : true , // 기본 true (304) false(200)

     data: "데이터" , // 전송할 데이터
                              // $('폼아이디').serialize(); - 폼통째로 전달 시리얼라이징
                              // name=데이터&tel=데이터 - 쿼리스트링 방식
                              // data : {'name':'test'} , - json 타입( 바깥 따옴표없다. 안따옴표 필수)


     dataType : // 데이터 타입(응답결과시) xml , html , text , json , jsonp , script ,text
     timeout : 1000 , // 1초 - 서버응답대기시간 설정 (millisecond) 
 
     contentType : // 응답되어 받을 문서의 Type 설정 
                              (기본 : application/x-wwwform-urlencoded;charset=UTF-8)
 
     beforeSend : function() { } ,// 전송전 호출할 함수
     complete :  function(jqXHR) { } , // 완료수 callback (complete or always)
     success :  function(jqXHR) { } , // 성공 후 callback (success or done)
                                                          // jqXHR 안에 결과로온 데이터가 문자열로 들어있음 
     error :  function(jqXHR) { } , // 오류발생시 callback(error or fail ) 

 }); 
```

운수사 리스트
[hellobus].[dbo].[cp_list]
user_id, co_no, assist_co_no
cp_type --> 0: 지부, 7: 운수사
SELECT *
FROM [hellobus].[dbo].[cp_list] CPL left join [2bt].[dbo].[company_list] COL
ON CPL.co_no = COL.co_no
WHERE cp_type = 7


승인요청
승인요청 누르면 req_co_user_list insert
INSERT INTO [hellobus].[dbo].[req_co_user_list] ([phone_no], [co_no], [input_date] ) VALUES ( , , )
운수사가 승인하면 co_user에 insert
INSERT INTO [2bt].[dbo].[co_user] (co_no, phone_no, etc) VALUES ( , , )