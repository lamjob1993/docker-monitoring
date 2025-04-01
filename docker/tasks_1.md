# Запускаем Docker 
- Запускаем образ на коленке

## Шаг 1
- Запускаем **Prometheus**
- Возьмем [за пример запуска](https://github.com/prometheus/prometheus) официальный репозиторий
```bash
    docker run --name prometheus -d -p 127.0.0.1:9090:9090 prom/prometheus
``` 

## Шаг 2
- Запускаем Grafana

## Шаг 3
- Запускаем NodeExporter

## Шаг 4
- Проверяем веб-морды на всех ПО
- Снимаем метрики с дашборда Node Exporter Full
