# MongoDB (6)  - Document
## Document  추가 

```shell
> db.[COLLECTION_NAME].insert(document)
```

배열형식의 인자를 전달해주면 여러 다큐먼트를 동시에 추가 할 수 있댜. 


## Document 제거 
```shell
db.[COLLECTION_NAME].remove(criteria, justOne)
```

| parameter | 	type	| 설명 |
|--|--|--|
|*criteria	|document	| 삭제 할 데이터의 기준 값 (criteria) 입니다. 이 값이 { } 이면 컬렉션의 모든 데이터를 제거한다.|
|justOne	| boolean |	선택적(Optional) 매개변수이며 이 값이 true 면 1개 의 다큐먼트만 제거한다. 이 매개변수가 생략되면 기본값은 false 로 서, criteria에 해당되는 모든 다큐먼트를 제거한다. |


```shell
> db.user.remove( {"name": "홍길동"})
```


## Document 조회 
```shell
> db.COLLECTION_NAME.find(query, projection)
```


| parameter | 	Type	 | 설명  |
|--|--|--|
| query |	document	| Optional(선택적).  다큐먼트를 조회할 때 기준을 정한다. 기준이 없이 컬렉션에 있는 모든 다큐먼트를 조회 할때는 이 매개변수를 비우거나 비어있는 다큐먼트 { } 를 전달한다.|
| projection |	document |	Optional. 다큐먼트를 조회할 때 보여질 field를 정한다.|


**반환(return) 값: cursor**

criteria에 해당하는 Document들을 선택하여 cursor를 반환한다.  cursor 는 query 요청의 결과값을 가르키는 pointer 이다. cursor 객체를 통하여 보이는 데이터의 수를 제한 할 수 있고, 데이터를 sort 할 수 도 있다.  10분동안 사용되지 않으면 만료된다. 




모든 document 조회 
```shell
> db.user.find()
```
결과가 다음과 같이 보여진다. 
```shell
> db.user.find()
{ "_id" : ObjectId("61dba2fdfc9301357c16fa67"), "name" : "홍길동", "email" : "latte@gmail.com" }
{ "_id" : ObjectId("61dba93889357c931b800f51"), "name" : "홍길동2", "email" : "latte2@gmail.com" }
{ "_id" : ObjectId("61dbaf99fc9301357c16fb93"), "name" : "이미숙", "email" : " lee@gmail.com" }
```

결과가 한줄로 나와서 보기 힘들면 find() 메소드 뒤에 .pretty() 를 붙여준다. 

```shell
> db.user.find().pretty()
{
        "_id" : ObjectId("61dba2fdfc9301357c16fa67"),
        "name" : "홍길동",
        "email" : "latte@gmail.com"
}
{
        "_id" : ObjectId("61dba93889357c931b800f51"),
        "name" : "홍길동2",
        "email" : "latte2@gmail.com"
}
{
        "_id" : ObjectId("61dbaf99fc9301357c16fb93"),
        "name" : "이미숙",
        "email" : " lee@gmail.com"
}
```

Email이 latt@gmail.com인 사용자 찾기 
```shell
db.user.find({"email": "latte@gmail.com"});
```

## 연산자
### Query 연산자 
likes 값이 10보다 크고 30보다 작은 Document 조회
```shell
db.articles.find( { “likes”: { $gt: 10, $lt: 30 } } ).pretty()
```

|	operator	|	설명	|
|--|--|--|
|	$eq	|	(equals) 주어진 값과 일치하는 값	|
|	$gt	|	(greater than) 주어진 값보다 큰 값	|
|	$gte	|	(greather than or equals) 주어진 값보다 크거나 같은 값	|
|	$lt	|	(less than) 주어진 값보다 작은 값	|
|	$lte	|	(less than or equals) 주어진 값보다 작거나 같은 값	|
|	$ne	|	(not equal) 주어진 값과 일치하지 않는 값	|
|	$in	|	주어진 배열 안에 속하는 값	|
|	$nin	|	주어빈 배열 안에 속하지 않는 값	|



### 논리 연산자 
```shell
> db.articles.find({ $or: [ { "title": "article01" }, { "writer": "Alpha" } ] })
```
|	operator	|	설명	|
|--|--|
|	$or	|	주어진 조건중 하나라도 true 일 때 true	|
|	$and	|	주어진 모든 조건이 true 일 때 true	|
|	$not	|	주어진 조건이 false 일 때 true	|
|	$nor	|	주어진 모든 조건이 false 일때 true	|


writer 값이 “Velopert” 이고 likes 값이 10 미만인 Document 조회
```shell
db.articles.find( { $and: [ { "writer": "Velopert" }, { "likes": { $lt: 10 } } ] } )
```


### 정규식 연산자

**Pattern:**
```shell
{ <field>: { $regex: /pattern/, $options: '<options>' } }
{ <field>: { $regex: 'pattern', $options: '<options>' } }
{ <field>: { $regex: /pattern/<options> } }
{ <field>: /pattern/<options> }
```


```shell
db.articles.find( { “title” : /article0[1-2]/ } )
```

