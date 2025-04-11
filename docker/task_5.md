# Пишем тестовый Docker Compose

Пишем тестовый **Docker Compose** для обкатки переменных (variables) из связки 10-ти контейнеров c Node Exporter

## Task 5

### Пишем **Docker Compose**
- Отрезаем на новой **VM**:
  - **`CPU`**: 6/8 Cores
  - **`RAM`**: 4/6Gb
  - **`SSD`**: В дефолте у вас должен быть на 10Gb, [согласно разделу](https://github.com/lamjob1993/linux-monitoring/tree/main/linux_install)
- Разворачиваем 10 контейнеров с Node Exporter
- Разворачиваем Grafana + Prometheus
- Должна получиться связка мониторинга
- Открываем официальный дашборд для Node Exporter и пробиваем в глобальных настройках Grafana переменные основных лейблов:
  - **`job`**
  - **`ip`**
  - **`port`**
  - **`instance`**
- То есть нам нужно, чтобы на дашборде отображались сверху переменные с возможностью выбора `ip` или `instance`
- [На этом дашборде](https://play.grafana.org/d/T512JVH7z/loki-nginx-service-mesh-json-version?orgId=1&from=now-6h&to=now&timezone=utc&var-datasource=ac4000ca-1959-45f5-aa45-2bd0898f7026&var-label_name=filename&var-label_value=%2Fvar%2Flog%2Fnginx%2Fjson_access.log&var-job=$__all&var-instance=$__all) отражены переменные в верхней части, нужно сделать также и на нашем дашборде
