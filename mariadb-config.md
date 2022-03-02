# MariaDB 설정


## 루트로 작업하기 
ubuntu에서 root로 작업하기 위해서는 다음과 같이 입력한다. 
```shell
sudo su 
```

## 설치 
업데이트 패키지가 있는지 체크하고 설치된 패키지를 업데이트 한다.
```shell
sudo apt update & sudo apt-get -y upgrade
```
MariaDB 설치 
```shell
sudo apt-get install -y mariadb-server
```

설치하고 나면 자동으로 프로세스가 떠 있다
```shell
root@instance-20210604-1640:~# ps -ef | grep mysql
mysql    1038167       1  0 02:47 ?        00:00:00 /usr/sbin/mysqld
root     1038396 1024963  0 02:53 pts/1    00:00:00 grep --color=auto mysql
```

시스템 계정 = mysql ID를 대응시켜서 사용하는 것이 추세라고 합니다. 그래서 sudo를 통해 root 권한이 있음을 증명하여 mysql에 접속한다면 비밀번호를 입력할 필요가 없다. 그러므로 아래 명령어를 통해 접속합니다.
```shell
sudo mysql
```
그럼 다음과 같이 mysql에 접속할 수 있다. 
```shell
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 36
Server version: 10.3.34-MariaDB-0ubuntu0.20.04.1 Ubuntu 20.04

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MariaDB [(none)]>
```
use를 사용하여 db를 변경한다. 
```shell
root@instance-20210604-1640:~# sudo mysql
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 37
Server version: 10.3.34-MariaDB-0ubuntu0.20.04.1 Ubuntu 20.04

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MariaDB [(none)]> use mysql
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
MariaDB [mysql]>
```


## 서비스 확인
apt-get으로 설치하면 서비스로 설치가 된다.  다음 명령으로 서비스를 확인한다.


```shell
systemctl list-units --type service
```

mariadb.service를 확인할 수 있다. 
```shell
lvm2-monitor.service                                       loaded active exited  Monitoring of LVM2 mirrors, snapshots etc. using d>
  mariadb.service                                            loaded active running MariaDB 10.3.34 database server                   >
  netfilter-persistent.service                               loaded active exited  netfilter persistent configuration                >
  networkd-dispatcher.service                                loaded active running Dispatcher daemon for systemd-networkd
```


## 서비스 중단 

systemctl 명령어로 서비스를 중지하거나 다시 시작할 수 있다. 

* start 시작
* stop 중지 
* status 상태 확인
* restart 서비스 재시작 
* enable 시스템이 재부팅하면 자동으로 서비스 실행하도록 등록


다음 명령어로 상태를 학인해 본다.
```shell
systemctl status mariadb.service
```

다음과 같이 결과가 출력된다. 

```shell
root@instance-20210604-1640:~# systemctl status mysqld.service
● mariadb.service - MariaDB 10.3.34 database server
     Loaded: loaded (/lib/systemd/system/mariadb.service; enabled; vendor preset: enabled)
     Active: active (running) since Wed 2022-03-02 02:47:49 UTC; 25min ago
       Docs: man:mysqld(8)
             https://mariadb.com/kb/en/library/systemd/
   Main PID: 1038167 (mysqld)
     Status: "Taking your SQL requests now..."
      Tasks: 30 (limit: 1117)
     Memory: 64.0M
     CGroup: /system.slice/mariadb.service
             └─1038167 /usr/sbin/mysqld
```

중지하고 다시 시작해 보자.
```shell
systemctl stop mariadb.service
systemctl start mariadb.service
```

## MariaDB 삭제 

다음 명령어로 삭제할 수 있다. 
```shell
sudo apt-get purge mariadb-server
```

## 설정 파일 변경 
/etc/mysql/conf.d 아래에 my.cnf 파일을 만들고 다음과 같이 입력한다.
```shell
[mysqld]
character-set-client-handshake = FALSE
character-set-server=utf8mb4
collation-server=utf8mb4_unicode_ci
lower_case_table_names = 1
autocommit=0
bind-address            = 0.0.0.0


[client]
default-character-set = utf8mb4

[mysql]

default-character-set = utf8mb4

[mysqldump]
default-character-set = utf8mb4
```

설정파일을 변경하고 서비스를 재시작한다. 

## root 비밀번호 설정

root 비밀번호 설정하고 접속해보자.  다음의 sql문을 실행한다.
```shell

MariaDB [mysql]> update user set password = password('password') where user = 'root';
Query OK, 1 row affected (0.000 sec)
```

그리고 바로 적용되도록  다음을 실행한다. 
```shell
flush privileges; 
```

## 접속확인
지금 DB 서버가 외부 접속을 허용하는 지 확인을 해보아야한다. netsat -tnlp 명령어로 해당하는 포트(3306은 마리아DB와 MySQL 서버의 Default 포트이다.)

```shell
netstat -tnlp
```
외부에서 접속이 안될 때는   /etc/mysql/mariadb.conf.d 디렉터리로 이동한다. 여기서 50-server.cnf 파일을 수정해야한다. bind-address를 주석처리한다.

```shell
# Instead of skip-networking the default is now to listen only on
# localhost which is more compatible and is not less secure.
#bind-address            = 127.0.0.1
```



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
