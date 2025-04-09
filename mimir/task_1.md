# Установка Mimir в разрезе мониторинга

_Пользуемся официальной документацией на GitHub (в основном там прописаны Docker файлы на запуск и всегда есть конфиги)_

## Task 1

 - [Вспоминаем нашу схему взаимодействия мониторинга с Mimir](https://github.com/lamjob1993/docker-monitoring/blob/main/mimir/README.md#%D1%81%D1%85%D0%B5%D0%BC%D0%B0-%D1%80%D0%B0%D0%B1%D0%BE%D1%82%D1%8B-mimir)
   - По данному `Tasks` разделу нужно написать Docker Compose для связки:
     - `Grafana`
     - `Mimir`
     - `Prometheus`
     - `Nginx`
     - `MinIO`
       - MinIO работает как распределенное хранилище, что обеспечивает высокую доступность, отказоустойчивость и масштабируемость.
- Постепенно погружаемся в [схему балансировки](https://grafana.com/docs/mimir/next/get-started/play-with-grafana-mimir/) для горизонтального масштабирования `Prometheus` в разрезе трех инстансов `Mimir` (так называемый хэшринг из кольца `Mimir`, или кольцо хэшринга) завязанных на `Nginx`
 - [Посмотрите этот ролик](https://grafana.com/docs/mimir/latest/get-started/)
 - [Вручную без инструкции напишите Docker Compose](https://grafana.com/docs/mimir/next/get-started/play-with-grafana-mimir/)
   - Если не справляетесь, то по этой же ссылке есть готовые конфиги в GitHub:
     - ```bash
       git clone https://github.com/grafana/mimir.git
       cd mimir 
       cd docs/sources/mimir/get-started/play-with-grafana-mimir/
       ``` 
- Настройте рабочий конфиг `Mimir`
- Настройте конфиг `Nginx`
- Настройте `Prometheus` на `remote-write` в `Mimir`
- Пропишите `Data Source Mimir` в `Grafana`
- Снимите метрики в разделе **Explore** из `Data Source Mimir` в `Grafana`
- Достаточно вывести метрику `up`

