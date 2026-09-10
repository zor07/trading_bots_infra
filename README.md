# Trading Bots — Infra

Файлы для деплоя на VPS. Используется совместно с основным репо [zor07/trading_bots](https://github.com/zor07/trading_bots).

## Как устроен CI/CD

### Схема

```
git push → master
    └── GitHub Actions (.github/workflows/deploy.yml)
            ├── Собирает Docker-образ (Dockerfile в корне репо)
            ├── Пушит в ghcr.io/zor07/trading_bots:latest
            └── SSH на VPS → cd /opt/trading-bots-infra && git pull && ./deploy.sh
```

### Что происходит на VPS

`deploy.sh` делает три вещи:
1. `docker compose pull` — тянет свежий образ из ghcr.io
2. `docker compose up -d` — перезапускает контейнеры
3. `docker image prune -f` — удаляет старые образы

### Первоначальная настройка VPS

```bash
# Клонировать этот репо на сервер
git clone git@github.com:zor07/trading-bots-infra.git /opt/trading-bots-infra
cd /opt/trading-bots-infra

# Создать .env из примера и заполнить реальными значениями
cp .env.example .env
nano .env

# Сделать deploy.sh исполняемым
chmod +x deploy.sh

# Первый запуск
docker compose pull
docker compose up -d
```

### Секреты в GitHub Actions

В настройках репо (Settings → Secrets and variables → Actions) нужно добавить:

| Секрет | Описание |
|--------|----------|
| `VPS_HOST` | IP или домен сервера |
| `VPS_USER` | SSH-пользователь (например, `root`) |
| `VPS_SSH_KEY` | Приватный SSH-ключ для доступа на сервер |

`GITHUB_TOKEN` добавлять не нужно — он генерируется автоматически.

### Переменные окружения (.env)

| Переменная | Описание |
|------------|----------|
| `DB_HOST` | Имя сервиса БД (по умолчанию `trading-bots-db`) |
| `DB_PORT` | Порт БД внутри Docker-сети (по умолчанию `5432`) |
| `DB_NAME` | Имя базы данных |
| `DB_USERNAME` | Пользователь БД |
| `DB_PASSWORD` | Пароль БД |
| `APP_BASE_URL` | Публичный URL приложения (используется в ссылках из Telegram) |
| `TELEGRAM_BOT_TOKEN` | Токен бота от @BotFather |
| `TELEGRAM_BOT_USERNAME` | Username бота (без @) |

### Порты на VPS

| Сервис | Порт на хосте | Описание |
|--------|--------------|----------|
| App | `127.0.0.1:8080` | Только localhost — предполагается reverse proxy (nginx) |
| DB | `127.0.0.1:5437` | Только localhost |
