# MongoDB (5)  -Collection 
 
## Collection 생성

```shell
db.createCollection(name, [options]) 
```


**Option** 
| Field | Type | 설명 |
|--|--|--|
|capped | Boolean | 이 값을 true 로 설정하면 capped collection 을 활성화 시킨다.. Capped collection 이란, 고정된 크기(fixed size) 를 가진 컬렉션으로서, size 가 초과되면 가장 오래된 데이터를 덮어쓴다. 이 값을 true로 설정하면 size 값을 꼭 설정해야한다. |
| autoIndex	| Boolean | 	이 값을 true로 설정하면, _id 필드에 index를 자동으로 생성한다. 기본값은 false 이다. |
| size	| number | Capped collection 을 위해 해당 컬렉션의 최대 사이즈(maximum size)를 ~ bytes로 지정한다. |
| max	| number	| 해당 컬렉션에 추가 할 수 있는 최대 갯수를 설정한다. |

옵션없이 chat 컬렉션을 추가한다. 
```shell
> db.createCollection("chat")
```
컬렉션을 옵션과 함께 생성한다. 
```shell
> db.createCollection("msg", {
  capped: true, 
  autoIndex: true, 
  size: 6142800,
  max: 10000
})
```

따로 createCollection() 메소드를 사용하지 않아도 document를 추가하면 자동으로 컬렉션이 생성된다. 

```shell
db.user.insert({ "name": "홍길동2",  "email": "latte2@gmail.com"});
```



 collection 리스트를 확인하려면 show collections 명령어를 사용한다. 
 ```shell
 > show colletions
 ```

## Collection 삭제 

Collection을 제거 할 땐 drop()  메소드를 사용한다. 

```shell
> db.[collection name].drop()
```