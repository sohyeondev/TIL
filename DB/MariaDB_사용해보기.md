> 팀 프로젝트에서 MariaDB 사용해보기 (참고 : https://doncolmi.github.io/testProject2/)



# MariaDB 설치

MariaDB 공식 사이트 : https://mariadb.org/

  1. download 버튼 클릭
  2. MariaDB Server 10.6.4 설치(Windows ×86_64)
  3. User setting 창 : 체크박스 모두 체크 및 비밀번호 설정
  4. Database settings 창 : 체크박스 모두 체크 및 Buffer pool size 4086 MB로 변경

# MariaDB 세팅

### [1] Win+Q => MySQL 검색(MariaDB는 MySQL 기반) 및 실행

### [2] root 계정으로 MariaDB 서버에 접속

```
Enter password: 설치시 설정했던 비밀번호
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 3
Server version: 10.6.4-MariaDB mariadb.org binary distribution

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
```

### [3] 계정 만들기

1. 계정생성

```
CREATE USER '계정이름'@'%' IDENTIFIED BY '패스워드';
```

```
MariaDB [(none)]> CREATE USER '계정이름'@'%' IDENTIFIED BY '패스워드';
Query OK, 0 rows affected (0.009 sec)
```
*'계정이름'@'localhost' -> localhost에서만 접속 가능 <br>
'계정이름'@'%' -> 어디에서나 접속 가능*

2. 권한 부여

```
GRANT ALL PRIVILEGES ON *.* TO '계정이름'@'%' WITH GRANT OPTION;
```

```
MariaDB [(none)]> GRANT ALL PRIVILEGES ON *.* TO '계정이름'@'%' WITH GRANT OPTION;
Query OK, 0 rows affected (0.011 sec)
```

3. Ctrl+C를 눌러서 나오기

4. 계정에 접근하기

```
mysql -u 계정명 -p
```

```
C:\Program Files\MariaDB 10.6\bin>mysql -u 계정명 -p
Enter password: ****
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 4
Server version: 10.6.4-MariaDB mariadb.org binary distribution

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
```

### [4] 생성한 계정에 DB 만들기

1. DB 만들기

```
CREATE DATABASE DB명;
```

```
MariaDB [(none)]> CREATE DATABASE DB명;
Query OK, 1 row affected (0.001 sec)
```
  *DB 삭제 -> DROP DATABASE DB명;*

2. DB에 접속하기

```
use DB명;
```

```
MariaDB [(none)]> use DB이름;
Database changed
MariaDB [DB명]>
```
 *DB 목록 확인 -> show database;*
 
3. TABLE 생성하기

```
CREATE TABLE 테이블명 ( 필드명 타입 PRIMARY KEY, 필드명 타입, … );
```

```
MariaDB [DB명]> CREATE TABLE 테이블명 ( name VARCHAR(20), id VARCHAR(20) PRIMARY KEY, pw VARCHAR(20) );
Query OK, 0 rows affected (0.027 sec)
```
  *VARCHAR(N) = CHAR(N) -> 타입은 string, 길이는 N byte<br>
  PRIMARY KEY -> 테이블에서 레코드가 가지는 유일한 값<br>
  TABLE 목록 확인 -> show tables;<br>
  TABLE 삭제 -> DROP TABLE 테이블명;<br>
  TABLE 이름 변경 -> ALTER TABLE 기존테이블명 RENAME 새테이블명;*
 
 4. 생성한 TABLE 확인하기

```
DESC 테이블명;
```
```
MariaDB [DB명]> DESC 테이블명;
+-------+-------------+------+-----+---------+-------+
| Field | Type        | Null | Key | Default | Extra |
+-------+-------------+------+-----+---------+-------+
| name  | varchar(20) | YES  |     | NULL    |       |
| id    | varchar(20) | NO   | PRI | NULL    |       |
| pw    | varchar(20) | YES  |     | NULL    |       |
+-------+-------------+------+-----+---------+-------+
3 rows in set (0.029 sec)
```
  *Null -> 공백을 인정하는지 안하는지<br>
  Default -> 초기값<br>
  FIELD 삭제 -> ALTER TABLE 테이블명 drop 필드명;<br>
  FIELD 추가 -> ALTER TABLE 테이블명 add 필드명 타입 after 필드명;<br>
  (after 필드명 다음에 위치하는 새로운 필드 추가)<br>
  FIELD 수정 -> ALTER TABLE 테이블명 CHANGE 기존필드명 새로운필드명 타입;(기존필드명 타입)*
  
 5. TABLE에 값 추가하기

```
INSERT INTO 테이블명 VALUES ( 필드값, 필드값, … );
```
```
MariaDB [DB명]> INSERT INTO 테이블명 VALUES ( '김철수', 'testuserID', 'testuserPW' );
Query OK, 1 row affected (0.101 sec)
```

 6. TABLE 값 확인하기
```
SELECT * FROM 테이블명;
```
```
MariaDB [DB명]> SELECT * FROM 테이블명;
+--------+------------+------------+
| name   | id         | pw         |
+--------+------------+------------+
| 김철수 | testuserID | testuserPW |
+--------+------------+------------+
1 row in set (0.001 sec)
```
