# Разворачиваем первый проект Java (пишем Java Application)

- _Почему будем использовать JDK 17? Потому что на момент 2025г многие компании используют версии 11-17. 17 - золотая середина для нас. Сбербанк до сих пор переходит с 11-ой версии на 17-ую на момент 2025г._
- _Для выполнения заданий используем Ubuntu 22.04 под VPN._

### Шаг 1. Пошаговый план

- **Подготовка**: Установим необходимое программное обеспечение.
- **Создание проекта Spring Boot**: Воспользуемся онлайн-инструментом для быстрой генерации основы проекта.
- **Изучение структуры проекта**: Посмотрим, какие файлы создались и для чего они нужны.
- **Добавление Actuator**: Настроим Actuator для отдачи метрик.
- **Создание простого JSON API**: Напишем код для примера API.
- **Запуск приложения локально**: Проверим, что все работает на вашем компьютере.
- **Подготовка к Docker**: Создадим файлы `Dockerfile` и `docker-compose.yml`.
- **Сборка и запуск в Docker**: Запустим приложение в контейнере.
- **Подготовка к GitHub**: Добавим `.gitignore`.


### Шаг 2. Установка OpenJDK 17

```bash
sudo apt update
sudo apt install openjdk-17-jdk
java -version
javac -version
```

### Шаг 3. Настройка переменной JAVA_HOME (рекомендуется)
- Хотя многие современные Java-приложения и инструменты (включая Maven и Spring Boot) могут работать без явно установленной JAVA_HOME, иметь ее правильно настроенной — хорошая практика, которая может потребоваться для других инструментов или скриптов.
- Пакетный менеджер устанавливает JDK в стандартное место (обычно в `/usr/lib/jvm/`). Чтобы узнать точный путь, можно использовать команду `readlink -f $(which java)`. Например, она может показать /usr/lib/jvm/java-17-openjdk-amd64/bin/java. Корневая директория JDK в этом случае будет `/usr/lib/jvm/java-17-openjdk-amd64`.
- Отредактируйте файл профиля вашего пользователя (например, `~/.bashrc`). Откройте его в текстовом редакторе: `nano ~/.bashrc`
- Настройка OpenJDK 17 (добавьте в конец файла):

```bash
export JAVA_HOME="/usr/lib/jvm/java-17-openjdk-amd64/" # Укажите точный путь
export PATH=$PATH:$JAVA_HOME/bin
```

- Примените изменения, выполнив: `source ~/.bashrc`
- Проверьте, что переменная установлена: `echo $JAVA_HOME`
- Результат: `/usr/lib/jvm/java-17-openjdk-amd64/`

### Шаг 4. Установка Maven
- `sudo apt install maven`
- `mvn -version`

### Шаг 5. Установка Docker
- Docker уже должен быть установлен в системе

### Шаг 6. Создание основы проекта (Spring Initializr)

