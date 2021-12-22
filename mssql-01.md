# MSSQL 정리 

## Data Types
### Integers

**bigint**
-2^63 (-9223372036854775808) 부터 2^63-1 (9223372036854775807) 까지의 정수를 지정 가능 합니다.

**int**

-2^31 (-2,147,483,648) 부터  2^31 - 1 (2,147,483,647)까지의 정수를 지정 가능 합니다.

**smallint**

2^15 (-32,768) 부터  2^15 - 1 (32,767)까지의 정수를 지정 가능 합니다.

**tinyint**

0 부터 255 까지의 정수를 지정 가능 합니다.

**bit**

1 또는 0  이라는 데이터를 저장 가능하며 참, 거짓에 사용 됩니다.

**decimal and numeric**

**decimal** 

-10^38 -1  10^38 -1 까지의 수에 대해 정밀도와 크기를 사용자에 의해 지정 가능 합니다.

**numeric**

decimal 형과 같습니다.

**money and smallmoney**
**money**
돈과 관계되는 데이터 타입으로  -2^63 (-922,337,203,685,477.5808) 부터 2^63 - 1 (+922,337,203,685,477.5807)까지의 수치를 사용가능 하며 1000단위 컴마를 사용 가능 합니다. 천조 까지의 데이터를 처리 가능합니다.

**smallmoney** 

-214,748.3648 부터 +214,748.3647 까지 사용 가능하며 1000단위 컴마 지정이 가능 합니다.

### Approximate Numerics

**float**

부동(정확하지 않은) 소수 데이터 형으로  -1.79E + 308 부터  1.79E + 308 까지의 데이터 사용이 가능 합니다.

**real** 

-3.40E + 38 부터 3.40E + 38 까지의 부동 소수 데이터 형입니다.

### datetime and smalldatetime
**datetime**
1월  1일, 1753년, 부터 12월 31일, 9999년, 까지의 데이터를 1000분의 일초 단위로 지정 가능 합니다.

**smalldatetime**

1월 1일, 1900년, through 6월 6일, 2079년, 까지 1분 단위 정확도로 지정 가능 합니다.

### Character Strings

**char**

8,000바이트(문자)까지 지정 가능한 유니코드가 아닌 고정길이 데이터 형입니다.

**varchar**

8,000바이트 까지 지정 가능한 유니코드가 아닌 가변길이 데이터 형입니다.

**text**

2^31 - 1 (2,147,483,647) 유니코드가 아닌 가변길이 데이터형으로 2기가 까지 지정 가능 합니다.

### Unicode Character Strings
**nchar**

4,000바이트 까지 지정 가능한 유니코드 고정길이 데이터 형입니다.

**nvarchar**

4,000바이트 까지 지정 가능한 유니코드 가변길이 데이터 형입니다.

**ntext**

2^30 - 1 (1,073,741,823) 즉, 1기가 까지 지정 가능한 가변길이 유니코드 데이터 형입니다.

**Binary Strings**
**binary**

8,000 바이트 까지 이진 데이터를 지정 가능한 고정길이 데이터형입니다.

**varbinary**

8,000바이트 까지 이진 데이터를 지정 가능한 가변길이 데이터형입니다.

**image**

2기가까지 이진 데이터를 지정 가능한 가변길이 데이터형입니다.

### Other Data Types
**cursor**
커서 지정에 사용하는 형입니다. 커서에서만 사용 합니다.

**rowversion**
sql2000에서 timestamp 형에서 변경된 데이터 형으로 데이터베이스에 걸쳐 유일하게 사용하는 고유 값을 지정 하는 데이터 형입니다. 해당 로우가 변경될시 함께 변경됩니다. timestamp 형이라 보통 불립니다.

**sql_variant**
text, ntext, timestamp, 과 sql_variant 형을 제외한 모든 데이터형을 저장 가능한 데이터 형입니다.

**table**
sql2000부터 생긴 데이터 형으로 테이블과 같은 2차원 데이터를 저장하기위한 데이터 형입니다.

**uniqueidentifier**
어떠한 경우라도 중복되지 않는 항상 고유한 값을 저장하는 데이터 형입니다.


## Table Creation
```sql
CREATE TABLE  [ [datbasename.ownwer.] | owner.] TABLE01 (
    COL_1 varchar(10) NOT NULL PRIMARY  KEY,
    COL_2 int  DEFAULT 0
    COL_3 AS ( COL_2 * 100)
    COL_4 datetime DEFAULT GetDate()
    COL_5 AS USER_NAME()
)
```

**uniqueidentifier 데이터 형식**

```
CREATE TABLE  TABLE02 (
    COL_1 uniqueidentifier DEFAULE NEWID()
    COL_2 varchar(50) NOT NULL
)
```
```sql
INSERT INTO ( COL_2) VALUES ( ‘aaaaaaaaaaa”); 
```
COL_1에는 ‘F7FLSNKDLDFSDFE98777SDFSDFSF’과 같은 아이디 값이 들어간다. 


**select newid()**

select newid() 를 실행하면 다음과 같은 형식의 값이 나온다. 
```shell
{B47FE73F-E614-4841-B64D-D7DF4DE0017E}
```

### 참조키. (foreign key) 
참조키를 정의하는 기본형식은 아래와 같습니다.
```sql
[CONSTRAINT constraint_name]
    [FOREIGN KEY] [(column[,...n])]
    REFERENCES ref_table [(ref_column[,...n])]
    [ON DELETE { CASCADE | NO ACTION } ]
    [ON UPDATE { CASCADE | NO ACTION } ]
```
 
**constraint_name** 
제약조건의 이름으로 데이타베이스 내에서 유일해야 합니다.
* ref_table : 참조되는 테이블 이름입니다.
* ref_column : 참조되는 컬럼또는 컬럼들입니다.

**NO ACTION** 
참조되는 테이블의 행이 삭제 또는 수정되었을 경우 SQLServer에서는 오류 메시지를 띄우고, 해당 작업은 롤백(RollBack)됩니다.

**CASCADE**
참조되는 테이블의 행이 삭제 또는 수정되었을 경우 이를 참조하는 테이블에서 자동으로 변경되도록 설정  하는 옵션입니다. 옵션을 지정하지 않으면 디폴트는 NO ACTION 입니다.


