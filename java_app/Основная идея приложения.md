```java
package com.example.creditpipeline;

import com.github.javafaker.Faker;
import jakarta.persistence.*;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.slf4j.MDC;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.CommandLineRunner;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.stereotype.Repository;
import org.springframework.stereotype.Service;
import org.springframework.web.bind.annotation.*;

import java.math.BigDecimal;
import java.time.LocalDateTime;
import java.util.List;
import java.util.Map;
import java.util.Random;
import java.util.UUID;
import java.util.concurrent.TimeUnit;

@SpringBootApplication
public class CreditPipelineApplication {

    public static void main(String[] args) {
        SpringApplication.run(CreditPipelineApplication.class, args);
    }

    // Компонент для генерации фейковых данных при старте (опционально)
    @org.springframework.stereotype.Component
    static class DataInitializer implements CommandLineRunner {

        @Autowired
        private PipelineService pipelineService;

        @Override
        public void run(String... args) throws Exception {
            Logger log = LoggerFactory.getLogger(DataInitializer.class);
            log.info("Запуск инициализации тестовых данных...");
            Faker faker = new Faker();
            for (int i = 0; i < 5; i++) { // Создадим несколько заявок для старта
                try {
                    String applicant = faker.name().fullName();
                    BigDecimal amount = BigDecimal.valueOf(faker.number().randomDouble(2, 1000, 50000));
                    pipelineService.processApplication(Map.of("applicantName", applicant, "amount", amount));
                    log.info("Создана тестовая заявка для {}", applicant);
                } catch (Exception e) {
                    log.error("Ошибка при создании тестовой заявки", e);
                }
            }
            log.info("Инициализация тестовых данных завершена.");
        }
    }
}

// --- Модель данных (Entity) ---
@Entity
@Table(name = "credit_applications")
class CreditApplication {

    @Id
    @GeneratedValue(strategy = GenerationType.UUID)
    private UUID id;

    @Column(nullable = false)
    private String applicantName;

    @Column(nullable = false)
    private BigDecimal amount;

    @Column(nullable = false)
    private String status; // e.g., RECEIVED, VALIDATING, SCORING, APPROVED, REJECTED, ERROR

    @Column(nullable = false)
    private LocalDateTime createdAt;

    private LocalDateTime updatedAt;

    private String errorMessage; // Для сохранения сообщений об ошибках

    // Getters and Setters
    public UUID getId() { return id; }
    public void setId(UUID id) { this.id = id; }
    public String getApplicantName() { return applicantName; }
    public void setApplicantName(String applicantName) { this.applicantName = applicantName; }
    public BigDecimal getAmount() { return amount; }
    public void setAmount(BigDecimal amount) { this.amount = amount; }
    public String getStatus() { return status; }
    public void setStatus(String status) { this.status = status; }
    public LocalDateTime getCreatedAt() { return createdAt; }
    public void setCreatedAt(LocalDateTime createdAt) { this.createdAt = createdAt; }
    public LocalDateTime getUpdatedAt() { return updatedAt; }
    public void setUpdatedAt(LocalDateTime updatedAt) { this.updatedAt = updatedAt; }
    public String getErrorMessage() { return errorMessage; }
    public void setErrorMessage(String errorMessage) { this.errorMessage = errorMessage; }
}

// --- Репозиторий (Data Access) ---
@Repository
interface CreditApplicationRepository extends JpaRepository<CreditApplication, UUID> {
    // Spring Data JPA автоматически предоставит реализацию
}

// --- Сервис (Бизнес-логика) ---
@Service
class PipelineService {

    private static final Logger log = LoggerFactory.getLogger(PipelineService.class);
    private final Random random = new Random();
    private final Faker faker = new Faker();

    @Autowired
    private CreditApplicationRepository repository;

    public CreditApplication processApplication(Map<String, Object> applicationData) {
        String applicantName = (String) applicationData.getOrDefault("applicantName", faker.name().fullName());
        BigDecimal amount = (BigDecimal) applicationData.getOrDefault("amount", BigDecimal.valueOf(faker.number().randomDouble(2, 500, 100000)));
        UUID traceId = UUID.randomUUID(); // Для сквозной трассировки логов

        MDC.put("traceId", traceId.toString()); // Добавляем traceId в контекст логгера
        MDC.put("applicant", applicantName);

        log.info("Получена новая заявка: {}", applicationData);

        CreditApplication app = new CreditApplication();
        app.setApplicantName(applicantName);
        app.setAmount(amount);
        app.setCreatedAt(LocalDateTime.now());
        app.setStatus("RECEIVED");
        repository.save(app);
        MDC.put("applicationId", app.getId().toString());

        try {
            // Имитация этапов конвейера
            simulateStep("VALIDATION", app, 500);
            simulateStep("SCORING", app, 1000);
            simulateStep("EXTERNAL_CHECKS", app, 800);

            // Имитация случайных ошибок
            if (random.nextInt(10) < 2) { // ~20% шанс ошибки
                simulateRandomError(app);
            } else {
                // Принятие решения
                if (amount.compareTo(BigDecimal.valueOf(50000)) > 0 && random.nextBoolean()) {
                    app.setStatus("REJECTED");
                    log.warn("Заявка отклонена из-за высокой суммы и случайного фактора.");
                } else {
                    app.setStatus("APPROVED");
                    log.info("Заявка одобрена.");
                }
            }

        } catch (Exception e) {
            log.error("Критическая ошибка в конвейере", e);
            app.setStatus("ERROR");
            app.setErrorMessage(e.getMessage());
        } finally {
            app.setUpdatedAt(LocalDateTime.now());
            repository.save(app);
            log.info("Обработка заявки {} завершена со статусом {}", app.getId(), app.getStatus());
            MDC.clear(); // Очищаем контекст логгера
        }
        return app;
    }

    private void simulateStep(String stepName, CreditApplication app, int maxDelayMs) throws InterruptedException {
        log.debug("Начало этапа: {}", stepName);
        app.setStatus(stepName);
        app.setUpdatedAt(LocalDateTime.now());
        repository.save(app);
        TimeUnit.MILLISECONDS.sleep(random.nextInt(maxDelayMs)); // Имитация работы
        log.debug("Завершение этапа: {}", stepName);
    }

    private void simulateRandomError(CreditApplication app) throws RuntimeException {
        int errorType = random.nextInt(6);
        String errorMessage;
        switch (errorType) {
            case 0:
                errorMessage = "Nginx proxy error: upstream timed out (110: Connection timed out) while reading response header from upstream";
                log.error("[External System] {}", errorMessage);
                break;
            case 1:
                errorMessage = "HTTP Error 502: Bad Gateway - Cannot connect to scoring service.";
                log.error("[Scoring Service] {}", errorMessage);
                break;
            case 2:
                errorMessage = "HTTP Error 503: Service Unavailable - Validation service is temporarily overloaded.";
                log.warn("[Validation Service] {}", errorMessage);
                break;
            case 3:
                errorMessage = "RabbitMQ connection refused. Check broker status.";
                log.error("[Messaging Queue] {}", errorMessage);
                break;
            case 4:
                errorMessage = "Kafka producer timeout. Message delivery failed for topic 'credit_events'.";
                log.error("[Event Stream] {}", errorMessage);
                break;
            case 5:
                errorMessage = "Database connection pool exhausted. Could not get JDBC Connection";
                 log.warn("[Database] {}", errorMessage);
                 break;
            default:
                 errorMessage = "Unknown internal processing error occurred.";
                 log.error("[Internal Logic] {}", errorMessage);
                 break;

        }
        app.setStatus("ERROR");
        app.setErrorMessage(errorMessage);
        // Не всегда бросаем исключение, иногда просто логируем ошибку и ставим статус ERROR
        if (random.nextBoolean()) {
             throw new RuntimeException(errorMessage); // Иногда ошибка фатальна для процесса
        }
    }

     public List<CreditApplication> getAllApplications() {
        log.info("Запрос на получение всех заявок");
        return repository.findAll();
    }

     public CreditApplication getApplicationById(UUID id) {
        log.info("Запрос на получение заявки с ID: {}", id);
        MDC.put("applicationId", id.toString());
        CreditApplication app = repository.findById(id).orElse(null);
         if (app == null) {
            log.warn("Заявка с ID {} не найдена", id);
         }
        MDC.remove("applicationId");
         return app;
     }
}

// --- Контроллер (API) ---
@RestController
@RequestMapping("/api/pipeline") // Выбираем REST API как наиболее популярный и гибкий вариант
class PipelineController {

    private static final Logger log = LoggerFactory.getLogger(PipelineController.class);


    @Autowired
    private PipelineService pipelineService;

    // Пример POST эндпоинта для создания новой заявки
    @PostMapping("/submit")
    public ResponseEntity<?> submitApplication(@RequestBody(required = false) Map<String, Object> applicationData) {
        log.info("Получен запрос на /api/pipeline/submit");
         if (applicationData == null) {
             applicationData = Map.of(); // Обработка случая без тела запроса - генерируем все данные
         }
        try {
            CreditApplication createdApp = pipelineService.processApplication(applicationData);
            if ("ERROR".equals(createdApp.getStatus())) {
                 // Возвращаем 500, если обработка завершилась ошибкой
                 return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR)
                                      .body(Map.of("message", "Ошибка обработки заявки", "applicationId", createdApp.getId(), "errorDetails", createdApp.getErrorMessage()));
            }
            return ResponseEntity.status(HttpStatus.CREATED).body(createdApp);
        } catch (Exception e) {
            log.error("Неожиданная ошибка в контроллере при обработке /submit", e);
            return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR)
                                 .body(Map.of("message", "Внутренняя ошибка сервера", "error", e.getMessage()));
        }
    }

     // Пример GET эндпоинта для получения всех заявок
    @GetMapping("/applications")
    public ResponseEntity<List<CreditApplication>> getAllApplications() {
        log.info("Получен запрос на /api/pipeline/applications");
        List<CreditApplication> applications = pipelineService.getAllApplications();
        return ResponseEntity.ok(applications);
    }

     // Пример GET эндпоинта для получения заявки по ID
    @GetMapping("/applications/{id}")
    public ResponseEntity<?> getApplicationById(@PathVariable UUID id) {
        log.info("Получен запрос на /api/pipeline/applications/{}", id);
        CreditApplication application = pipelineService.getApplicationById(id);
        if (application != null) {
            return ResponseEntity.ok(application);
        } else {
            return ResponseEntity.status(HttpStatus.NOT_FOUND)
                                 .body(Map.of("message", "Заявка не найдена", "applicationId", id));
        }
    }
}
```

