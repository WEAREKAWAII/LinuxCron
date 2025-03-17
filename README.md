# Linux 환경에서 Crontab을 통해 환경 정보 수집하기

## Error 로그 수집하기

```
cat /var/log/syslog | grep "error"
```

```
vi /usr/local/bin/log_monitor.sh
```

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