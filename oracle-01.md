# Oracle 




## 권한 생성
### 계정생성
```sql
CREATE USER 생성할 계정이름 IDENTIFIED BY ‘계정비밀번호’; 
```


### 권한 설정
```sql
GRANT CREATE SESSION, CREATE TABLE, CREATE SEQUENCE, CREATE VIEW TO 권한을 줄 계정명;
```


### 함수실행권한 부여
```sql
CRANT EXECUTE ON 권한을 줄 함수명 TO 권한을 받을 계정;
```

### SYNONYM 생성
```sql
CREATE SYNONYM 계정명.테이블명 FOR 테이블소유자 게정명.테이블명;
```


### 테이블 권한 부여
```sql
GRANT SELECT, INSERT, UPDATE, DELETE ON 권한줄 테이블 명 TO 권한받을 계정; 
```



## DDL 

### 테이블 생성 
```sql
CREATE TABLE ATTACH_FILE
(
	FILE_SEQ             NUMBER NOT NULL ,
	FILE_SIZE            NUMBER NULL ,
	REG_DATE             DATE NULL ,
	FILE_NAME            VARCHAR2(100) NULL 
);
```

### PK 생성 
```sql
ALTER TABLE ATTACH_FILE
	ADD CONSTRAINT  PK_ATTACH_FILE PRIMARY KEY (FILE_SEQ);
```


### INDEX 생성
```sql
CREATE UNIQUE INDEX IX_ATTACH_FILE_01 ON EAPP_ATTACH_FILE
(FILE_NAME   ASC);
```

### 칼럼추가
```sql
ALTER TABLE EAPP_FORM ADD ( 
     칼럼명 칼럼타입 [, 칼럼명 칼럼타입]
);
```
example.

```sql
ALTER TABLE APP_FORM ADD ( 
       FM_TYPE CHAR(1) DEFAULT  '1',
       REL_FORM_ID VARCHAR(40),
       PROP_SRCH_TYPE(1)
);
```


### COMMENT 추가
#### 테이블 커맨트 추가
```sql
COMMENT ON 테이블명 IS '설명';
```
```sql
COMMENT ON EMPLOYEE IS ‘임직원’;
```

#### 칼럼 커멘트 추가
```sql
COMMENT ON COLUMN 테이블명.필드명 IS '설명';
```
```sql
COMMENT ON  COLUMN EMPLOYEE.USER_TYPE IS ‘사용자 유형’ ;
```

#### 컬럼커멘트 삭제
```sql
COMMENT ON COLUMN 테이블명.필드명 IS '';
```

```sql
COMMENT ON  COLUMN EMPLOYEE.FORM_TYPE IS ‘’ ;
```
#### 커멘트 확인
```sql
SELECT * FROM ALL_COL_COMMENTS WHERE TABLE_NAME = '테이블명';
```



## Dictionary 

### USER_OBJECTS[OBJ]
다음 같은 객체를 찾기 위해 사용할 수 있다. 클러스터, 데이터베이스 링크, 함수 , 인덱스, 패키지, 패키지바디, 프로시저, 시퀀스, 시너님,테이블, 트리거 , 뷰같은 것들이다. 

### USER_TABLES[TABS] / USER_TAB_COLUMNS[COLS]
에 구체적인 정보를 나열한다.  테이블에 대한 열정의를 보기 위해서는 where절에서 TABLE_NAME을 열거하여 USER_TAB_COLUMNS을 쿼리한다. 
정보는 SQLPLUS의 describe 명령을 통해 포함할 수 있다. 그러나 describe는 디폴트와 통계열 참조 조건을 부여하지 않는다. 추상데이타 타입을 사용하는 경우 desc는 사용된 데이터 탕입의 이름을 보여주지 않는다. 

### USER_VIEWS
뷰에대한 기본적인 쿼리는 USER_VIEWS 데이터 사전뷰를 통해서 엑세스할 수 있다. 

### USER_SYNONYMS[SYN]
사용자가 소유한 모든 시너님을 나열한다. 

### USER_SEQUENCES[SEQ]

### 제약과 주석[Constraints & Comments]
#### USER_CONSTRAINTS
제약 정보는 USER_CONSTRAINTS를 통해서 액세스할 수 있다. 이 정보는 오래된 데이터 제약이나 애플리케이션의 데이터 문제 해결을 할 때 매우 유용한다. 

