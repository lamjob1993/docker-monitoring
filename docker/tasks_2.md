# Запускаем Docker Compose

## Tasks 2

### Шаг 1
- Пишем **Docker Compose** для простейшей связки мониторинга:
  - **Grafana + Prometheus + Node Exporter**

### Шаг 2
- Объявляем `volumes`

### Шаг 3
- Назначаем общую сетку:
  - `network_monitoring`

### Шаг 4
- Пробрасываем конфиги
  - Кладем конфиг в корень директории с `Docker Compose` файлом

  - ```yaml
    volumes:
      - ./prometheus.yml:/etc/prometheus/prometheus.yml
    ```
  - проброс конфига `./prometheus.yml` из корня `./` директории **Docker Compose** через двоеточие `:` в директорию контейнера `:/etc/prometheus/prometheus.yml`

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

  - ```bash
    docker images   # список скачанных образов
    ```

---

### Шпаргалка

_Сначала думаем над каждым заданием в разделе Tasks, если не можем понять какой-то инструмент не менее 40-ка минут и только потом смотрим этот раздел по пунктам!_

#### Готовый Docker Compose в качестве примера

```yaml
version: '3.3'  # Указываем версию формата Docker Compose (удаляем строку, так как может возникнуть конфликт версий на разных Docker Compose).

services:
  prometheus:
    image: prom/prometheus:latest  # Используем официальный образ Prometheus с тегом latest.
    container_name: prometheus  # Задаем имя контейнера для удобства идентификации.
    ports:
      - "9090:9090"  # Пробрасываем порт 9090 на хост, чтобы веб-интерфейс Prometheus был доступен.
    volumes:
      - ./prometheus.yml:/etc/prometheus/prometheus.yml  # Монтируем локальный файл конфигурации Prometheus в контейнер.
    command:
      - '--config.file=/etc/prometheus/prometheus.yml'  # Указываем путь к конфигурационному файлу внутри контейнера.
    networks:
      - network_monitoring  # Подключаем контейнер к пользовательской сети network_monitoring.
    restart: unless-stopped  # Контейнер будет автоматически перезапускаться, если он остановлен не вручную.

  node_exporter:
    image: quay.io/prometheus/node-exporter:latest  # Используем официальный образ Node Exporter с тегом latest.
    container_name: node_exporter  # Задаем имя контейнера.
    ports:
      - "9100:9100"  # Пробрасываем порт 9100 на хост, чтобы метрики были доступны.
    networks:
      - network_monitoring  # Подключаем контейнер к пользовательской сети network_monitoring.
    restart: unless-stopped  # Контейнер будет автоматически перезапускаться, если он остановлен не вручную.

  grafana:
    image: grafana/grafana:latest  # Используем официальный образ Grafana с тегом latest.
    container_name: grafana  # Задаем имя контейнера.
    ports:
      - "3000:3000"  # Пробрасываем порт 3000 на хост, чтобы веб-интерфейс Grafana был доступен.
    volumes:
      - grafana-storage:/var/lib/grafana  # Создаем том для хранения данных Grafana (например, дашбордов).
    environment:
      - GF_SECURITY_ADMIN_PASSWORD=admin  # Устанавливаем пароль администратора Grafana по умолчанию.
    networks:
      - network_monitoring  # Подключаем контейнер к пользовательской сети network_monitoring.
    restart: unless-stopped  # Контейнер будет автоматически перезапускаться, если он остановлен не вручную.

volumes:
  grafana-storage:  # Определяем том для хранения данных Grafana.

networks:
  network_monitoring:  # Создаем пользовательскую сеть для изоляции и связи контейнеров.
```

