# Введение в Java

- [Какую роль играет Java](https://github.com/lamjob1993/kubernetes-monitoring/tree/main/kubernetes/legend), как язык бэкенда в банке (в нашем проекте). 

## Что такое Java и для чего мы ее используем?

**Java** — это **универсальный, объектно-ориентированный язык программирования**, разработанный компанией Sun Microsystems (ныне принадлежит Oracle) в 1995 году.

### 🟩 Основные особенности Java:

- ✅ **Платформонезависимый**:  
  Программы на Java запускаются на любой платформе, где установлена **Java Virtual Machine (JVM)**. Это делает Java популярной для кроссплатформенной разработки.

- ✅ **Объектно-ориентированный (ООП)**:  
  В Java всё строится вокруг классов и объектов. Поддерживает такие принципы ООП, как инкапсуляция, наследование, полиморфизм и абстракция.

- ✅ **С автоматическим управлением памятью (Garbage Collection)**:  
  Не нужно вручную освобождать память — Java сама очищает её от ненужных объектов.

- ✅ **Безопасный и надёжный**:  
  Java имеет систему защиты, которая предотвращает многие распространённые ошибки и уязвимости.

### 🧰 Для чего используется Java?

| Область | Примеры |
|--------|---------|
| 💻 ПК-программы | Рабочие приложения, десктопные клиенты |
| 🌐 Веб-приложения | Серверная часть сайтов, REST API |
| 📱 Мобильные приложения | Приложения под Android |
| ☁️ Облачные сервисы | Микросервисы, серверы, backend |
| 🤖 Искусственный интеллект / Big Data | Spark, Hadoop, TensorFlow on Java |

### 🔹 Как работает Java?

1. Вы пишете код на Java → `.java` файл.
2. Компилируете его с помощью `javac` → получается байт-код `.class`.
3. Байт-код выполняется виртуальной машиной Java (JVM), которая есть на любом устройстве.

> То есть Java не компилируется напрямую в машинный код, а в специальный "промежуточный" формат — **байт-код**, который понимает JVM.

### 🧠 Кратко:

🔹 **Java** — мощный, универсальный язык программирования.  
🔹 Он используется для создания **веб-приложений, мобильных приложений, серверов и даже роботов**.  
🔹 Пишешь один раз — запускаешь везде (`Write Once, Run Anywhere`).

---

### Что такое JRE и JDK?

#### 🔹 Что такое **JRE**?

**JRE** — это **Java Runtime Environment**, то есть **среда выполнения Java**.

#### Основное предназначение:
- Запуск уже скомпилированных Java-приложений.
- Содержит **виртуальную машину Java (JVM)** и стандартные библиотеки, необходимые для исполнения программ.

#### Кому нужно?
- Конечным пользователям, которые хотят просто **запускать** Java-программы.
- Не подходит для разработки и компиляции кода.

#### 🔹 Что такое **JDK**?

**JDK** — это **Java Development Kit**, то есть **комплект разработчика Java**.

#### Основное предназначение:
- Разработка Java-приложений: написание, компиляция, запуск и отладка программ.

#### Что включает:
- **JRE** (можно запускать программы),
- Инструменты разработки:
  - `javac` — компилятор,
  - `java` — запуск приложений,
  - `javadoc` — генерация документации,
  - `jar` — упаковка в JAR-файлы,
  - и другие.

#### Кому нужно?
- **Разработчикам**, которым нужно писать и компилировать Java-код.

#### 🟡 Сравнение: JDK vs JRE

| Функция | **JRE** | **JDK** |
|--------|---------|---------|
| Запуск Java-программ | ✅ | ✅ |
| Компиляция кода | ❌ | ✅ |
| Отладка программ | ❌ | ✅ |
| Включает JVM | ✅ | ✅ |
| Для конечного пользователя | ✅ | ❌ |
| Для разработчика | ❌ | ✅ |

#### 🟢 Итог:

🔹 **JRE** нужен только для **запуска** Java-приложений.  
🔹 **JDK** нужен для **разработки** Java-приложений и включает в себя JRE.  
🔹 Если вы пишете код на Java — устанавливайте **JDK**.

---

### Что такое **Maven**

Это инструмент для управления проектами на Java.  

Главное, что он делает:

- **Управляет зависимостями** — подключает библиотеки, которые нужны вашему проекту.  
  Например, если вы хотите использовать готовую библиотеку для работы с JSON или для тестирования, вы просто указываете её в настройках, и Maven сам скачает её и добавит в проект.

- **Собирает проект** — компилирует код, запускает тесты и создаёт исполняемый файл (например, JAR).

🔹 **Зависимости** — это сторонние библиотеки, которые помогают писать код быстрее, не изобретая велосипед.  
Maven автоматически их находит, скачивает и подключает.

Всё это описывается в одном файле — `pom.xml`.

---

### Что такое Spring Boot?

Представьте, что вы строите дом, но вместо того, чтобы делать все с нуля (кирпичи, раствор и т.д.), вам привезли готовые блоки, каркас, сантехнику. Spring Boot — это такой набор "готовых блоков" для Java, который сильно упрощает создание веб-приложений. Он сам настраивает многие вещи за вас, чтобы вы могли сосредоточиться на логике вашего приложения.

---

### **Что такое Apache Tomcat?**

Это **веб-сервер и контейнер servlet'ов**, написанный на языке Java. Он используется для запуска **Java-приложений веба**, таких как:

- Веб-сайты, построенные на **Servlet'ах** и **JSP (JavaServer Pages)**.
- REST API, построенные с помощью фреймворков вроде **Spring Boot** или **Jakarta EE**.

#### 🟩 Что делает Tomcat?

- Запускает Java-код на сервере.
- Обрабатывает HTTP-запросы от браузеров или других клиентов.
- Выполняет **servlet'ы** и **JSP-страницы**.
- Может работать как отдельный сервер или быть частью более крупной Java-платформы (например, Jakarta EE).

#### 🟢 Когда используют Tomcat?

- При разработке веб-приложений на Java.
- На тестовых и продакшен-серверах.
- Часто используется в связке с такими фреймворками, как **Spring**, **Hibernate**, **Vaadin** и др.

---

### 🔹 Что такое **Servlet**?

**Servlet** — это **Java-класс**, который обрабатывает запросы от клиентов (например, браузера) и отправляет им ответы.  
Он работает внутри **веб-сервера**, такого как **Apache Tomcat**, и позволяет создавать динамические веб-страницы или API.

#### 🟩 Простыми словами:

- Servlet — это "среда", которая получает HTTP-запрос (GET, POST и т.д.).
- Он выполняет нужную логику (например, достаёт данные из базы, проверяет пользователя).
- Затем он формирует ответ (HTML, JSON, XML и т.п.) и отправляет его обратно клиенту.

#### 🔧 Как работает Servlet?

1. Клиент (браузер, мобильное приложение и т.п.) отправляет HTTP-запрос (например, `http://example.com/login`).
2. Сервер (Tomcat) получает этот запрос.
3. Определяет, какой servlet должен обработать этот URL.
4. Вызывает метод этого servlet'а (`doGet`, `doPost` и т.д.).
5. Servlet выполняет код и отправляет ответ обратно клиенту.

---

### Что такое Actuator?

Это функция Spring Boot, которая добавляет в ваше приложение "глаза и уши". Она позволяет вам узнать, что происходит внутри приложения: работает ли оно, сколько памяти использует, какие компоненты запущены и так далее. Эти данные (метрики) очень важны для мониторинга. Actuator предоставляет их по специальным веб-адресам (эндпоинтам).

---

### Что такое API и JSON?

**API (Application Programming Interface)**: Это как меню в ресторане. Оно говорит вам, какие "блюда" (операции) доступны у приложения и как их "заказать" (какую информацию отправить). В нашем случае это будет веб-API, доступное по HTTP-запросам.

**JSON (JavaScript Object Notation)**: Это простой и понятный формат для обмена данными между разными программами. Выглядит как вложенные списки и словари с парами "ключ": "значение", например: {"имя": "Андрей", "возраст": 30}. Это идеальный формат для веб-API.
