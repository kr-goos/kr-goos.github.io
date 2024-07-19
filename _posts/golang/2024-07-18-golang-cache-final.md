---
title: "[GO] Golang 으로 Cache 마지막편 Cache를 사용한 웹서버 구현"
description: Dummy Cache 구현과 Cache interface 사용을 알아보겠습니다.
author: 김우석
date: 2024-07-19 08:45:00 +0900
categories: [Golang, Cache]
tags: [Golang, Go, Go언어, Cache, interface]
image:
  path: /assets/img/posts/window-golang-install/golang.svg
---

## dummy cache 란?
- 실제 캐시 작업을 수행하지 않지만, 캐시 인터페이스를 충족시켜 코드의 다른 부분에서 캐시 동작을 모방할 수 있도록 함

## [Golang] Dummy Cache 구현
- 실제로 데이터를 저장하거나 반환하지 않는 캐시의 한 형태로 Cahce 인터페이스의 기본 구현체로 사용하기 위해 추가

```golang
package cache

import (
	"context"
	"time"
)

type dummyCache struct{}

func NewDummyCache() *dummyCache {
	return &dummyCache{}
}

func (c *dummyCache) Get(ctx context.Context, key string) (any, error) {
	return nil, nil
}

func (c *dummyCache) Set(ctx context.Context, key string, value any, ttl time.Duration) error {
	return nil
}

func (c *dummyCache) Delete(ctx context.Context, key string) error {
	return nil
}

func (c *dummyCache) SetTTL(ctx context.Context, key string, ttl time.Duration) error {
	return nil
}

func (c *dummyCache) GetTTL(ctx context.Context, key string) (time.Duration, error) {
	return 0, nil
}

func (c *dummyCache) Exists(ctx context.Context, key string) (bool, error) {
	return false, nil
}

func (c *dummyCache) Clear(ctx context.Context) error {
	return nil
}

func (c *dummyCache) Close() error { return nil }

func (c *dummyCache) Description() string {
	return "DummyCache: A dummy cache implementation that does nothing"
}
```

## cache interface 맛보기

### 코드 구현
```golang
package main

import (
	"context"
	"flag"
	"fmt"
	"internal/cache"
	"log"
	"time"
)

// Args 구조체는 명령줄 인수로부터 받아올 값을 저장
type Args struct {
	cacheType string
	addr      string
	password  string
	db        int
}

// NewDefaultArgs 함수는 기본 인수값들을 설정한 Args 구조체를 반환
func NewDefaultArgs() Args {
	return Args{
		cacheType: cache.DUMMYCACHE,
		addr:      "localhost:6379",
	}
}


func main() {

	args := NewDefaultArgs()

    // 명령줄 인수들을 받아와 Args 구조체에 저장
	flag.StringVar(&args.cacheType, "t", args.cacheType, "Cache Type (memory : inmemory, redis : redis ) [ default : dummy cache ]")
	flag.StringVar(&args.addr, "a", args.addr, "server address (format: 8.8.8.8:80 )")
	flag.StringVar(&args.password, "p", args.password, "server password")
	flag.IntVar(&args.db, "d", args.db, "Database to be selected after connecting to the server")
	flag.Parse()

    // NewCache 함수를 사용하여 캐시 인스턴스를 생성
	c, err := cache.NewCache(args.cachType, args.addr, args.password, args.db)
	if err != nil {
		log.Fatalln("invalid cache type : ", args.cachType)
	}
	defer c.Close()

	fmt.Println(c.Description())

	// 예시 키값 지정
	key := "k1"

    // 캐시에 값을 저장하고 TTL 3 second 을 설정
	err = c.Set(context.TODO(), key, "in memory cache", 3*time.Second)
	if err != nil {
		log.Printf("'%s' %v\n", key, err)
	}

    // 캐시에서 값을 가져옴
	v, err := c.Get(context.TODO(), key)
	if err != nil {
		log.Printf("'%s' %v\n", key, err)
	} else {
		fmt.Printf("%s key : %s , value : %+v\n", time.Now().Format("2006/01/02 15:04:05"), key, v)
	}

    // 캐시에서 TTL 값을 가져옴
	ttl, err := c.GetTTL(context.TODO(), key)
	if err != nil {
		log.Printf("'%s' %v\n", key, err)
	} else {
		fmt.Printf("%s key : %s ttl : %v\n", time.Now().Format("2006/01/02 15:04:05"), key, ttl)
	}

	// 강제 key 값 만료 후 값 확인
	fmt.Println("5 second sleep ...")
	time.Sleep(5 * time.Second)

    // 기존 3초로 만료시간이 지정되었던 key 에 대해 값을 가져옴
	v, err = c.Get(context.TODO(), key)
	if err != nil {
		log.Printf("'%s' %v\n", key, err)
	} else {
		fmt.Printf("%s key : %s , value : %+v\n", time.Now().Format("2006/01/02 15:04:05"), key, v)
	}

    // 만료된 key 의 TTL 을 가져옴
	ttl, err = c.GetTTL(context.TODO(), key)
	if err != nil {
		log.Printf("'%s' %v\n", key, err)
	} else {
		fmt.Printf("%s key : %s ttl : %v", time.Now().Format("2006/01/02 15:04:05"), key, ttl)
	}

}
```

