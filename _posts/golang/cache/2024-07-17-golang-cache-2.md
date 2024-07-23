---
title: "[Golang] Cache 2편 - In-Memory Cache 구현"
description: In-Memory Cache 구현을 알아보겠습니다.
author: 김우석
date: 2024-07-17 13:15:00 +0900
categories: [Golang, Cache, In-Memory cache]
tags: [Golang, Go, Go언어, Cache]
image:
  path: /assets/img/posts/window-golang-install/golang.svg
---

## In-Memory Cache란?
- In-memory cache는 데이터를 메모리(RAM)에 저장하여 빠르게 접근할 수 있게 하는 데이터 저장소이며 일반적으로 디스크 기반 저장소나 데이터베이스에 비해 훨씬 빠른 읽기 및 쓰기 속도를 제공

## 주요 특징
- **속도**
    - RAM은 디스크보다 훨씬 빠르기 때문에, 데이터를 메모리에 저장하면 접근 속도가 매우 빨라짐
    - 자주 접근해야 하는 데이터에 유용
- **일시성**
    - 시스템 재부팅 또는 애플리케이션 재시작 시 데이터가 소실됨
    - 영구 저장이 필요한 데이터는 적합하지 않음
- **용량 제한**
    - RAM은 디스크에 비해 용량이 제한적
    - 인메모리 캐시는 보통 용량 제한이 있으며, 용량 초과 시 가장 오래된 항목을 삭제하는 방식(LRU, Least Recently Used) 등을 사용
- **데이터 만료**
    - 각 항목에 TTL(Time-To-Live)을 설정하여 일정 시간이 지나면 데이터가 자동으로 삭제되도록 할 수 있음

## 사용 사례
- 자주 참조되는 데이터 저장 (예: 사용자 세션 정보, 설정 데이터 등)
- 데이터베이스 쿼리 결과 캐싱
- 계산 비용이 높은 작업의 결과 캐싱 (예: 이미지 변환 결과, 복잡한 계산 결과 등)


## [Golang] In-Memory Cache 구현

```golang
package cache

import (
	"context"
	"sync"
	"time"
)

type cacheItem struct {
	value  any
	expiry time.Time
}

type InMemoryCache struct {
	items map[string]*cacheItem
	mtx   sync.RWMutex
}

func NewInMemoryCache() *InMemoryCache {
	return &InMemoryCache{
		items: make(map[string]*cacheItem),
	}
}

func (c *InMemoryCache) Get(_ context.Context, key string) (any, error) {
	c.mtx.RLock()
	item, exists := c.items[key]
	if !exists {
		c.mtx.RUnlock()
		return nil, ErrKeyNotFound
	}

	if item.expiry.Before(time.Now()) {
		c.mtx.RUnlock()
		c.mtx.Lock()
		delete(c.items, key)
		c.mtx.Unlock()
		return nil, ErrKeyExpired
	}
	c.mtx.RUnlock()
	return item.value, nil
}

func (c *InMemoryCache) Set(_ context.Context, key string, value any, ttl time.Duration) error {

	expiry := time.Now().Add(ttl)
	item := &cacheItem{
		value:  value,
		expiry: expiry,
	}
	c.mtx.Lock()
	c.items[key] = item
	c.mtx.Unlock()
	return nil
}

func (c *InMemoryCache) Delete(_ context.Context, key string) error {
	c.mtx.Lock()
	defer c.mtx.Unlock()

	delete(c.items, key)
	return nil
}

func (c *InMemoryCache) SetTTL(_ context.Context, key string, ttl time.Duration) error {
	c.mtx.Lock()
	defer c.mtx.Unlock()

	item, exists := c.items[key]
	if !exists {
		return ErrKeyNotFound
	}
	item.expiry = time.Now().Add(ttl)
	return nil
}

func (c *InMemoryCache) GetTTL(_ context.Context, key string) (time.Duration, error) {
	c.mtx.RLock()
	defer c.mtx.RUnlock()

	item, exists := c.items[key]
	if !exists {
		return 0, ErrKeyNotFound
	}
	ttl := time.Until(item.expiry)
	return ttl, nil
}

func (c *InMemoryCache) Exists(_ context.Context, key string) (bool, error) {
	c.mtx.RLock()
	defer c.mtx.RUnlock()

	_, exists := c.items[key]
	return exists, nil
}

func (c *InMemoryCache) Clear(_ context.Context) error {
	c.mtx.Lock()
	defer c.mtx.Unlock()

	c.items = make(map[string]*cacheItem)
	return nil
}

func (c *InMemoryCache) Close() error { return nil }

func (c *InMemoryCache) Description() string {
	return "InMemoryCache: A simple in-memory cache implementation"
}
```

