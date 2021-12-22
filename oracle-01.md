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


## Functions 
### TO_CHAR 


Data Type은 DATE일 때.

날자를 문자열로 변환 
```sql
select to_char(sysdate, 'YYYYMMDDHH24MISS') from dual;
```
결과 
```sql
20140808194808
```
### TO_DATE
날자를 Date로  변환 
```sql
select to_date('20140808194808', 'YYYYMMDDHH24MISS') from dual;
```


## Connect By 
재귀쿼리의 대표적인 connect by 구분에 대해서 살펴보자. 테이블을 DEPARTMENT 이고,    상위 부서와 하위 부서의 관계는 UP_DEPT_IDX 칼럼에 상위 부서의 값을 넣어서 연관을 지었다. 즉, 나의 부서의 상위부서의 ID를 찾아서 가면 내 부서의 상위부서를 모두 찾을 수 있다. 반대로 내 부서의 하위 부서의 UP_DEPT_IDX 칼럼의 ID 값을 찾아가면 모두 찾을 수 있다.  

위로 찾아갈까, 아니면 아래 방향으로 찾아갈까?  이것을 결정짖는 것이 PRIOR 이다.  START WITH를 무엇을 가지고 시작할 것인지를 결정한다. 

```sql
select (LPAD(' ', 2 * (dept_depth -1))  ||  dept_name ) DNAME, dept_id, dept_status
   from department 
  where  dept_status != 'DELETED'
  start with dept_id = 'D1'
  connect by  DEPT_ID =  prior up_dept_id;
```

시작을 내 부서의 아이디로 시작을 하고 PRIOR를 상위부서의 연관 값을 가진 UP_DEPT_IDX 앞에 두었다. 이 경우에는 내 부서를 기준으로 상위부서를 찾아가는 것을 반복한다. 

```sql
select (LPAD(' ', 2 * (dept_depth -1))  ||  dept_name ) DNAME, dept_id, dept_status
   from department 
  where  dept_status != 'DELETED'
  start with dept_id = 'D1'
  connect by  prior  DEPT_ID =   up_dept_id;
```
마찬가지로 시작을 내 부서의 아이디로 하고 PRIOR를 DEPT_ID 앞에두었다.  

두개의 테이블을 사용해서도 connect by 구문의 사용이 가능하다.
```sql
select (LPAD(' ', 2 * (A.dept_depth -1))  ||  A.dept_name ) DNAME, A.dept_id
   from department A, department B
  where A.dept_id = B.dept_id
    and A.dept_status != 'DELETED'
  start with B.dept_id = 'ROOT'
  connect by prior A.DEPT_ID = B.up_dept_idx;
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



## Analytic Functions
### 부서를 그룹으로 하여 ROW NUMBER 부여하기 

```sql
SELECT DEPT_ID, ROW_NUMBER() OVER( PARTITION BY DEPT_ID  ORDER BY DEPT_ID ASC ) , EMP_ID
FROM JOBS
```

위 쿼리를 실행하면 결과는 다음과 같다. PARTITION BY 구문에 의해서 DEPT_ID를 grouping 하고 ROW NUMBER를 부여한다. 

### 부서를 기준으로 카운트하기 

```sql
SELECT  dept_id , COUNT(*) OVER( PARTITION  BY dept_id ORDER BY DEPT_ID ASC ), emp_id
FROM JOBS;
```

모든 행을 반환하면서 부서를 그룹핑하여 부서에 속한 종원원의 수를 카운트하여 반환한다. 



### 최소값 


```sql
SELECT manager_id, last_name, salary
FROM (
  SELECT manager_id, last_name, salary,
  MAX(salary) OVER (PARTITION BY manager_id) AS rmax_sal
  FROM employees)
WHERE salary = rmax_sal;
```

### 최대값 

```sql
SELECT manager_id, last_name, salary
FROM (
  SELECT manager_id, last_name, salary,
  MAX(salary) OVER (PARTITION BY manager_id) AS rmax_sal
  FROM employees)
WHERE salary = rmax_sal;
```

### 평균값 
```sql
SELECT submit_date, num_votes, TRUNC(AVG(num_votes)
OVER(ORDER BY submit_date ROWS UNBOUNDED PRECEDING)) AVG_VOTE_PER_DAY
FROM vote_count
ORDER BY submit_date;
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


## Hints 
### ALL_ROWS
   최소한의 자원을 사용하여 결과값의 전체를 추출하게 합니다.

```sql 
SELECT /*+ALL_ROWS */
EMPNO,ENAME
FROM EMP
WHERE EMPNO = 7655;
```

