# ДЗ 3

## 4 балла

```sh
docker build -t count .
docker run -d --rm --name hw3-4 -p 8081:8000 -e GREETING="Hello Docker" count
```

```sh
curl localhost:8081/
curl -X POST localhost:8081/increment
curl localhost:8081/
docker logs hw3-4
docker stop hw3-4
```

Вывод curl

```text
{"greeting": "Hello Docker", "count": 0, "hostname": "50db92e09893", "storage": "sqlite"}
{"greeting": "Hello Docker", "count": 1, "hostname": "50db92e09893", "storage": "sqlite"}
{"greeting": "Hello Docker", "count": 1, "hostname": "50db92e09893", "storage": "sqlite"}
```

Логи

```text
2026-11-22 13:17:25,569 listening port=8000 storage=sqlite
2026-11-22 13:17:26,521 http 172.17.0.1 "GET / HTTP/1.1" 200 -
2026-11-22 13:17:26,541 http 172.17.0.1 "POST /increment HTTP/1.1" 200 -
2026-11-22 13:17:26,556 http 172.17.0.1 "GET / HTTP/1.1" 200 -
```

## 6 баллов

В `.env` лежат `APP_PORT=8080`, `GREETING="Hello, World"`.

```sh
cp .env.example .env
docker compose up -d --build --wait
curl localhost:8080/
curl -X POST localhost:8080/increment
curl -X POST localhost:8080/increment
curl -X POST localhost:8080/increment

docker compose down
docker compose up -d --wait
curl localhost:8080/
```

Вывод curl

```text
{"greeting": "Hello, World", "count": 0, "hostname": "43044c7306d8", "storage": "redis"}
{"greeting": "Hello, World", "count": 1, "hostname": "43044c7306d8", "storage": "redis"}
{"greeting": "Hello, World", "count": 2, "hostname": "43044c7306d8", "storage": "redis"}
{"greeting": "Hello, World", "count": 3, "hostname": "43044c7306d8", "storage": "redis"}
{"greeting": "Hello, World", "count": 3, "hostname": "65aaee39f837", "storage": "redis"}
```

## 8 баллов — Redis

`app` подключается к `redis://redis:6379/0`.

```sh
docker compose stop redis
curl -i localhost:8080/healthz
curl -i localhost:8080/readyz
curl -i -X POST localhost:8080/increment

docker compose start --wait redis
curl -i localhost:8080/readyz
curl localhost:8080/
curl -X POST localhost:8080/increment
docker compose down
```

Результаты запросов

```text
GET /healthz -> 200 {"status": "alive"}
GET /readyz -> 503 {"error": "storage_unavailable"}
POST /increment -> 503 {"error": "storage_unavailable"}
GET /readyz -> 200 {"status": "ready", "storage": "redis"}
GET / -> 200 {"greeting": "Hello, World", "count": 3, "hostname": "65aaee39f837", "storage": "redis"}
POST /increment -> 200 {"greeting": "Hello, World", "count": 4, "hostname": "65aaee39f837", "storage": "redis"}
```
