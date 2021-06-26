# nc (net cat)
- 다른 인터넷 서비스와 수동으로 통신할 수 있는 command line 명령어
- 포트 연결 -> string 전송
- this wrapper over TCP

```
e.g.

printf 'HEAD / HTTP/1.1\r\nHost: en.wikipedia.org\r\n\r\n' | nc en.wikipedia.org 80
```

## reset packet (RST)
- 어떤 응답할 서버도 없을 때 돌려주는 error message

## fetching content

If you would like to save the results of an nc command to a file, you can do this with the `>` shell redirection operator. For instance, this will save the results to the file example.txt:

```
printf "GET / HTTP/1.1\r\nHost: www.example.com\r\n\r\n" | nc www.example.com 80 > example.txt
```

## printf
- `echo`와 유사하게 string을 출력해준다
- `echo`는 `printf` 와는 달리 `\n`과 같은 줄바꿈 특수문자를 해석해주지 않는다는 차이점이 있음

## pipe (|)
- 파이프 이전의 output을 파이프 이후의 input으로 넣어주고 싶을 때 사용
- 위의 예제에서는 printf 의 출력문을 nc의 input으로 넣어주기 위해서 pipe 사용

## What happens if you send a header you just made up?
- 정상적으로 response가 옴

```
$ printf 'HEAD / HTTP/1.1\r\nHost: en.wikipedia.org\r\nBitch: wow\r\n\r\n' | nc en.wikipedia.org 80

HTTP/1.1 301 TLS Redirect
Date: Sat, 26 Jun 2021 08:12:57 GMT
Server: Varnish
X-Varnish: 319164557
X-Cache: cp5008 int
X-Cache-Status: int-front
Server-Timing: cache;desc="int-front", host;desc="cp5008"
Permissions-Policy: interest-cohort=()
Set-Cookie: WMF-Last-Access=26-Jun-2021;Path=/;HttpOnly;secure;Expires=Wed, 28 Jul 2021 00:00:00 GMT
Set-Cookie: WMF-Last-Access-Global=26-Jun-2021;Path=/;Domain=.wikipedia.org;HttpOnly;secure;Expires=Wed, 28 Jul 2021 00:00:00 GMT
X-Analytics: public_cloud=1
X-Client-IP: 15.164.171.254
Location: https://en.wikipedia.org/
Content-Length: 0
Connection: keep-alive
```

## What happens if you send an HTTP verb you just made up, instead of GET or HEAD or POST or the other real ones?
- 405 Method not allowed error 발생
```
$ printf 'WOW / HTTP/1.1\r\nHost: en.wikipedia.org\r\nBitch: wow\r\n\r\n' | nc en.wikipedia.org 80

HTTP/1.1 405 Method not allowed
Date: Sat, 26 Jun 2021 08:13:14 GMT
Server: Varnish
X-Varnish: 291587701
X-Cache: cp5008 int
X-Cache-Status: int-front
Server-Timing: cache;desc="int-front", host;desc="cp5008"
Permissions-Policy: interest-cohort=()
Set-Cookie: WMF-Last-Access=26-Jun-2021;Path=/;HttpOnly;secure;Expires=Wed, 28 Jul 2021 00:00:00 GMT
Set-Cookie: WMF-Last-Access-Global=26-Jun-2021;Path=/;Domain=.wikipedia.org;HttpOnly;secure;Expires=Wed, 28 Jul 2021 00:00:00 GMT
X-Analytics: public_cloud=1
X-Client-IP: 15.164.171.254
Content-Type: text/html; charset=utf-8
Content-Length: 1823
Connection: keep-alive

<!DOCTYPE html>
<html lang="en">
<meta charset="utf-8">
<title>Wikimedia Error</title>
<style>
* { margin: 0; padding: 0; }
body { background: #fff; font: 15px/1.6 sans-serif; color: #333; }
.content { margin: 7% auto 0; padding: 2em 1em 1em; max-width: 640px; }
.footer { clear: both; margin-top: 14%; border-top: 1px solid #e5e5e5; background: #f9f9f9; padding: 2em 0; font-size: 0.8em; text-align: center; }
img { float: left; margin: 0 2em 2em 0; }
a img { border: 0; }
h1 { margin-top: 1em; font-size: 1.2em; }
.content-text { overflow: hidden; overflow-wrap: break-word; word-wrap: break-word; -webkit-hyphens: auto; -moz-hyphens: auto; -ms-hyphens: auto; hyphens: auto; }
p { margin: 0.7em 0 1em 0; }
a { color: #0645ad; text-decoration: none; }
a:hover { text-decoration: underline; }
code { font-family: sans-serif; }
.text-muted { color: #777; }
</style>
<div class="content" role="main">
<a href="https://www.wikimedia.org"><img src="https://www.wikimedia.org/static/images/wmf-logo.png" srcset="https://www.wikimedia.org/static/images/wmf-logo-2x.png 2x" alt="Wikimedia" width="135" height="101">
</a>
<h1>Error</h1>
<div class="content-text">
<p>Our servers are currently under maintenance or experiencing a technical problem.

Please <a href="" title="Reload this page" onclick="window.location.reload(false); return false">try again</a> in a few&nbsp;minutes.</p>

<p>See the error message at the bottom of this page for more&nbsp;information.</p>
</div>
</div>
<div class="footer"><p>If you report this error to the Wikimedia System Administrators, please include the details below.</p><p class="text-muted"><code>Request from 15.164.171.254 via cp5008 cp5008, Varnish XID 291587701<br>Upstream caches: cp5008 int<br>Error: 405, Method not allowed at Sat, 26 Jun 2021 08:13:14 GMT</code></p>
</div>
</html>
```
