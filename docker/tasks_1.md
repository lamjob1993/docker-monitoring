# Запускаем Docker 
- Запускаем образы "на коленке"

## Tasks 1

## Шаг 1
- Запускаем **Prometheus**
- Возьмем [за пример запуска](https://github.com/prometheus/prometheus) официальный репозиторий
    - ```bash
      docker run --name prometheus -d -p 127.0.0.1:9090:9090 prom/prometheus
      ```
- Описание предыдущего примера
    - Скачивание образа (если не скачан) и запуск образа **Prometheus** с объявлением имени `--name prometheus` в `-d` интерактивном режиме с пробросом портов `-p :9090:9090` (внутренний/внешний) из **Docker Hub** по адресу `prom/prometheus`

## Шаг 2
- Запускаем **Grafana**
    - Тот же способ 

## Шаг 3
- Запускаем **Node Exporter**
    - Тот же способ 

## Шаг 4
- Проверяем веб-морды на всех ПО
- Снимаем метрики с дашборда [Node Exporter Full](https://grafana.com/grafana/dashboards/1860-node-exporter-full/)
