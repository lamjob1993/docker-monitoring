# Установка Java JDK версии 21

- Почему 21? Потому что на момент 2025гг многие компании используются версии 17-18. 21 - золотая середина.
- Для работы берем Ubuntu 22.04 под VPN.

### Шаг 0. Изучение структуры проекта 
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
### Шаг 1. Установка OpenJDK 21

```bash
sudo apt update
sudo apt install openjdk-21-jdk
java -version
javac -version
```

### Шаг 2. Настройка переменной JAVA_HOME (рекомендуется)
- Хотя многие современные Java-приложения и инструменты (включая Maven и Spring Boot) могут работать без явно установленной JAVA_HOME, иметь ее правильно настроенной — хорошая практика, которая может потребоваться для других инструментов или скриптов.
- Пакетный менеджер устанавливает JDK в стандартное место (обычно в `/usr/lib/jvm/`). Чтобы узнать точный путь, можно использовать команду `readlink -f $(which java)`. Например, она может показать /usr/lib/jvm/java-21-openjdk-amd64/bin/java. Корневая директория JDK в этом случае будет `/usr/lib/jvm/java-21-openjdk-amd64`.
- Отредактируйте файл профиля вашего пользователя (например, `~/.bashrc`). Откройте его в текстовом редакторе: `nano ~/.bashrc`
- Настройка OpenJDK 21 (установленного из репозиториев Ubuntu):

```bash
export JAVA_HOME="/usr/lib/jvm/java-21-openjdk-amd64" # Укажите точный путь
export PATH=$PATH:$JAVA_HOME/bin
```

- Примените изменения, выполнив: `source ~/.bashrc`
- Проверьте, что переменная установлена: `echo $JAVA_HOME`




