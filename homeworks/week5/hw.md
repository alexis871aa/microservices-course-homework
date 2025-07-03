# 🧱 Домашнее задание — Неделя 5
**Курс: «Микросервисы, как в BigTech 2.0»**

---

## 📌 Задание

На этой неделе вы начинаете работу с асинхронной коммуникацией между сервисами с помощью Apache Kafka. Вам нужно развернуть брокеры Kafka и реализовать взаимодействие между `OrderService` и новым `AssemblyService` через события.

### ✅ Что нужно сделать:

1. Поднять Kafka в KRaft-режиме с одним брокером:
    - Разместить конфигурацию в deploy/compose/core/docker-compose.yml 
    - Использовать официальный образ Kafka без ZooKeeper (KRaft)

2. Создать AssemblyService:
    - Оформлен в том же стиле: cmd/, internal/, слои api, service, di, config 
    - Поддержка .env, конфигов, DI 
    - Kafka Consumer: слушает событие OrderPaid, после получения — ждёт 10 секунд 
    - Kafka Producer: отправляет событие ShipAssembled после задержки

3. В `AssemblyService`:
    - Реализовать **Kafka Consumer**, который обрабатывает событие об оплате заказа (`OrderPaid`)
        - Контракт события описан в [`contracts/assembly_service_contracts.md`](contracts/assembly_service_contracts.md)
        - Логика: при получении события — подождать 10 секунд
    - Реализовать **Kafka Producer**, который отправляет событие об успешной сборке (`ShipAssembled`) после задержки
        - Контракт события описан в [`contracts/assembly_service_contracts.md`](contracts/assembly_service_contracts.md)

4. В `OrderService`:
    - Реализовать **Kafka Producer**, который отправляет событие `OrderPaid` после успешной оплаты
        - Контракт описан в [`contracts/order_service_contracts.md`](contracts/order_service_contracts.md)
    - Реализовать **Kafka Consumer**, который обрабатывает событие `ShipAssembled` и обновляет статус заказа на `COMPLETED`
        - Контракт события описан в [`contracts/assembly_service_contracts.md`](contracts/assembly_service_contracts.md)
          
5. Создать `NotificationService`:
    - Архитектура такая же (DI, config, api, service)
    - Kafka Consumer: слушает события OrderPaid и ShipAssembled
    - Подключает Telegram-бота и отправляет уведомления в чат с захардкоженым ID (шаблоны сообщений, логика отправки)
    - У Telegram-бота зарегистрирован enдпоинт `/start`, который отвечает приветственным сообщением

---

📂 Для упрощения выполнения домашнего задания, в папке [`/boilerplates`](boilerplates) находятся вспомогательные файлы:
- `Taskfile.yml` — файл с готовыми командами для управления проектом:
  - Команды для работы с Kafka и Docker Compose
  - Команда `test-api` для проверки работоспособности API  
  - Команды для генерации кода и линтинга
- Папка `kafka/` — готовые компоненты для работы с Kafka:
  - `consumer/` — базовая реализация Kafka Consumer
  - `producer/` — базовая реализация Kafka Producer  
  - `kafka.go` — общие утилиты для работы с Kafka
- Папка `deploy/` — конфигурации для развертывания:
  - `compose/` — Docker Compose файлы для всех сервисов
  - `env/` — шаблоны переменных окружения и скрипты для их генерации
  - `docker/` — Docker образы для сервисов
- Папка `middleware/kafka/` — middleware для логирования Kafka сообщений

---

📁 Контракты событий находятся в папке [`/contracts`](contracts) и описывают взаимодействие между сервисами через Kafka:
- [`assembly_service_contracts.md`](contracts/assembly_service_contracts.md) — события для `AssemblyService`: `OrderPaid` (входящее) и `ShipAssembled` (исходящее)
- [`order_service_contracts.md`](contracts/order_service_contracts.md) — события для `OrderService`: `OrderPaid` (исходящее)

📌 Контракты описывают **структуру событий для Kafka** — к финалу ваша реализация должна соответствовать этим контрактам для корректного взаимодействия между сервисами.

---

## 🛠 Рекомендации по оформлению проекта

1. **Соблюдайте единую архитектуру для всех сервисов**:
   - Структурируйте новый `AssemblyService` по аналогии с существующими сервисами
   - Выделяйте слои api, service, repository, config
   - Используйте dependency injection через пакет app/di
   - Поддерживайте общий стиль оформления кода

