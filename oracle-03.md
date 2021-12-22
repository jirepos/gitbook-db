# Oracle 쿼리 활용 


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

## With 구문 
하나의 WITH 구문 
```sql
WITH EXAMPLE AS
(
 SELECT 'WITH' AS MYSTR
 FROM DUAL
)

SELECT * FROM EXAMPLE
```
두개의 WITH 구문 

```sql
WITH EXAMPLE1 AS ( 
  SELECT 'EX1' A FROM DUAL 
  UNION ALL
  SELECT 'EX2' FROM DUAL
), 
EXAMPLE2 AS ( 
  SELECT 'EX3' A FROM DUAL 
  UNION ALL
  SELECT A FROM EXAMPLE1 
)
  SELECT * FROM EXAMPLE2
```

## INSERT INTO .. SELECT 
```sql
INSERT INTO table2 (fld_id)
  SELECT table1.fld_id
  FROM table1 WHERE table1
```






## CONNECT BY LEVEL (Oracle Only)
CONNECT BY LEVEL  구문을   사용하여 루프를 실행할 수 있다
```sql
CONNECT BY LEVEL 부등호 :변수
```
다음은 'a'를 다섯 개 반환한다. 
```sql
SELECT ‘a’ FROM dual 
  CONNECT BY LEVEL <= 5;
```

SELECT에 LEVEL을 직접사용할  수도 있음

```sql
  SELECT LEVEL FROM dual 
  CONNECT BY LEVEL <= 5;
```
1에서 5까지 행이 반복된다. 
```shell
1
2
3
4
5
```

## MERGE INTO 

오라클에서 쿼리문을 작성하다 보면, 하나의 쿼리문으로 INSERT, UPDATE, DELETE 작업을 해야 하는 경우가 있다. 이럴 때에는 MERGE 문을 사용하면 간단하게 쿼리문을 작성할 수 있다. 오라클 9i부터 MERGE 문을 사용할 수 있으며, DELETE 절은 10g부터 사용할 수 있다.








## 문자열 분리하여 행으로 반환하기

### REGEXP_SUBSTR 
정규식을 사용하여  문자열을  찾기
패턴에  매칭된 문자열을 배열로 보관하고 배열처럼 위치를 지정하여 꺼내올 수 있음

```sql
regexp_substr(원본문자열, 정규식, 시작위치, 매칭순번)
```
```sql
SELECT regexp_substr('C/01/02', '[^/]+', 1,1)   FROM dual;
```
* ^은  not 의 의미. ‘/’ 이 아닌 문자열을 찾으면 ‘C’, ’01’, ’02’ 가 해당하고 배열로 보 관된다.
* 시작위치는 1부터 시작하고, 1로 지정한다. 
* 매칭된 문자열의 배열에서 첫번째문자열을 꺼내온다. 결과는 ‘C’


**두번째 매칭된 문자열 꺼내오기** 

두번째 매칭된 문자열을 꺼내오려면  매칭순번에 2를 설정한다.

```sql
SELECT regexp_substr(‘C/01/02’, ‘[^/]+’, 1,2) FROM dual;
```
결과는 01이 반환된다. 

### REGEXP_REPLACE 

정규식을 사용하여 패턴에 매칭된 문자열을 삭제

```sql
regexp_replace(원본문자열’, 정규식, 치환할 문자열’)
```

문자열을 Split 하기 위해서는 구분자 의 갯수를 파악해야 하는데, 구분자가 될 문자를 제외하고 모두 제거해야 한다.  regexp_repladce() 함수를 사용한다.  남은 문자열은 구분자 문자만 남개 되므로 length() 함수를 사용하여 갯수를 세면 된다. 

```sql
SELECT length(regexp_replace('C/01/02', '[^/]+', '')) +1   FROM dual; 
```
‘/’ 가 아닌 문자열을 모두  제거 했기 때문에 ‘/’ 문자만 남개 된다.  length 함수를 사용하여 이것의 길이를 세면 몇 개의 구분자가 있는지 알게 된다. 
‘C/01/02’는  ‘/’을 제외한 문자열을 리플레이스 하면 ‘//’   만 남게 되고 length() 함수를 사용하면 2이다. 
실제 배열은 ‘c’  ’01’, ’02’  세 개의 문자열을 가지고 있으므로  + 1을 해야 한다.

결과는 2 이지만 + 1을 했기 때문에 3이 표시된다.


### 부서경로 문자열을 부서코드의 배열로 반환 
부서 경로의 문자열이 다음과 같다. 
```sql
'C01/D01/D02/D03/D04’
```
사용자  A의 부서 아이디는  D04 이고 상위 부서들이 있다.  ‘/’ 구분자를 이용하여 사용자  A의 상위 부서들을  행(Row)로 반환하는 쿼리를 작성해 보자.
```sql
SELECt regexp_substr(A.DEPT_PATH,'[^/]+', 1,  ROWNUM) AS DEPT_ID
 FROM (SELECT  'C01/D01/D02/D03/D04’ AS DEPT_PATH FROM DUAL) A
CONNECT BY ROWNUM <= LENGTH(regexp_replace(A.DEPT_PATH,'[^/]+')) + 1
```