### AND_EQUAL
```sql
  /*+ AND_EQUAL (table index index [index] [index] [index] ) */
```  
복수의 단일 컬럼을 스캔하여 머지 방식으로 처리하게 합니다. 
### APPEND_HINT
```sql
/*+ APPEND */
```             
직렬 모드 데이터베이스에서 Direct INSERT를 실행하게 합니다. Enterprise Edition 이 아닌 데이터베이스의 기본 모드는 직렬 모드입니다. 이러한 직렬 모드 데이터 베이스에서의 INSERT 작업은 Conventional를 기본값으로 하고 병렬 처리 시에는 Direct INSERT를 기본값으로 합니다.
 
### CACHE_HINT
```sql
/*+ CACHE (table) +/
```             
풀 테이블 스캔의 사용 시, 테이블에서 읽어온 블럭을 버퍼의 LRU 리스트 의 MRU 쪽에 위치시킵니다. 작은 테이블의 사용 시 유용합니다.
 
### CHOOSE_HINT
```sql
/*+ CHOOSE +/
```
Rule-Based 와 Cost-Based 방식 간의 선택을 유도합니다. 선택 기준은 사용 객체의 분석 정보 존재 여부이며, 사용되는 객체들중 하나라도 분석 정보가 존재한다면 Cost-Based 방식을 사용하게 됩니다.
 

```sql
SELECT /*+CHOOSE */
EMPNO,ENAME
FROM EMP
WHERE EMPNO = 7655;
```
### CLUSTER_HINT
```sql
/*+ CLUSTER (table) +/
```             
지정 테이블의 클러스터 스캔을 유도합니다. 클러스터된 객체에만 사용할 수 있습니다.

### CURSOR_SHARING_EXACT
```sql
/*+ CURSOR_SHARING_EXACT +/
```             
바인드 변수 값의 교체를 불가능하게 합니다. 기본적으로 CURSOR_SHARING 파라미터를 사용하여, 안전하다고 판단될 시 SQL 내의 바인드 변수 값을 교체할 수 있게 되어 있습니다.


 
### DRIVING_SITE
```sql
/*+ DRIVING_SITE (table) +/
```             
오라클이 선택한 SITE 대신, 지정한 SITE를 사용하여 쿼리를 실행합니다. Rule-Based 와 Cost-Based, 두 모드 다 사용 가능합니다.
 
### DYNAMIC_SAMPLING
```sql
/*+ DYNAMIC_SAMPLING ( [table] n ) +/
```
해당 객체의 Selectivity 와 Cardinality 에 대한 보다 자세한 정보를 자동으로 생성시켜 실행합니다.값은 0 부터 10 까지 지정할 수 있으며, 높을 수록 보다 자세한 정보를 생성하게 됩니다. 테이블에 해당 값을 지정하지 않았을 경우, 기본 값은 CURSOR 레벨의 값이 쓰여집니다.
 
### EXPAND_GSET_TO_UNION
```sql
/*+ EXPAND_GSET_TO_UNION +/
```
GROUP BY GROUPING SET 혹은 GROUP BY ROLLUP 등과 같은 구문을 포함하는 쿼리에 사용할 수 있습니다. 이 힌트는 기존의 쿼리를 개별적인 그룹 생성 후, UNION ALL 방식으로 실행되게 유도합니다.
 
### FACT_HINT
```sql
/*+ FACT (table) +/
```             
스타 변형 구문에서 사용되며 해당 테이블이 FACT 테이블로 사용되게 유도합니다.
 
### FIRST_ROWS
```sql
/*+ FIRST_ROWS (n) +/
```             
전체 결과값의 반환 대신 지정한 숫자만큼 로우의 결과값을 반환하는데 집중하게 유도합니다.
```sql 
SELECT /*+FIRST_ROWS */
EMPNO,ENAME
FROM EMP
WHERE EMPNO = 7655;
```

### FULL_HINT
```sql
/*+ FULL (table) */
```             
지정한 테이블에 대해 풀 테이블 스캔을 유도합니다.
 
### HASH_HINT
```sql
/*+ HASH (table) */
```             
지정한 테이블에 대해 hash 스캔을 수행하도록 유도합니다.클러스터 테이블 만을 대상으로 합니다.
 
### HASH_AJ
```sql
/*+ HASH_AJ */
```             
EXISTS 구문 뒤에 오는 서브 쿼리에 사용되며 HASH_SJ, MERGE_SJ 혹은 NL_SJ 등을 사용할 수 있습니다. HASH_SJ 은 hash semi-join 이고, MERGE_SJ 은 sort merge semi-join 이며 NL_SJ 은 nested loop semi-join 입니다.
 
