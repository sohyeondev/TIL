> 리액트를 aws로 연결해보자



일단 저번에 탄력적 IP 할당받고, 도메인 연결하고, HTTPS 인증까지 마친 서버를 그대로 이용하기로 했다.

안에 있던 내용을 git에 백업한 후 지우고, 리액트랑 mariaDB를 로컬에서 연동한 것을 그냥 복사&복붙

(VScode에도 연동해놔서 복사복붙이 간편했다.)



###### 문제1. react-scripts: Permission denied

```
sudo chmod +x node_modules/.bin/react-scripts
```

VScode 터미널에서도 될 거 같지만 그냥 Termius에서 진행

일단 cd로 node_modules 폴더가 있는 client 폴더로 이동해서 권한을 줬다.

**sudo 명령어는 유닉스 및 유닉스 계열 운영 체제에서 super user로 프로그램을 구동할 수 있도록 한다.*

**chmod 명령어는 change mode의 축약어로 대상 파일과 디렉토리의 사용권한을 변경한다.*

**+x 옵션은 모든 사용자에게 실행 권한을 추가한다.*



###### 문제2. npm start를 하면 자동으로 localhost:3000 창을 띄워준다.

주소창에 탄력적IP:3000을 치면 리액트 화면이 잘 구동되지만, 아예 처음부터 이 서버로 창을 띄우고 싶다.

참고: https://samtao.tistory.com/58?category=921294

"서버 배포시"에 해당하기 때문에 일단 .env.production을 이용해보기로...

```
NODE_PATH=src/
REACT_APP_API_ROOT="http://api.탄력적IP:8443"
REACT_APP_HOME_URL="http://탄력적IP:3000"
```

일단, 나한테 맞게 고쳐서 해봤는데 여전히 localhost:3000으로 창이 띄워진다.

블로그에서도 적용이 안된다고 했으므로 일단 그 다음 과정을 따라가보자

```
npm install env-cmd --save
```

[package.json] 파일에 "scripts"부분 수정

```
  "scripts": {
    "start": "react-scripts start",
    "build": "env-cmd -f .env.production react-scripts build",
    "test": "react-scripts test",
    "eject": "react-scripts eject"
  },
```

... 여전히 localhost:3000에서 열린다...

근데 찾아봐도 기본포트 변경은 있는데 아예 열리는 창 주소 변경은 잘 안나온다....

방법이 뭘까...

[package.json] 파일에 "homepage"를 추가해봤다.

```
  },
  "homepage": "http://탄력적IP:3000/"
}
```

여전히 안된다...

[package.json] 파일의 "scripts"를 변경해봤다.

```
  "scripts": {
    "start": "HOST=탄력적IP react-scripts start",
    "build": "env-cmd -f .env.production react-scripts build",
    "test": "react-scripts test",
    "eject": "react-scripts eject"
  },
```

처음엔 build를 건드렸다면 이번엔 start를 건드렸다.

아예 에러가난다...

```
^Cubuntu@ip-172-31-11-73:~/boomtest/client$ npm start

> boom@0.1.0 start /home/ubuntu/boomtest/client
> HOST=탄력적IP react-scripts start

Attempting to bind to HOST environment variable: 탄력적IP
If this was unintentional, check that you haven't mistakenly set it in your shell.
Learn more here: https://cra.link/advanced-config

Could not find an open port at 탄력적IP
Network error message: listen EADDRNOTAVAIL: address not available 탄력적IP

npm ERR! code ELIFECYCLE
npm ERR! errno 1
npm ERR! boom@0.1.0 start: `HOST=탄력적IP react-scripts start`
npm ERR! Exit status 1
npm ERR! 
npm ERR! Failed at the boom@0.1.0 start script.
npm ERR! This is probably not a problem with npm. There is likely additional logging output above.

npm ERR! A complete log of this run can be found in:
npm ERR!     /home/ubuntu/.npm/_logs/2021-09-10T02_23_49_805Z-debug.log
```

인스턴스 실행중인데...ㅠㅠ

.env.production 파일을 .env로 수정하고 [package.json] 파일의 "scripts"를 또 변경해봤다.

[.env]

```
PUBLIC_URL=http://3.34.61.45:3000
```

[package.json]

```
  "scripts": {
    "start": "react-scripts start",
    "build": "env-cmd -f .env react-scripts build",
    "test": "react-scripts test",
    "eject": "react-scripts eject"
  },
```

에러는 안나지만 localhost:3000으로 연결된다.

근데 그 이후로 뭐가 잘못됐는지 에러가 난다...

아 무엄얄머일머닉

localhost는 여전히 열리는데 탄력적IP에선 연결이 거부됐대!!!ㅠㅠ

싹 날리고 git clone 해왔다.

일단 문제2는... 유보



###### 문제3. mariaDB 연결

어쨌든 클라이언트는 연결이 되니까 넘어가고

서버를 키면

```
Error: connect ECONNREFUSED 127.0.0.1:3306
```

이런 문제가 나온다.

3306은 mariaDB 포트니까...

사실 보안그룹 문제인줄 알고 보안그룹에 추가했는데 여전하다.

