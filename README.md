### Hexlet tests and linter status:
[![Actions Status](https://github.com/d3f38/ai-for-developers-project-386/actions/workflows/hexlet-check.yml/badge.svg)](https://github.com/d3f38/ai-for-developers-project-386/actions)

# Calendar Booking Service

Сервис записи на встречи: владелец календаря заводит типы событий, гость выбирает
свободный слот и бронирует его.

**Опубликованное приложение: https://ai-for-developers-project-386-qaf1.onrender.com**

## Архитектура

Проект сделан в подходе Design First: сначала описан API-контракт, затем по нему
независимо реализованы фронтенд и бэкенд.

| Слой | Стек | Расположение |
| --- | --- | --- |
| Контракт | TypeSpec → OpenAPI 3 | `src/main.tsp`, `api-specs/openapi.yaml` |
| Бэкенд | Express + TypeScript, хранилище в памяти | `backend/` |
| Фронтенд | React 19 + Vite + Tailwind | `frontend/` |
| E2E-тесты | Playwright | `tests/e2e/` |

В продакшене это один контейнер: Express отдаёт API по `/api` и раздаёт собранный
фронтенд, включая SPA-fallback для маршрутов роутера.

## Запуск в Docker

Приложение слушает порт из переменной окружения `PORT`.

```bash
docker build -t booking-app .
docker run --rm -e PORT=8080 -p 8080:8080 booking-app
# http://localhost:8080
```

Хранилище живёт в памяти процесса, поэтому на старте бэкенд создаёт демонстрационные
типы событий. Отключается через `SEED_DEMO_DATA=false`.

## Локальная разработка

```bash
npm install && npm --prefix backend install && npm --prefix frontend install
npm run dev          # бэкенд :3000 + фронтенд :5173
npm run compile      # пересобрать OpenAPI из TypeSpec
```

## Тесты

Playwright покрывает основные пользовательские сценарии: полный путь бронирования,
недоступность занятого слота и сообщение о конфликте.

```bash
npx playwright install chromium
npm run test:e2e
```

По умолчанию тесты сами собирают и поднимают приложение на `:3100`. Чтобы прогнать
их против контейнера или задеплоенного стенда:

```bash
E2E_BASE_URL=http://localhost:8080 npm run test:e2e
```
