# Query 이해 및 활용 






## IN 구문 

아래 쿼리에서는 제일 먼저 USER_LOG 테이블에 접근한다.  즉, IN 뒤의 있는 괄호의 서브쿼리를 먼저 실행해서 그에 대한 결과를 가져온다. 

```sql
SELECT USER_ID, PWD 
FROM   EMPLOYEE 
WHERE  USER_ID IN ( SELECT USER_ID FROM USER_LOG WHERE DEPT_ID = 'D1')
```

이후에는 EMPLOYEE 테이블에서 하나의 레코드를 가져오고 그 레코드의 USER_ID가 앞에서 가져온 IN 이하의 요소들에 있는지 체크한다. 하나라도 있으면 그 레코드를 출력한다. 

실행계획상에는 다음과 같이 보인다.  먼저 USER_LOG 테이블의 인덱스에 접근하는 것을 볼 수 있다. IN 구문의 쿼리를 먼저 실행하고 소팅한 다음에 EMPLOYEE 테이블에 하나씩 USER_ID로 접근하는 것을 볼 수 있다.  
```shell
▼ SELECT STATEMENT
  ▼ NESTED LOOPS
    ▼ NESTED LOOPS 
      ▼ SORT(UNIQUE)
        INDEX(FAST FULL SCAN)           PK_USER_LOG
      INDEX(UNIQUE SCAN)                PK_EMPLOYEE
    TABLE ACCESS(BY INDEX ROWID)        EMPLOYEE
```


## EXISTS 
EXISTS 구문은 EMPLOYEE에 접근하여 하나의 레코드를 가져오고 그 레코드에 대해서 서브 쿼리를 실행하여 그 결과가 있으면 첫 번째 것만 읽고 두번째 것은 읽지 않는다.

```sql
SELECT A.USER_ID, A.PWD 
FROM EMPLOYEE A
WHERE EXISTS ( SELECT 1 
               FROM USER_LOG B 
               WHERE B.USER_ID = A.USER_ID 
               AND   B.DEPT_ID = 'D1')
```                               
실행계획을 살펴보자. 

그런데, IN 구문과 차이가 없다.  이것은 옵티마이저가 가장 빠른 방법을 선택한 것으로 추정하지만 기본 동작 방식은 앞에서 설명한 것이 맞으므로 주의한다. 

```shell
▼ SELECT STATEMENT
  ▼ NESTED LOOPS
    ▼ NESTED LOOPS 
      ▼ SORT(UNIQUE)
        INDEX(FAST FULL SCAN)           PK_USER_LOG
      INDEX(UNIQUE SCAN)                PK_EMPLOYEE
    TABLE ACCESS(BY INDEX ROWID)        EMPLOYEE
```




## NOT IN과 NOT EXISTS 
IN과 EXIST 를 사용하는 경우와  달리 NOT이 들어가면 조건을 제외한 모든 행이 해당 되므로 범위가 넓어져서 FULL SCAN을 하게 될 수 있다. 가급적 사용하지 않아야 한다. 

실행계획을 보면 범위가 넓어져 성능에 문제가 발생할 수 있음을 추정할 수 있다. 
```shell
▼ SELECT STATEMENT
  ▼ HASH JOIN(RIGHT ANTI)
     INSEX (FAST FULL SCAN)          PK_USER_LOG
     TABLE ACCESS(FULLD)             EMPLOYEE
```


