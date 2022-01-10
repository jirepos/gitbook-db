# MongoDB (7)  - Projection

MongoDB에서 프로젝션은 문서의 전체 데이터를 선택하는 것이 아니라 필요한 데이터만 선택하는 것을 의미합니다. 문서에 5개의 필드가 있고 3개만 표시해야 하는 경우 그 중에서 3개의 필드만 선택합니다.



### find() 메서드 

MongoDB 쿼리 문서에 설명된 MongoDB의 find() 메서드 는 검색하려는 필드 목록인 두 번째 선택적 매개변수를 허용합니다. MongoDB에서 find() 메소드 를 실행 하면 문서의 모든 필드가 표시됩니다. 이를 제한하려면 값이 1 또는 0인 필드 목록을 설정해야 합니다. 1은 필드를 표시하는 데 사용되고 0은 필드를 숨기는 데 사용됩니다.

> 1 대신 true도 가능하다.

```shell
> db.COLLECTION_NAME.find({},{KEY:1})
```
```shell
> db.user.find({}, {"_id": false, "name": true})
{ "name" : "Hong gildong" }
{ "name" : "홍길동2" }
{ "name" : "이미연" }
{ "name" : "이미숙" }
```


## limit() 

MongoDB의 레코드를 제한하려면 limit () 메서드 를 사용해야 합니다 . 이 메소드는 표시하려는 문서의 수인 하나의 숫자 유형 인수를 허용합니다.

```shell
>db.COLLECTION_NAME.find().limit(NUMBER)
```

```shell
> db.user.find({}).limit(1)
{ "_id" : ObjectId("61dba2fdfc9301357c16fa67"), "name" : "Hong gildong", "email" : "latte@gmail.com" }
```



## skip()

limit() 메서드 외에도 숫자 유형 인수를 허용하고 문서 수를 건너뛰는 데 사용되는 skip() 메서드가 하나 더 있습니다.

```shell
> db.user.find({})
{ "_id" : ObjectId("61dba2fdfc9301357c16fa67"), "name" : "Hong gildong", "email" : "latte@gmail.com" }
{ "_id" : ObjectId("61dba93889357c931b800f51"), "name" : "홍길동2", "email" : "latte2@gmail.com" }
{ "_id" : ObjectId("61dbaf99fc9301357c16fb93"), "name" : "이미연", "email" : " lee@gmail.com" }
{ "_id" : ObjectId("61dbbc2efc9301357c16fcbf"), "name" : "이미숙", "email" : "misook@gmail.com" }
> db.user.find({}).limit(3).skip(2)
{ "_id" : ObjectId("61dbaf99fc9301357c16fb93"), "name" : "이미연", "email" : " lee@gmail.com" }
{ "_id" : ObjectId("61dbbc2efc9301357c16fcbf"), "name" : "이미숙", "email" : "misook@gmail.com" }
>
```
2 개의 문서를 건너 띄고 총 3개까지 가져온다. 

