# Пишем Dockerfile и запускаем

Пишем наш первый **Dockerfile** на примере базового мониторинга: **Grafana + Prometheus + Node Exporter**

## Tasks 3

Пишем **dockerfile**:
- Это стандартное имя и его автомтом подхватывает **Docker** при билде, для своего имени используйте ключ `-f` - `docker build -t prometheus -f prometheus-dockerfile .`

### Шаг 1
- Создаем **Dockerfile**
- Берем в основу легковесный **Alpine Linux**

### Шаг 2
- Пишем самостоятельно **Dockerfile**

### Шаг 3
- Создаем директорию `/etc/prometheus`

### Шаг 4
- Создаем директорию `/prometheus` для данных Prometheus

### Шаг 5
- Скачиваем бинарный файл **Prometheus** с официального сайта

### Шаг 6
- Копируем бинарный файл prometheus в `/bin` для удобства использования

### Шаг 7
- Копируем пример конфигурационного файла `prometheus.yml` в `/etc/prometheus`

### Шаг 8
- Указываем рабочую директорию внутри контейнера `WORKDIR /prometheus`

### Шаг 9
- Определяем команду, которая будет запускаться при старте контейнера `CMD ["/bin/prometheus", "--config.file=/etc/prometheus/prometheus.yml"]`

### Шаг 10
- 

### Шаг 11
- Открываем порт `9090` для веб-интерфейса **Prometheus**
