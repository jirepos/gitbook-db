# MariaDB 설정

## Database 생성

```sql
CREATE DATABASE lattedb CHARACTER SET = utf8mb4 COLLATE = utf8mb4_unicode_ci;
```

## 사용자 생성

### localhost 사용자 생성

```sql
CREATE USER 'latte'@'localhost' IDENTIFIED BY '1234' PASSWORD EXPIRE NEVER; 
GRANT ALL PRIVILEGES ON lattedb.* TO 'latte'@'localhost';
flush privileges;
```

### 모든 호스트에서 접근가능한 사용자 생성

```sql
CREATE USER 'latte'@'%' IDENTIFIED BY '1234' PASSWORD EXPIRE NEVER; 
GRANT ALL PRIVILEGES ON lattedb.* TO 'latte'@'%';
flush privileges;
```

### 패스워드 변경

```sql
SET PASSWORD FOR latte@localhost=OLD_PASSWORD('1234')
```

```sql
SET PASSWORD FOR 'latte'@'localhost' = '1234';
```

### 사용자 변경

```sql
ALTEr USER 'lattedb'@'localhost' IDENTIFIED BY '1234' PASSWORD EXPIRE NEVER;
```

## mysql 설정

MySQL 인스턴스는 mysqld를 직접 호출하는 것이 아니라 mysqld_safe를 통해 시작시키는 것을 권장한다. mysqld_safe는 한마디로 말하면 mysqld를 감시하는 데몬이다. /etc/mysql/conf.d/my.cnf 파일을 생성하고 다음과 같이 설정한다.

```shell
[mysqld]
character-set-client-handshake = FALSE
character-set-server=utf8mb4
collation-server=utf8mb4_unicode_ci

[client]
default-character-set = utf8mb4

[mysql]
default-character-set = utf8mb4

[mysqldump]
default-character-set = utf8mb4
```

### \[mysql]

mysql 클라이언트 툴을 이용한 DB 접속시에만 적용되는 설정

```shell
[mysql]
skip-character-set-client-handshake = FALSE # 클라이언트가 보내는 문자셋 정보를 무시하고 서버의 문자셋 사용
character-set-server = utf8mb4
collation-server = utf8mb4
init_connect="SET collation_connection = utf8mb4_unicode_ci"
init_connect="SET NAMES utf8mb4"
```

init-connect 옵션은 DB 서버의 init-connect 변수에 저장되는 값이다. DB 서버는 클라이언트와 connection이 맺어지면 init-connect 변수에 있는 명령어 스크립트를 자동으로 수행한다. 문제는 init-connect 옵션은 DB 접속 계정의 권한이 SUPER privilege 일 때, 즉 root 유저일 때는 수행되지 않는다. init-connect 옵션을 두 개 쓴느 경우가 있다. 이렇게 하면 둘 다 적용되는게 아니라, 마지막 명령어만 서버의 init-connect 변수에 저장됩니다. 만약 2개 이상의 명령어를 수행하고 싶으면 세미콜론 (;)을 구분자로 하여 명령어를 이어 적으면 된다.

### \[mysqld_safe]

mysqld_safe 데몬에만 적용되는 설정

```shell
[mysqld_safe]
default-character-set = utf8mb4
```

### \[mysqld]

mysqld 데몬 프로세스 관련 설정

```shell
[mysqld]
default-character-set = utf8mb4
```

### \[client]

모든 클라이언트 툴을 이용한 DB 접속시에만 적용되는 설정

```shell
[mysql]
default-character-set = utf8mb4
```

### \[mysqldump]

mysqldump 툴을 이용한 DB 접속시에만 적용되는 설정

```shell
[mysqldump]
default-character-set = utf8mb4
```

## SQL 실행

```shell
mysql -u root -p -h 127.0.0.1 < create.sql
```

## rownum 생성

쿼리 툴에서

