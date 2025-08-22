# 🧱 Домашнее задание — Неделя 8
**Курс: «Микросервисы, как в BigTech 2.0»**

---

## 📌 Задание

На этой неделе вы настраиваете единый вход в систему через Envoy Proxy:

1. Envoy проксирует HTTP↔HTTP на `OrderService`.
2. Envoy транскодирует HTTP↔gRPC на `InventoryService` (JSON↔gRPC) через `grpc_json_transcoder`.
3. Аутентификация всех защищённых маршрутов через `ext_authz` с обращением в `IAM`.
4. В `IAM` реализовать ручку `Check` по контракту Envoy ext_authz (gRPC).

---

### ✅ Что нужно сделать

1) Envoy как шлюз:
- Разместить конфиг `envoy.yaml` (в `deploy/envoy/envoy.yaml`).
- Сконфигурировать listener с HTTP-фильтрами: `envoy.filters.http.ext_authz` и `envoy.filters.http.grpc_json_transcoder`.
- Настроить маршруты:
  - `/order` → кластер `order_service` (HTTP↔HTTP, без транскодера).
  - `/inventory` → кластер `inventory_service` (HTTP↔gRPC через транскодер).

2) HTTP↔HTTP для OrderService:
- Проброс всех методов/путей под `/order` на апстрим `OrderService` без изменений протокола.

3) HTTP↔gRPC для InventoryService:
- Подключить `grpc_json_transcoder` с указанием дескриптора и сервиса, чтобы REST-запросы к `/inventory/...` конвертировались в gRPC методы `InventoryService` и обратно.

4) Аутентификация через IAM:
- Включить фильтр `ext_authz` для всех защищённых маршрутов.
- Исключить публичные эндпоинты (например, `/auth/login`, `/auth/register`, `/healthz`).
- Реализовать в `IAM` gRPC-сервис `envoy.service.auth.v3.Authorization/Check`. На успех — 200/OK, на отказ — 401/403. Возвращайте заголовки с user-id/roles по необходимости.

---


## 🔐 Контракт IAM для ext_authz

- gRPC: реализовать `envoy.service.auth.v3.Authorization` с методом `Check`.
- На вход: метаданные запроса (заголовки, путь, метод). На выход: `OK`/`Denied` и опциональные заголовки.
- Подробнее смотрите protobuf Envoy: `envoy/service/auth/v3/external_auth.proto`.

Полезно: в `week6/contracts/iam_service_contracts.md` лежит контекст по IAM.

---

## 🛠 Рекомендации

- Разместите артефакт дескриптора для `grpc_json_transcoder` (например, `inventory.pb`) в образе Envoy (`/etc/envoy/`).
- Для gRPC-кластеров включайте `http2_protocol_options: {}`.
- Пропускайте технические пути (`/healthz`, `/metrics`, `/readyz`) без аутентификации либо по отдельному `virtual_host`.
- Конфигурацию держите в репозитории (`deploy/envoy/envoy.yaml`) и снабдите простым `docker-compose` для локального запуска.

---

## 💡 Полезные подсказки

- Если REST-роуты не совпадают с gRPC методами — используйте `auto_mapping: true` и/или явно опишите HTTP rules в proto через `google.api.http` (и пересоберите дескриптор).
- При проблемах с авторизацией включите логи Envoy по категориям `ext_authz`, `router`, `upstream`.
- Для отладки транскодера проверяйте, что дескриптор собран с тем же proto, что использует `InventoryService`.

---

**Автор курса: Олег Козырев, 2025**
