> HeidiSQL 사용해보기



# HeidiSQL 이란?

* MariaDB와 관련된 작업을 GUI로 처리하고 결과를 확인할 수 있는 클라이언트 프로그램



# HeidiSQL로 MariaDB 시작하기

#### 1. [시작] 메뉴에서 [HeidiSQL] 선택

![](C:\Users\User\AppData\Roaming\Typora\typora-user-images\image-20210825152841472.png)

#### 2. 이미 생성되어 있는 localhost 세션 선택(or 신규 세션 추가)

![](C:\Users\User\AppData\Roaming\Typora\typora-user-images\image-20210825152911797.png)

#### 3. 사용자 = 계정명, 암호 = 계정암호, 포트 = 3306

![](C:\Users\User\AppData\Roaming\Typora\typora-user-images\image-20210825152943433.png)

####  4. [열기] 버튼 클릭

![](C:\Users\User\AppData\Roaming\Typora\typora-user-images\image-20210825153037601.png)



# MariaDB 환경변수 설정

* MariaDB 설치 후, 설치 폴더 위치가 아닌 위치에서도 MariaDB를 실행시킬 수 있도록 설정



#### 1. window + r => sysdm.cpl 입력

![](C:\Users\User\AppData\Roaming\Typora\typora-user-images\image-20210825153654613.png)

#### 2. [시스템 속성] - [고급] - [환경변수(N)] 클릭

![](C:\Users\User\AppData\Roaming\Typora\typora-user-images\image-20210825153808170.png)

#### 3. [시스템 변수] - [Path] 선택

![](C:\Users\User\AppData\Roaming\Typora\typora-user-images\image-20210825154038197.png)

#### 4. C:\Program Files\MariaDB 10.6\bin 추가

![](C:\Users\User\AppData\Roaming\Typora\typora-user-images\image-20210825154112994.png)



# HeidiSQL 사용하기

#### 1. 테이블 생성

```
CREATE TABLE 테이블명 (
	필드명 필드타입 속성,
	필드명 필드타입 속성,
	...
);
```



#### 2. 생성한 테이블 확인

```
DESC 테이블명;
```



*쿼리창에서 실행*

![](C:\Users\User\AppData\Roaming\Typora\typora-user-images\image-20210825155245371.png)



*필드 속성*

| 속성           | 설명                     |
| -------------- | ------------------------ |
| NOT NULL       | 반드시 입력해야하는 필드 |
| AUTO_INCREMENT | 자동으로 숫자 증가       |
| PRIMARY KEY    | 기본키로 지정            |
| UNIQUE         | 유일한 값을 가짐         |



#### 3. 테이블에 데이터 저장

```
INSERT INTO users (NAME, id, pw) VALUES ('김철수', 'testid', 'testpw');
```



#### 4. 입력한 데이터 조회

```
SELECT * FROM users; /*모든 데이터 조회*/
SELECT name FROM users; /*특정 데이터 조회*/
```



*파란 화살표를 클릭하면 실행됨<br>한 번 실행한 쿼리문은 주석처리 or 지워주지 않으면 다시 실행됨*

![](C:\Users\User\AppData\Roaming\Typora\typora-user-images\image-20210825160253789.png)