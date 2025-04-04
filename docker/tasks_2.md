# Пишем Dockerfile

Пишем наш первый **Dockerfile** на примере базового мониторинга: **Grafana + Prometheus + Node Exporter**

## Tasks 2

Пишем **Dockerfile**:
- Это стандартное имя и его автомтом подхватывает **Docker** при билде, для своего имени используйте ключ - `-f`:
  - `docker build -t prometheus -f prometheus-dockerfile .`
    - Здесь `.` указывает текущую директорию, где находится файл **Dockerfile** 


- Пишем самостоятельно **Dockerfile**
- Берем в основу легковесный **Alpine Linux**


- Обновляем пакеты в **Alpine Linux** и устанавливаем необходимые зависимости:
  - `RUN apk update && apk add --no-cache wget tar`


- Создаем директорию `/etc/prometheus`


- Создаем директорию `/prometheus` для данных Prometheus


- Скачиваем арив с бинарным файлом **Prometheus** с официального сайта


- Распаковываем и копируем бинарный файл **prometheus** в `/bin` для удобства использования


- Копируем пример конфигурационного файла `prometheus.yml` в `/etc/prometheus`


- Указываем рабочую директорию внутри контейнера `WORKDIR /prometheus`


- Определяем команду, которая будет запускаться при старте контейнера `CMD ["/bin/prometheus", "--config.file=/etc/prometheus/prometheus.yml"]`


- Открываем порт `9090` для веб-интерфейса **Prometheus**


- Собираем образ со своим именем: `docker build -t my-image-name .`
  - `my-image-name` - может быть любым
    - В нашем случае будет: Grafana, Prometheus, Node Exporter


- Далее собираем таким же образом всю связку **Grafana + Prometheus + Node Exporter** по отдельности


- Проверяем список образов `docker images`


- Запускаем по очереди контейнеры `docker run my-image-name`


- Проверяем связку **Grafana + Prometheus + Node Exporter** на веб-мордах

--- 

### Шпаргалка

_Сначала думаем над каждым заданием в разделе Tasks, если не можем понять какой-то инструмент не менее 40-ка минут и только потом смотрим этот раздел по пунктам!_

#### Готовый Dockerfile в качестве примера

```dockerfile
# Используем официальный базовый образ Alpine Linux для минимизации размера итогового образа.
# Alpine — это легковесный дистрибутив Linux, часто используемый в Docker-образах.
FROM alpine:latest

# Устанавливаем метаданные для образа (например, maintainer).
# Это полезно для идентификации автора или организации, создавшей образ.
LABEL maintainer="your-email@example.com"

# Обновляем пакеты в Alpine Linux и устанавливаем необходимые зависимости.
# wget используется для скачивания бинарного файла Prometheus.
# tar нужен для распаковки архива с Prometheus.
RUN apk update && apk add --no-cache wget tar

# Создаем директорию /etc/prometheus для конфигурационных файлов.
# Это стандартная практика для хранения конфигураций в Linux-системах.
RUN mkdir -p /etc/prometheus

# Создаем директорию /prometheus для данных Prometheus.
# Prometheus сохраняет свои временные данные (TSDB) в этой директории.
RUN mkdir -p /prometheus

# Скачиваем бинарный файл Prometheus с официального сайта.
# Здесь мы используем конкретную версию (например, 2.47.0), чтобы избежать неожиданных обновлений.
# Вы можете заменить версию на актуальную с официального сайта https://prometheus.io/download/.
RUN wget https://github.com/prometheus/prometheus/releases/download/v2.47.0/prometheus-2.47.0.linux-amd64.tar.gz

# Распаковываем скачанный архив в текущую директорию.
# После распаковки получаем директорию prometheus-2.47.0.linux-amd64, содержащую бинарные файлы.
RUN tar -xzf prometheus-2.47.0.linux-amd64.tar.gz

# Копируем бинарный файл prometheus в /bin для удобства использования.
# Теперь команда prometheus будет доступна глобально внутри контейнера.
RUN cp prometheus-2.47.0.linux-amd64/prometheus /bin/

# Копируем пример конфигурационного файла prometheus.yml в /etc/prometheus.
# Этот файл содержит базовую конфигурацию для сбора метрик.
# Пример конфигурации можно взять из официального репозитория Prometheus.
COPY prometheus.yml /etc/prometheus/prometheus.yml

# Указываем рабочую директорию внутри контейнера.
# Это директория, в которой будут выполняться команды по умолчанию.
WORKDIR /prometheus

# Определяем команду, которая будет запускаться при старте контейнера.
# Prometheus запускается с указанием пути к конфигурационному файлу.
CMD ["/bin/prometheus", "--config.file=/etc/prometheus/prometheus.yml"]

# Открываем порт 9090 для веб-интерфейса Prometheus.
# Это стандартный порт, на котором Prometheus предоставляет HTTP API и веб-интерфейс.
EXPOSE 9090
```