### 설명
- 예제는 Mutex 를 직접 제어하였으나, 여러 고루틴이 동시에 읽고 쓸 수 있도록 설계되어 있어 동기화 문제를 자동으로 처리해주는 `sync.Map` 를 활용 할 수도 있습니다.

```golang
type cacheItem struct {
	value  any
	expiry time.Time
}
```
- `cacheItem`은 캐시에 저장되는 항목의 구조체로, `value`는 실제 저장된 데이터, `expiry`는 항목의 만료 시간을 나타냄

```golang
type InMemoryCache struct {
	items map[string]*cacheItem
	mtx   sync.RWMutex
}

```
- `InMemoryCache`는 캐시의 구현체로, `items` 맵은 캐시된 항목을 저장하며, `mtx`는 동시 접근을 위한 읽기-쓰기 뮤텍스를 사용하여 데이터의 안전성을 보장

```golang
func NewInMemoryCache() *InMemoryCache {
	return &InMemoryCache{
		items: make(map[string]*cacheItem),
	}
}
```
- `InMemoryCache` 인스턴스를 생성하며 `items` 맵을 초기화하여 캐시 항목을 저장할 준비

```golang
func (c *InMemoryCache) Get(_ context.Context, key string) (any, error) {
	c.mtx.RLock()
	item, exists := c.items[key]
	if !exists {
		c.mtx.RUnlock()
		return nil, ErrKeyNotFound
	}

	if item.expiry.Before(time.Now()) {
		c.mtx.RUnlock()
		c.mtx.Lock()
		delete(c.items, key)
		c.mtx.Unlock()
		return nil, ErrKeyExpired
	}
	c.mtx.RUnlock()
	return item.value, nil
}
```
- 주어진 `key`에 대한 캐시 항목을 조회
- 읽기 & 쓰기 잠금을 사용하여 데이터 무결성을 유지
- 항목이 존재하지 않으면 `ErrKeyNotFound`를 반환
- 만료되지 않았다면 해당 `value`를 반환하며 만료된 데이터는 삭제
- 지연 삭제 (Lazy Deletion) 방식
- **삭제 메커니즘**
    - 지연 삭제 (Lazy Deletion) 방식 : 위 코드는 get(key) 을 한 경우 만료된 key에 대한 삭제 작업이 이루어짐 (get이 없다면 계속 삭제되지 못하고 데이터가 저장되어 있음)
    - 주기적 정리 (Periodic Cleanup): 일정한 주기로 만료된 키를 정리하는 작업을 수행하는 별도의 고루틴을 실행

    ```golang
    type InMemoryCache struct {
        data        map[string]cacheItem
        mu          sync.RWMutex
        stopCleanup chan struct{}
    }

    func NewMemoryCache() *InMemoryCache {
        cache := &InMemoryCache{
            data:        make(map[string]cacheItem),
            stopCleanup: make(chan struct{}),
        }
        go cache.startCleanup()
        return cache
    }

    func (c *InMemoryCache) startCleanup() {
        ticker := time.NewTicker(5 * time.Minute)
        defer ticker.Stop()
        for {
            select {
            case <-ticker.C:
                c.cleanup()
            case <-c.stopCleanup:
                return
            }
        }
    }
    ```
    
    - **`주기적 정리`는 백그라운드에서 자동으로 수행되고, `지연 삭제` 는 키를 액세스할 때마다 수행되어 즉각적인 관리를 보장합니다. 이렇게 하면 캐시의 메모리 사용을 효율적으로 유지할 수 있습니다.**