Перейдите в браузер на сайт [https://start.spring.io/](https://start.spring.io/)

Заполните форму следующим образом:

* **Project:** Maven Project
* **Language:** Java
* **Spring Boot:** Выберите последнюю стабильную версию (не SNAPSHOT).
* **Project Metadata:**
    * **Group:** `com.example` (или `com.yourname`)
    * **Artifact:** `credit-pipeline`
    * **Name:** `credit-pipeline`
    * **Description:** `Simple credit pipeline template`
    * **Package name:** `com.example.creditpipeline`
    * **Packaging:** Jar
    * **Java:** 17 (убедитесь, что выбрана версия 17)
* **Dependencies:** Нажмите "Add Dependencies..." и добавьте:
    * **Spring Web:** Для создания веб-API.
    * **Spring Boot Actuator:** Для метрик и мониторинга.

Нажмите **Generate**. Скачается ZIP-архив. Распакуйте его в удобную для вас папку, например, `~/projects/credit-pipeline`.

### Шаг 7. Изучение структуры проекта 

- Перейдите в терминале в распакованную папку `credit-pipeline`: `cd ~/projects/credit-pipeline`.
- Посмотрите на структуру файлов командой `tree` (если команда не найдена, установите: `sudo apt install tree`):

```bash
.
├── .mvn
│   └── wrapper
│       ├── maven-wrapper.jar
│       └── maven-wrapper.properties
├── src
│   ├── main
│   │   ├── java
│   │   │   └── com
│   │   │       └── example
│   │   │           └── creditpipeline
│   │   │               └── CreditPipelineApplication.java  # Главный класс
│   │   └── resources
│   │       ├── application.properties  # Файл настроек
│   │       ├── static                  # Для статических файлов
│   │       └── templates               # Для шаблонов страниц
│   └── test
│       └── java
│           └── com
│               └── example
│                   └── creditpipeline
│                       └── CreditPipelineApplicationTests.java # Файл для тестов
├── .gitignore         # Указывает Git, что игнорировать
├── mvnw               # Скрипт Maven Wrapper для Linux/macOS
├── mvnw.cmd           # Скрипт Maven Wrapper для Windows
└── pom.xml            # Описание проекта Maven
```

* `pom.xml`: Конфигурационный файл Maven. Описывает проект, его зависимости (библиотеки) и как его собирать.
* `src/main/java`: Здесь находится ваш основной Java-код.
* `src/main/resources`: Здесь находятся файлы конфигурации, шаблоны и статические ресурсы.
* `CreditPipelineApplication.java`: Точка входа в ваше Spring Boot приложение.
* `application.properties`: Файл для настроек приложения.
* `mvnw`, `mvnw.cmd`, `.mvn/`: Maven Wrapper. Позволяет запускать Maven, даже если он не установлен глобально, используя версию, указанную в проекте. На Linux вы будете использовать `./mvnw`.
* `.gitignore`: Файл для Git, указывающий, какие файлы не нужно включать в репозиторий (например, результаты сборки).

**Шаг 8: Настройка Actuator**

Откройте файл `src/main/resources/application.properties` в текстовом редакторе (например, `nano src/main/resources/application.properties`):

```properties
# Указываем порт, на котором будет работать приложение (по умолчанию 8080)
server.port=8080

# Настраиваем Actuator
# Включаем все доступные эндпоинты Actuator по веб-интерфейсу
management.endpoints.web.exposure.include=*

# Другие полезные настройки Actuator
# management.endpoint.health.show-details=always # Показывать детали здоровья
# management.info.build.enabled=true # Включает информацию о сборке (нужна настройка в pom.xml)
```

* `server.port=8080`: Приложение будет слушать порт 8080.
* `management.endpoints.web.exposure.include=*`: Разрешает доступ ко всем эндпоинтам Actuator (health, info, metrics и т.д.) через веб.

Сохраните и закройте файл.

**Шаг 9: Создание простого JSON API**

Создадим Java-класс, который будет обрабатывать веб-запросы и возвращать JSON. Это будет "контроллер".

Создайте новый файл `src/main/java/com/example/creditpipeline/CreditController.java` и откройте его в редакторе.

Скопируйте в него следующий код:

```java
package com.example.creditpipeline;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

import java.util.HashMap;
import java.util.Map;
import java.util.UUID; // Добавим для генерации ID

// Эта аннотация говорит Spring, что это контроллер для RESTful веб-сервисов
@RestController
// Все методы в этом контроллере будут доступны по пути /api/credit/...
@RequestMapping("/api/credit")
public class CreditController {

    // Внутренний класс для представления данных входящей заявки (будет преобразован из JSON)
    public static class ApplicationRequest {
        private String clientId;
        private Double loanAmount;

        // Пустой конструктор нужен Spring'у для создания объекта из JSON
        public ApplicationRequest() {
        }

        // Геттеры и сеттеры для полей - Spring использует их для работы с JSON
        public String getClientId() {
            return clientId;
        }

        public void setClientId(String clientId) {
            this.clientId = clientId;
        }

        public Double getLoanAmount() {
            return loanAmount;
        }

        public void setLoanAmount(Double loanAmount) {
            this.loanAmount = loanAmount;
        }
    }

    // Внутренний класс для представления данных исходящего ответа (будет преобразован в JSON)
    public static class ApplicationStatus {
        private String status;
        private String applicationId;
        private String message;

        // Конструктор для удобного создания объекта ответа
        public ApplicationStatus(String status, String applicationId, String message) {
            this.status = status;
            this.applicationId = applicationId;
            this.message = message;
        }

        // Геттеры для полей - Spring использует их для преобразования в JSON
        public String getStatus() {
            return status;
        }

        public String getApplicationId() {
            return applicationId;
        }

        public String getMessage() {
            return message;
        }
        // Сеттеры не нужны, если поля устанавливаются только через конструктор
    }

    // Этот метод обрабатывает POST запросы на /api/credit/apply
    // @RequestBody говорит, что тело запроса (ожидается JSON) нужно преобразовать в объект ApplicationRequest
    @PostMapping("/apply")
    public ApplicationStatus processApplication(@RequestBody ApplicationRequest request) {
        System.out.println("--- Новый запрос на '/api/credit/apply' ---");
        System.out.println("Получена заявка от клиента: " + request.getClientId() + " на сумму: " + request.getLoanAmount());

        // --- Имитация логики кредитного конвейера ---
        String status = "PROCESSING";
        String message = "Ваша заявка принята в обработку.";
        String applicationId = UUID.randomUUID().toString(); // Генерируем уникальный ID заявки

        if (request.getLoanAmount() != null && request.getLoanAmount() > 100000) {
            status = "REJECTED";
            message = "Заявка на сумму свыше 100000 отклонена (имитация).";
        } else if (request.getLoanAmount() != null && request.getLoanAmount() <= 0) {
             status = "REJECTED";
             message = "Сумма должна быть положительной.";
        }
        // --- Конец имитации логики ---

        System.out.println("Результат обработки: ID=" + applicationId + ", Статус=" + status);
        System.out.println("---------------------------------------");


        // Возвращаем объект ApplicationStatus, который Spring автоматически превратит в JSON
        return new ApplicationStatus(status, applicationId, message);
    }

     // Этот метод обрабатывает GET запрос на /api/credit/info
     // Просто для примера, отдаем некоторую информацию о "конвейере"
    @GetMapping("/info")
    public Map<String, String> getPipelineInfo() {
        System.out.println("--- Новый запрос на '/api/credit/info' ---");
        Map<String, String> info = new HashMap<>();
        info.put("pipelineName", "SimpleCreditPipelineTemplate");
        info.put("version", "1.0.0");
        info.put("description", "Тестовый шаблон кредитного конвейера с Actuator и JSON API");
        System.out.println("Ответ: " + info);
        System.out.println("---------------------------------------");
        return info;
    }
}
```
- Сохраните и закройте файл. Мы добавили два API эндпоинта: `/api/credit/apply` (POST для "подачи заявки") и `/api/credit/info` (GET для получения информации).
- Spring Boot автоматически преобразует объекты `ApplicationRequest`, `ApplicationStatus` и `Map` в JSON благодаря аннотации `@RestController`.