연결을 안해서 그런가 보다...

참고 : https://aws.amazon.com/ko/getting-started/hands-on/create-mariadb-db/

1. AWS에 로그인하고 서비스에서 RDS로 들어간다.
2. 리전을 서울로 설정
3. 데이터베이스 생성 클릭
4. [표준생성]-[MariaDB]-[프리티어] 선택
5. DB 인스턴스 식별자에 DB이름 입력, 마스터 사용자 이름 및 비밀번호 설정
6. 쭉 내려가다가 Public accessibility(퍼블릭 액세스)에서 yes(예)
7. 보안그룹도 새로 만들자
8. 그러고 생성!

잘 만든걸까...

인스턴스가 생성되는동안 MySQL Workbench를 다운로드하자.

https://dev.mysql.com/downloads/workbench/

로그인 하라고 나오면 No thanks를 누르면 된다.

MySQL Workbench가 설치되었으면 열어준다.

1. 상단 메뉴의 [Database]-[Connect to Database]
2. 호스트 이름 : DB인스턴스의 엔드포인트를 넣는다.
3. 포트 : 기본값 3306
4. 사용자이름 : 생성시 설정했던 이름
5. 암호 : [저장소에 저장] 클릭 후 생성시 설정했던 비밀번호
6. ok를 누른다!

...? 창이 그대로 꺼진다... 나한테 왜그래...?

...에러라도 떠줘...ㅠㅠ



모르겠어서 aws랑 HeidiSQL 연결을 쳐봤다.

HeidiSQL을 열고, 기존에 있던 세션을 바꿀거다(여기에 이미 DB가 있으므로...)

[설정]

1. 네트워크 유형은 MySQL (SSH tunnel)
2. 호스트명은 127.0.0.1 그대로
3. 사용자는 그냥 root 사용할 거
4. 암호는 설정한거
5. 포트도 3306

[SSH 터널]로 이동

1. plink.exe를 다운받아야한댄다.
2. https://www.chiark.greenend.org.uk/~sgtatham/putty/ 로 들어가기
3. Download it here 클릭
4. plink.exe를 찾아서 다운로드 해준다.
5. 다시 SSH 터널로 돌아와서 다운한 plink.exe를 선택해주고
6. SSH 호스트 + 포트에는 내 EC2 탄력적 IP
7. 사용자 이름은 난 ubuntu
8. 개인 키 파일은 처음에 연결할 때 다운받은 ppk 파일
9. 포트번호는 3306이 아닌 포트번호, 난 그냥 써있는 3307 하기로 했다.



하...

Lost connection to MySQL server at 'reading initial communication packet' 어쩌구 쏼라 에러가 뜬다.

으음 초심으로 돌아가보자...

ubuntu에는 mariaDB를 안설치한 것 같다.

터미너스를 켰다.

1. sudo apt-get install mariadb-server를 설치한다.
2. mysqladmin -u root -p password '비밀번호' (비밀번호 초기화)
3. 응 안돼~ sudo apt install mysql-client-core-8.0나 sudo apt install mariadb-client-10.3을 깔라고 한다. 후자 선택.
4. 또 뭐라 쏼라쏼라 하면서 에러가 떴다. sudo apt-get update를 해줬다.
5. 설치 완료 다시 비밀번호를 초기화해본다.
6. mysqladmin: connect to server at 'localhost' failed 에러가 뜬다. 밑에를 읽어보니 mysql을 실행하라는건가...

아너일머ㅣㅇ기ㅏㅁㄱ 뭐 다 안돼!!

다시 차분히해보자...

드디어 뭔가 되는 걸 찾았다....다시...

1. sudo apt-get update
2. sudo apt-get install mariadb-server
3. sudo apt install mariadb-client-10.3
4. sudo mysql -u root
5. USE mysql;
6. SELECT User, Host, plugin FROM mysql.user;
7. 그럼 plugin에 unix_socket로 되어있다.(아님 auth_socket?)
8. update user set plugin='mysql_native_password' where user='root';
9. flush privileges;
10. select user, host, plugin from user;
11. eixit; 로 나가준다.
12. 그래도 안되기 때문에 또 sudo mysql
13. set password = password('비밀번호');
14. flush privileges;
15. exit
16. mysql -u root -p를 하고 비밀번호를 입력하면 된다.

뒤에를 나중에 해서 이것만 해도 되는 줄 알고 앞에걸 다시 원상복귀 해놨더니 안되더라.

둘 다 해야하나봐...

그리고 heidiSQL를 가서 다시 열기를 누르면~ 성공!

그리고... database는 날아갔다...ㄸㄹㄹ

괜찮아... 어차피 별 거 없었다...



###### 문제4. 다른 컴퓨터에선 안된다...

저렇게 연결을 하고... 보안그룹에서 3307도 허용해서 내 컴퓨터에선 잘 되는데

역시 RDS를 사용해야하나봐...ㄸㄹㄹ

다시 그냥 후다닥 만들어준다...

이번엔 mysql work어쩌구 안쓰고 걍 heidisql 쓸래

몇 번째야!!!!!