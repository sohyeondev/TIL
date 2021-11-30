	ü db_backup.sh
#!/bin/bash
# set -exuo pipefail

# 변수 설정

HOST="$(/usr/bin/hostname)"
LOG="/tmp/backup.log"
PUSH="/root/SHELL/monitor/telegram_example.sh"
DATE="$(/bin/date +%Y.%m.%d)"
## 백업할 디렉토리와 파일 지정
BAK_LIST="/etc/my.cnf.d"
## 백업 디렉토리
BAK_PATH="/mnt/BACKUP/${HOST}"
## 백업 파일명
BAK_FILE="${BAK_PATH}/${DATE}_${HOST}.tgz"
## DB 백업 데릭토리
DB_BAK_PATH="/root/SHELL/BACKUP/xtrabackup_backupfiles"
## DB 백업 파일명
DB_BAK_FILE="${BAK_PATH}/${DATE}_${HOST}_DB.tgz"

# 스토리지에 마운트

/usr/bin/mount /mnt

# 로그 파일 생성

/usr/bin/touch "${LOG}"

# 백업 디렉토리 확인

if [ -e "${BAK_PATH}" ]
then
        ## 백업 디렉토리가 존재한다면
        /bin/echo "백업 디렉토리가 존재합니다."
else
        ## 백업 디렉토리가 없으면 생성
        /usr/bin/mkdir -p "${BAK_PATH}"
fi

# --- 로그 기록 시작
{

        /bin/echo
        /bin/echo "=== 백업 시작 시각 : "
        /bin/date
        /bin/echo

        ## 이전 백업 파일 삭제
        /bin/echo "=== 이전 백업 파일 삭제 : "
        /bin/echo
        /usr/bin/find ${BAK_PATH} -mtime +7 -exec sh -c "ls -1 {}; rm {}" \;
        /bin/echo

        ## 백업
        ### DB dump
        /usr/bin/mariabackup \
                --backup \
                --no-lock \
                -u root \
                --target-dir="${DB_BAK_PATH}"

        ### DB apply logs
        /usr/bin/mariabackup \
                --prepare \
                --target-dir="${DB_BAK_PATH}"

        ### p : 퍼미션 유지 P : 절대 경로 유지
        /usr/bin/tar czpPf "${BAK_FILE}" ${BAK_LIST}
        ### DB 백업 디렉토리 압축
        /usr/bin/tar czpPf "${DB_BAK_FILE}" ${DB_BAK_PATH}

        ## 백업 파일 정보
        NAME="$(/usr/bin/ls -al "${BAK_FILE}" | awk '{print $9}')"
        SIZE="$(/usr/bin/ls -al "${BAK_FILE}" | awk '{print $5}')"
        /bin/echo "=== 백업 파일 정보 : "
        /bin/echo " | 파일명 : ${NAME}"
        /bin/echo " | 파일크기 : ${SIZE}"
        /bin/echo

        ## DB 백업 파일 정보
        NAME="$(/usr/bin/ls -al "${DB_BAK_FILE}" | awk '{print $9}')"
        SIZE="$(/usr/bin/ls -al "${DB_BAK_FILE}" | awk '{print $5}')"
        /bin/echo "=== 백업 파일 정보 : "
        /bin/echo " | 파일명 : ${NAME}"
        /bin/echo " | 파일크기 : ${SIZE}"
        /bin/echo

        /bin/echo "=== 백업 종료 시각 : "
        /bin/date
        /bin/echo

}>|"${LOG}"
# --- 로그 기록 끝

# 스토리지에 언마운트

/usr/bin/umount /mnt

# 텔레그램으로 백업 로그를 전송

"${PUSH}" "${HOST}" "$(/usr/bin/cat "${LOG}")"

# 로그 파일 및 데이터베이스 백업 파일 삭제

/usr/bin/rm -rf "${LOG}" "${DB_BAK_PATH}"

	ü mnt_mon.sh
#!/bin/bash

# 하는 일 : /mnt 디렉토리의 용량을 감시
# 1. /mnt 디렉토리의 크기를 확인
# 2. 크기가 1기가 이상일 경우 관리자에게 알림
# 3. 1기가 미만일 경우 아무 것도 안함

DIR="/mnt"
SIZE=$(du -m ${DIR} | awk '{print $1}')
HOST="$(hostname)"
echo ${SIZE}

if [ ${SIZE} -ge 1024 ]
then
        TEXT="${DIR} 사용량이 1기가가 넘었습니다!"
        /root/SHELL/monitor/telegram_example.sh "${HOST}" "${TEXT}"
fi

	ü telegram_example.sh
#!/bin/bash

## telegram bot으로 메세지를 보내는 쉘 스크립트
## 2개의 파라미터가 필요함
## 파라미터가 두개가 안될 경우 사용방법을 출력하고 스크립트를 종료
## 1. 서버 호스트이름
## 2. 메세지
## 실행 결과는 현재 날짜/시각, 서버이름, 지정한 메세지를 텔레그램으로 보냄

# 파라미터 확인
if [ $# -ne 2 ]
then
        echo
        echo "Usage "
        echo "$0 {HOSTNAME} {MESSAGES}"
        echo
        echo "example) "
        echo "$0 \"cent1\" \"/var/log/nginx 파티션을 확인하세요\""
        echo
        exit 0
fi

# 텔레그램 봇 관련 정보
ID="2078744784"
API_TOKEN="2130909493:AAEXdGjNrAO3PK7dLMasBN1RB8ainrnFr9E"
URL="https://api.telegram.org/bot${API_TOKEN}/sendMessage"

# 날짜
DATE="$(date "+%Y-%m-%d %H:%M")"

# 보낼 메세지 작성
TEXT="${DATE} {$1} $2"

# 메세지 보내기
curl -s -d "chat_id=${ID}&text=${TEXT}" ${URL} > /bin/nul

	ü crontab -l
# DB서버 백업
00 04 * * * /root/SHELL/BACKUP/db_backup.sh > /dev/null 2>&1

# /mnt 로컬디스크 감시
00 05 * * * /root/SHELL/monitor/mnt_mon.sh > /dev/null 2>&1
![image](https://user-images.githubusercontent.com/85976426/144036059-13efc9ea-5c05-4c0f-b531-8b175e58cd6c.png)
