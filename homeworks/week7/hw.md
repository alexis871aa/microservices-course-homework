# 🧱 Домашнее задание — Неделя 7
**Курс: «Микросервисы, как в BigTech 2.0»**

---

## 📌 Задание

На этой неделе вы:

1. Настроите Envoy как входную точку в систему с маршрутизацией и проверкой сессии.
2. Реализуете новый сервис — `NotificationService`, который будет обрабатывать события из Kafka и уведомлять пользователей через Telegram.

---

### ✅ Что нужно сделать:

#### 🔸 1. Envoy Gateway

- Настроить **Envoy Proxy** как внешний шлюз к сервисам.
- Прописать **HTTP-маршруты** для каждого доступного сервиса (например, `/order`, `/auth`, `/payment` и т.д.)
- Добавить **проверку сессии**:
    - Все запросы, кроме публичных (например, `Register`, `Login`), должны перед обработкой обращаться к `AuthService.Whoami`.
    - Если сессия невалидна — вернуть 401.
    - Реализация может быть через **Lua-скрипт**, **ext_authz** или отдельный микросервис-аутентификатор.

#### 🔸 2. NotificationService

Создать новый микросервис `NotificationService` с полной архитектурой:
- `cmd/`, `internal/`, `config`, `service`, `di`, `client`, `consumer`

Реализовать в нём:

✅ Kafka Consumer:

- Подписаться на топики `OrderPaid` и `ShipAssembled`.
- Контракты событий описаны в:
    - [`contracts/order_service_contracts.md`](contracts/order_service_contracts.md)
    - [`contracts/assembly_service_contracts.md`](contracts/assembly_service_contracts.md)

✅ Запрос в AuthService:

- При получении события — вызвать `GetUser` (gRPC) у `AuthService`.
- Проверить поле `notification_methods`.

✅ Интеграция с Telegram:

- Если среди методов есть `telegram`, отправить уведомление через созданного Telegram-бота.

---

## 🧱 Требуемая структура проекта (NotificationService)

```
/notification/
├── go.mod
├── .env
├── /cmd/
│   └── main.go                          # Точка входа, загрузка DI и запуск
├── /internal/
│   ├── api/                             # Пока не используется
│   ├── config/
│   │   ├── config.go                    # Интерфейсы конфигурации
│   │   └── env/
│   │       └── env_config.go           # Реализация через переменные окружения
│   ├── di/
│   │   ├── app.go                       # Bootstrap, graceful shutdown
│   │   └── service_provider.go         # Самописный DI-контейнер
│   ├── client/
│   │   ├── grpc/
│   │   │   ├── client.go               # Интерфейсы gRPC-клиентов
│   │   │   └── auth_service/           # gRPC-клиент AuthService
│   │   └── http/
│   │       ├── client.go               # Интерфейсы HTTP-клиентов
│   │       └── telegram/               # Клиент Telegram API
│   │           └── telegram.go
│   ├── service/
│   │   ├── service.go                  # Интерфейс NotificationService
│   │   ├── notify/                     # Реализация NotificationService
│   │   │   └── service.go              # Обработка и отправка уведомлений
│   │   └── consumer/                   # Kafka-консьюмеры
│   │       ├── order_paid/             # Обработка события OrderPaid
│   │       │   ├── consumer.go
│   │       │   └── handler.go
│   │       └── ship_assembled/         # Обработка события ShipAssembled
│   │           ├── consumer.go
│   │           └── handler.go

```

---

## ✅ Требования к реализации

### Envoy:

- Разместить конфиг `envoy.yaml` в корне или в `/infra/envoy/`
- Пример маршрутизации:
  ```yaml
  - match:
      prefix: /order
    route:
      cluster: order_service
  ```

- Проверка сессии:
    - Настроить ext_authz или написать Lua-фильтр, который делает gRPC/HTTP-запрос в `AuthService.Whoami`
    - Пример в уроках недели

### Kafka Consumer:

- Подключиться к Kafka через `platform/kafka`
- Один Consumer — одно событие
- Обработка каждого события:
    - Обратиться в `AuthService.GetUser`
    - Распарсить список `notification_methods`
    - Отправить уведомления через доступные каналы (начиная с Telegram)

### Telegram:

- Можно реализовать через HTTP-запрос к Telegram Bot API
- Описание канала для отправки — в `target` поля `NotificationMethod`

---

## 💡 Полезные подсказки

- **Сначала изучите материалы седьмой недели**:  
  В уроках седьмой недели представлены примеры и объяснения, которые помогут в выполнении задания.  
  👉 Рекомендуется сначала ознакомиться с уроками, а затем приступать к реализации.

- **Не стесняйтесь обращаться за помощью**:  
  Если вы столкнулись с трудностями или у вас возникли вопросы, обращайтесь в чат курса или к своему ревьюеру (если вы на тарифе с проверкой).
  > Если в течение 30 минут вы не можете решить проблему, лучше попросить помощи.  
  > **Спросить — не значит сдаться**, это ускоряет процесс обучения благодаря опыту других.

---

**Автор курса: Олег Козырев, 2025**
