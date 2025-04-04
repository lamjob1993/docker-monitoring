# Пишем Docker Compose

Пишем **Docker Compose** для простейшей связки мониторинга: **Grafana + Prometheus + Node Exporter**

## Tasks 3

### Шаг 1. Пишем **Docker Compose**

#### Рассмотрим готовый Docker Compose в качестве примера

```yml
version: '3.3'  # Версия формата Docker Compose (удаляем строку, так как может возникнуть конфликт версий на разных Docker Compose).

services:
  prometheus:
    image: prom/prometheus:latest  # Используем официальный образ Prometheus с тегом latest.
    container_name: prometheus  # Задаем имя контейнера для удобства идентификации.
    ports:
      - "9090:9090"  # Пробрасываем порт 9090 на хост, чтобы веб-интерфейс Prometheus был доступен.
    volumes:
      - ./prometheus.yml:/etc/prometheus/prometheus.yml  # Монтируем локальный файл конфигурации Prometheus в контейнер.
      - prometheus-data:/prometheus  # Создаем том для хранения данных Prometheus. Сохранит историю метрик после перезапуска контейнера.
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
  prometheus-data:  # Определяем том для хранения данных Prometheus.

networks:
  network_monitoring:  # Создаем пользовательскую сеть для изоляции и связи контейнеров.
```

---

### Шаг 2. Важно

1. **В Docker Compose тома (volumes) могут быть объявлены двумя способами:**
- **Локальные файлы/директории**: Когда вы монтируете локальный файл или директорию с хоста в контейнер (например, `./prometheus.yml:/etc/prometheus/prometheus.yml`). В этом случае объявление тома в разделе `volumes` не требуется.
- **Именованные тома**: Когда вы создаете специальный том Docker для хранения данных (например, `grafana-storage`).

**Объявление именованного тома (volume).**
Grafana использует именованный том (`grafana-storage`) для хранения данных, таких как:
- Настройки дашбордов.
- Пользовательские данные (например, источники данных).
- Плагины.

Если вы не создадите именованный том, все данные будут потеряны при перезапуске контейнера. Использование именованного тома гарантирует, что данные сохранятся даже после остановки или удаления контейнера.

Пример:
```yaml
volumes:
  - grafana-storage:/var/lib/grafana
```
Здесь мы монтируем том `grafana-storage` в директорию `/var/lib/grafana`, где Grafana хранит свои данные.

---

2. **Почему том может быть не объявлен?**

Prometheus может использовать локальный файл `prometheus.yml` для конфигурации:
```yaml
volumes:
  - ./prometheus.yml:/etc/prometheus/prometheus.yml
```
Это означает, что конфигурация Prometheus берется из файла на хосте (`./prometheus.yml`). Поскольку файл находится на хосте, его не нужно дополнительно объявлять в разделе `volumes`. Docker автоматически монтирует его в контейнер.

Однако, если вы хотите сохранить данные Prometheus (например, метрики), вы можете добавить именованный том для директории `/prometheus`, где Prometheus хранит свои временные данные. Это полезно, если вы хотите сохранить историю метрик после перезапуска контейнера.


3. **Конфиги**
- Пробрасываем конфиги
  - Кладем конфиг в корень директории с `Docker Compose` файлом

  - ```yaml
    volumes:
      - ./prometheus.yml:/etc/prometheus/prometheus.yml
    ```
  - проброс конфига `./prometheus.yml` из корня `./` директории **Docker Compose** через двоеточие `:` в директорию контейнера `:/etc/prometheus/prometheus.yml`


### Шаг 3. Запуск
- Запускаем Compose с помощью команды `docker compose up -d`
- Проверяем, что все веб-морды нашего ПО работают
- Загружаем дашборд [Node Exporter Full](https://grafana.com/grafana/dashboards/1860-node-exporter-full/) в **Grafana** и проверяем работу

### Шаг 4. Финал
- Если всё успешно запустилось, то нужно удалить **Docker Compose** проект `docker compose down` и проверить, что всё удалилось:
  - ```bash
    docker ps      # список запущенных контейнеров (запущенные в данный момент)
    docker ps -a   # список когда либо запущенных контейнеров (все стопнутые контейнеры в ожидании)
    ```
- Образы `images` - пусть остаются для следующих проектов в качестве теста, чтобы их потом повторно не загружать
- Проверка образов
  - ```bash
    docker images   # список скачанных образов
    ```
