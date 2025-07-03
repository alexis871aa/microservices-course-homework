# 🧱 Домашнее задание — Неделя 3
**Курс: «Микросервисы, как в BigTech 2.0»**

---

## 📌 Задание

На этой неделе вы начнёте работать с реальными хранилищами данных. Нужно заменить in-memory-реализации на полноценные СУБД и добавить поддержку миграций, а также docker-compose для локального запуска зависимостей сервисов.

### ✅ Что нужно сделать:

1. Добавить `docker-compose.yml`:
    - `PostgreSQL` — для `OrderService`
    - `MongoDB` — для `InventoryService`
    - Разместить конфигурации в `deploy/compose/`:
        - `deploy/compose/core/docker-compose.yml` - описание общей сети
        - `deploy/compose/order/docker-compose.yml` - описание зависимостей Order Service (пока только PostgreSQL)
        - `deploy/compose/inventory/docker-compose.yml` - описание зависимостей Inventory Service (пока только MongoDB)
2. В `OrderService`:
    - Заменить `map`-хранилище на работу с PostgreSQL
    - Добавить миграции (`order/migrations/*.sql`)
    - Обеспечить автоматический запуск миграций при старте сервиса
3. В `InventoryService`:
    - Заменить `map`-хранилище на MongoDB
4. Обновить unit-тесты (если нужно), чтобы работали с новой реализацией

---

## 🛠 Рекомендации по оформлению проекта

1. **Используйте монорепозиторий с Go Workspaces**:
  - Продолжайте работу с файлом `go.work`.
  - Поддерживайте порядок и единообразие между сервисами.

2. **Следуйте принципам чистой архитектуры**:
  - Не смешивайте работу с базой данных с бизнес-логикой.
  - Используйте интерфейсы для абстрагирования от конкретных СУБД.

3. **Автоматизируйте настройку окружения**:
  - Используйте Docker Compose для локальной разработки.
  - Обеспечьте автоматическое применение миграций.

---

## 🛠 Актуальная структура проекта

```
.
├── README.md
├── Taskfile.yml
├── buf.work.yaml
├── deploy
│   └── compose
│       ├── core
│       │   └── docker-compose.yml
│       ├── inventory
│       │   └── docker-compose.yml
│       └── order
│           └── docker-compose.yml
├── go.work
├── go.work.sum
├── inventory
│   ├── cmd
│   │   └── main.go
│   ├── go.mod
│   ├── go.sum
│   └── internal
│       ├── api
│       │   └── inventory
│       │       └── v1
│       │           ├── api.go
│       │           ├── get.go
│       │           └── list.go
│       ├── converter
│       │   └── part.go
│       ├── model
│       │   ├── errors.go
│       │   └── part.go
│       ├── repository
│       │   ├── converter
│       │   │   └── part.go
│       │   ├── mocks
│       │   │   └── mock_part_repository.go
│       │   ├── model
│       │   │   └── part.go
│       │   ├── part
│       │   │   ├── get.go
│       │   │   ├── init.go
│       │   │   ├── list.go
│       │   │   └── repository.go
│       │   └── repository.go
│       └── service
│           ├── mocks
│           │   └── mock_part_service.go
│           ├── part
│           │   ├── get.go
│           │   ├── get_test.go
│           │   ├── list.go
│           │   ├── list_test.go
│           │   ├── service.go
│           │   └── suite_test.go
│           └── service.go
├── order
│   ├── cmd
│   │   └── main.go
│   ├── go.mod
│   ├── go.sum
│   ├── internal
│   │   ├── api
│   │   │   └── order
│   │   │       └── v1
│   │   │           ├── api.go
│   │   │           ├── cancel.go
│   │   │           ├── create.go
│   │   │           ├── get.go
│   │   │           ├── new_order.go
│   │   │           └── pay.go
│   │   ├── client
│   │   │   ├── converter
│   │   │   │   └── part.go
│   │   │   └── grpc
│   │   │       ├── client.go
│   │   │       ├── inventory
│   │   │       │   └── v1
│   │   │       │       ├── client.go
│   │   │       │       └── list_parts.go
│   │   │       ├── mocks
│   │   │       │   ├── mock_inventory_client.go
│   │   │       │   └── mock_payment_client.go
│   │   │       └── payment
│   │   │           └── v1
│   │   │               ├── client.go
│   │   │               └── pay_order.go
│   │   ├── converter
│   │   │   └── order.go
│   │   ├── migrator
│   │   │   └── migrator.go
│   │   ├── model
│   │   │   ├── error.go
│   │   │   ├── order.go
│   │   │   └── part.go
│   │   ├── repository
│   │   │   ├── converter
│   │   │   │   └── order.go
│   │   │   ├── mocks
│   │   │   │   └── mock_order_repository.go
│   │   │   ├── model
│   │   │   │   └── order.go
│   │   │   ├── order
│   │   │   │   ├── create.go
│   │   │   │   ├── get.go
│   │   │   │   ├── repository.go
│   │   │   │   └── update.go
│   │   │   └── repository.go
│   │   └── service
│   │       ├── mocks
│   │       │   └── mock_order_service.go
│   │       ├── order
│   │       │   ├── cancel.go
│   │       │   ├── cancel_test.go
│   │       │   ├── create.go
│   │       │   ├── create_test.go
│   │       │   ├── get.go
│   │       │   ├── get_test.go
│   │       │   ├── pay.go
│   │       │   ├── pay_test.go
│   │       │   ├── service.go
│   │       │   └── suite_test.go
│   │       └── service.go
│   └── migrations
│       ├── 20250404191615_create_uuid_ossp_extension.sql
│       └── 20250404191624_create_orders_table.sql
├── package-lock.json
├── package.json
├── payment
│   ├── cmd
│   │   └── main.go
│   ├── go.mod
│   ├── go.sum
│   └── internal
│       ├── api
│       │   └── payment
│       │       └── v1
│       │           ├── api.go
│       │           └── pay.go
│       ├── model
│       │   └── errors.go
│       └── service
│           ├── mocks
│           │   └── mock_payment_service.go
│           ├── payment
│           │   ├── pay.go
│           │   ├── pay_test.go
│           │   ├── service.go
│           │   └── suite_test.go
│           └── service.go
└── shared
    ├── api
    │   └── order
    │       └── v1
    │           ├── components
    │           │   ├── create_order_request.yaml
    │           │   ├── create_order_response.yaml
    │           │   ├── enums
    │           │   │   ├── order_status.yaml
    │           │   │   └── payment_method.yaml
    │           │   ├── errors
    │           │   │   ├── bad_gateway_error.yaml
    │           │   │   ├── bad_request_error.yaml
    │           │   │   ├── conflict_error.yaml
    │           │   │   ├── forbidden_error.yaml
    │           │   │   ├── generic_error.yaml
    │           │   │   ├── internal_server_error.yaml
    │           │   │   ├── not_found_error.yaml
    │           │   │   ├── rate_limit_error.yaml
    │           │   │   ├── service_unavailable_error.yaml
    │           │   │   ├── unauthorized_error.yaml
    │           │   │   └── validation_error.yaml
    │           │   ├── get_order_response.yaml
    │           │   ├── order_dto.yaml
    │           │   ├── pay_order_request.yaml
    │           │   └── pay_order_response.yaml
    │           ├── order.openapi.yaml
    │           ├── params
    │           │   └── order_uuid.yaml
    │           └── paths
    │               ├── order_by_uuid.yaml
    │               ├── order_cancel.yaml
    │               ├── order_pay.yaml
    │               └── orders.yaml
    ├── go.mod
    ├── go.sum
    ├── pkg
    │   ├── openapi
    │   │   └── order
    │   │       └── v1
    │   │           ├── oas_cfg_gen.go
    │   │           ├── oas_client_gen.go
    │   │           ├── oas_handlers_gen.go
    │   │           ├── oas_interfaces_gen.go
    │   │           ├── oas_json_gen.go
    │   │           ├── oas_labeler_gen.go
    │   │           ├── oas_middleware_gen.go
    │   │           ├── oas_operations_gen.go
    │   │           ├── oas_parameters_gen.go
    │   │           ├── oas_request_decoders_gen.go
    │   │           ├── oas_request_encoders_gen.go
    │   │           ├── oas_response_decoders_gen.go
    │   │           ├── oas_response_encoders_gen.go
    │   │           ├── oas_router_gen.go
    │   │           ├── oas_schemas_gen.go
    │   │           ├── oas_server_gen.go
    │   │           ├── oas_unimplemented_gen.go
    │   │           └── oas_validators_gen.go
    │   └── proto
    │       ├── inventory
    │       │   └── v1
    │       │       ├── inventory.pb.go
    │       │       └── inventory_grpc.pb.go
    │       └── payment
    │           └── v1
    │               ├── payment.pb.go
    │               └── payment_grpc.pb.go
    └── proto
        ├── buf.gen.yaml
        ├── buf.yaml
        ├── inventory
        │   └── v1
        │       └── inventory.proto
        └── payment
            └── v1
                └── payment.proto
```