```sql
set @rownum := 0; 

SELECT @rownum := @rownum +1, reg_dt  , post_id 
   FROM   blog_post 
   WHERE  reg_dt < now()
   order by reg_dt desc , post_id desc
   limit 0, 10
```

MyBatis 사용시

```sql
SELECT @rownum := @rownum +1, m.reg_dt  , m.post_id 
    FROM   blog_post m
          ,(select @rownum := 0 ) temp
    WHERE  m.reg_dt < now()
    order by m.reg_dt desc , m.post_id desc
    limit 0, 10
```

## 문자열 Datetime으로 변환

| 형식 | 설명                 | 형식 | 설명              |
| -- | ------------------ | -- | --------------- |
| %Y | 4글자 년              | %T | hh:mm:ss        |
| %y | 2글자 년              | %r | hh:mm:ss AM/PM  |
| %m | 2글자 월(ex : 01, 12) | %M | 월(영문 전체 March)  |
| %c | 월(ex: 1, 12)       | %b | 월(영문 축약 Mar)    |
| %d | 2글자 일(01, 30)      | %W | 일(영문 전체 Monday) |
| %e | 일(ex: 1, 30)       | %a | 일(영문 축양 Mon)    |
| %H | 24시간 시간            | %i | 분               |
| %l | 12시간 시간            | %S | 초               |

```sql
SELECT STR_TO_DATE('20210407090000', '%Y%m%d%H%i%s') TEST1 -- DATETIME 형식으로 반환
       ,STR_TO_DATE('20210407', '%Y%m%d') TEST2 -- DATE 형식으로 반환
       ,STR_TO_DATE('090000', '%H%i%s') TEST3 -- TIME 형식으로 반환
       ,STR_TO_DATE('2021-04-07', '%Y%m%d') TEST4 -- 문자열과 포맷이 다를 경우 NULL
       ,STR_TO_DATE('2021-04-07', '%Y-%m-%d') TEST5 -- 문자열과 포맷을 맞췄을 경우
```

[W3School 참고](https://www.w3schools.com/mysql/func_mysql_str_to_date.asp)

## 조건값 이전 데이터 몇 건 가져오기

order by에 reg_dt를 asc로 설정해야 한다. 그리고 밖에서 desc로 다시 sort한다.

```sql
    SELECT A.* 
    FROM (
			SELECT @rownum := @rownum +1, m.reg_dt  , m.post_id 
			    FROM   blog_post m
			          ,(select @rownum := 0 ) temp
			    WHERE  m.reg_dt > str_to_date('2021-08-01', '%Y-%m-%d')
			    order by m.reg_dt  , m.post_id desc
			    limit 4
         ) A
    order by A.reg_dt desc  
```

## 조건 값 이 후 데이터 몇 건 가져오기

order by에 reg_dt를 desc로 설정해애 한다.

```sql
    
    SELECT @rownum := @rownum +1, m.reg_dt  , m.post_id 
    FROM   blog_post m
          ,(select @rownum := 0 ) temp
    WHERE  m.reg_dt < str_to_date('2021-08-04', '%Y-%m-%d')
    order by m.reg_dt desc , m.post_id desc
    limit 0, 4
```


## Auto Commit 설정 확인 
```sql
show variables like 'autocommit%';
```
mysqld에 적용하고 싶으면 my.ini 파일에 다음과 같이 추가한다. 
```shell
[mysqld]
autocommit=0
```

## basedir 확인
```sql
SHOW VARIABLES WHERE Variable_Name LIKE "%dir";
```
## my.ini 파일 
Windows에서는 basedir 아래의  data 폴더에 my.ini 파일이 있다. 


## Mariadb 서비스 확인 
Ctrl + Shift + ESC 누른다. 
작업관리자에서 서비스 탭에서 MariaDB를 찾는다. 
거기서 중지 및 시작을 할 수 있다. 



## CharacterSet 확인 
```sql
SHOW VARIABLES WHERE Variable_Name LIKE "C%";
```
