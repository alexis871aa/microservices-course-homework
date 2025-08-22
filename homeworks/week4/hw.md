# 🧱 Домашнее задание — Неделя 4
**Курс: «Микросервисы, как в BigTech 2.0»**

---

## 📌 Задание

На этой неделе вам предстоит добавить e2e тесты и улучшить инфраструктуру для автоматизации работы с окружением. Вы будете использовать Testcontainers для написания тестов с реальными базами данных и настроите переменные окружения для гибкой конфигурации сервисов.

### ✅ Что нужно сделать:

1. Добавить **e2e тесты** с использованием Testcontainers:
   - Написать **хотя бы один e2e-тест для `InventoryService`**, тестирующий API как черный ящик с MongoDB в контейнере с помощью testcontainers
   - Организовать запуск тестов с тегом `integration`

2. Настроить управление **переменными окружения**:
   - Создать шаблоны `.env` файлов для всех сервисов
   - Реализовать автоматическую генерацию конфигурации из шаблонов
   - Добавить поддержку загрузки переменных окружения в сервисы
   - Организовать структуру `internal/config` с интерфейсами и моками для конфигураций

3. Улучшить **Docker Compose** конфигурации:
   - Обновить конфигурации, чтобы использовать переменные окружения

4. Реализовать **платформенную библиотеку** (`/platform`):
   - Обёртка над логгером (на базе `go.uber.org/zap`)
   - Компоненты для работы с миграциями
   - Компоненты для проверки состояния сервиса (health check)
   - Интегрировать её в `OrderService`, `InventoryService`, `PaymentService`

5. Реорганизовать структуру приложений:
   - Добавить структуру `app` с точкой входа в приложение и dependency injection
   - Реализовать корректную инициализацию компонентов через DI
   - Добавить health check для проверки работоспособности сервисов

6. Обеспечить **непрерывную интеграцию**:
   - Настроить запуск e2e тестов в CI/CD пайплайне

---

## 🛠 Рекомендации по оформлению проекта

1. **Организуйте e2e тесты**:
   - Используйте теги сборки `//go:build integration` для отделения e2e тестов от юнит-тестов
   - Рекомендуемая структура тестов приведена в разделе "E2E тесты"

2. **Следуйте принципам двенадцатифакторного приложения**:
   - Храните конфигурацию в переменных окружения
   - Отделяйте конфигурацию от кода
   - Используйте одинаковые окружения для разработки и запуска

3. **Автоматизируйте управление окружением**:
   - Используйте шаблоны и генераторы конфигурации

4. **Используйте архитектуру с зависимостями**:
   - Централизуйте создание и инициализацию компонентов в пакете `app`
   - Используйте dependency injection для передачи зависимостей между компонентами
   - Выделяйте интерфейсы для компонентов, чтобы обеспечить возможность мокирования

---

## 🛠 Актуальная структура проекта

