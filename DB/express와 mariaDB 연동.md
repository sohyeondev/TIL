> Express와 MariaDB 연동<br>



### 1. express와 mysql 설치

1.  프로젝트 경로로 이동(cd)
2.  express와 mysql 설치

```
npm install express --save
npm install mysql --save
```

3. 프로젝트의 package.json 에 dependencies 항목에 express와 mysql이 있는지 확인



### 2. server.js 파일 생성

 1. 루트 위치에 server.js 생성

    ![](C:\Users\User\AppData\Roaming\Typora\typora-user-images\image-20210825145203468.png)

    *BOOM_HTML -> 프로젝트 폴더<br>

    createConnection(options) -> 데이터베이스 접속 메서드<br />

    ###### options // user와 password 속성은 필수

    | 속성 이름 |                      설명                      |     ex      |
    | :-------: | :--------------------------------------------: | :---------: |
    |   host    |                 연결할 호스트                  | 'localhost' |
    |   post    | 연결할 포트<br />*MariaDB 설치시 설정했던 포트 |   '3306'    |
    |   user    |               사용자(계정) 이름                | 'boomtest'  |
    | password  |             사용자(계정) 비밀번호              |   '1234'    |
    | database  |              연결한 데이터 베이스              | 'boomting'  |
    |   debug   |                디버그 모드 여부                |             |

 2. server.js 실행

    ![](C:\Users\User\AppData\Roaming\Typora\typora-user-images\image-20210825150018083.png)

