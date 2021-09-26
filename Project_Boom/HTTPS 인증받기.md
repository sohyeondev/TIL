> 도메인을 활용해보자



이미 예전에 받아놓은 무료 도메인 사용!

근데 ip에 연결하니까 'Invalid Host header'가 뜬다.

이건 react쪽을 수정해야하는데,

[client]-[node_modules]-[react-scripts]-[config]-[webpackDevServer.config.js]를 수정하면 된다.

```
disableHostCheck:
   !proxy || process.env.DANGEROUSLY_DISABLE_HOST_CHECK === 'true',
// Enable gzip compression of generated files.
```

원래 위에 처럼 되어 있는 코드를

```
disableHostCheck: true,
//  !proxy || process.env.DANGEROUSLY_DISABLE_HOST_CHECK === 'true',
// Enable gzip compression of generated files.
```

위에 있는 코드로 수정하고, 리액트를 종료시켰다가 다시 시작하면 완료!



> Let's encrypt를 사용해서 ssl인증서 발급받기

참고 : https://www.sunny-son.space/AWS/HTTPS%20%EC%84%A4%EC%A0%95%ED%95%98%EA%B8%B0/



#### 1.Nginx 설치&방화벽 설치(or AWS 사용)&웹서버 확인

이미 완료

skip



#### 2. 서버 블록 설정하기

###### 새로운 Nginx 서버블록을 만들기 위해 자신의 도메인 이름으로 된 폴더 생성

```
sudo mkdir -p /var/www/{도메인이름}/html
sudo chown -R $USER:$USER /var/www/{도메인이름}/html
sudo chmod -R 755 /var/www/{도메인이름}
```

###### 폴더 생성 후 [etc]-[nginx]-[sites-available]에 {도메인이름} 파일 생성

###### *** proxy_pass를 추가하여 리버스 프록시 설정

```
sudo vi /etc/nginx/sites-available/{도메인이름}

server {
        listen 80;
        listen [::]:80;

        root /var/www/{도메인이름}/html;
        index index.html index.htm index.nginx-debian.html;

        server_name {도메인이름} www.{도메인이름};

        location / {
                #try_files $uri $uri/ =404; 주석처리
                proxy_pass http://localhost:8080;
                proxy_set_header X-Real-IP $remote_addr;
                proxy_set_header X-Forward-For $proxy_add_x_forwarded_for;
                proxy_set_header Host $http_host;
        }

}
```

###### 위 파일을 [sites-enabled] 폴더에 연결

```
sudo ln -s /etc/nginx/sites-available/{도메인이름} /etc/nginx/sites-enabled/
```

###### [etc]-[nginx]-[nginx.conf]에서 해시 버킷 메모리 문제 해결

###### *** 주석 달려있는 곳에 주석 해제

```
...
http {
    ...
    server_names_hash_bucket_size 64;
    ...
}
...
```

###### Nginx 설정 잘됐는지 확인

```
sudo nginx -t

nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
```

###### 위와 같은 문구가 뜬다면 nginx restart

```
sudo service nginx restart
```



#### 3. Certbot 설치

###### 레포지토리 추가

```
sudo add-apt-repository ppa:cerbot/certbot
```

###### *** 잘된건진 모르겠으나 밑에와 같이 뜸, 중간에 한 번 Enter 침

```
 The PPA has been DEPRECATED.

To get up to date instructions on how to get certbot for your systems, please see https://certbot.eff.org/docs/install.html.
 More info: https://launchpad.net/~certbot/+archive/ubuntu/certbot
Press [ENTER] to continue or Ctrl-c to cancel adding it.

Hit:1 http://ap-northeast-2.ec2.archive.ubuntu.com/ubuntu focal InRelease
Get:2 http://ap-northeast-2.ec2.archive.ubuntu.com/ubuntu focal-updates InRelease [114 kB]
Get:3 http://ap-northeast-2.ec2.archive.ubuntu.com/ubuntu focal-backports InRelease [101 kB]  
Get:4 http://ap-northeast-2.ec2.archive.ubuntu.com/ubuntu focal-updates/main amd64 Packages [1221 kB]
Get:5 http://ap-northeast-2.ec2.archive.ubuntu.com/ubuntu focal-updates/universe amd64 Packages [855 kB]
Get:6 http://security.ubuntu.com/ubuntu focal-security InRelease [114 kB]                     
Ign:7 http://ppa.launchpad.net/certbot/certbot/ubuntu focal InRelease                         
Err:8 http://ppa.launchpad.net/certbot/certbot/ubuntu focal Release                 
  404  Not Found [IP: 91.189.95.85 80]
Reading package lists... Done      
E: The repository 'http://ppa.launchpad.net/certbot/certbot/ubuntu focal Release' does not have a Release file.
N: Updating from such a repository can't be done securely, and is therefore disabled by default.
N: See apt-secure(8) manpage for repository creation and user configuration details.
```