### INDEX
```sql
/*+ INDEX (table index [index] [index] ... ) */
```             
지정한 테이블의 인덱스 스캔을 실행하도록 유도합니다. Domain, B-tree, bitmap, bitmap join 인덱스 등이 사용될 수 있으나, bitmap 인덱스 들의 사용 시, INDEX 힌트보다는 INDEX_COMBINE 힌트 사용이 추천됩니다.

```sql
SELECT /*+INDEX(EMP EMPNO_INDEX) */
EMPNO, ENAME
FROM EMP
WHERE DEPTNO=10
```

```sql
select /*+ index(c cust_primary_key_idx) */ * from customer c;
```

### INDEX_ASC
```sql
/*+ INDEX-ASC (table [index] [index] ... ) +/
```             
해당 테이블의 인덱스를 순차적 방식으로 스캔하게 합니다. 해당 쿼리가 인덱스 범위 스캔의 사용 시, 인덱스 값의 순차적 방식으로 읽게 됩니다.
 
### INDEX_COMBINE
```sql
/*+ INDEX_COMBINE (table [index] [index] ... ) +/
```             
해당 테이블에 Bitmap 인덱스의 존재 시, Bitmap 인덱스를 통한 액세스를 유도합니다.   힌트 내에 인덱스의 이름이 쓰여지지 않을 시, 해당 인덱스의 Boolean 값을 사용하여 최적의 Cost를 산출하여 실행하게 됩니다.
 
### INDEX_DESC
```sql
/*+ INDEX_DESC (table [index] [index] ... ) +/
```

지정한 인덱스에 대해 인덱스 스캔을 역순으로 실행합니다. 해당 쿼리가 인덱스 범위 스캔의 사용 시, 인덱스 컬럼의 값을 사용하여 역순으로 실행합니다. 파티션 인덱스에서는 파티션 별 개별적인 실행이 이루어집니다.
 
### INDEX_FFS
```sql
/*+ INDEX_FFS (table [index] [index] ... ) +/
```
풀 테이블 스캔 대신에 빠른 풀 테이블 스캔의 실행을 유도합니다.
 
### LEADING_HINT
```sql
/*+ LEADING (table) +/
```             
테이블 간의 조인 시에 지정한 테이블을 먼저 수행하도록 유도합니다. 두 개 이상의 LEADING 힌트의 사용 시, 힌트 자체가 사용되어 지지 않습니다. ORDERED 힌트와 더불어 사용시, LEADING 힌트는 사용되지 않습니다.
 
### MERGE
```sql
/*+ MERGE (table) +/
```             
각 쿼리의 결과값을 머지합니다. 해당 쿼리 내에 GROUP BY 절의 사용 이나 SELECT 구문에 DISTINCT 가 사용되었을 시, 머지의 실행이 가능할 경우에만 힌트가 실행됩니다.       IN 과 서브 쿼리의 사용 시, 서브 쿼리와 상위 쿼리 간의 상호 관계가 없을 때에만 머지의 실행이 가능합니다. 이 힌트는 Cost-based 가 아닙니다. 따라서 액세스하는 실행 쿼리 블럭에 MERGE 힌트가 반드시 명시되어야만 합니다. 그렇지 않을 경우 옵티마이저는 다른 실행 계획을 수립합니다.
 
### MERGE_AJ
HASH_AJ 를 참조하십시요.
 
### MERGE_SJ
HASH_AJ 를 참조하십시요.
 
### NL_AJ
HASH_AJ 를 참조하십시요.
 
### NL_SJ
HASH_AJ 를 참조하십시요.
 
### NOAPPEND
```sql
/*+ NOAPPEND +/
```             
병럴 모드에서의 INSERT 작업을 Conventional 방식으로 수행합니다. 병렬 모드에서는 Direct-path INSERT 가, 직렬 모드에서는 Conventional INSERT가 기본값입니다.
 
### NOCACHE
```sql
/*+ NOCACHE (table) +/
```             
풀 테이블 스캔의 사용 시, 테이블에서 읽어온 블럭을 버퍼의 LRU 리스트 의 LRU 쪽에 위치시킵니다. 기본 모드입니다.
 
### NO_EXPAND
```sql
/*+ NO_EXPAND +/
```

실행 쿼리 내에 OR 나 WHERE 절의 IN 이 사용되었을 시, Cost-Based 옵티마이저가 쿼리 처리를위해 OR 를 사용한 확장을 사용하는 것을 방지합니다.   일반적으로 옵티마이저는 위와 같은 경우 OR – 확장의 가격이 확장을 사용하지 않는 것보다 적을 시, 확장 방식으로 수행합니다.
 
