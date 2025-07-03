# 🧱 Домашнее задание — Неделя 2
**Курс: «Микросервисы, как в BigTech 2.0»**

---

## 📌 Задание

На этой неделе вы продолжаете развивать свои сервисы и приводите их архитектуру в соответствие с тем, что показывается в уроках. Также необходимо начать писать **unit-тесты** и обеспечить базовый уровень качества.

### ✅ Что нужно сделать:

1. Привести архитектуру **`OrderService`** к виду, показанному в уроках.
2. Привести архитектуру **`InventoryService`** к виду, показанному в уроках.
3. Привести архитектуру **`PaymentService`** к виду, показанному в уроках.
4. Настроить вывод покрытия тестами в `README.md` (например, через `go test -cover`).
5. Обеспечить **тестовое покрытие выше 40% unit-тестами** в каждом сервисе (`order`, `inventory`, `payment`).

---

📁 Ваша цель — структурировать сервисы, выделив слои: `api`, `service`, `repository`, а также покрыть ключевые usecase тестами. Архитектурный стиль — чистая архитектура.

📂 Для упрощения выполнения домашнего задания, в папке [`/boilerplates`](boilerplates) находятся вспомогательные файлы:
- `Taskfile.yml` — файл с готовыми командами для генерации кода, форматирования, линтинга и других задач. Вы можете использовать этот файл для экономии времени, скопировав его в корень вашего проекта.
  - В файле реализована команда `test-api`, которая запускает автоматические проверки вашего API. Эта команда выполняет ряд тестовых сценариев, взаимодействуя с вашими сервисами через HTTP и gRPC. Успешное прохождение этих тестов может служить подтверждением корректности реализации API.
  - Также реализована команда `test-coverage`, которая запускает юнит-тесты и выводит процент покрытия кода тестами для каждого модуля.
- `README.md` — пример файла с отображением бейджа покрытия тестами, который можно использовать в своем репозитории. **Важно**: вам потребуется адаптировать ссылку на бейдж под свой GitHub аккаунт, заменив идентификатор gist в URL.
- Папка `workflows` — содержит готовые конфигурации для CI/CD. Для использования скопируйте их в директорию `.github/workflows` в корне вашего репозитория:
  - `ci.yml` — основной конфигурационный файл, который запускает линтинг, тесты и обновляет бейдж покрытия
  - `lint-reusable.yml` — запускает проверку линтером golangci-lint
  - `test-reusable.yml` — запускает юнит-тесты для всех модулей
  - `test-coverage-reusable.yml` — рассчитывает покрытие кода тестами и обновляет бейдж в Gist

---

## 🛠 Рекомендации по оформлению проекта

1. **Соблюдайте разделение слоёв**:
  - `internal/api` — хендлеры (gRPC, HTTP)
  - `internal/service` — бизнес-логика (интерфейсы и реализации)
  - `internal/repository` — in-memory хранилище
  - `internal/repository/model`, `internal/repository/converter` — сущности и конверторы repo слоя
  - `internal/client` — внешние сервисы (например, `InventoryService`, `PaymentService`)
  - `internal/model`, `internal/converter` — сущности и конверторы сервисного слоя

2. **Сгенерируйте моки для repo и сервисного слоёв через `mockery`**:
  - Храните в `internal/repository/mocks/` и `internal/service/mocks/`.

3. **Оформите `suite_test.go` и используйте `go test -coverprofile=coverage.out`**:
  - Выведите итоговое покрытие в `README.md`.

---

## 🛠 Актуальная структура проекта

```
.
├── README.md
├── Taskfile.yml
├── buf.work.yaml
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
│   └── internal
│       ├── api
│       │   └── order
│       │       └── v1
│       │           ├── api.go
│       │           ├── cancel.go
│       │           ├── create.go
│       │           ├── get.go
│       │           ├── new_order.go
│       │           └── pay.go
│       ├── client
│       │   ├── converter
│       │   │   └── part.go
│       │   └── grpc
│       │       ├── client.go
│       │       ├── inventory
│       │       │   └── v1
│       │       │       ├── client.go
│       │       │       └── list_parts.go
│       │       ├── mocks
│       │       │   ├── mock_inventory_client.go
│       │       │   └── mock_payment_client.go
│       │       └── payment
│       │           └── v1
│       │               ├── client.go
│       │               └── pay_order.go
│       ├── converter
│       │   └── order.go
│       ├── model
│       │   ├── error.go
│       │   ├── order.go
│       │   └── part.go
│       ├── repository
│       │   ├── converter
│       │   │   └── order.go
│       │   ├── mocks
│       │   │   └── mock_order_repository.go
│       │   ├── model
│       │   │   └── order.go
│       │   ├── order
│       │   │   ├── create.go
│       │   │   ├── get.go
│       │   │   ├── repository.go
│       │   │   └── update.go
│       │   └── repository.go
│       └── service
│           ├── mocks
│           │   └── mock_order_service.go
│           ├── order
│           │   ├── cancel.go
│           │   ├── cancel_test.go
│           │   ├── create.go
│           │   ├── create_test.go
│           │   ├── get.go
│           │   ├── get_test.go
│           │   ├── pay.go
│           │   ├── pay_test.go
│           │   ├── service.go
│           │   └── suite_test.go
│           └── service.go
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

### 🔧 Комментарии

- **Каждый сервис — отдельный модуль с `go.mod`**, подключённый в `go.work`.
- **В каждом сервисе выделены слои: `api`, `service`, `repository`.**
- **Контракты и автогенерация (`protobuf`, `ogen`) находятся в `shared/`**.
- **Моки и тесты** для всех слоёв размещаются рядом с реализациями.

---

## 💡 Полезные подсказки

- 📚 Прежде чем писать код, **ознакомьтесь с уроками второй недели** — они показывают, как правильно выделить слои и писать тесты.
- 🧠 Если вы застряли — **обратитесь в чат или к ревьюеру**, особенно если за 30 минут не продвинулись.
> **Спросить — не значит сдаться.** Это ускоряет обучение и избавляет от тупиков.

---

**Автор курса: Олег Козырев, 2025**
