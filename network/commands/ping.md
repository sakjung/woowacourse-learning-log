# Ping

- ping 명령어는 message를 보내기 이전에 host의 IP address를 먼저 look up 한다
```
ping yahoo.com
```

```
ping -c  7 yahoo.com

# send pings 7 times
```

- `ping google.com`을 했을 때 Host의 IP address가 항상 같지 않음
  google이 많은 IP address (more than one computer)를 사용하고 있기 때문