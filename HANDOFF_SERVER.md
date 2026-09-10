# Задача для Клода на сервере

## Что нужно сделать

1. Клонировать infra репо (если ещё не):
   git clone git@github.com:zor07/trading_bots_infra.git /opt/trading_bots_infra

2. Создать .env из .env.example, заполнить реальными значениями:
   cp .env.example .env
   Пинать пользователя за каждое значение которое неизвестно (токен бота, пароль БД, домен).

3. chmod +x deploy.sh

4. Настроить nginx как reverse proxy:
   - app слушает на 127.0.0.1:8080
   - nginx слушает 80/443, проксирует на 8080
   - SSL через certbot (Let's Encrypt)
   - Спросить у пользователя домен если не знаешь

5. Первый запуск:
   docker compose pull
   docker compose up -d

6. Проверить что всё работает:
   docker compose ps
   docker compose logs -f trading-bots-app

7. Дополнить README.md в этой папке:
   - Как смотреть логи
   - Как перезапустить вручную
   - Как откатиться
   - Схема nginx

## Правила
- Каждый шаг показывать перед выполнением, ждать апрув
- Не коммитить без апрува