### 코드 빌드
- `go build -o cache`

### 코드 실행
- 코드 실행 전 옵션 확인
```bash
$ ./cache -h
Usage of C:\project\golang_blog\cmd\cache\cache:
  -a string
        server address (format: 8.8.8.8:80 ) (default "localhost:6379")
  -d int
        Database to be selected after connecting to the server
  -p string
        server password
  -t string
        Cache Type (m : inmemory, r : redis ) [ default : dummy cache ] (default "d")  
```

### [Dummy cache] `./cache` 명령 수행
```bash
$ ./cache
DummyCache: A dummy cache implementation that does nothing
2024/07/18 16:28:29 key : k1 , value : <nil>
2024/07/18 16:28:29 key : k1 ttl : 0s
5 second sleep ...
2024/07/18 16:28:34 key : k1 , value : <nil>
2024/07/18 16:28:34 key : k1 ttl : 0s
```

### [In-Memory Cache] `./cache -t=m` 명령 수행
```bash
$ ./cache -t=m
InMemoryCache: A simple in-memory cache implementation
2024/07/18 16:47:17 key : k1 , value : in memory cache
2024/07/18 16:47:17 key : k1 ttl : 2.9373745s
5 second sleep ...
2024/07/18 16:47:22 'k1' key expired
2024/07/18 16:47:22 'k1' key not found
```

### [Redis Cache] `./cache -t=r -a=localhost:6379 -d=0 -p=` 명령 수행
```bash
$ ./cache -t=r -a=localhost:6379 -d=0 -p=
RedisCache: A Redis-based cache implementation
2024/07/18 16:50:09 key : k1 , value : in memory cache
2024/07/18 16:50:09 key : k1 ttl : 3s
5 second sleep ...
2024/07/18 16:50:14 'k1' key not found
2024/07/18 16:50:14 'k1' key not found
```

## Cache Interface 를 활용한 웹 서버 구현