|	option	|	설명	|
|--|--|
|	i	|	대소문자 무시	|
|	m	|	정규식에서 anchor(^) 를 사용 할 때 값에 \n 이 있다면 무력화	|
|	x	|	정규식 안에있는 whitespace를 모두 무시	|
|	s	|	dot (.) 사용 할 떄 \n 을 포함해서 매치	|


### $where 연산자 
\$where 연산자를 통하여 javascript expression 을 사용 할 수 있다. 



https://velopert.com/479



## Document Update

```shell
db.[COLLECTION_NAME].update(SELECTION_CRITERIA,UPDATED_DATA )
```

### 특정 필드 업데이트 
이름이 홍길동인 document를 찾아서 이름을 변경해보자. 

```shell
> db.user.update({"name": "홍길동"}, {$set: { 'name': 'Hong gildong' }})
```

### 여러 문서 업데이트
기본적으로 MongoDB는 단일 문서만 업데이트합니다. 여러 문서를 업데이트하려면 'multi' 매개변수를 true로 설정해야 합니다.



```shell
> db.user.update( { 'name': '이미숙'}, {$set: { 'name': '이미연'}}, { multi: true });
WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })
>
```
### 특정 필드 추가 
특정 field의 값을 수정할 땐 $set 연산자를 사용합니다. 이 연산자를 사용해서 똑같은 방법을 새로운 field를 추가 할 수도 있습니다.
```shell
> db.user.update({ 'name': '이미연'}, { $set : { age : 10 }});
WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })
> db.user.find();
{ "_id" : ObjectId("61dba2fdfc9301357c16fa67"), "name" : "Hong gildong", "email" : "latte@gmail.com" }
{ "_id" : ObjectId("61dba93889357c931b800f51"), "name" : "홍길동2", "email" : "latte2@gmail.com" }
{ "_id" : ObjectId("61dbaf99fc9301357c16fb93"), "name" : "이미연", "email" : " lee@gmail.com", "age" : 10 }
```

age 필드가 추가된 것을 확인할 수 있다. 

### 특정 필드 제거 
방금 전 추가했던 age 필드를 제거해 보자. 
```shell
> db.user.update({'name': '이미연'}, { $unset : { age : 10 }});
WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })
> db.user.find();
{ "_id" : ObjectId("61dba2fdfc9301357c16fa67"), "name" : "Hong gildong", "email" : "latte@gmail.com" }
{ "_id" : ObjectId("61dba93889357c931b800f51"), "name" : "홍길동2", "email" : "latte2@gmail.com" }
{ "_id" : ObjectId("61dbaf99fc9301357c16fb93"), "name" : "이미연", "email" : " lee@gmail.com" }
```

\$unset을 사용한다. 

### 조건에 없는 Document 추가하기 
조건에 맞는 Document가 없으면 추가해보자. 
```shell
> db.user.update( { 'name': '이미숙'}, { 'name': '이미숙', 'email': 'misook@gmail.com' } , { upsert: true } );
WriteResult({
        "nMatched" : 0,
        "nUpserted" : 1,
        "nModified" : 0,
        "_id" : ObjectId("61dbbc2efc9301357c16fcbf")
})
> db.user.find()
{ "_id" : ObjectId("61dba2fdfc9301357c16fa67"), "name" : "Hong gildong", "email" : "latte@gmail.com" }
{ "_id" : ObjectId("61dba93889357c931b800f51"), "name" : "홍길동2", "email" : "latte2@gmail.com" }
{ "_id" : ObjectId("61dbaf99fc9301357c16fb93"), "name" : "이미연", "email" : " lee@gmail.com" }
{ "_id" : ObjectId("61dbbc2efc9301357c16fcbf"), "name" : "이미숙", "email" : "misook@gmail.com" }
>
```
upsert:true를 사용한다. 

* **upsert**	
boolean.	Optional. (기본값: false) 이 값이 true 로 설정되면 query한 document가 없을 경우, 새로운 document를 추가합니다.
* **multi**	
boolean.	Optional. (기본값: false)  이 값이 true 로 설정되면, 여러개의 document 를 수정합니다.
* **writeConcern**	
document.	Optional.  wtimeout 등 document 업데이트 할 때 필요한 설정값입니다. 기본 writeConcern을 사용하려면 이 파라미터를 생략하세요. 





## 기존 문서 대체 (Save) 
save() 메소드는 save() 메소드에 전달 된 새 문서로 기존의 문서를 대체합니다.
```shell
> db.COLLECTION_NAME.save({_id:ObjectId(),NEW_DATA})
```


```shell
>db.mycol.save(
   {
      "_id" : ObjectId("507f191e810c19729de860ea"), 
		"title":"Tutorials Point New Topic",
      "by":"Tutorials Point"
   }
)
WriteResult({
	"nMatched" : 0,
	"nUpserted" : 1,
	"nModified" : 0,
	"_id" : ObjectId("507f191e810c19729de860ea")
})
```

## 문서 삭제 
```shell
> db.[COLLECTION_NAME].remove(DELLETION_CRITTERIA)
```
```shell
> db.mycol.remove({'title':'MongoDB Overview'})
```

### 하나만 삭제 
```shell
> db.COLLECTION_NAME.remove(DELETION_CRITERIA,1)
```


### 모든 문서 삭제 
```shell
> db.mycol.remove({})
```




