# Пишем Docker Compose для обкатки переменных в Grafana

Пишем новый **Docker Compose** для обкатки переменных (variables) из связки 10-ти контейнеров c Node Exporter.
- Файлов Docker Compose может быть много, для каждого проекта свой Compose. Писал об этом в [Task_4.md](https://github.com/lamjob1993/docker-monitoring/blob/main/docker/task_4.md).

## Task 5

### Пишем **Docker Compose**
- Отрезаем на новой **VM**:
  - **`CPU`**: 6/8 Cores
  - **`RAM`**: 4/6Gb
  - **`SSD`**: В дефолте у вас должен быть на 10Gb, [согласно разделу](https://github.com/lamjob1993/linux-monitoring/tree/main/linux_install)
- Разворачиваем 10 контейнеров с Node Exporter
- Разворачиваем Grafana + Prometheus
- Должна получиться связка мониторинга
- Открываем официальный дашборд для Node Exporter и пробиваем в глобальных настройках Grafana переменные основных лейблов (если нет каких-то лейблов, то объявите их вручную, если ip адрес не отделяется от порта, то разделите их через relabel_config):
  - **`job`**
  - **`ip`**
  - **`port`**
  - **`instance`**
- То есть нам нужно, чтобы на дашборде отображались сверху переменные с возможностью выбора `ip` или `instance`
- [На этом дашборде](https://play.grafana.org/d/T512JVH7z/loki-nginx-service-mesh-json-version?orgId=1&from=now-6h&to=now&timezone=utc&var-datasource=ac4000ca-1959-45f5-aa45-2bd0898f7026&var-label_name=filename&var-label_value=%2Fvar%2Flog%2Fnginx%2Fjson_access.log&var-job=$__all&var-instance=$__all) отражены переменные в верхней части, как нам нужно, сделайте также на вашем дашборде


**Следующие разделы для обучения**
- [Шпаргалка по формулам](https://github.com/lamjob1993/docker-monitoring/blob/main/docker/%D0%A8%D0%BF%D0%B0%D1%80%D0%B3%D0%B0%D0%BB%D0%BA%D0%B0%20%D0%BF%D0%BE%20%D1%84%D0%BE%D1%80%D0%BC%D1%83%D0%BB%D0%B0%D0%BC.md)
- [Шпаргалка по переменным](https://github.com/lamjob1993/docker-monitoring/blob/main/docker/%D0%A8%D0%BF%D0%B0%D1%80%D0%B3%D0%B0%D0%BB%D0%BA%D0%B0%20%D0%BF%D0%BE%20%D0%BF%D0%B5%D1%80%D0%B5%D0%BC%D0%B5%D0%BD%D0%BD%D1%8B%D0%BC.md)
- [Шпаргалка по регуляркам](https://github.com/lamjob1993/docker-monitoring/blob/main/docker/%D0%A8%D0%BF%D0%B0%D1%80%D0%B3%D0%B0%D0%BB%D0%BA%D0%B0%20%D0%BF%D0%BE%20%D1%80%D0%B5%D0%B3%D1%83%D0%BB%D1%8F%D1%80%D0%BA%D0%B0%D0%BC.md)

