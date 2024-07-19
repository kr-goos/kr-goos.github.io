---
title: "[GO] Golang 으로 Cache 1편 Cache interface 구현"
description: 캐시란 무엇인지 알아보고 interface 를 구현하는 방법을 알아보겠습니다.
author: 김우석
date: 2024-07-17 11:18:00 +0900
categories: [Golang, Cache]
tags: [Golang, Go, Go언어, Cache]
image:
  path: /assets/img/posts/window-golang-install/golang.svg
---

## 캐시란?
- 자주 사용되는 데이터를 메모리와 같은 빠른 저장소에 임시로 저장하여, 필요할 때 더 빠르게 접근할 수 있도록 하는 기술

## 작동 원리
- 데이터 요청 : 클라이언트가 데이터를 요청할 때, 먼저 캐시를 확인
- 캐시 적중 (Cache Hit) : 요청한 데이터가 캐시에 존재하면, 이를 바로 반환
- 캐시 미스 (Cache Miss) : 요청한 데이터가 캐시에 없으면, 원본 데이터 소스(ex> 데이터베이스)에서 데이터를 가져와 캐시에 저장한 후 반환

## 주요 목적
- 성능 향상: 데이터 접근 시간을 단축시켜 애플리케이션의 **응답 속도를 개선**
- 부하 분산: 원본 데이터 소스에 대한 요청 수를 줄여 **서버의 부하를 감소**

## 캐시의 종류
- 메모리 캐시: RAM과 같은 빠른 저장소에 데이터를 저장 (예: Redis, Memcached)
- 디스크 캐시: 하드 드라이브나 SSD와 같은 느린 저장소에 저장하지만, 일반 데이터베이스보다 빠르게 접근할 수 있음

## 캐시 전략
- TTL (Time to Live): 캐시에 저장된 데이터의 유효 기간을 설정하여 일정 시간 후 자동으로 제거되도록 함
- LRU (Least Recently Used): 가장 오래 사용되지 않은 데이터를 삭제하여 새로운 데이터를 저장할 공간을 확보

## 인터페이스 설계의 이점
**인터페이스는 Go에서 중요한 설계 원칙 중 하나로, 다음과 같은 이점을 가짐**
- **유연성**
    - 다양한 구현체를 동일한 방식으로 사용할 수 있음
- **테스트 용이성**
    - 테스트할 때 Mock 객체를 쉽게 만들 수 있어, 실제 캐시 구현체를 사용하지 않고도 테스트할 수 있음
- **캡슐화 및 의존성 주입**
    - 구체적인 구현에 대한 의존성을 줄이고, 코드의 캡슐화를 향상시킬 수 있음
- **다형성**
    - 다른 구현체가 동일한 메서드를 제공함으로써, 코드에서 다형성을 활용할 수 있음 (하나의 인터페이스로 여러 타입의 객체를 처리)
- **코드 일관성 및 유지보수**
    - 각 구현체는 특정 조건을 준수해야 하며, 이를 통해 코드의 일관성을 유지할 수 있음 ( 새로운 캐시 구현체를 추가하더라도 기존 코드를 변경할 필요가 줄어듦)
- **기능 확장 및 변경 용이성**
    - 기존 캐시 구현체의 기능을 확장하거나 변경할 때, 인터페이스를 통해 새로운 기능을 추가해도 기존 코드에 영향을 주지 않음

## [Golang] Cache Interface 설계
- Cache 인터페이스는 다양한 캐시 구현체(In-Memory Cache, Redis Cache 등)를 동일한 방식으로 사용할 수 있도록 설계

### 설명

```golang
package cache

import "errors"

var (
	ErrKeyNotFound = errors.New("key not found")
	ErrKeyExpired  = errors.New("key expired")
	ErrCacheClosed = errors.New("cache closed")
)
```
- 인터페이스의 구현체에서 사용 할 에러 정의

```golang
package cache

import (
	"context"
	"time"
)

// 세 가지 메모리 타입을 정의
const (
	INMEMORYCACHE = "memory"
	REDISCACHE    = "redis"
	DUMMYCACHE    = "dummy"
)

type Cache interface {
	Get(context.Context, string) (any, error)
	Set(context.Context, string, any, time.Duration) error
	Delete(context.Context, string) error
	SetTTL(context.Context, string, time.Duration) error
	GetTTL(context.Context, string) (time.Duration, error)
	Exists(context.Context, string) (bool, error)
	Clear(context.Context) error
	Close() error
	Description() string
}

// cacheType 에 따라, 다른 구현체의 인스턴스를 생성하여
// Cache interface 로 리턴함
func NewCache(cacheType string, addr, password string, db int) (Cache, error) {
	switch cacheType {
	case INMEMORYCACHE:
		return NewInMemoryCache(), nil
	case REDISCACHE:
		return NewRedisCache(addr, password, db)
	default:
		return NewDummyCache(), nil
	}
}

```
- Get(context.Context, string) (any, error)
    - 역할: 캐시에서 데이터를 읽어옵니다.
    - 설명: 키를 통해 캐시에서 데이터를 검색합니다. 
- Set(context.Context, string, any, time.Duration) error
    - 역할: 캐시에 데이터를 저장합니다.
    - 설명: 주어진 키와 값을 캐시에 저장하며, TTL(Time to Live)을 설정하여 데이터의 유효 기간을 관리합니다.
- Delete(context.Context, string) error
    - 역할: 캐시에서 데이터를 삭제합니다.
    - 설명: 주어진 키에 해당하는 데이터를 캐시에서 삭제합니다. 
- SetTTL(context.Context, string, time.Duration) error
    - 역할: 이미 존재하는 캐시 항목의 TTL을 설정합니다.
    - 설명: 특정 키에 대해 TTL을 설정하여 데이터의 유효 기간을 갱신합니다. 
- GetTTL(context.Context, string) (time.Duration, error)
    - 역할: 특정 키에 대한 TTL을 조회합니다.
    - 설명: 주어진 키에 대한 TTL을 반환하여 데이터의 남은 유효 기간을 확인합니다. 
- Exists(context.Context, string) (bool, error)
    - 역할: 특정 키가 캐시에 존재하는지 확인합니다.
    - 설명: 주어진 키가 캐시에 존재하는지 여부를 확인합니다. 
- Clear(context.Context) error
    - 역할: 캐시를 비웁니다.
    - 설명: 캐시의 모든 데이터를 삭제합니다. 이 메서드는 캐시를 완전히 비울 때 사용됩니다.
- Close() error
    - 역할: 캐시 리소스를 정리합니다.
    - 설명: 캐시가 사용 중인 리소스를 정리하고, 필요한 경우 연결을 종료합니다.
- Description() string
    - 역할: 캐시 구현체에 대한 설명을 제공합니다.
    - 설명: 캐시 구현체에 대한 간단한 설명을 반환하여, 어떤 캐시가 사용되고 있는지 알 수 있게 합니다.