```shell
Owner  제약의 소유자
Constarint_Name 제약의 이름
Constraint_Type 제약의 유형
                 C : CHECK 제약, NOT NULL 포함
                 P: PRIMARY KEY 제약 
                 R: FOREIGN KEY(참조) 제약
                 U: UNIQUE 제약 
                 V: WITH CHECK OPTION 제약 

```
### USER_CONS_COLUMNS 제약열 
제약과 관련된 열을 볼수 있다.
```sql 
SELECT column_name, position 
FROM USER_CONS_COLUMNS
and Constraint_Name = 'SYS_C0008791'
```
```shell
COLUMN-NAME POSITION
-----------------------------------
FIRSTNAME          1
LASTNAME           2
```
BIRTHDAY 테이블에 대한 FirstName과 LastName의 조합은 프라이머리 키를 구성한다.


### USER_TAB_COMMENTS / USER_COL_COMMENTS
자체 테이블에 대하여 주석을 달기 위해서 사용. USER_COLS_COMMNETS는 열주석이다. 


### 인덱스와 클러스터 
**USER_INDEXES[IND] /  USER_IND_COLUMNS**
```shell
<참고>
ALL_IND_COLUMNS 
사용자가 액세스할수 있는 모든 테이블에 있는 인덱스에 대한 열 정보를 표시
DBA_IND_COLUMNS 
전체 데이터베이스에 대한 열 레벨의 인덱스 정보 
```
**USER_CLUSTER[CLU] /  USER_CLU_COLUMNS**

### 링크와 스냅삿
```shell
데이터베이스 링크 : USER_DB_LINKS
DB_Link 데이터베이스 링크의 이름
Username 원격데이터베이스에서 사요되는 사요자 이름
Password 원격 데이터베이스에서의 사용자의 패스워드
Host 원격 데이터베이스 연결을 위해 사용할 SQL*Net 스트링
Created 타임 스탬프
```
```sql
SELECT* FROM USER_DB_LINKS
WHERE UPPER(Host) = 'HQ';
```
### 사용자와 권한
```shell
사용자 : USER_USERS
dba_users    모든 사용자
user_users    현재 사용자
all_users   
```
```shell
리소스 제한: USER_RESOURCE_LIMITS
테이블 권한: USER_TAB_PRIVS
열권한 : USER_COL_PRIVS
시스템 권한: USER_SYS_PRIVS
```

### How To

**테이블 스페이스 조회**
dba_tablespaces
**테이블 스페이스에서 사용 가능한 공간의 확인**
dba_free_space

**오라클 역할(Role)들을  조회**
dba_roles
**몇가지**
```shell
user/dba_sys_privs  시스템 권한의 정보 조회
user/dba_tab_privs  테이블 객체 권한의 정보 
user/dba_col_privs  각 객체 컬럼 권한의 정보
role_sys_privs      역할에 주어진 시스템 권한에 대한 정보 조회
role_tab_privs      역할에 주어진 테이블 객체 권한 조회
user/role_role_privs 사용자 또는 다른 역할에 주어진 역할에 대한 정보
session_roles       현재 접속중인 사용자에게 주어진 역할 조회
dba_roles          데이터 베이스 내의 모든 역할의 조회 
```
**사용자에 대한 정보**
```shell 
dba_users    모든 사용자
user_users    현재 사용자
all_users   
```
**현재 사용되고 있는 인덱스 조회** 
```shell
user_indexes
user_ind_columns
```
**오라클 뷰를 위한 데이터 사전 뷰**
```shell
dba_views 모든 뷰를 조회
user_views 현재 사용자가 가지고 있는 뷰의 조회에 사용
all_views   현재 사용자가 액세스할스 이쓴 모든 뷰의 조회
```
**현재 접속되어 있는 사용자의 자원 제한 사항을 확인**
```shell
user_resource_limits
```
### foreign key 삭제 방법

user_cons_columns table 을 참조하시면 constraint_name 을 찾을수 있습니다. 이것을 이용하여 
```sql
alter table table명 drop CONSTRAINT constraint_name;
```
하시면 삭제가 될겁니다.

### primary key 제약조건 없애기
```sql
alter table table명 drop constraint primary key명; 
```



## 통계 
### 통계 생성 
```sql
ANALYZE TABLE[INDEX] EMPLOYEE COMPUTE STATISTICS;
```
통계정보 확인 
```sql
SELECT table_name, blocks, num_rows, avg_row_len
     FROM user_tables
   WHERE table_name = 'EMPLOYEE’;
```

