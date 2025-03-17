# Linux 환경에서 Crontab을 통해 환경 정보 수집하기

- ## /var/log 디렉터리에 어떤 정보가 저장될까?

### CentOS

- /var/log/message - 다음을 제외한 모든 syslog 메시지

- /var/log/secure - 보안 및 인증 관련 메시지 및 오류

- /var/log/maillog - 메일 서버 관련 메시지 및 오류

- /var/log/cron - 주기적으로 실행한 작업과 관련된 로그 파일

- /var/log/boot.log - 시스템 시작과 관련된 로그 파일

### Ubuntu

- /var/log/syslog - 일반적인 시스템 로그

- /var/log/auth.log - 인증 및 보안 관련 로그


## Error 로그 수집하기
- syslog에서 error 확인해보기
```
cat /var/log/syslog | grep "error"
```
- monitor를 위해 shell 작성
```
vi /usr/local/bin/log_monitor.sh
```
- 중복 내용 없이 안 읽은 부불에 대해서만 로그 읽어오기
```
#!/bin/bash
LOG_FILE="/var/log/syslog"
LOG_HISTORY="/var/log/monitoring/syslog_position"
LOG_OUTPUT="/var/log/monitoring/syslog_errors.log"

# 마지막으로 읽은 줄 번호 가져오기
LAST_LINE=$(cat "$LOG_HISTORY" 2>/dev/null || echo 0)

# 신규 로그 검색 후 저장
awk "NR>$LAST_LINE" "$LOG_FILE" | grep -i "ERROR" >> "$LOG_OUTPUT"

# 현재 로그 파일의 마지막 줄 번호 저장
wc -l < "$LOG_FILE" > "$LOG_HISTORY"

```

- 1분마다 로그 기록 수집

```
crontab -e

* * * * * /usr/local/bin/log_monitor.sh
```

- 에러 로그 생성하기
```
logger -p user.err "TEST ERROR: This is a simulated error for testing"


for i in {1..5}; do logger -p user.err "TEST ERROR #$i: Simulated error log"; sleep 1; done
```
- 결과 화면

![alt text](image.png)

## 로그인 정보 로그 수집하기

## CPU USAGE 정보 수집하기