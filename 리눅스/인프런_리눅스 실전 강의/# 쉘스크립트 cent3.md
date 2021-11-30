	ü storage_backup.sh
#!/bin/bash

# 변수 설정

HOST="$(/usr/bin/hostname)"
LOG="/tmp/backup.log"
PUSH="/root/SHELL/monitor/telegram_example.sh"
DATE="$(/bin/date +%Y.%m.%d)"
## 백업할 디렉토리와 파일 지정
BAK_LIST="/etc/exports /etc/nfs.conf /etc/nfsmount.conf"
## 백업 디렉토리
BAK_PATH="/mnt/BACKUP/${HOST}"
## 백업 파일명
BAK_FILE="${BAK_PATH}/${DATE}_${HOST}.tgz"

# 스토리지에 마운트

/usr/bin/mount /mnt

# 로그 파일 생성

/usr/bin/touch "${LOG}"

# 백업 디렉토리 확인

if [ -e "${BAK_PATH}" ];
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
        ### p : 퍼미션 유지 P : 절대 경로 유지
        /usr/bin/tar czpPf "${BAK_FILE}" ${BAK_LIST}
        ## 백업 파일 정보
        NAME="$(/usr/bin/ls -al "${BAK_FILE}" | awk '{print $9}')"
        SIZE="$(/usr/bin/ls -al "${BAK_FILE}" | awk '{print $5}')"
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

# 로그 파일 삭제

/usr/bin/rm -f "${LOG}"

	ü backup_mon.sh
#!/bin/bash

## 변수 지정
TODAY=$(/bin/date +%Y.%m.%d)
PUSH="/root/SHELL/monitor/telegram_example.sh"
LOG="/tmp/bak_report"
HOST=$(hostname)
WEB="/nfs/BACKUP/cent1/*.tgz"
DB1="/nfs/BACKUP/cent2/*cent2.tgz"
DB2="/nfs/BACKUP/cent2/*DB.tgz"
STR="/nfs/BACKUP/cent3/*.tgz"
WBK_CNT=$(/usr/bin/ls -1 ${WEB} | /usr/bin/wc -l)
DBK_CNT1=$(/usr/bin/ls -1 ${DB1} | /usr/bin/wc -l)
DBK_CNT2=$(/usr/bin/ls -1 ${DB2} | /usr/bin/wc -l)
SBK_CNT=$(/usr/bin/ls -1 ${STR} | /usr/bin/wc -l)

## 함수 정의
# 인자를 두 개를 받아 비교
# 인자1 : 백업이름
# 인자2 : 백업카운트

function chk_cnt() {
        # 인자를 변수에 할당
        NAME="$1"
        CNT="$2"

        # 백업카운트(${CNT})가 8이 아닐 경우 확인하도록 메세지
        # 8이 맞을 경우 문제가 없다는 메세지 출력
        if [ "${CNT}" -eq 8 ]
        then
                /usr/bin/echo "${NAME} 백업파일 문제 없음."
                /usr/bin/echo
        else
                /usr/bin/echo "${NAME} 백업파일이 8개가 아닙니다! 확인하세요!"
                /usr/bin/echo
        fi
}

## 레포팅 파일 초기화

/usr/bin/rm -f "${LOG}"
/usr/bin/touch "${LOG}"

## 레포트 메세지 작성
{
        /usr/bin/echo
        /usr/bin/echo "========================"
        /usr/bin/echo "       백업 확인        "
        /usr/bin/echo "========================"
        /usr/bin/echo
        /usr/bin/echo "웹서버 백업파일 : "
        /usr/bin/ls -1 ${WEB} | /usr/bin/grep "${TODAY}"
        /usr/bin/echo
        /usr/bin/echo "DB서버 백업파일 : "
        /usr/bin/ls -1 ${DB1} | /usr/bin/grep "${TODAY}"
        /usr/bin/ls -1 ${DB2} | /usr/bin/grep "${TODAY}"
        /usr/bin/echo
        /usr/bin/echo "Storage서버 백업파일 : "
        /usr/bin/ls -1 ${STR} | /usr/bin/grep "${TODAY}"
        /usr/bin/echo

        # 백업 별 파일숫자 확인
        chk_cnt Web_System "${WBK_CNT}"
        chk_cnt DB_System "${DBK_CNT1}"
        chk_cnt DB_Dump "${DBK_CNT2}"
        chk_cnt STORAGE_System "${SBK_CNT}"
}>|"${LOG}"

## 텔레그램으로 레포트 메세지를 보냄

"${PUSH}" "${HOST}" "$(/usr/bin/cat "${LOG}")"

	ü log_mon.sh
#!/bin/bash

# 하는 일 : 로그 디렉토리의 용량을 감시
# 1. 로그 디렉토리의 크기를 확인
# 2. 크기가 1기가 이상일 경우 관리자에게 알림
# 3. 1기가 미만일 경우 아무 것도 안함

DIR="/var/log/nginx"
SIZE=$(du -m /var/log/nginx | awk '{print $1}')
HOST="$(hostname)"
echo ${SIZE}

if [ ${SIZE} -ge 1024 ]
then
        TEXT="${DIR} 사용량이 1기가가 넘었습니다!"
        /root/SHELL/monitor/telegram_example.sh "${HOST}" "${TEXT}"
else
        TEXT="${DIR} 사용량은 \"${SIZE}\"입니다!"
        /root/SHELL/monitor/telegram_example.sh "${HOST}" "${TEXT}"
fi

	ü part.sh
#!/bin/bash

# 파티션별 사용량을 확인해서 80% 이상인 파티션이 있으면 관리자에게 메세지를 보냄
# 1. 파티션별 사용량 확인
# 2. 크기를 비교해서 처리
# 3. use가 80% 이상이면 관리자에게 메세지
# 4. 80% 미만이면 아무것도 안함

# TEXT 변수에 보낼 메세지를 작성
TEXT="$(df -h |\
        awk '{
                gsub("%","");
                USE=$5;
                MNT=$6;
                if ( USE > 30 )
                        print MNT, "파티션이 ", USE, "%를 사용 중 입니다."
                }' |\
        grep -v '^[A-Z]')"
HOST="$(hostname)"
# 80% 이상 디스크를 사용하는 파티션이 있을 경우
# TEXT 변수의 내용(메세지)를 관리자에게 보냄
if [ ${#TEXT} -gt 1 ]
then
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
# 스토리지서버 백업
00 04 * * * /root/SHELL/BACKUP/storage_backup.sh > /dev/null 2>&1

# 백업 감시
30 10 * * * /root/SHELL/monitor/backup_mon.sh > /dev/null 2>&1
![image](https://user-images.githubusercontent.com/85976426/144036125-d5780adb-65fe-4be3-8918-f2a29005ca30.png)