**`pom.xml` (Основные зависимости):**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>3.2.4</version> <relativePath/> </parent>
    <groupId>com.example</groupId>
    <artifactId>credit-pipeline</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>credit-pipeline</name>
    <description>Stub credit pipeline application</description>
    <properties>
        <java.version>17</java.version>
    </properties>
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-jpa</artifactId>
        </dependency>
        <dependency>
            <groupId>org.postgresql</groupId>
            <artifactId>postgresql</artifactId>
            <scope>runtime</scope>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
        <dependency>
            <groupId>io.micrometer</groupId>
            <artifactId>micrometer-registry-prometheus</artifactId>
            <scope>runtime</scope>
        </dependency>
        <dependency>
            <groupId>net.logstash.logback</groupId>
            <artifactId>logstash-logback-encoder</artifactId>
            <version>7.4</version> </dependency>
         <dependency>
            <groupId>com.github.javafaker</groupId>
            <artifactId>javafaker</artifactId>
            <version>1.0.2</version> </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>
</project>
```

**`src/main/resources/application.properties`:**

```properties
# Server port
server.port=8080

# Database connection (будет переопределено переменными окружения в Docker)
spring.datasource.url=jdbc:postgresql://localhost:5432/credit_pipeline_db
spring.datasource.username=user
spring.datasource.password=password
spring.datasource.driver-class-name=org.postgresql.Driver