###### 설치하기

###### *** sudo apt로 하면 안되고 sudo apt-get으로 해야 됨

```
sudo apt-get install python3-certbot-nginx
```



#### 4. SSL 인증서 가져오기

```
sudo certbot --nginx -d {도메인이름}
```

###### 이메일 주소 입력 후, 서비스 약관에 동의

```
Saving debug log to /var/log/letsencrypt/letsencrypt.log
Plugins selected: Authenticator nginx, Installer nginx
Enter email address (used for urgent renewal and security notices) (Enter 'c' to
cancel): {이메일 주소}

- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
Please read the Terms of Service at
https://letsencrypt.org/documents/LE-SA-v1.2-November-15-2017.pdf. You must
agree in order to register with the ACME server at
https://acme-v02.api.letsencrypt.org/directory
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
(A)gree/(C)ancel: a

- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
Would you be willing to share your email address with the Electronic Frontier
Foundation, a founding partner of the Let's Encrypt project and the non-profit
organization that develops Certbot? We'd like to send you email about our work
encrypting the web, EFF news, campaigns, and ways to support digital freedom.
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
(Y)es/(N)o: y
```

###### https를 어떻게 설정할지

###### *** 1번: 리다이렉트 없음

###### *** 2번: 모든 http 연결을 https로 리다이렉트(선택)

```
Obtaining a new certificate
Performing the following challenges:
http-01 challenge for {도메인이름}
Waiting for verification...
Cleaning up challenges
Deploying Certificate to VirtualHost /etc/nginx/sites-enabled/{도메인이름}

Please choose whether or not to redirect HTTP traffic to HTTPS, removing HTTP access.
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
1: No redirect - Make no further changes to the webserver configuration.
2: Redirect - Make all requests redirect to secure HTTPS access. Choose this for
new sites, or if you're confident your site works on HTTPS. You can undo this
change by editing your web server's configuration.
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
Select the appropriate number [1-2] then [enter] (press 'c' to cancel): 2
```

###### 발급되면 밑에와 같이 나옴

###### *** IMPORTANT NOTES: 이후 내용에 pem 위치가 나와있음.

```
Congratulations! You have successfully enabled
https://{도메인이름}

You should test your configuration at:
https://www.ssllabs.com/ssltest/analyze.html?d={도메인이름}
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -

IMPORTANT NOTES:
```



#### 5. SSL 자동으로 갱신시키기

###### Let's Encrypt의 인증서는 90일 동안만 유효하기 때문에 그 이후 갱신해줘야 함.

###### *** 자동 갱신

```
sudo certbot renew --dry-run
```

###### *** 수동 갱신

```
sudo certbot renew
```



> pem 파일에 접근 권한을 줘보자



ssh 인증서를 발급받고 node 서버를 실행하면 Error: EACCES: permission denied, open '/etc/letsencrypt/live/{도메인이름}/privkey.pem' 이런 에러가 뜬다.

구글에 검색을 하면 여러 방법을 제시하고 마지막 4번째로 하라고 하는데(보안 문제로)

참고: https://stackoverflow.com/questions/48078083/lets-encrypt-ssl-couldnt-start-by-error-eacces-permission-denied-open-et

```
// Create group with root and nodeuser as members
$ sudo addgroup nodecert
$ sudo adduser nodeuser nodecert
$ sudo adduser root nodecert

// Make the relevant letsencrypt folders owned by said group.
$ sudo chgrp nodecert /etc/letsencrypt/live
$ sudo chgrp nodecert /etc/letsencrypt/archive

// Allow group to open relevant folders
$ sudo chmod 710 /etc/letsencrypt/live
$ sudo chmod 710 /etc/letsencrypt/archive
```