---

## ⚙️ Docker Compose

- Конфигурации docker-compose расположены в `deploy/compose/`:
    - `deploy/compose/core/docker-compose.yml` — инфраструктура
    - `deploy/compose/order/docker-compose.yml` — Postgres
    - `deploy/compose/inventory/docker-compose.yml` — MongoDB

---

## 🔄 Миграции

- Миграции находятся в `order/migrations/` в виде `.sql` файлов.
- Миграции должны применяться автоматически при старте OrderService (через `goose`).

---

📂 Для упрощения выполнения домашнего задания, в папке [`/boilerplates`](boilerplates) находятся вспомогательные файлы:
- `Taskfile.yml` — файл с готовыми командами для генерации кода, форматирования, линтинга и работы с Docker Compose:
  - Команды `up-*` и `down-*` для управления контейнерами различных сервисов (`up-core`, `up-inventory`, `up-order`, `up-all` и т.д.)
  - Команда `test-api` для проверки работоспособности API всех сервисов
  - Команда `test-coverage` для расчёта покрытия кода тестами
- Папка `deploy/compose` с готовыми примерами Docker Compose конфигураций:
  - `core` — базовая сеть для микросервисов
  - `order` — конфигурация для PostgreSQL
  - `inventory` — конфигурация для MongoDB
- `README.md` — пример файла с отображением бейджа покрытия тестами, который можно использовать в своем репозитории. **Важно**: вам потребуется адаптировать ссылку на бейдж под свой GitHub аккаунт, заменив идентификатор gist в URL.
- Папка `workflows` — содержит готовые конфигурации для CI/CD. Для использования скопируйте их в директорию `.github/workflows` в корне вашего репозитория:
  - `ci.yml` — основной конфигурационный файл, который запускает линтинг, тесты и обновляет бейдж покрытия
  - `lint-reusable.yml` — запускает проверку линтером golangci-lint
  - `test-reusable.yml` — запускает юнит-тесты для всех модулей
  - `test-coverage-reusable.yml` — рассчитывает покрытие кода тестами и обновляет бейдж в Gist

---

## 💡 Полезные подсказки

- 📚 Посмотрите уроки третьей недели — они показывают подключение баз, структуру миграций и docker-compose.
- 🧠 Если застряли — спрашивайте. Чем раньше — тем лучше.
> **Спросить — не значит сдаться.** Это ускоряет обучение.

---

**Автор курса: Олег Козырев, 2025**
