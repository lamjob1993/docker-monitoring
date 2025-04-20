# Установка Grafana Mimir в разрезе мониторинга

_Пользуемся официальной документацией на GitHub (в основном там прописаны Docker файлы на запуск и всегда есть конфиги)_

Данное задание является тестовым для тренировки навыка базового масштабирования. Далее мы развернем Mimir для более наглядной практики.

## Task 1

 - [Вспоминаем нашу схему взаимодействия мониторинга с Mimir](https://github.com/lamjob1993/docker-monitoring/blob/main/mimir/README.md#%D1%81%D1%85%D0%B5%D0%BC%D0%B0-%D1%80%D0%B0%D0%B1%D0%BE%D1%82%D1%8B-mimir)
   - По данному `Task` разделу нужно написать **Docker Compose** для отдельной связки мониторинга (можете поднять этот Compose на отдельной виртуалке, чтобы не ломать предыдущий из раздела [Docker](https://github.com/lamjob1993/docker-monitoring/tree/main/docker))
   - На будущее: можно создавать Compose файлы с разными именами и запускать каждый на одной машине
     - Это особенно полезно, если вы хотите запускать разные конфигурации сервисов или тестировать различные среды (например, `development`, `staging`, `production`) на одном хосте
     - В целом можете уже начинать тренироваться
- Постепенно погружаемся в [схему балансировки](https://grafana.com/docs/mimir/next/get-started/play-with-grafana-mimir/) для горизонтального масштабирования `Prometheus` в разрезе трех инстансов `Mimir` (так называемый хэшринг из кольца `Mimir`, или кольцо хэшринга) завязанных на `Nginx`
 - [Посмотрите этот ролик](https://grafana.com/docs/mimir/latest/get-started/)
 - Вручную [без инструкции](https://grafana.com/docs/mimir/next/get-started/play-with-grafana-mimir/) напишите Docker Compose [для связки мониторинга](https://grafana.com/docs/mimir/next/get-started/play-with-grafana-mimir/tutorial-architecture.png):
     - **`Grafana`**
     - **`Mimir`**
     - **`Prometheus`**
     - **`Nginx`**
       - [Балансировщик нагрузки](https://github.com/lamjob1993/linux-monitoring/blob/main/nginx/README.md)
     - **`MinIO`**
       - `MinIO` работает как распределенное хранилище, что обеспечивает высокую доступность, отказоустойчивость и масштабируемость
       - Читаем что такое `MinIO` самостоятельно и учим
     - Если не справляетесь, то по этой же ссылке есть готовые конфиги в GitHub:
       - ```bash
         git clone https://github.com/grafana/mimir.git
         cd mimir 
         cd docs/sources/mimir/get-started/play-with-grafana-mimir/
         ``` 
- Настройте [рабочий конфиг](https://github.com/grafana/mimir/blob/main/docs/sources/mimir/get-started/play-with-grafana-mimir/config/mimir.yaml) `Mimir`
- Настройте конфиг `Nginx`
- Настройте `Prometheus` на `remote-write` в `Mimir`
- Пропишите `Data Source Mimir` в `Grafana`
- Снимите метрики в разделе `Explore` из `Data Source Mimir` в `Grafana`
- Достаточно вывести метрику `up`
- Сохраните получившийся `Docker Compose` в качестве бэкапа