### NO_FACT
```sql
/*+ NO_FACT (table) +/
```
Star 변형 시, 해당 테이블의 FACT 테이블로서의 사용을 방지합니다.
 
#### NO_INDEX
```sql
/*+ NO_INDEX (table [index] [index] ... ) +/
```             
지정 테이블의 인덱스 사용을 방지합니다.
 
### NO_MERGE
```sql
/*+ NO_MERGE (table) +/
```
머지 처리 방식의 사용을 방지합니다.
 
### NOPARALLEL
```sql
/*+ NOPARALLEL (table) +/
```
지정한 테이블의 병렬 처리를 방지합니다.  테이블의 지정된 PARALLEL 값에 대해서 우선권을 가집니다.  중첩 테이블에 대해서는 병렬 처리를 할 수 없습니다.
 
### NOPARALLEL_INDEX
```sql
/*+ NOPARALLEL_INDEX (table [index] [index] ... ) +/
```             
인덱스 스캔 작업의 병렬 처리를 방지합니다. 인덱스에 지정된 PARALLEL 값에 우선권을 가집니다.
 
### NO_PUSH_PRED
```sql
/*+ NO_PUSH_PRED (table) +/
```             
결과값에 대한 조인 방식 서술의 강제적 수행을 방지합니다.
 
### NO_PUSH_SUBQ
```sql
/*+ NO_PUSH_SUBQ +/
```             
서브 쿼리의 결과값을 머지하지 않는 실행 계획이 실행 계획 설립 단계에서 제일 마지막으로 참조되는 것을 방지합니다. 일적으로 서브 쿼리의 Cost 가 높거나, 처리 로우의 갯수를 크게 줄여주지 못할 때에는 서브 쿼리를 마지막에 참조하는 것이 성능 향상에 도움이 됩니다.
 
