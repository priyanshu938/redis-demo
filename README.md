# redis-demo

Small Node.js demo project showing how Redis can be used as a cache in two common flows:

- `server_api.js`: cache external API responses in Redis
- `server.js`: cache Postgres product records in Redis

## Prerequisites

- Node.js 18+
- Redis running on `localhost:6379`
- PostgreSQL running on `localhost:5432` for the product demo

This project expects the following Postgres database settings:

- database: `mydb`
- user: `user`
- password: `pass`

## Install

```bash
npm install
```

## Start Redis

If you are using Redis Stack in Docker, one simple option is:

```bash
docker run -d --name redis-stack -p 6379:6379 redis/redis-stack:latest
```

To open the Redis CLI inside that container:

```bash
docker exec -it redis-stack redis-cli
```

## Start Postgres

If you want to run Postgres in Docker with the credentials used by this project:

```bash
docker run -d --name postgres-db -e POSTGRES_USER=user -e POSTGRES_PASSWORD=pass -e POSTGRES_DB=mydb -p 5432:5432 postgres:16
```

## Demo 1: Cache external API responses

Run:

```bash
node server_api.js
```

Test it:

```bash
curl http://localhost:3000/posts/1
```

Behavior:

- first request fetches data from `jsonplaceholder.typicode.com`
- later requests return the cached value from Redis
- cache TTL is 600 seconds

## Demo 2: Cache Postgres product data

Seed the database:

```bash
node seed.js
```

Optional check:

```bash
node check.js
```

Start the server:

```bash
npm start
```

Test it:

```bash
curl http://localhost:3000/product/500000
```

Behavior:

- valid product IDs are `1` to `1000000`
- first request reads from Postgres
- later requests return the cached value from Redis
- cache TTL is 600 seconds

## Files

- `server_api.js` - Redis cache in front of an external API
- `server.js` - Redis cache in front of Postgres
- `seed.js` - creates the `products` table and inserts up to 1,000,000 rows
- `check.js` - prints row count and sample product rows

## Notes

- Both demo servers use port `3000`, so run one at a time.
- `seed.js` can take a while because it inserts 1,000,000 rows in batches.