# JPA/Hibernate settings
spring.jpa.hibernate.ddl-auto=update # В dev можно 'create-drop', в prod 'validate' или 'none'
spring.jpa.show-sql=false # Логировать SQL запросы (false, т.к. логируем через Logback)
spring.jpa.properties.hibernate.format_sql=true # Форматировать SQL в логах, если show-sql=true

# Actuator endpoints (для метрик Prometheus и проверки здоровья)
management.endpoints.web.exposure.include=health,info,prometheus
management.endpoint.health.show-details=when_authorized
#management.endpoint.prometheus.enabled=true # Включено по умолчанию, если есть зависимость

# Logging (уровни можно переопределить в logback-spring.xml)
logging.level.root=INFO
logging.level.com.example.creditpipeline=DEBUG # Более детальные логи для нашего пакета
logging.level.org.springframework=INFO
logging.level.org.hibernate.SQL=WARN # Уменьшаем шум от Hibernate SQL, если show-sql=true
```

**`src/main/resources/logback-spring.xml` (Для JSON логов):**

```xml
<configuration>
    <springProperty scope="context" name="springAppName" source="spring.application.name" defaultValue="credit-pipeline-app"/>

    <appender name="CONSOLE_JSON" class="ch.qos.logback.core.ConsoleAppender">
        <encoder class="net.logstash.logback.encoder.LogstashEncoder">
            <includeMdc>true</includeMdc> <timestampPattern>yyyy-MM-dd'T'HH:mm:ss.SSS'Z'</timestampPattern> <timeZone>UTC</timeZone>
            <customFields>{"application_name":"${springAppName}"}</customFields> <fieldNames>
                 <timestamp>@timestamp</timestamp>
                <version>[ignore]</version> <levelValue>[ignore]</levelValue> <logger>logger_name</logger>
                <thread>thread_name</thread>
                <level>log_level</level>
                <message>message</message>
                <stackTrace>stack_trace</stackTrace>
            </fieldNames>
        </encoder>
    </appender>

    <root level="INFO"> <appender-ref ref="CONSOLE_JSON" />
        </root>

    <logger name="com.example.creditpipeline" level="DEBUG" additivity="false">
        <appender-ref ref="CONSOLE_JSON" />
         </logger>
     <logger name="org.springframework.web" level="INFO" additivity="false">
         <appender-ref ref="CONSOLE_JSON" />
     </logger>
      <logger name="org.hibernate.SQL" level="WARN" additivity="false">
         <appender-ref ref="CONSOLE_JSON" />
     </logger>


