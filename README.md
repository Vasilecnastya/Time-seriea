## Описание

Приложение-экспортер на языке программирования Python. Собирает для Prometheus информацию
об использовании ядер процессора, общее и используемое количество оперативной памяти и дискового пространства

## Подготовка к запуску
Необходимо установить следующие пакеты командой
```
pip install prometheus-client psutil python-dotenv
```
В файле `params.env` необходимо указать переменные окружения
`EXPORTER_HOST`, `EXPORTER_PORT`, `INTERVAL`

В конфиге `prometheus.yml` необходимо добавить в `scrape_configs`:
```yml
- job_name: "metrics"
  static_configs:
  - targets: ["localhost:8000"]
```
Также рекомендуется установить значения `scrape_interval` и `evaluation_interval` на `5s`

В Grafana нужно импортировать `dashboard` и настроить `datasource`

## Запросы на PromQL

Использование процессоров: `avg(cpu_usage{job="metrics"})` (среднее по всем ядрам), `cpu_usage{job="metrics"}` (процент нагрузки по каждому ядру)

Всего, используемая оперативная память (в байтах): `{__name__=~"memory_total|memory_used"}`

Всего, используемое дисковое пространство (в байтах): `{__name__=~"disk_used|disk_total"}` (для отдельных дисков),
`sum({__name__=~"disk_used|disk_total"}) by (__name__)` (для общего случая)

(дополнительный запрос) Количество потоков процессора: `count(cpu_usage{job="metrics"})`