2. **Работа с Kafka**:
   - Используйте общую библиотеку из `/platform/kafka`
   - Разделяйте консьюмеры и продьюсеры на отдельные компоненты
   - Применяйте контракты для сериализации/десериализации событий

3. **Управление конфигурацией**:
   - Используйте переменные окружения для конфигурации Kafka и сервисов
   - Создайте шаблон `.env.template` для `NotificationService` и `Assembly Service`
   - Соблюдайте единообразие именования переменных в конфигах

4. **Docker и инфраструктура**:
   - Добавьте Kafka в docker-compose.yml с поддержкой KRaft-режима
   - Убедитесь, что все сервисы подключаются к одной сети Docker

---

## 🛠 Актуальная структура проекта

```
.
├── README.md
├── Taskfile.yml
├── assembly
│   ├── cmd
│   │   └── main.go
│   ├── go.mod
│   ├── go.sum
│   └── internal
│       ├── app
│       │   ├── app.go
│       │   └── di.go
│       ├── config
│       │   ├── config.go
│       │   ├── env
│       │   │   ├── kafka.go
│       │   │   ├── logger.go
│       │   │   ├── order_assembled_producer.go
│       │   │   └── order_paid_consumer.go
│       │   ├── interfaces.go
│       │   └── mocks
│       │       ├── mock_kafka_config.go
│       │       ├── mock_logger_config.go
│       │       ├── mock_order_assembled_producer_config.go
│       │       └── mock_order_paid_consumer_config.go
│       ├── converter
│       │   └── kafka
│       │       ├── decoder
│       │       │   └── order_paid.go
│       │       └── kafka.go
│       ├── model
│       │   └── events.go
│       └── service
│           ├── consumer
│           │   └── order_consumer
│           │       ├── consumer.go
│           │       └── handler.go
│           ├── mocks
│           │   ├── mock_consumer_service.go
│           │   └── mock_order_producer_service.go
│           ├── producer
│           │   └── order_producer
│           │       └── producer.go
│           └── service.go
├── buf.work.yaml
├── deploy
│   ├── compose
│   │   ├── assembly
│   │   ├── core
│   │   │   └── docker-compose.yml
│   │   ├── inventory
│   │   │   └── docker-compose.yml
│   │   ├── notification
│   │   ├── order
│   │   │   └── docker-compose.yml
│   │   └── payment
│   ├── docker
│   │   └── inventory
│   │       └── Dockerfile
│   └── env
│       ├── assembly.env.template
│       ├── core.env.template
│       ├── generate-env.sh
│       ├── inventory.env.template
│       ├── notification.env.template
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
├── notification
│   ├── cmd
│   │   └── main.go
│   ├── go.mod
│   ├── go.sum
│   └── internal
│       ├── app
│       │   ├── app.go
│       │   └── di.go
│       ├── client
│       │   └── http
│       │       ├── client.go
│       │       └── telegram
│       │           └── client.go
│       ├── config
│       │   ├── config.go
│       │   ├── env
│       │   │   ├── kafka.go
│       │   │   ├── logger.go
│       │   │   ├── order_assembled_consumer.go
│       │   │   ├── order_paid_consumer.go
│       │   │   └── telegram_bot.go
│       │   ├── interfaces.go
│       │   └── mocks
│       │       ├── mock_kafka_config.go
│       │       ├── mock_logger_config.go
│       │       ├── mock_order_assembled_consumer_config.go
│       │       ├── mock_order_paid_consumer_config.go
│       │       └── mock_telegram_bot_config.go
│       ├── converter
│       │   └── kafka
│       │       ├── decoder
│       │       │   ├── order_assembled.go
│       │       │   └── order_paid.go
│       │       └── kafka.go
│       ├── model
│       │   └── events.go
│       └── service
│           ├── consumer
│           │   ├── order_assembled_consumer
│           │   │   ├── consumer.go
│           │   │   └── handler.go
│           │   └── order_paid_consumer
│           │       ├── consumer.go
│           │       └── handler.go
│           ├── mocks
│           │   ├── mock_order_assembled_consumer_service.go
│           │   ├── mock_order_paid_consumer_service.go
│           │   └── mock_telegram_service.go
│           ├── service.go
│           └── telegram
│               ├── service.go
│               └── templates
│                   ├── assembled_notification.tmpl
│                   └── paid_notification.tmpl
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
│   │   │   │   ├── kafka.go
│   │   │   │   ├── logger.go
│   │   │   │   ├── order_assembled_consumer.go
│   │   │   │   ├── order_http.go
│   │   │   │   ├── order_paid_producer.go
│   │   │   │   ├── payment_grpc.go
│   │   │   │   └── postgres.go
│   │   │   ├── interfaces.go
│   │   │   └── mocks
│   │   │       ├── mock_inventory_grpc_config.go
│   │   │       ├── mock_kafka_config.go
│   │   │       ├── mock_logger_config.go
│   │   │       ├── mock_order_assembled_consumer_config.go
│   │   │       ├── mock_order_http_config.go
│   │   │       ├── mock_order_paid_producer_config.go
│   │   │       ├── mock_payment_grpc_config.go
│   │   │       └── mock_postgres_config.go
│   │   ├── converter
│   │   │   ├── kafka
│   │   │   │   ├── decoder
│   │   │   │   │   └── order_assembled.go
│   │   │   │   └── kafka.go
│   │   │   └── order.go
│   │   ├── model
│   │   │   ├── error.go
│   │   │   ├── events.go
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
│   │       ├── consumer
│   │       │   └── order_consumer
│   │       │       ├── consumer.go
│   │       │       └── handler.go
│   │       ├── mocks
│   │       │   ├── mock_consumer_service.go
│   │       │   ├── mock_order_producer_service.go
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
│   │       ├── producer
│   │       │   └── order_producer
│   │       │       └── producer.go
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
│       ├── kafka
│       │   ├── consumer
│       │   │   ├── consumer.go
│       │   │   ├── group_handler.go
│       │   │   └── message.go
│       │   ├── kafka.go
│       │   └── producer
│       │       └── producer.go
│       ├── logger
│       │   ├── logger.go
│       │   ├── logger_bench_test.go
│       │   └── noop_logger.go
│       ├── middleware
│       │   └── kafka
│       │       └── logging.go
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
    │       ├── events
    │       │   └── v1
    │       │       └── order.pb.go
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
        ├── events
        │   └── v1
        │       └── order.proto
        ├── inventory
        │   └── v1
        │       └── inventory.proto
        └── payment
            └── v1
                └── payment.proto
```