</configuration>
```

**3. Инструкция по запуску в контейнере (Docker)**

**`Dockerfile`:**

```dockerfile
# --- Этап сборки ---
FROM maven:3.8.5-openjdk-17 AS build
WORKDIR /app
# Копируем только pom.xml для кэширования зависимостей
COPY pom.xml .
# Скачиваем зависимости (если pom.xml не изменился, этот слой будет взят из кэша)
RUN mvn dependency:go-offline -B
# Копируем исходный код
COPY src ./src
# Собираем приложение
RUN mvn package -DskipTests

# --- Этап выполнения ---
FROM openjdk:17-jdk-slim
WORKDIR /app
# Копируем собранный JAR из этапа сборки
COPY --from=build /app/target/credit-pipeline-*.jar app.jar
# Открываем порт, на котором работает приложение
EXPOSE 8080
# Команда для запуска приложения
ENTRYPOINT ["java", "-jar", "app.jar"]

# Можно добавить переменные окружения по умолчанию, но лучше задавать их при запуске контейнера
# ENV SPRING_DATASOURCE_URL=jdbc:postgresql://db:5432/credit_pipeline_db
# ENV SPRING_DATASOURCE_USERNAME=user
# ENV SPRING_DATASOURCE_PASSWORD=password
```

**Инструкция:**

1.  **Клонируйте репозиторий или создайте файлы:**
    * Создайте структуру папок: `src/main/java/com/example/creditpipeline`, `src/main/resources`.
    * Поместите Java-файлы в `src/main/java/com/example/creditpipeline`.
    * Поместите `application.properties` и `logback-spring.xml` в `src/main/resources`.
    * Поместите `pom.xml` и `Dockerfile` в корень проекта.

2.  **Установите Docker и Docker Compose** (Docker Compose удобен для запуска нескольких контейнеров).

3.  **Создайте `docker-compose.yml` (рекомендуется):**

    ```yaml
    version: '3.8'

    services:
      db:
        image: postgres:15 # Используйте актуальную версию PostgreSQL
        container_name: credit-pipeline-db
        environment:
          POSTGRES_DB: credit_pipeline_db
          POSTGRES_USER: user
          POSTGRES_PASSWORD: password # В реальном проекте используйте секреты!
        volumes:
          - postgres_data:/var/lib/postgresql/data # Сохранение данных БД
        ports:
          - "5432:5432" # Можно убрать, если доступ нужен только из app
        networks:
          - credit-network

      app:
        container_name: credit-pipeline-app
        build: . # Собираем образ из Dockerfile в текущей директории
        ports:
          - "8080:8080" # Проброс порта приложения наружу
        environment:
          # Переопределяем настройки БД для подключения к контейнеру db
          SPRING_DATASOURCE_URL: jdbc:postgresql://db:5432/credit_pipeline_db # 'db' - имя сервиса postgres в docker-compose
          SPRING_DATASOURCE_USERNAME: user
          SPRING_DATASOURCE_PASSWORD: password # Пароль должен совпадать с настройками db
          # Можно переопределить другие настройки через переменные окружения
          # LOGGING_LEVEL_COM_EXAMPLE_CREDITPIPELINE: DEBUG
        depends_on:
          - db # Запускать только после запуска базы данных
        networks:
          - credit-network

    networks:
      credit-network:
        driver: bridge

    volumes:
      postgres_data: # Именованный volume для данных PostgreSQL
    ```

4.  **Запуск:**
    * Откройте терминал в корневой папке проекта.
    * Выполните команду: `docker-compose up --build`
        * `--build` пересобирает образ `app`, если были изменения в коде или `Dockerfile`.
    * Docker Compose скачает образ PostgreSQL, соберет образ вашего приложения и запустит оба контейнера.

5.  **Проверка:**
    * **Логи приложения:** `docker logs credit-pipeline-app -f` (флаг `-f` для слежения). Вы должны увидеть логи в формате JSON.
    * **Доступ к API:**
        * Отправьте POST-запрос (например, через `curl` или Postman) на `http://localhost:8080/api/pipeline/submit`:
            ```bash
            # С пустым телом (данные сгенерируются)
            curl -X POST http://localhost:8080/api/pipeline/submit -H "Content-Type: application/json" -d '{}'

            # С данными
            curl -X POST http://localhost:8080/api/pipeline/submit -H "Content-Type: application/json" -d '{"applicantName": "Иван Иванов", "amount": 15000.75}'
            ```
        * Получите все заявки: `curl http://localhost:8080/api/pipeline/applications`
    * **Метрики Prometheus:** Откройте в браузере или `curl http://localhost:8080/actuator/prometheus`. Вы увидите метрики в формате Prometheus.
    * **Проверка БД:** Можно подключиться к контейнеру БД (например, через `docker exec -it credit-pipeline-db psql -U user -d credit_pipeline_db`) и проверить таблицу `credit_applications`.