```
.
├── README.md
├── Taskfile.yml
├── buf.work.yaml
├── deploy
│   ├── compose
│   │   ├── core
│   │   │   └── docker-compose.yml
│   │   ├── inventory
│   │   │   └── docker-compose.yml
│   │   └── order
│   │       └── docker-compose.yml
│   ├── docker
│   │   └── inventory
│   │       └── Dockerfile
│   └── env
│       ├── generate-env.sh
│       ├── inventory.env.template
│       ├── order.env.template
│       └── payment.env.template
├── go.work
├── go.work.sum
├── inventory
│   ├── cmd
│   │   └── main.go
│   ├── go.mod
│   ├── go.sum
│   ├── internal
│   │   ├── api
│   │   │   └── inventory
│   │   │       └── v1
│   │   │           ├── api.go
│   │   │           ├── get.go
│   │   │           └── list.go
│   │   ├── app
│   │   │   ├── app.go
│   │   │   └── di.go
│   │   ├── config
│   │   │   ├── config.go
│   │   │   ├── env
│   │   │   │   ├── inventory_grpc.go
│   │   │   │   ├── logger.go
│   │   │   │   └── mongo.go
│   │   │   ├── interfaces.go
│   │   │   └── mocks
│   │   │       ├── mock_inventory_grpc_config.go
│   │   │       ├── mock_logger_config.go
│   │   │       └── mock_mongo_config.go
│   │   ├── converter
│   │   │   └── part.go
│   │   ├── model
│   │   │   ├── const.go
│   │   │   ├── errors.go
│   │   │   └── part.go
│   │   ├── repository
│   │   │   ├── converter
│   │   │   │   └── part.go
│   │   │   ├── mocks
│   │   │   │   └── mock_part_repository.go
│   │   │   ├── model
│   │   │   │   └── part.go
│   │   │   ├── part
│   │   │   │   ├── get.go
│   │   │   │   ├── init.go
│   │   │   │   ├── list.go
│   │   │   │   └── repository.go
│   │   │   └── repository.go
│   │   └── service
│   │       ├── mocks
│   │       │   └── mock_part_service.go
│   │       ├── part
│   │       │   ├── get.go
│   │       │   ├── get_test.go
│   │       │   ├── list.go
│   │       │   ├── list_test.go
│   │       │   ├── service.go
│   │       │   └── suite_test.go
│   │       └── service.go
│   └── tests
│       └── integration
│           ├── constants.go
│           ├── inventory_test.go
│           ├── setup.go
│           ├── suite_test.go
│           ├── teardown.go
│           └── test_environment.go
├── order
│   ├── cmd
│   │   └── main.go
│   ├── go.mod
│   ├── go.sum
│   ├── internal
│   │   ├── api
│   │   │   ├── health
│   │   │   │   └── health.go
│   │   │   └── order
│   │   │       └── v1
│   │   │           ├── api.go
│   │   │           ├── cancel.go
│   │   │           ├── create.go
│   │   │           ├── get.go
│   │   │           ├── new_order.go
│   │   │           └── pay.go
│   │   ├── app
│   │   │   ├── app.go
│   │   │   └── di.go
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
│   │   ├── config
│   │   │   ├── config.go
│   │   │   ├── env
│   │   │   │   ├── inventory_grpc.go
│   │   │   │   ├── logger.go
│   │   │   │   ├── order_http.go
│   │   │   │   ├── payment_grpc.go
│   │   │   │   └── postgres.go
│   │   │   ├── interfaces.go
│   │   │   └── mocks
│   │   │       ├── mock_inventory_grpc_config.go
│   │   │       ├── mock_logger_config.go
│   │   │       ├── mock_order_http_config.go
│   │   │       ├── mock_payment_grpc_config.go
│   │   │       └── mock_postgres_config.go
│   │   ├── converter
│   │   │   └── order.go
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
│       ├── app
│       │   ├── app.go
│       │   └── di.go
│       ├── config
│       │   ├── config.go
│       │   ├── env
│       │   │   ├── logger.go
│       │   │   └── payment_grpc.go
│       │   ├── interfaces.go
│       │   └── mocks
│       │       ├── mock_logger_config.go
│       │       └── mock_payment_grpc_config.go
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
├── platform
│   ├── go.mod
│   ├── go.sum
│   └── pkg
│       ├── closer
│       │   └── closer.go
│       ├── grpc
│       │   └── health
│       │       └── health.go
│       ├── logger
│       │   ├── logger.go
│       │   ├── logger_bench_test.go
│       │   └── noop_logger.go
│       ├── migrator
│       │   ├── migrator.go
│       │   └── pg
│       │       └── migrator.go
│       └── testcontainers
│           ├── app
│           │   ├── app.go
│           │   └── opts.go
│           ├── constants.go
│           ├── mongo
│           │   ├── config.go
│           │   ├── connect.go
│           │   ├── init.go
│           │   ├── mongo.go
│           │   └── opts.go
│           ├── network
│           │   └── network.go
│           └── path
│               └── path.go
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

## ⚙️ Переменные окружения

- В директории `deploy/env` находятся шаблоны и скрипты для управления конфигурацией:
  - `.env.template` — шаблон с дефолтными значениями переменных
  - `generate-env.sh` — скрипт для генерации `.env` файлов для всех сервисов

---

## 🧪 E2E тесты

- Используйте Testcontainers для создания временных контейнеров с базами данных в e2e тестах
- Различайте уровни тестирования:
  - **e2e-тесты** для тестирования API как черного ящика (например, для InventoryService)
- Тесты помечаются тегом `integration` для отдельного запуска через команду `task test-integration`
- Рекомендуемая структура файлов для e2e-тестов:
  ```
  .
  └── tests/integration
      ├── constants.go       # Константы для тестов
      ├── inventory_test.go  # Тесты для inventory API
      ├── setup.go           # Код для подготовки тестового окружения
      ├── suite_test.go      # Основной файл тестового suite
      ├── teardown.go        # Код для очистки после тестов
      └── test_environment.go # Настройка тестового окружения
  ```

---

## 📦 Платформенная библиотека

- `/platform` должна содержать общие компоненты для всех микросервисов:
  - Логгер для унифицированного логирования во всех сервисах (на базе `go.uber.org/zap`)
  - Мигратор для работы с базами данных (в частности PostgreSQL)
  - Утилиты для корректного закрытия ресурсов
  - Testcontainers для e2e-тестов
  - Health check компоненты для мониторинга состояния сервисов
- Платформенная библиотека должна быть отдельным Go-модулем
- Все сервисы должны использовать эту библиотеку для общей функциональности

---

## 🏗 Архитектура приложения

- Каждый сервис должен иметь структуру с четким разделением ответственности:
  - `internal/app` - точка входа в приложение, инициализация и DI
    - `app.go` - основной тип приложения
    - `di.go` - регистрация зависимостей
  - `internal/config` - компоненты для работы с конфигурацией
    - `config.go` - основной конфигурационный объект
    - `env/` - загрузка конфигурации из переменных окружения
    - `interfaces.go` - интерфейсы конфигурации
    - `mocks/` - моки для тестирования
  - `internal/api` - API слой (HTTP, gRPC)
    - `health/` - эндпоинты для проверки состояния сервиса
  - `internal/service` - бизнес-логика
  - `internal/repository` - доступ к данным
  - `internal/client` - внешние клиенты (gRPC, HTTP, Redis и так далее)

### Dependency Injection

- Используйте DI для создания и управления зависимостями между компонентами:
  - Используйте интерфейсы вместо конкретных реализаций для упрощения тестирования
  
### Сервисы состояния

- Добавьте в каждый сервис механизмы проверки состояния:
  - Реализуйте HTTP endpoint `/health` для проверки работоспособности сервиса
  - Реализуйте gRPC Health Check Service (при использовании gRPC)

### Правильная обработка shutdown

- Реализуйте корректное завершение работы сервиса:
  - Перехватывайте сигналы OS (SIGTERM, SIGINT)
  - Закрывайте ресурсы всех зависимостей приложения

---

📂 Для упрощения выполнения домашнего задания, в папке [`/boilerplates`](boilerplates) находятся вспомогательные файлы:
- `Taskfile.yml` — файл с готовыми командами для управления проектом:
  - Команды `up-*` и `down-*` для управления контейнерами сервисов
  - Команда `test-api` для проверки работоспособности API
  - Команда `test-integration` для запуска e2e тестов
  - Команда `env:generate` для создания `.env` файлов из шаблонов
- Папка `deploy` с примерами конфигураций:
  - `compose` — конфигурации Docker Compose для всех сервисов
  - `docker` — образы Docker для сервисов
  - `env` — шаблоны переменных окружения и скрипты для их генерации
- Папка `pkg` с полезными библиотеками, которые нужно перенести в платформенную библиотеку:
  - `testcontainers` — готовые обертки для Testcontainers (MongoDB, сети, приложения)
  - `closer` — утилиты для корректного освобождения ресурсов
- `README.md` — пример файла с отображением бейджа покрытия тестами. **Важно**: вам потребуется адаптировать ссылку на бейдж под свой GitHub аккаунт, заменив идентификатор gist в URL.
- Папка `workflows` — содержит готовые конфигурации для CI/CD. Для использования скопируйте их в директорию `.github/workflows` в корне вашего репозитория:
  - `ci.yml` — основной конфигурационный файл для CI
  - `lint-reusable.yml` — запускает проверку линтером
  - `test-reusable.yml` — запускает юнит-тесты
  - `test-coverage-reusable.yml` — рассчитывает покрытие кода тестами и обновляет бейдж
  - `test-integration-reusable.yml` — запускает e2e тесты

---

## 💡 Полезные подсказки

- 📚 Изучите подход к написанию e2e тестов с Testcontainers из уроков 4-й недели
- 🔄 Используйте готовые утилиты из `shared/pkg/testcontainers` для упрощения работы с контейнерами
- 🧠 Если возникают трудности с настройкой — обращайтесь в чат или к ревьюеру
> **Спросить — не значит сдаться.** Это ускоряет обучение.

---

**Автор курса: Олег Козырев, 2025**
