# Пишем глобальную связку Docker Compose на базе горизонтального масштабирования Prometheus с помощью Mimir

Пользуемся официальной документацией на GitHub (в основном там прописаны Docker файлы на запуск и всегда есть конфиги)

## Task 2

- По данному заданию возьмите готовый `Docker Compose` из раздела [Docker: task_4.md](https://github.com/lamjob1993/docker-monitoring/blob/main/docker/task_4.md)
- И совместите его с `Docker Compose` из раздела [Mimir: task_1.md](https://github.com/lamjob1993/docker-monitoring/blob/main/mimir/task_1.md)
- Назовите этот Compose, как `Mimir Global Compose`
- Должен получиться большой конфиг `Docker Compose`, завязанный в базе на Mimir из разделов: `task_4` + `task_1`, как указано выше
- Запустите и проверьте работу новой связки мониторинга
- Список развернутого мониторинга должен получиться следующий:
  - [Docker: task_4.md](https://github.com/lamjob1993/docker-monitoring/blob/main/docker/task_4.md)
    - **+**
  - [Mimir: task_1.md](https://github.com/lamjob1993/docker-monitoring/blob/main/mimir/task_1.md)
    - **`Nginx`** + **`x3 Mimir`** + **`MinIO`** + [вспоминаем схему](https://grafana.com/docs/mimir/next/get-started/play-with-grafana-mimir/tutorial-architecture.png)
  - То есть у вас по сути Task_4 + добавление нового софта со схемы
  - Не забываем, что `Nginx` должен быть отдельный для `x3 Mimir`
  - И не забываем, что `Prometheus` тоже должен быть один центральный
- Пропишите `Mimir` в качестве `Data Source` в `Grafana` и снимите метрики со всех экспортеров
- Сохраните получившийся `Docker Compose` в качестве бэкапа