**4. Чем читать логи и как подключить ELK**

* **Простые способы чтения логов:**
    * `docker logs <container_name>`: Показывает стандартный вывод (stdout/stderr) контейнера. Так как мы настроили вывод в консоль в JSON, вы увидите JSON-логи.
    * `docker logs -f <container_name>`: Следить за логами в реальном времени.
    * **Файлы логов (если настроили файловый аппендер):** Можно скопировать файлы из контейнера (`docker cp`) или использовать volume для папки `logs`.

* **Продвинутые инструменты (централизованное логирование):**
    * **Стек ELK/EFK:** Самый популярный вариант.
        * **E**lasticsearch: Распределенная поисковая и аналитическая система для хранения логов.
        * **L**ogstash (или **F**luentd/Filebeat): Агент для сбора, обработки и отправки логов в Elasticsearch. Filebeat - более легковесный агент для простой пересылки. Logstash - для сложной обработки/фильтрации.
        * **K**ibana: Веб-интерфейс для поиска, анализа и визуализации данных (логов) в Elasticsearch.

    * **Graylog:** Альтернатива ELK со схожим функционалом.
    * **Loki (от Grafana):** Система логирования, оптимизированная для интеграции с Prometheus и Grafana, часто используется в Kubernetes-окружениях.

**Как подключить стек ELK к приложению (через Filebeat):**