---

### 🔧 Комментарии

- **Каждый сервис — отдельный модуль с `go.mod`**, подключённый в `go.work`.
- **В каждом сервисе выделены слои: `api`, `service`, `client`, `repository`, `config`, `app`** с dependency injection.
- **Новые сервисы `AssemblyService` и `NotificationService`** построены по той же архитектуре.
- **Kafka consumer/producer компоненты** выделены в отдельные сервисы в service слое.
- **Платформенная библиотека `/platform`** содержит общие компоненты для работы с Kafka, логированием и другими задачами.

---

## ✅ Требования к реализации

### Kafka:

- Один брокер Kafka, режим KRaft (без ZooKeeper)
- Использовать confluentinc/cp-kafka образ

### AssemblyService:

- Kafka Consumer:
    - Слушает `OrderPaid`
    - После получения запускает фоновую задачу с задержкой (rand от 1 до 10 сек)
- Kafka Producer:
    - После задержки публикует `ShipAssembled`

### OrderService:

- Kafka Producer:
    - После вызова `PayOrder` публикует `OrderPaid`
- Kafka Consumer:
    - Слушает `ShipAssembled`
    - Обновляет статус заказа на `ASSEMBLED`

### NotificationService:

- Consumer OrderPaid и ShipAssembled 
- Отправка уведомлений в Telegram 
- Архитектура: конфиги, DI, client, service

---

## 💡 Полезные подсказки

- 📚 Ознакомьтесь с материалами пятой недели: там подробно разобрано, как поднять Kafka, подключить её к сервисам и работать с событиями
- 🤖 Зарегистрируйте Telegram-бота и получите токен у @BotFather
- 🧪 Проверьте, что сообщения корректно приходят в Kafka и потребляются подписанными сервисами
- 🛠 Используйте логгер из /platform/logger для отладки всех этапов работы с Kafka и Telegram
- 🧠 Если возникают трудности с настройкой — обращайтесь в чат или к ревьюеру
> **Спросить — не значит сдаться.** Это ускоряет обучение.

---

**Автор курса: Олег Козырев, 2025**
