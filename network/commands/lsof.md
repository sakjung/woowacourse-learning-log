## lsof (list open files)

- 어떤 프로그램들이 listening 하고 있는지 확인할 수 있는 linux programme
- `-i`옵션을 사용하면 internet sockets 만 list함
```
sudo lsof -i
```

## `printf` + `nc -l`

```
printf 'HTTP/1.1 302 Moved\r\nLocation: https://www.eff.org/' | nc -l 2345
```

- netcat을 활용해서 printf의 응답을 돌려줄 수 있다