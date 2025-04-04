# Установка Mimir в разрезе мониторинга

_Пользуемся официальной документацией на GitHub (в основном там прописаны Docker файлы на запуск и всегда есть конфиги)_

## Tasks 1

 - [Вспоминаем нашу схему взаимодействия мониторинга с Mimir](https://github.com/lamjob1993/docker-monitoring/blob/main/mimir/README.md#%D1%81%D1%85%D0%B5%D0%BC%D0%B0-%D1%80%D0%B0%D0%B1%D0%BE%D1%82%D1%8B-mimir)
   - По данному `Tasks` разделу нужно написать Docker Compose для:
     - `Grafana`
     - `Mimir`
     - `Prometheus`
     - `Nginx`
     - `MinIO`
 - [Смотрим на режимы деплоя Mimir на официальном сайте](https://grafana.com/docs/mimir/latest/references/architecture/deployment-modes/)
   - А также постепенно погружаемся в схему балансировки, в следующих `Tasks` будет горизонтальное масштабирование `Prometheus` в разрезе трех инстансов `Mimir` (так называемый хэшринг из кольца `Mimir`, или кольцо хэшринга) завязанных на `Nginx`
 - [Смотрим этот ролик](https://grafana.com/docs/mimir/latest/get-started/)
 - [Смотрим на этот репозиторий с конфигами и черпаем вдохновение для деплоя Mimir](https://github.com/ktsstudio/mimir-demo/tree/main/simple)
 - Настройте рабочий конфиг `Mimir` 
 - Настройте `Prometheus` на `remote-write` в `Mimir`
 - Пропишите `Data Source Mimir` в `Grafana`
 - Снимите метрики в разделе **Explore** из `Data Source Mimir` в `Grafana`
   - Достаточно вывести метрику `up`

