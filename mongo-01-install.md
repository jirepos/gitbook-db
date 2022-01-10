# MongoDB (1)  설치

## Installation 

아래의 URL에서 community 버전을 다운로드 받는다. 

[Community 버전 다운로드](https://www.mongodb.com/try/download/community)


* Version 5.05
* Platform Windows
* Package MSI 


설치는 Default로 설치한다. 

* Install MongoDB as a Service
  * Run service as Network Service
* Service Name; MongoDB 
  




## Shell에서 MongoDB 접속 

접속하기 위해서 환경변수 PATH에 다음을 추가한다. 
```shell
C:\Program Files\MongoDB\Server\5.0\bin
```
터미널에서 다음을 입력한다. 
 ```shell
 mongo
 ```

 리스트에서 방금 만든 데이터베이스를 보려면 최소 한개의 Document를 추가해야한다. 


```shell
db.user.insert({ "name": "홍길동2",  "email": "latte2@gmail.com"});
```







## MongoDB 이해하기 


NoSQL은 다음과 같이 RDBMS에선 하기 힘든 일을 쉽게 지원한다.

* 수평 확장 가능한 분산 시스템
* Schema-less
* 완화된 ACID

## Document

MongoDB는 Document 기반 데이터 베이스이다. Database → Collection →  Document →  Field 계층으로 이루어져 있다. Document는 RDBMS의 Row에 해당한다. 

```shell
Database 
--------------------
Collections
--------------------
Documents
--------------------
Fields
```

 Document 기반 데이터베이스은 RDBMS와 다르게 자유로이 데이터 구조를 잡을 수 있다는 점이다. MongoDB는 BSON으로 데이터가 쌓이기 때문에 Array 데이터나 Nested한 데이터를 쉽게 넣을 수 있다.
