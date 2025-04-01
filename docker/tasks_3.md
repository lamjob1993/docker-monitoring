# Пишем Dockerfile и запускаем

Пишем наш первый **Dockerfile** на примере базового мониторинга: **Grafana + Prometheus + Node Exporter**

## Tasks 3

### Шаг 1
- Создаем **Dockerfile**
- Берем в основу легковесный **Alpine Linux**

### Шаг 
- Пишем самостоятельно **Dockerfile**

### Шаг 
- Создаем директорию `/etc/prometheus`

### Шаг 
- Создаем директорию `/prometheus` для данных Prometheus

### Шаг 
- Скачиваем бинарный файл Prometheus с официального сайта

### Шаг 
- Копируем бинарный файл prometheus в `/bin` для удобства использования

### Шаг 
- Копируем пример конфигурационного файла `prometheus.yml` в `/etc/prometheus`

### Шаг 
- Указываем рабочую директорию внутри контейнера `WORKDIR /prometheus`

### Шаг 
- Определяем команду, которая будет запускаться при старте контейнера
  - CMD ["/bin/prometheus", "--config.file=/etc/prometheus/prometheus.yml"]

### Шаг 
- 

### Шаг 
- Открываем порт `9090`
