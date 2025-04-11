## Пишем глобальный Docker Compose бэкенда мониторинга

Пишем **Docker Compose** для связки бэкенда мониторинга аналогично [первому разделу](https://github.com/lamjob1993/linux-monitoring/tree/main).

## Task 4

### Шаг 1. Пишем **Docker Compose**
- Отрезаем на **VM**:
  - **`CPU`**: 6 Cores
  - **`RAM`**: 4Gb
  - **`SSD`**: В дефолте у вас должен быть на 10Gb, [согласно разделу](https://github.com/lamjob1993/linux-monitoring/tree/main/linux_install)
- Берем в работу бэк мониторинга и поднимаем (не забываем про подключение `volumes`, про сеть и про проброс портов):
