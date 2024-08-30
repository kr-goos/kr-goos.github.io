---
title: "[Nginx] SSL 인증서 생성 및 설정"
description: Ubuntu에서 Nginx를 위한 자체 서명 SSL 인증서를 만들고 적용해 보겠습니다.
author: 김우석
date: 2024-08-30 12:45:00 +0900
categories: [Nginx, SSL]
tags: [Nginx, Ubuntu, SSL]
image:
  path: /assets/img/posts/nginx/nginx.png
---

## 1. OpenSSL을 사용한 자체 서명된 인증서 생성

### 1.1 개인 키(Private Key) 생성
- 서버에서 사용할 개인키를 생성

```bash
openssl genpkey -algorithm RSA -out server.key -aes256
```

- `-aes256` : 암호화 방법을 지정하며, 비밀번호 입력을 요구 (생략 하고 싶은 경우 해당 옵션을 제거)


### 1.2 CSR(Certificate Signing Request) 생성
- 인증서를 서명하기 위해 필요한 정보를 포함하는 파일

```bash
openssl req -new -key server.key -out server.csr
```

- 개인키 생성 시 `-aes256`옵션을 추가한 경우 위에서 입력한 비밀번호를 입력해야 함
- 도메인 이름, 조직 정보 등을 물어보지만, 빈 값(`Enter`)으로 입력할 수 있음


### 1.3 자체 서명된 인증서 생성
- CSR을 사용하여 자체 서명된 인증서를 생성
```bash
openssl x509 -req -days 365 -in server.csr -signkey server.key -out server.crt
```
- CSR 파일과 개인키를 사용하여 `server.crt`라는 인증서를 생성(365일 동안 유효)


## 2. Nginx 구성 파일 수정
- `/etc/nginx/sites-available/default` 또는 `/etc/nginx/nginx.conf` 파일을 수정하여 SSL 설정

```nginx
server {
    listen 443 ssl;
    server_name your_domain_or_IP;

    ssl_certificate /path/to/server.crt;
    ssl_certificate_key /path/to/server.key;

    ssl_protocols TLSv1.2 TLSv1.3;

    location / {
        root /var/www/html;
        index index.html;
    }
}

server {
    listen 80;
    server_name your_domain_or_IP;

    # Redirect HTTP to HTTPS
    return 301 https://$host$request_uri;
}
```

- `ssl_certificate` : 인증서 파일의 경로를 지정
- `ssl_certificate_key` : 개인 키 파일의 경로를 지정


## 3. Nginx 설정 테스트 및 재시작
- 설정 파일에 오류가 없는지 테스트 (설정 파일의 문법을 확인)
    - ```bash
      sudo nginx -t
      ```
    - 결과 확인
        - ```bash
          nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
          nginx: configuration file /etc/nginx/nginx.conf test is successful
          ```
- Nginx 재시작
    - ```bash
      sudo systemctl restart nginx
      ```
    - 아래와 같은 에러가 발생할 경우
        - ```bash
          Job for nginx.service failed because the control process exited with error code.
          See "systemctl status nginx.service" and "journalctl -xeu nginx.service" for details.
          ```
        - 로그 확인 (`vi /var/log/nginx/error.log`)
            - ```bash
              cannot load certificate key "/home/server.key": PEM_read_bio_PrivateKey() failed (SSL:  error:1400006B:UI routines::processing error:while reading strings error:0480006D:PEM routines::problems getting password error:07880109:common libcrypto routines::interrupted or cancelled error:07880109:common libcrypto routines::interrupted or cancelled error:1C80009F:Provider routines::unable to get passphrase error:1400006B:UI routines::processing error:while reading strings error:0480006D:PEM routines::problems getting password error:07880109:common libcrypto routines::interrupted or cancelled error:04800068:PEM routines::bad password read)
              ```
            - 위와 같은 에러가 확인되는 경우, 해당 오류 메시지는 Nginx가 SSL 인증서의 개인 키를 읽으려 할 때 발생한 문제
                - 원인 : 비밀번호를 올바르게 입력하지 못했거나, 비밀번호 입력이 중단된 경우에도 발생할 수 있음
                - **해결방법**
                    1. 비 암호화 된 키 파일로 변환 (server.key -> decrypted_server.key)
                        - ```bash
                          openssl rsa -in /home/server.key -out /home/decrypted_server.key
                          ```
                    2. nginx 설정 파일 업데이트
                        - ```nginx
                          ssl_certificate_key /{파일저장경로}/decrypted_server.key;
                          ```
                    3. nginx 설정 테스트 
                        - ```bash
                          sudo nginx -t
                          ```
                    4. nginx 서비스 재시작
                        - ```bash
                          sudo systemctl restart nginx
                          ```


## 브라우저 경고 무시
- 자체 서명된 인증서는 신뢰할 수 있는 CA(Certificate Authority)에서 서명되지 않았기 때문에 브라우저에서 "안전하지 않음" 경고가 나타납니다. 이 경고를 무시하고 진행할 수 있지만, **테스트 환경에서만 사용**하는 것이 좋습니다.