# TCP/IP model (IETF model)

| Layer | Protocols | Concepts |
|:----:|:-----:|:---------:|
|Application|	HTTP, SSH|	URLs, passwords, head commands, server headers, web pages|
|Transport	|TCP, UDP|	port numbers, sessions|
|Internet	|IP	| IP addresses, routes|
|Hardware (Network Access)|	wifi, ethernet, DSL lines |	signal strength, access points|

- layers above hardware does not need to know the things like strength of wireless signal
- each layer depends on the layer one below it and provides particular guarantees to the layer above it
- 서로에게 API를 주고 있는 셈
- 층 별로 관심사를 나눠서 층 마다의 feature를 재사용 가능하게끔 만들어 놓음
- 예를 들어, HTTP와 SSH는 공통적으로 ‘어떻게 서버에 connection을 만들 것인가’라는 부분이 필요함
- 이러한 필요는 TCP laeyer에 bundled 되어 있음
- IP는 Internet layer에서 단독적으로 존재