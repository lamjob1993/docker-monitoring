## Пишем глобальный Docker Compose бэкенда мониторинга

Пишем **Docker Compose** для связки бэкенда мониторинга аналогично инстансам [первого раздела](https://github.com/lamjob1993/linux-monitoring/tree/main). Я понимаю, что пока это тестовый полигон, но пока что мы учимся поэтапно и это нормально.

## Task 4

### Пишем **Docker Compose**
- Отрезаем на **VM**:
  - **`CPU`**: 6 Cores
  - **`RAM`**: 4Gb
  - **`SSD`**: В дефолте у вас должен быть на 10Gb, [согласно разделу](https://github.com/lamjob1993/linux-monitoring/tree/main/linux_install)
- Берем в работу бэк мониторинга и поднимаем (не забываем про подключение `volumes`, про сеть и про проброс портов):
  - **`alertmanager`**
  - **`blackbox-exporter`**
  - **`nginx`**
  - **`nginx_exporter`**
  - **`custom_exporter_bash`**
  - **`grafana`**
  - **`node-exporter`**
  - **`postgresql`**
  - **`postgresql_exporter`**
  - **`process-exporter`**
  - **`prometheus`**
  - **`prometheus_federate`**
  - **`pushgateway`**
- Далее рисуем глобальный дашборд, который будет отображать все инстансы выше по списку сверху вниз
  - Подумайте над логикой:
    - Сверху должно быть видно алерты
    - Далее железо и сеть
    - Далее процессы
    - Далее статус базы данных
    - Далее статус сервера
    - И в самом конце вывести информацию для администраторов отдельным `row`
      - Например взять официальные дашборды для Grafana и Prometheus и вывести с них статистику для админов в `row`
- Идем в Draw.io или в Figma и рисуем схему вашего получившегося бэка, согласно **Docker Compose** и выкладываем к себе в pet-прект в репозиторий:
  - Схема должна быть вот [такой](https://miro.com/app/board/uXjVIMhc1ds=/) или [такой](https://raw.githubusercontent.com/lamjob1993/linux-monitoring/fd9a2eb51245e64e09c1da8e2b77ff13d26eaadf/.files/.bucket/%D0%94%D0%B8%D0%B0%D0%B3%D1%80%D0%B0%D0%BC%D0%BC%D0%B0%20%D0%BC%D0%BE%D0%BD%D0%B8%D1%82%D0%BE%D1%80%D0%B8%D0%BD%D0%B3%D0%B0.drawio.svg)
  - Первый вариант лучше, он красивый и хорошо читаемый
  - Схема должна читаться в GitHub, формат должен быть `.svg`, [вот такой ](https://github.com/lamjob1993/linux-monitoring/blob/main/.files/.bucket/%D0%94%D0%B8%D0%B0%D0%B3%D1%80%D0%B0%D0%BC%D0%BC%D0%B0%20%D0%BC%D0%BE%D0%BD%D0%B8%D1%82%D0%BE%D1%80%D0%B8%D0%BD%D0%B3%D0%B0.drawio.svg)
  - Схему выгружаем в формате `.svg` с сайтов: Draw.io или Figma
  - Исходный код `.svg` открываем в VS Code для проверки и помещаем в GitHub
