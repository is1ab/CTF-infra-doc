若檔案太大，建議將檔案放置在此
[diced/zipline: A ShareX/file upload server that is easy to use, packed with features, and with an easy setup!](https://github.com/diced/zipline)

1. 建立資料夾 `file-server`
2. 在資料夾內建立檔案 `docker-compose.yaml`
3. 產生密碼
```
echo "POSTGRESQL_PASSWORD=$(openssl rand -base64 42 | tr -dc A-Za-z0-9 | cut -c -32 | tr -d '\n')" > .env
echo "CORE_SECRET=$(openssl rand -base64 42 | tr -dc A-Za-z0-9 | cut -c -32 | tr -d '\n')" >> .env
```
4. `docker compose up -d`
5. http://ip:3000 建立帳號


docker-compose.yaml
```
services:
  postgresql:
    image: postgres:16
    restart: unless-stopped
    env_file:
      - .env
    environment:
      POSTGRES_USER: ${POSTGRESQL_USER:-zipline}
      POSTGRES_PASSWORD: ${POSTGRESQL_PASSWORD:?POSTGRESSQL_PASSWORD is required}
      POSTGRES_DB: ${POSTGRESQL_DB:-zipline}
    volumes:
      - pgdata:/var/lib/postgresql/data
    healthcheck:
      test: ['CMD', 'pg_isready', '-U', 'zipline']
      interval: 10s
      timeout: 5s
      retries: 5

  zipline:
    image: ghcr.io/diced/zipline
    ports:
      - '3000:3000'
    env_file:
      - .env
    environment:
      - DATABASE_URL=postgres://${POSTGRESQL_USER:-zipline}:${POSTGRESQL_PASSWORD}@postgresql:5432/${POSTGRESQL_DB:-zipline}
    depends_on:
      - postgresql
    volumes:
      - './uploads:/zipline/uploads'
      - './public:/zipline/public'
      - './themes:/zipline/themes'

volumes:
  pgdata:
```