Это наиболее распространенный и рекомендуемый подход в контейнеризированной среде, так как он не требует изменения кода приложения для отправки логов по сети. Filebeat читает логи прямо из вывода Docker-контейнера.

1.  **Развернуть ELK Стек:** Проще всего сделать это с помощью Docker Compose. Существуют готовые `docker-compose.yml` файлы для ELK (например, от `deviantony/docker-elk`).
2.  **Настроить Filebeat:**
    * Добавьте сервис `filebeat` в ваш `docker-compose.yml` (или в отдельный `docker-compose.yml` для ELK).
    * **Монтирование Docker Socket:** Filebeat нужен доступ к Docker API для обнаружения контейнеров и их логов. Смонтируйте Docker socket: `volumes: - /var/run/docker.sock:/var/run/docker.sock:ro`
    * **Конфигурация `filebeat.yml`:** Создайте файл конфигурации для Filebeat и смонтируйте его в контейнер Filebeat. Ключевые секции:
        * `filebeat.autodiscover:` Используйте автообнаружение Docker-контейнеров.
            ```yaml
            filebeat.autodiscover:
              providers:
                - type: docker
                  hints.enabled: true # Использовать подсказки из лейблов контейнеров
                  # Можно фильтровать контейнеры по лейблам
                  # templates:
                  # - condition:
                  #     contains:
                  #       docker.container.labels.collect_logs: "true"
                  #   config:
                  #     - type: container
                  #       paths:
                  #         - /var/lib/docker/containers/${data.docker.container.id}/*.log
                  #       json.keys_under_root: true # Т.к. логи уже в JSON
                  #       json.overwrite_keys: true
                  #       json.message_key: message # Указать поле с основным текстом лога
            ```
        * `output.elasticsearch:` Укажите адрес вашего Elasticsearch.
            ```yaml
            output.elasticsearch:
              hosts: ["elasticsearch:9200"] # Имя сервиса Elasticsearch в Docker Compose
              # username: "elastic"
              # password: "changeme"
            ```
        * `processors:` Можно добавить процессоры для обогащения логов метаданными Docker.
            ```yaml
            processors:
              - add_docker_metadata: ~
            ```
    * **Настройка Приложения (Лейблы):** Добавьте лейбл к вашему `app` сервису в `docker-compose.yml`, чтобы Filebeat его обнаружил (если используете фильтрацию по лейблам):
        ```yaml
        services:
         app:
           # ... остальная конфигурация
           labels:
             - "co.elastic.logs/enabled=true" # Пример стандартного лейбла для Filebeat
             # или ваш собственный лейбл:
             # - "collect_logs=true"
        ```
3.  **Запуск:** Запустите ELK и Filebeat (`docker-compose up -d` для стека ELK, затем `docker-compose up --build` для вашего приложения с Filebeat).
4.  **Настройка Kibana:**
    * Зайдите в веб-интерфейс Kibana.
    * Перейдите в `Stack Management` -> `Index Patterns`.
    * Создайте Index Pattern (шаблон индекса), обычно `filebeat-*`. Kibana должна обнаружить поля из ваших JSON-логов.
    * Перейдите в `Discover`, чтобы просматривать и искать логи.

Теперь Filebeat будет автоматически собирать JSON-логи из стандартного вывода вашего Java-приложения, обогащать их метаданными контейнера и отправлять в Elasticsearch, где вы сможете их анализировать с помощью Kibana.
