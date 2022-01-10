# MongoDB (8)  - 레코드 정렬
MongoDB에서 문서를 정렬하려면 sort () 메서드 를 사용해야 합니다. 이 메서드는 정렬 순서와 함께 필드 목록이 포함된 문서를 허용합니다. 정렬 순서를 지정하려면 1과 -1이 사용됩니다. 1은 오름차순으로 사용하고 -1은 내림차순으로 사용합니다.


```shell
> db.COLLECTION_NAME.find().sort({KEY:1})
```
오름차순으로 정렬한다. 
```shell
> db.user.find({}).sort({'name': -1})
{ "_id" : ObjectId("61dba93889357c931b800f51"), "name" : "홍길동2", "email" : "latte2@gmail.com" }
{ "_id" : ObjectId("61dbaf99fc9301357c16fb93"), "name" : "이미연", "email" : " lee@gmail.com" }
{ "_id" : ObjectId("61dbbc2efc9301357c16fcbf"), "name" : "이미숙", "email" : "misook@gmail.com" }
{ "_id" : ObjectId("61dba2fdfc9301357c16fa67"), "name" : "Hong gildong", "email" : "latte@gmail.com" }
```






