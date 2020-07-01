# DNS Configuration
DNS Server: `<DNS Server>`

## DNS 구축

### Linux - bind9
https://bind9.readthedocs.io/en/latest/

```sh
$ sudo apt-get install bind9
```

## DNS 설정

### Linux Server - Ubuntu 18.04 기준
* `/etc/systemd/resolved.conf` 의 DNS 설정을 변경합니다.
```sh
$ cat /etc/systemd/resolved.conf | sed 's/#DNS=/DNS=<DNS Server>/' | sudo tee /etc/systemd/resolved.conf
```

* `systemd-resolved.service` 를 재시작합니다.
```sh
$ systemctl restart systemd-resolved.service
```

* 변경된 DNS 설정을 확인합니다.
```sh
$ systemd-resolve --status | grep 'DNS Servers'
```

### MacOS - Catalina 기준
`시스템 환경설정 > 네트워크 > 고급 > DNS` 에서 `<DNS Server>` 를 DNS 서버로 추가/수정합니다.

### Windows - 10 기준
`네트워크 > 속성 > 이더넷 > 인터넷 프로토콜 버전 4(TCP/IPv4)` 에서 `다음 DNS 서버 주소 사용`의 `기본 설정 DNS 서버`를 `<DNS Server>` 로 변경합니다.