### 웹서버 구현
```golang
package main

import (
	"context"
	"encoding/json"
	"fmt"
	"internal/cache"
	"log"
	"net/http"
	"sync"
	"time"
)

var (
	c   cache.Cache
	mtx sync.Mutex
)

func main() {
	http.HandleFunc("/setcache", setCacheHandler)
	http.HandleFunc("/set", setHandler)
	http.HandleFunc("/get", getHandler)
	http.HandleFunc("/delete", deleteHandler)
	http.HandleFunc("/description", descriptionHandler)

	log.Println("Starting server on :8080")
	log.Fatal(http.ListenAndServe(":8080", nil))

	if c != nil {
		c.Close()
	}
}

func setCacheHandler(w http.ResponseWriter, r *http.Request) {

	if r.Method != http.MethodPost {
		http.Error(w, "Invalid request method", http.StatusMethodNotAllowed)
		return
	}

	var args struct {
		CacheType string `json:"cacheType"`
		Addr      string `json:"addr"`
		Password  string `json:"password"`
		DB        int    `json:"db"`
	}

	if err := json.NewDecoder(r.Body).Decode(&args); err != nil {
		http.Error(w, "Invalid request payload", http.StatusBadRequest)
		return
	}

	newCache, err := cache.NewCache(args.CacheType, args.Addr, args.Password, args.DB)
	if err != nil {
		http.Error(w, "Could not initialize cache: "+err.Error(), http.StatusInternalServerError)
		return
	}

	mtx.Lock()
	c = newCache
	mtx.Unlock()
	fmt.Fprintln(w, "Cache initialized")
}

func setHandler(w http.ResponseWriter, r *http.Request) {
	if c == nil {
		http.Error(w, "Cache not initialized", http.StatusInternalServerError)
		return
	}

	key := r.URL.Query().Get("key")
	value := r.URL.Query().Get("value")
	ttl, _ := time.ParseDuration(r.URL.Query().Get("ttl"))

	if key == "" || value == "" {
		http.Error(w, "Missing key or value", http.StatusBadRequest)
		return
	}

	err := c.Set(context.Background(), key, value, ttl)
	if err != nil {
		http.Error(w, err.Error(), http.StatusInternalServerError)
		return
	}

	fmt.Fprintf(w, "Key %s set with value %s\n", key, value)
}

func getHandler(w http.ResponseWriter, r *http.Request) {
	if c == nil {
		http.Error(w, "Cache not initialized", http.StatusInternalServerError)
		return
	}

	key := r.URL.Query().Get("key")

	if key == "" {
		http.Error(w, "Missing key", http.StatusBadRequest)
		return
	}

	value, err := c.Get(context.Background(), key)
	if err != nil {
		http.Error(w, err.Error(), http.StatusInternalServerError)
		return
	}

	fmt.Fprintf(w, "Key %s has value %v\n", key, value)
}

func deleteHandler(w http.ResponseWriter, r *http.Request) {
	if c == nil {
		http.Error(w, "Cache not initialized", http.StatusInternalServerError)
		return
	}

	key := r.URL.Query().Get("key")

	if key == "" {
		http.Error(w, "Missing key", http.StatusBadRequest)
		return
	}

	err := c.Delete(context.Background(), key)
	if err != nil {
		http.Error(w, err.Error(), http.StatusInternalServerError)
		return
	}

	fmt.Fprintf(w, "Key %s deleted\n", key)
}

func descriptionHandler(w http.ResponseWriter, r *http.Request) {
	if c == nil {
		http.Error(w, "Cache not initialized", http.StatusInternalServerError)
		return
	}

	fmt.Fprintf(w, "%s", c.Description())
}
```

### REST API 호출을 통한 테스트
- vscode 를 사용한다면, rest client 확장 설치
```rest-client
@serverPath=http://localhost:8080

### setcache
POST {{serverPath}}/setcache
content-type: application/json

{
  "cacheType": "memory",
  "addr": "localhost:6379",
  "password": "",
  "db": 0
}

### set
GET {{serverPath}}/set?key=your_key&value=your_value&ttl=5s

### get
GET {{serverPath}}/get?key=your_key

### delete
GET {{serverPath}}/delete?key=your_key

### description
GET {{serverPath}}/description
```
- Postman 설치 후 이용
```postman
POST http://localhost:8080/setcache
content-type: application/json

{
  "cacheType": "redis",
  "addr": "localhost:6379",
  "password": "",
  "db": 0
}

GET http://localhost:8080/set?key=your_key&value=your_value&ttl=5s

GET http://localhost:8080/get?key=your_key

GET http://localhost:8080delete?key=your_key
```

## 구현 완료

# [전체코드 링크](https://github.com/kr-goos/golang_blog)