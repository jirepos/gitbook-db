# MongoDB (9)  - Indexing

인덱스는 쿼리의 효율적인 해결을 지원합니다. 인덱스가 없으면 MongoDB는 컬렉션의 모든 문서를 검색하여 쿼리 문과 일치하는 문서를 선택해야 합니다. 이 스캔은 매우 비효율적이며 MongoDB에서 대용량 데이터를 처리해야 합니다.

인덱스는 데이터 세트의 작은 부분을 탐색하기 쉬운 형식으로 저장하는 특수 데이터 구조입니다. 인덱스는 인덱스에 지정된 필드 값에 따라 정렬된 특정 필드 또는 필드 집합의 값을 저장합니다.




## createIndex() 메서드

인덱스를 생성하기 위해서는 MongoDB의 createIndex() 메소드를 사용해야 합니다.
```shell
> db.COLLECTION_NAME.createIndex({KEY:1})
```
여기서 key는 인덱스를 생성하려는 필드의 이름이고 1은 오름차순입니다. 내림차순으로 인덱스를 생성하려면 -1을 사용해야 합니다.


```shell
> db.user.createIndex({'name': 1})
{
        "numIndexesBefore" : 1,
        "numIndexesAfter" : 2,
        "createdCollectionAutomatically" : false,
        "ok" : 1
}
```
에서 createIndex () 메서드는 여러 필드에 인덱스를 만들려면 여러 필드를 전달할 수 있습니다.

```shell
> db.mycol.createIndex({"title":1,"description":-1})
```


|	매개변수	|	유형	|	설명	|
|--|--|--|
|	background	|	Boolean	|	인덱스 빌드가 다른 데이터베이스 활동을 차단하지 않도록 백그라운드에서 인덱스를 빌드합니다. 백그라운드에서 빌드하려면 true를 지정하십시오.기본값은 false 입니다.	|
|	unique	|	Boolean	|	인덱스 키 또는 키가 인덱스의 기존 값과 일치하는 문서 삽입을 컬렉션에서 허용하지 않도록 고유 인덱스를 만듭니다.고유 색인을 작성하려면 true를 지정하십시오.기본값은 false입니다.	|
|	name	|	string	|	인덱스의 이름입니다.지정하지 않으면 MongoDB는 인덱싱된 필드의 이름과 정렬 순서를 연결하여 인덱스 이름을 생성합니다.	|
|	sparse	|	Boolean	|	true인 경우 인덱스는 지정된 필드가 있는 문서만 참조합니다. 이러한 인덱스는 공간을 덜 사용하지만 일부 상황(특히 정렬)에서는 다르게 작동합니다.기본값은 false 입니다.	|
|	expireAfterSeconds|	integer	|	MongoDB가 이 컬렉션에 문서를 보관하는 기간을 제어하는 TTL로 값을 초 단위로 지정합니다.	|
|	weights	|	document	|	가중치는 1에서 99,999 사이의 숫자이며 점수 측면에서 다른 인덱싱된 필드에 상대적인 필드의 중요성을 나타냅니다.	|
|	default_language	|	string	|	텍스트 인덱스의 경우 중지 단어 목록과 형태소 분석기 및 토크나이저 규칙을 결정하는 언어입니다. 기본값은 영어 입니다.	|
|	language_override	|	string	|	텍스트 인덱스의 경우 기본 언어를 재정의할 언어가 포함된 문서의 필드 이름을 지정합니다.기본값은 언어입니다.	|



## dropIndex() 메서드
MongoDB의 dropIndex() 메서드를 사용하여 특정 인덱스를 삭제할 수 있습니다.
```shell
> db.COLLECTION_NAME.dropIndex({KEY:1})
```
여기서 key는 인덱스를 생성하려는 파일의 이름이고 1은 오름차순입니다. 내림차순으로 인덱스를 생성하려면 -1을 사용해야 합니다.


## dropIndexes() 메서드
이 메서드는 컬렉션에서 여러(지정된) 인덱스를 삭제합니다.

```shell
> db.COLLECTION_NAME.dropIndexes()
```



### getIndexes() 메서드
이 메서드는 컬렉션의 모든 인덱스에 대한 설명을 반환합니다.

다음은 getIndexes() 메서드의 기본 구문입니다.
```shell
db.COLLECTION_NAME.getIndexes()
```



## Indexing 제한 사항 
### 램 사용량
인덱스는 RAM에 저장되므로 인덱스의 전체 크기가 RAM 제한을 초과하지 않는지 확인해야 합니다. 전체 크기가 RAM 크기를 늘리면 일부 인덱스가 삭제되기 시작하여 성능이 저하됩니다.

### 쿼리 제한 사항
다음을 사용하는 쿼리에서는 인덱싱을 사용할 수 없습니다.
```shell
$nin, $not 등과 같은 정규식 또는 부정 연산자
$mod 등과 같은 산술 연산자
$where 절
```
따라서 쿼리에 대한 인덱스 사용을 항상 확인하는 것이 좋습니다.

### 인덱스 키 제한
버전 2.6부터 MongoDB는 기존 인덱스 필드의 값이 인덱스 키 제한을 초과하면 인덱스를 생성하지 않습니다.


### 최대 범위
* 컬렉션은 64개 이상의 인덱스를 가질 수 없습니다.
* 인덱스 이름의 길이는 125자를 초과할 수 없습니다.
* 복합 인덱스는 최대 31개의 필드를 인덱싱할 수 있습니다.


