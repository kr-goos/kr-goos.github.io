---
title: "[Docker] Ubuntu CA 인증서를 수동으로 구성하는 방법"
description: 네트워크가 되지 않는 환경에서 인증서를 수동으로 등록하는 방법을 알아보겠습니다.
author: 김우석
date: 2024-09-04 12:50:00 +0900
categories: [Docker, Ubuntu]
tags: [Linux, Ubuntu, Docker, SSL]
image:
  path: /assets/img/posts/docker/docker-icon.png
---

## 개요
네트워크가 차단된 환경에서 `update-ca-certificates`를 설치하지 못하여 CA 인증서를 수동으로 구성해야 하는 경우에 대해 알아보겠습니다.

## 시나리오
- **문제 상황**: 
  - 내부 네트워크에서만 운영되는 환경에서 Docker 이미지를 생성해야 하며, 외부 리소스에 접근할 수 없는 경우
  - 특정 CA 인증서가 필요하지만, 인터넷에 연결되어 있지 않아 `apt-get update`와 같은 명령어를 사용할 수 없는 경우
  - 외부 서버의 SSL/TLS 인증을 안전하게 수행하기 위해 필요한 CA 인증서를 수동으로 등록해야 하는 경우
- **해결 방법**: 
  - Dockerfile을 사용하여 인증서를 직접 복사하고 등록하여 SSL/TLS 연결을 안전하게 수행할 수 있도록 합니다.


## Dockerfile

```dockerfile
FROM ubuntu:20.04

USER root

# Copy sources.list to use internal mirror
COPY ./sources.list /etc/apt/sources.list

# Copy the .crt files to the image
COPY ./CA1.crt /usr/local/share/ca-certificates/
COPY ./CA2.crt /usr/local/share/ca-certificates/
COPY ./CA3.crt /usr/local/share/ca-certificates/

# Ensure that the /etc/ssl/certs directory exists
RUN mkdir -p /etc/ssl/certs

# Register certificates manually without update-ca-certificates
RUN cat /usr/local/share/ca-certificates/*.crt > /etc/ssl/certs/ca-certificates.crt

# (Optional) Set environment variables to use custom CA certificates
# ENV SSL_CERT_FILE=/etc/ssl/certs/ca-certificates.crt

# Update Package List via Internal Mirror Server
RUN apt-get update

# Start with bash shell
CMD ["bash"]
```

### 각 명령어 설명
- `FROM ubuntu:20.04`
    - Docker 컨테이너의 기본 이미지를 Ubuntu 20.04로 설정합니다.
- `USER root`
    - 시스템 파일을 수정할 수 있는 관리 권한이 필요한 root 사용자로 전환합니다.
- `COPY ./sources.list /etc/apt/sources.list`
    - 사용자 정의 sources.list 파일을 Docker 이미지에 복사하여 패키지 관리에 사용할 내부 미러를 설정합니다.
- `COPY ./CA1.crt /usr/local/share/ca-certificates/ , COPY ...`
    - CA 인증서 파일을 지정된 디렉토리에 복사하여 인증서를 등록할 수 있도록 합니다.
- `RUN mkdir -p /etc/ssl/certs`
    - `/etc/ssl/certs` 디렉토리가 존재하지 않을 경우 생성하여, 병합된 인증서 파일을 저장할 위치를 만듭니다.
- `RUN cat /usr/local/share/ca-certificates/*.crt > /etc/ssl/certs/ca-certificates.crt`
    - 복사된 모든 `.crt` 파일을 하나의 `ca-certificates.crt` 파일로 병합하여 `/etc/ssl/certs` 디렉토리에 등록합니다. SSL/TLS 응용 프로그램에서 사용할 수 있도록 인증서를 등록합니다.
- `ENV SSL_CERT_FILE=/etc/ssl/certs/ca-certificates.crt`
    - (선택 사항) 사용자 정의 CA 인증서를 위한 환경 변수를 설정합니다. 그러나 대부분의 응용 프로그램에서 작동하는 데 필수는 아닙니다.
- `RUN apt-get update`
    - 내부 미러에서 패키지 목록을 업데이트합니다. 이 명령은 sources.list에 설정된 내부 미러가 올바르게 구성되어 있어야 성공합니다.

## 중요 고려사항
- **파일 이름**: CA 인증서는 /etc/ssl/certs/ca-certificates.crt에 등록됩니다. 이 이름은 많은 응용 프로그램이 SSL/TLS 작업을 위해 인증서 파일을 이 이름으로 찾기 때문에 중요합니다.
- **네트워킹**: 이 설정은 네트워크가 차단된 환경에서 작동하도록 설계되었으며, apt-get update는 내부 미러에 접근할 수 있어야 성공합니다.

## 이미지 빌드
```bash
docker build --no-cache --progress=plain -f dockerfile -t CAmanually .
```