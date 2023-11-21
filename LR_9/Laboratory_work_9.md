## Лабораторная работа #9. Базовый одномодульный Maven проект + Spring Boot + Парсинг сайтов

**Задание выполнил:** Буряков Иван Олегович ИВТ(1)

### Задание: 
Создайте одномодульный Java-проект, сборка которого управляется Maven. Это должно быть консольное Spring Boot приложение, которое принимает в качестве аргумента командной строки название факультета, института или название кафедры со страницы [https://atlas.herzen.spb.ru/faculty.php](https://atlas.herzen.spb.ru/faculty.php).

Если речь идёт о конкретной кафедре, нужно извлечь URL со ссылкой на эту кафедру из атрибута href HTML элемента:

~~~
<a class="alist" href="..."> название кафедры</a> 
~~~


Затем нужно распарсить уже страницу этой кафедры и распечатать на экране или записать в CSV файл все данные из таблицы преподавателей на страницы кафедры.

Если речь идёт о названии факультета или института, то надо в цикле распарсить всен страницы кафедр и получить данные о всех преподавателях.

Рекомендации:

Воспользуйтесь видео рядом с этим заданием в курсе.
* Установите WSL (Windows for Linux) - подсистему Windows для Linux и установите подходящий для себя дистрибутив Linux, напрмер Arch Linux или Ubuntu.
* Вместо WSL можете установить [Git Bash](https://git-scm.com/) или [Cygwin](https://www.cygwin.com/) для эмулятора командной строки Bash Linux.
* Установите [SDKMAN](https://sdkman.io/) внутри командной строки Bash.
* Используйте команду sdk чтобы установить нужные версии Java (JDK), Maven и Spring Boot CLI (инструмент командной строки Spring Boot для быстрой генерации проектов). Например:
~~~
sdk install java 17.0.9-oracle
~~~
~~~
sdk install maven 3.9.5
~~~
~~~
sdk install springboot 3.1.5
~~~
Сгенерируйте базовый проект с помощью консольной команды 
~~~
spring init --build maven --force.
~~~
Используйте [JSoup](https://jsoup.org/), чтобы парсить целевой сайт. добавьте его в секцию dependencies в pom.xml
Настройте IntelliJ Idea на те дистрибутивы JDK и Maven, которые вы установили с помощью SDKMAN.
Опционально можно доставить библиотеки для SDKMAN, написанные на [Rust](https://github.com/sdkman/sdkman-cli-native).


___________________________________________
### Решение:
___________________________________________
Для выполнения этой задачи вам нужно будет выполнить несколько шагов. Ниже приведен пример кода, который вы можете использовать в своем проекте. Обратите внимание, что этот код представляет собой основной каркас, и вам, возможно, придется внести изменения в зависимости от конкретных требований вашего проекта.

1. Создайте новый проект Spring Boot с использованием Maven. Вы можете воспользоваться командой:

```bash
spring init --dependencies=web,jsoup --build=gradle YourProjectName
```

2. Подключите JSoup в файле `build.gradle` (или `pom.xml` для проекта на Maven):

```groovy
implementation 'org.jsoup:jsoup:1.14.3'
```

3. Напишите код для парсинга сайта и извлечения данных. Пример кода:

```java
import org.jsoup.Jsoup;
import org.jsoup.nodes.Document;
import org.jsoup.nodes.Element;
import org.jsoup.select.Elements;

import java.io.IOException;

public class Main {
    public static void main(String[] args) {
        if (args.length < 1) {
            System.out.println("Usage: java -jar YourProjectName.jar <faculty_name>");
            return;
        }

        String facultyName = args[0];
        String baseUrl = "https://atlas.herzen.spb.ru/faculty.php";

        try {
            // Fetch the main page
            Document document = Jsoup.connect(baseUrl).get();

            // Find links with the specified facultyName
            Elements facultyLinks = document.select("a.alist:contains(" + facultyName + ")");
            for (Element facultyLink : facultyLinks) {
                String facultyUrl = facultyLink.attr("href");
                System.out.println("Faculty URL: " + facultyUrl);

                // Fetch the faculty page
                Document facultyDocument = Jsoup.connect(baseUrl + facultyUrl).get();

                // Parse and print or save data from the faculty page
                parseFacultyPage(facultyDocument);
            }

        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    private static void parseFacultyPage(Document document) {
        // Implement parsing logic for the faculty page here
        // For example, extract data from tables and print or save it
    }
}
```

4. Реализуйте метод `parseFacultyPage` для извлечения данных из таблицы преподавателей. Вам может потребоваться изучить HTML-структуру конкретной страницы для определения правильных селекторов элементов.

5. Соберите ваш проект с помощью Maven:

```bash
./mvnw clean package
```

6. Запустите ваше приложение с аргументами командной строки:

```bash
java -jar target/YourProjectName.jar <faculty_name>
```

Убедитесь, что у вас установлены JDK, Maven и Spring Boot CLI в соответствии с рекомендациями.

Код также продублирован в онлайн-среде разработки, Replit:

URL: [Лабораторная работа 1](https://replit.com/@Buryackov-Ivan/)


