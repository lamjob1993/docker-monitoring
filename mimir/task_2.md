# Пишем глобальную связку Docker Compose на базе горизонтального масштабирования Mimir

Пользуемся официальной документацией на GitHub (в основном там прописаны Docker файлы на запуск и всегда есть конфиги)

## Task 2

- По данному заданию возьмите готовый `Docker Compose` из раздела [Docker: task_4.md](https://github.com/lamjob1993/docker-monitoring/blob/main/docker/task_4.md)
- И совместите его с Docker Compose из раздела [Mimir: task_1.md](https://github.com/lamjob1993/docker-monitoring/blob/main/mimir/task_1.md)
- Должен получиться большой конфиг `Docker Compose`, завязанный в базе на Mimir из разделов: `task_4` + `task_1`, как указано выше
- Проверьте работу новой связки мониторинга
- Список развернутого мониторинга, который должен получиться:
  - **`alertmanager`**
  - **`blackbox-exporter`**
  - **`nginx`**
  - **`nginx_exporter`**
  - **`custom_exporter_bash`**
  - **`grafana`**                            # должен быть один инстанс Grafana
  - **`node-exporter`**
  - **`postgresql`**
  - **`postgresql_exporter`**
  - **`process-exporter`**
  - **`prometheus`**                         # должен быть один инстанс Prometheus, в разрезе которого будет поднята обвязка Mimir
    - **`Nginx`** + **`x3 Mimir`** + **`MinIO`**
  - **`prometheus_federate`**
  - **`pushgateway`**
- Пропишите Mimir в качестве Data Source в Grafana и снимите метрики со всех экспортеров
- Сохраните получившийся `Docker Compose` в качестве бэкапа