나는 저기서 nodeuser를 ubuntu로 하고 권한을 줬는데도 다 안됨...

ubuntu로 들어가서 확인해보면 권한이 없다고 나온다.

```
chown -R ubuntu:ubuntu letsensrypt/

chown: cannot read directory 'letsencrypt/keys': Permission denied
chown: cannot read directory 'letsencrypt/archive': Permission denied
```

위와 같은 명령어들이 뜸...

그래서 그냥 임시방편이지만 첫번째 방법으로 하기로 했다.

일단 root 권한으로 들어가고

```
su

"su: Authentication failure" //에러, root 비밀번호 설정으로 해결

sodu passwd root

su //비밀번호 치고 들어가면 잘 됨
```

```
cd /etc/
chown -R ubuntu:ubunut letsencrypt/
cd letsensrypt
ll //권한확인하면 root에서 ubuntu로 바뀌어있고 다 잘됨
```



> nginx 환경설정



위에처럼 했는데 nginx가 실행이 안되는 문제가 발생했다.

환경설정 파일이 잘못되어있던건데,

그래서 일단 default파일을 위치를 옮기고 아까 설정한 {도메인이름} 파일에 환경설정을 해줌

```
cd /etc/nginx/sites-enabled/
sudo mv default ../default.back
```

```
sudo vim {도메인이름}

server {
        listen 80;
        listen [::]:80;

        root /var/www/{도메인이름}/html;
        index index.html index.htm index.nginx-debian.html;

        server_name {도메인이름} www.{도메인이름};

        location / {
                #try_files $uri $uri/ =404; 주석처리
                proxy_pass http://localhost:8080;
                proxy_set_header X-Real-IP $remote_addr;
                proxy_set_header X-Forward-For $proxy_add_x_forwarded_for;
                proxy_set_header Host $http_host;
        }

}
```

위에를 아래로 변경

###### *** 바뀐 부분

proxy_pass http://localhost:8080; > proxy_pass http://localhost:3000; (내가 사용하는 포트)

그리고 ssl 관련해서 추가해주면 끝!

```
server {
        listen 80;
        listen [::]:80;

        listen 443 ssl;

        root /var/www/{도메인이름}/html;
        index index.html index.htm index.nginx-debian.html;

        server_name {도메인이름} www.{도메인이름};

        error_page 497 https:/$server_name$request_uri;
        ssl on;
        ssl_certificate /etc/letsencrypt/live/{도메인이름}/fullchain.pem;
        ssl_certificate_key /etc/letsencrypt/live/{도메인이름}/privkey.pem;
        ssl_prefer_server_ciphers on;

        location / {
                #try_files $uri $uri/ =404; 주석처리
                proxy_pass http://localhost:3000;
                proxy_set_header X-Real-IP $remote_addr;
                proxy_set_header X-Forward-For $proxy_add_x_forwarded_for;
                proxy_set_header Host $http_host;
        }

}
```



WebSocket 관련해서 또  에러가 났다.

WebSocket connection to 'wss://{도메인이름}/sockjs-node' failed:

위와 같은 에러였는데 이것도 그냥 환경설정에서 몇 줄 추가해서 성공

```
server {
        listen 80;
        listen [::]:80;

        listen 443 ssl;

        root /var/www/boomtest.ga/html;
        index index.html index.htm index.nginx-debian.html;

        server_name boomtest.ga www.boomtest.ga;

        error_page 497 https:/$server_name$request_uri;
        ssl on;
        ssl_certificate /etc/letsencrypt/live/boomtest.ga/fullchain.pem;
        ssl_certificate_key /etc/letsencrypt/live/boomtest.ga/privkey.pem;
        ssl_prefer_server_ciphers on;

        location / {
                #try_files $uri $uri/ =404; 주석처리
                proxy_pass http://localhost:3000;
                proxy_set_header X-Real-IP $remote_addr;
                proxy_set_header X-Forward-For $proxy_add_x_forwarded_for;
                proxy_set_header Host $host;
                proxy_set_header Upgrade $http_upgrade;
                proxy_set_header Connection 'upgrade';
        }
}
```

최종 환경설정...

그리고 환경설정 바꾸면 무조건 다시 시작해야한다.

```
sudo service nginx restart
```