### NOREWRITE
```sql
/*+ NOREWRITE +/
````             
해당 쿼리 블럭의 쿼리 재생성의 실행을 방지합니다. QUERY_REWRITE_ENALBE 파라미터에 대해 우선권을 가집니다. NOREWRITE 힌트의 사용 시, Function-Based 인덱스의 사용이 금지됩니다.
 
### NO_UNNEST
```sql
/*+ NO_UNNEST +/
```             
해당 서브 쿼리 블럭의 UNNESTING 설정의 사용을 방지합니다.
 
### ORDERED
```sql
/*+ ORDERED +/
```             
FROM 절에 나열된 테이블의 순서대로 조인 작업을 실행합니다.
 
### ORDERED_PREDICATE
```sql
/*+ ORDERED_PREDICATE +/
```
옵티마이저에 의한 조인 관계의 Cost를 산출하기 위해 미리 정해둔 조인 관계 별 실행 순서의 사용을 방지합니다.   인덱스 키를 사용한 조인 관계들은 제외됩니다.  이 힌트는 쿼리의 WHERE 절에 사용하십시요.
 
### PARALLEL
```sql
/*+ PARALLEL (table [ [, n |, DEFAULT |, ] [, n | DEFAULT ] ] ) +/
```             
병렬 처리에 사용될 서버 프로세스의 갯수를 설정합니다.병렬 처리 조건에 위배될 시, 힌트는 사용되지 않습니다. 임시 테이블에 대한 PARALLEL_HINT 사용 시, 힌트는 사용되지 않습니다.
 
### PARALLEL_INDEX
```sql
/*+ PARALLEL_INDEX (table [ [index] [, index]...]
[ [, n |, DEFAULT |, ] [, n | DEFAULT ] ] ) +/
```
파티션 인덱스의 인덱스 범위 스캔 작업의 병렬 처리에 할당될 서버 프로세스의 갯수를 지정합니다.
 
### PQ_DISTRIBUTE
```sql
/*+ PQ_DISTRIBUTE (table [,] outer_distribution, inner_distribution) +/
```
병렬 조인 시, Producer 프로세스와 Consumer 프로세스 간의 데이터 전달 방식을 지정합니다.
 
### PUSH_PRED
```sql
/*+ PUSH_PRED (table) +/
```             
결과값에 대한 조인 방식 서술의 강제적 수행을 실행합니다.
 
### PUSH_SUBQ
```sql
/*+ PUSH_SUBQ +/
```             
머지가 불가능한 서브 쿼리들의 우선 실행 계획을 실행 계획 수립시 먼저 참조하도록 합니다. 서브 쿼리의 사용 객체가 Remote 테이블이거나, 머지 조인의 사용 시 힌트는 실행되지 않습니다.
 
### REWRITE
```sql
/*+ REWRITE [ ( [materialized_view] [materialized_view]...) ] +/
```
실행 계획의 가격에 상관없이 Materialized View 를 사용하여 쿼리 재생성을 하도록 합니다. Materialized View 를 지정할 시, 지정한 Materialized View 의 가격에 상관없이 무조건 쿼리 재생성을 실행합니다. Materialized View 를 지정하지 않을 시, 오라클은 사용 가능한 모든 Materialized View 를 참조하여 그 중 가장 가격이 낮은 Materialized View 를 사용하여 쿼리 재생성을 합니다.  Materialized View 를 지정하지 않는 힌트의 사용이 권장됩니다.
 
### ROW_ID
```sql
/*+ ROWID (table) +/
```
지정한 테이블의 스캔을 ROWID 방식으로 수행하게 합니다.
 
### RULE
```sql
/*+ RULE +/
````             
실행 계획을 Rule-Based 방식으로 실행하게 합니다.  해당 쿼리 블럭에 다른 힌트 또한 사용되었을 경우, 다른 힌트들은 사용되지 않습니다.

```sql
SELECT /*+RULE */
EMPNO,ENAME
FROM EMP
WHERE EMPNO = 7655;
```

### STAR
```sql
/*+ STAR +/
```             
Star 쿼리 계획이 사용 가능하다면, 실행하게 합니다. Star 쿼리 계획이란 가장 큰 테이블이 마지막 순서로 조인되며, 조인될 시 가장 큰 테이블 내의 Concatenated 인덱스에 대해 Nested Loop 조인 방식으로 실행되는 것을 말합니다.  최소한 세개 이상의 테이블이 사용되며, 제일 큰 테이블의 Concatenated 인덱스의 생성에 최소한 세 개 이상의 컬럼이 사용되어야 하며, 액세스나 조인 방식에 충돌이 없어야만 이 힌트는 사용됩니다.
 
### STAR_TRANSFORMATION
```sql
/*+ STAR_TRANSFORMATION +/
```             
옵티마이저가 Star 변형 작업에 최적화된 실행 계획을 수립, 실행하도록 합니다.       힌트를 사용하지 않을 시, 옵티마이저는 일반적인 작업에 최적화된 실행 계획을 수행합니다.  힌트를 사용하였어도 변형 작업에 맞추어진 실행 계획을 실행한다는 보장은 없습니다. 다른 일반적인 힌트의 사용과 마찬가지로 비교 분석 후, 오라클의 판단에 따라 다른 실행 계획이 실행될 수 있습니다.
 
### UNNEST
```sql
/*+ UNNEST +/
```             
서브 쿼리 블럭에 대해 인증성 만을 검사하게 합니다.  인증이 되었다면 그 이상의 검증 작업없이 서브쿼리에 대한 UNNESTING 의 설정을 가능하게 합니다.
 
### USE_CONCAT
```sql
/*+ USE_CONCAT +/
```             
WHERE 절의 OR 조인 을 UNION ALL 로 변경하여 수행하게 합니다. 일반적으로 이러한 변경은 결과값의 병합 수행의 가격이 수행하지 않을 시의 가격 보다 낮을 때에만 실행됩니다.
 
### USE_HASH
```sql
/*+ USE_HASH (table [table]...) +/
```             
Hash 조인 방식으로 각 테이블을 조인하게 합니다.
```sql
SELECT /*+ USE_HASH(l h) */ *
  FROM orders h, order_items l
  WHERE l.order_id = h.order_id
    AND l.order_id > 3500;
```

### USE_MERGE
```sql
/*+ USE_MERGE (table [table]...) +/
```             
Sort-Merge 방식으로 각 테이블을 조인하게 합니다.

```sql
SELECT /*+ USE_MERGE(employees departments) */ * 
  FROM employees, departments 
WHERE employees.department_id = departments.department_id;
```

### USE_NL
```sql
/*+ USE_NL (table [table]...) +/
```           
Nested-Loop 방식으로 각 테이블을 조인하게 합니다.
```sql
/*+USE_NL(inner_table) */
```
```sql
SELECT /*+ORDERD USE_NL(CUSTOMER) */
FROM ACCOUNT.BALANCE,
CUSTOMER.LAST_NAME,
CUSTOMER.FIRST_NAME
WHERE ACCOUNT.CUSTNO = CUSTOMER.CUSTNO;
```
```sql
SELECT /*+ USE_NL(l h) */ h.customer_id, l.unit_price * l.quantity
  FROM orders h ,order_items l
WHERE l.order_id = h.order_id;
```


