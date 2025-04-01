# Запускаем Docker Compose

## Tasks 2

### Шаг 1
- Пишем `Docker Compose` для простейшей связки мониторинга:
  - **Grafana + Prometheus + Node Exporter**

### Шаг 2
- Объявляем `volumes`

### Шаг 3
- Назначаем общую сетку
  - `network_monitoring`

### Шаг 4
- Пробрасываем конфиги
  - Кладем конфиг в корень директории с `Docker Compose` файлом

  - ```yaml
    volumes:
      - ./prometheus.yml:/etc/prometheus/prometheus.yml   # проброс конфига ./prometheus.yml из корня ./ через двоеточие : в директорию контейнера :/etc/prometheus/prometheus.yml
    ```

### Шаг 5
- Скачиваем только последние версии ПО: `latest`

### Шаг 6
- Удаляем в начале файла `Compose` версию `Docker Compose`, потому что она каждый раз обновляется, из-за этого скрипт `Compose` может не отработать

### Шаг 7
- Проверяем связку из **Шага 1**
  - Проверяем, что все веб-морды отрабатывают
  - Загружаем дашборд [Node Exporter Full](https://grafana.com/grafana/dashboards/1860-node-exporter-full/) в **Grafana** и проверяем работу

### Шаг 8
- Если всё успешно запустилось, то нужно удалить Compose проект `docker compose down` и проверить, что всё удалилось:

  - ```bash
    docker ps      # список запущенных контейнеров (запущенные в данный момент)
    docker ps -a   # список когда либо запущенных контейнеров (все стопнутые контейнеры в ожидании)
    ```
- Образы `images` - пусть остаются для следующих проектов, чтобы их потом повторно не загружать и не ждать
- Проверка образов
  - ```
    docker images   # список скачанных образов
    ```

---

### Шпаргалка

_Сначала думаем над каждым заданием в разделе Tasks, если не можем понять какой-то инструмент не менее 40-ка минут и только потом смотрим этот раздел по пунктам!_

#### Готовый Docker Compose в качестве примера

```yaml
version: '3.3'   #  эту строку удаляем для избежания конфликтов

services:
  prometheus:
    image: prom/prometheus:latest
    container_name: prometheus
    ports:
      - "9090:9090"
    volumes:
      - ./prometheus.yml:/etc/prometheus/prometheus.yml
    command:
      - '--config.file=/etc/prometheus/prometheus.yml'
    restart: unless-stopped

  node_exporter:
    image: quay.io/prometheus/node-exporter:latest
    container_name: node_exporter
    ports:
      - "9100:9100"
    restart: unless-stopped

  grafana:
    image: grafana/grafana:latest
    container_name: grafana
    ports:
      - "3000:3000"
    volumes:
      - grafana-storage:/var/lib/grafana
    environment:
      - GF_SECURITY_ADMIN_PASSWORD=admin
    restart: unless-stopped

volumes:
  grafana-storage:
```

