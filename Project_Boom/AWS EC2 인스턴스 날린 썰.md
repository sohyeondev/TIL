> AWS EC2 인스턴스는 종료XXXXX 중지OOOOO



AWS EC2는 계속 실행중이면 돈이 티끌모아 태산이 될 수 있어서 그때그때 실행하고 중지한다.

근데 중지를 누르는게 아니라 종료를 누르면...?

그대로...



이번에 한 번 그렇게 날려서... 처음부터 다시 했다.

또 날릴 수도 있으니까 대충의 과정을 써놔야지.



1. AWS EC2 인스턴스 생성(pem키는 'Users/User/.ssh'에 다운받아 놓는 것 추천)

2. 탄력적IP는 다시 할당 받을 것! (VScode에 원격 연결하려니 그 전이랑 겹쳐서 그런지는 몰라도 연결이 안됨.)

   1. 이미 존재하는 탄력적IP 주소 릴리스 해제
   2. 탄력적 IP 주소 할당
   3. 탄력적 IP 주소 연결
   4. 새로 만든 인스턴스 선택

3. Termius에 연결해놓기

   1. New Host 클릭
   2. Label : 내 마음대로 이름 정하기
   3. Address : 할당받은 탄력적 IP 주소
   4. Username : 인스턴스 username(ubuntu로 연결하면 ubuntu가 디폴트)
   5. Keys : +KEY를 눌러서 다운받은 pem키 추가

4. Termius에 접속해서 셋팅하기

   1. ```
      sudo apt-get update
      ```

   2. ```
      sudo apt-get install nginx
      ```

   3. ```
      sudo apt-get isntall nodejs
      ```

   4. ```
      sudo apt-get install npm
      ```

   5. ```
      sudo apt-get install mariadb
      ```

5. VScode 연결

   1. VScode에서 ctrl+shift+x(확장탭)

   2. 'Remote Development' 설치

   3. F1을 눌러 SSH 검색

   4. 'Remote-SSH: Connect to Host...' 선택

   5. 'Configure SSH Hosts...' 선택

   6. 'C:\Users\ {username}\ .ssh\config' 선택

   7. 파일 수정

      ```
      Host 아무거나
      	HostName 퍼블릭 IPv4 DNS
      	User 인스턴스 username
      	IdentityFile ~/.ssh/key이름.pem(key 경로)
      ```

   8. F1을 눌러 'Remote-SSH:Connect to Host...' 선택

   9. config 파일의 Host에 적힌 이름을 누르면 연결 완료

6. VScode에서 파일 수정

   1. DB 연결하기

      ```
      var connection = mysql.createConnection({
      	host: 'RDS 엔드포인트',
      	user: 'RDS user',
      	password: 'RDS pw',
      	database: 'DB 이름',
      	port: '3306',
      });
      ```
