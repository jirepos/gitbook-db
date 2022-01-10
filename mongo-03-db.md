# MongoDB (3)  데이터베이스 생성 



## DB 생성 및 삭제

### Database 생성: use

데이터베이스가 없으면 생성하고 있으면 사용한다. 

```shell
> use chat
```

현재 사용중인 데이터베이스를 확인하려면 db 명령어를 사용한다.

```shell
> db
```


데이터베이스 리스트들을 확인하려면 show dbs 명령어를 사용한다. 
```shell
> show dbs
```


### Database 제거: db.dropDatabase()
```shell
> db.dropDatabase();
```
