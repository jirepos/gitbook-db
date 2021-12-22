# Query 응용 


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













