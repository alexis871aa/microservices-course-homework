# 🧱 Домашнее задание — Неделя 7
**Курс: «Микросервисы, как в BigTech 2.0»**

---

## 📌 Задание

На этой неделе настраиваем продвинутое логирование (Zap + OpenTelemetry Collector → Elasticsearch + Kibana), а также добавляем метрики (Prometheus + Grafana) и распределённый трейсинг (OpenTelemetry). 

---

### ✅ Что нужно сделать:

#### 🔸 1. Инфраструктура логов (OTEL Collector + Elasticsearch + Kibana)

- В `deploy/compose/core/docker-compose.yml` добавить сервисы:
  - `otel-collector` (последний официальный образ `otel/opentelemetry-collector`)
  - `elasticsearch` и `kibana` (последние стабильные образы 9.x)
- Создать конфиг коллектора `deploy/otel/collector.yaml` с конвейером логов:
  - `receivers: otlp` (grpc: 4317, http: 4318)
  - `processors: batch` (и при необходимости `resource` для добавления сервисных атрибутов)
  - `exporters: elasticsearch` (подключение к ES, индекс например `otel-logs-%{+yyyy.MM.dd}`)
  - `service.pipelines.logs`: `[otlp -> batch -> elasticsearch]`


---

#### 🔸 2. Платформенный логер: два источника вывода (stdout + OTEL Collector)

- В `/platform/pkg/logger`:
  - Расширить конфиг логера, добавив поддержку нескольких выходов:
    - `LOG_OUTPUTS` — `stdout`, `otlp`.
    - `OTEL_COLLECTOR_ENDPOINT` — адрес OTLP/gRPC (например `otel-collector:4317`)
    - `SERVICE_NAME`, `LOG_LEVEL`.
  - Собрать `zap.Logger` из двух `zapcore.Core`:
    - Core 1: JSON encoder → stdout
    - Core 2: OTLP лог-экспортер → OTEL Collector (OTLP/gRPC)
  - Сохранить совместимость API (`Info`, `Error`, поля `zap`) для существующего кода.
  - Добавить корректный shutdown экспортера.

Структура платформы (изменения):

---

#### 🔸 3. Интеграция нового логера во все сервисы

- Во всех сервисах (`order`, `inventory`, `payment`, `assembly`, `notification`, `iam`):
  - Обновить `internal/config/env/logger.go` (или эквивалент) — добавить `OTEL_COLLECTOR_ENDPOINT`, `LOG_LEVEL`, `SERVICE_NAME`.
  - В `internal/app/di.go` инициализировать логер из платформы и прокинуть через DI во все слои (`api`, `service`, `consumer`, `producer`, `repository`, `client`).
  - Удалить обходные логеры/вызовы, оставить только платформенный логер.
- Проверить, что логи видны в stdout, попадают в Elasticsearch и отображаются в Kibana.

---

#### 🔸 4. Метрики (Prometheus + Grafana)

- Поднять/подключить **Prometheus** в `deploy/compose/core/docker-compose.yml`.
- В `OrderService` добавить метрики:
  - `orders_total` — счётчик созданных заказов
  - `orders_revenue_total` — суммарная выручка (Counter/CounterVec по валюте — по желанию)
- В `AssemblyService` добавить метрику:
  - `assembly_duration_seconds` — гистограмма длительности сборки
- Поднять **Grafana**, подключить Prometheus и отобразить графики указанных метрик на дашборде.

---

#### 🔸 5. Трейсинг (Distributed Tracing)

- Интегрировать **OpenTelemetry Tracing** в `OrderService` и `PaymentService`.
- Реализовать распределённый трейс сценария оплаты:
  - При `PayOrder` создать root span в `OrderService`.
  - Пробросить контекст в `PaymentService` (gRPC metadata) и продолжить трейс.
  - Убедиться, что в трейс-UI видно цепочку: `HTTP → OrderService → gRPC → PaymentService`.
- Экспорт трейсов через OTLP в OTEL Collector; далее в Jaeger.

---

## 💡 Полезные подсказки

- Держите формат логов строго JSON для корректного парсинга.
- Для обогащения полей используйте `zap.Field` и/или `resource` процессор в коллекторе.
- В Kibana проверьте индекс `otel-logs-*`.

---

**Автор курса: Олег Козырев, 2025**
