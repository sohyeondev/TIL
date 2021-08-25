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
*'계정이름'@'localhost' : localhost에서만 접속 가능 <br>
'계정이름'@'%' : 어디에서나 접속 가능*

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
mysql -u 계정이름 -p
```

```
C:\Program Files\MariaDB 10.6\bin>mysql -u 계정이름 -p
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
CREATE DATABASE DB이름;
```

```
MariaDB [(none)]> CREATE DATABASE DB이름;
Query OK, 1 row affected (0.001 sec)
```

2. DB에 접속하기

```
use DB이름;
```

```
MariaDB [(none)]> use DB이름;
Database changed
MariaDB [DB이름]>
```