```golang
func (c *InMemoryCache) Set(_ context.Context, key string, value any, ttl time.Duration) error {

	expiry := time.Now().Add(ttl)
	item := &cacheItem{
		value:  value,
		expiry: expiry,
	}
	c.mtx.Lock()
	c.items[key] = item
	c.mtx.Unlock()
	return nil
}
```
- 주어진 `key`에 `value`를 설정하고, ttl에 따라 만료 시간을 지정
- 쓰기 잠금을 사용하여 안전하게 데이터를 수정
- 만료 시간을 현재 시간에 `ttl`을 더한 값으로 설정하여 cacheItem을 생성하고 `items` 맵에 항목을 추가

```golang
func (c *InMemoryCache) Delete(_ context.Context, key string) error {
	c.mtx.Lock()
	defer c.mtx.Unlock()

	delete(c.items, key)
	return nil
}
```
- 주어진 `key`의 캐시 항목을 삭제
- 쓰기 잠금을 사용하여 안전하게 `items`맵에서 항목을 삭제

```golang
func (c *InMemoryCache) SetTTL(_ context.Context, key string, ttl time.Duration) error {
	c.mtx.Lock()
	defer c.mtx.Unlock()

	item, exists := c.items[key]
	if !exists {
		return ErrKeyNotFound
	}
	item.expiry = time.Now().Add(ttl)
	return nil
}
```
- 주어진 `key`의 TTL(Time-to-Live)을 설정
- 쓰기 잠금을 사용하여 안전하게 항목의 만료 시간을 수정
- 항목이 존재하지 않으면 `ErrKeyNotFound`를 반환
- 존재하는 경우, 만료 시간을 현재 시간에 ttl을 더한 값으로 업데이트

```golang
func (c *InMemoryCache) GetTTL(_ context.Context, key string) (time.Duration, error) {
	c.mtx.RLock()
	defer c.mtx.RUnlock()

	item, exists := c.items[key]
	if !exists {
		return 0, ErrKeyNotFound
	}
	ttl := time.Until(item.expiry)
	return ttl, nil
}
```
- 주어진 `key`의 남은 TTL을 조회
- 읽기 잠금을 사용하여 데이터 무결성을 유지
- 항목이 존재하지 않으면 `ErrKeyNotFound`를 반환하고, 존재한다면 현재 시간과 만료 시간의 차이를 계산하여 남은 TTL 을 반환

```golang
func (c *InMemoryCache) Exists(_ context.Context, key string) (bool, error) {
	c.mtx.RLock()
	defer c.mtx.RUnlock()

	_, exists := c.items[key]
	return exists, nil
}
```
- 주어진 `key`가 캐시에 존재하는지 확인
- 읽기 잠금을 사용하여 데이터 무결성을 유지
- 항목이 존재하면 `true`, 그렇지 않으면 `false`

```golang
func (c *InMemoryCache) Clear(_ context.Context) error {
	c.mtx.Lock()
	defer c.mtx.Unlock()

	c.items = make(map[string]*cacheItem)
	return nil
}
```
- map 을 초기화 시킴

```golang
func (c *InMemoryCache) Close() {}
```
- Interface 에 정의된 메서드이나, In-Memory Cache 의 구현체 에서는 선택적 요소 (사용 예 : {c.items = nil} 과 같이 사용해도 되지만, 단, items 가 nil 일 때의 예외처리를 다른 메서드에 추가할 것)

```golang
func (c *InMemoryCache) Description() string {
	return "InMemoryCache: A simple in-memory cache implementation"
}
```
- 인스턴스의 기본 설명을 문자열로 반환
