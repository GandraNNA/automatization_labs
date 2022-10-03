# Автоматизация принятия лабораторных работ в университете

## Описание

В данной работе выполнена задача создания автоматизированной системы
принятия лабораторных работ, ведущей автоматизированный сбор
информации о выполненных студентами лабораторных работах, выложенных
на ресурсе GitHub. Проверка правильности выполнения лабораторных работ
осуществляется с помощью сервисов автоматической интеграции и
тестирования Travis CI и AppVeyor. Полученная информация о результатах
выполнения лабораторных работ каждым студентом размещается в облачном
сервисе Google-таблицы в виде списков студентов, разбитых на вкладки
по группам, с указанием даты выполнения лабораторной работы и ссылкой
на неё же.

Студенты должны отправлять информацию о своих лабораторных работах по
электронной почте в определённом формате на заданный почтовый ящик.
Формат письма должен быть в следующем виде:

```
Заголовок: «Название предмета»
Тело письма:
    Номер группы
    ФИО
    Ссылка на выполненную работу
```    

Программа, по заданному расписанию, читает только новую почту в
заданном почтовом ящике, согласно RFC822 Standard for ARPA Internet
Text Messages и RFC5321 Simple Mail Transfer Protocol. После проверки
правильности заполнения письма программа заносит информацию о новой
выполненной студентом лабораторной работе в Google-таблицу. В случае
наличия ошибок в формате полученного электронного письма или в случае
неправильных данных о группе, ФИО студента или ошибки формата ссылки
на лабораторную работу, программа посылает письмо отправителю с
сообщением об ошибке и переходит к обработке следующего непрочитанного
письма.

После получения почты, по прошедшим проверку письмам, программа
проверяет результаты выполнения лабораторных работ по полученным на
них ссылкам на сервисе GitHub, используя REST API интерфейсы GitHub,
Travis CI, AppVeyor. В случае, если лабораторная работа прошла
автоматические проверки, и решённый номер варианта соответствует
присвоенному студенту номеру варианта, программа заносит результат в
Google-таблицу на вкладку с группой студента в строчку с его фамилией
и в столбец с датой выполнения данной лабораторной работы.

Алгоритм работы программы:

![image](https://user-images.githubusercontent.com/36998396/193651851-6fbd9cc4-c1b5-4c24-bb89-6fdeabc9ce49.png)

Программа начинается с чтения Yandex-почты, после информация сверяется
с содержимым Google-таблицы, в случае несовпадений, отправляет письмо
на почтовый ящик студента об ошибке. В случае правильных данных,
переходит по ссылке на GitHub, а после на Travis или AppVeyor в
зависимости от номера лабораторной, вычисленного из ссылки. Достаётся
информация о работе, сверяется вариант с данными из Google-таблицы и в
случае правильно выполненных условий, заносит в таблицу результат.

Наглядная диаграмма взаимодействия сервисов:

![image](https://user-images.githubusercontent.com/36998396/193650462-6ddb3598-67b2-4227-b1bb-e2cb289759fe.png)

Со стороны преподавателя происходит наименьшая работа с сервисами.
Необходимо только зайти на облачный сервис Google-таблицу и посмотреть
интересующие данные. При желании, по ссылке на работу на GitHub и при
возникающих вопросах по коду, общаться со студентом и в соответствии с
ответами выставлять оценку в Google-таблицу.

Диаграмма последовательности с точки зрения преподавателя:

![image](https://user-images.githubusercontent.com/36998396/193650394-e447edad-db14-4089-bafa-2cc5cb864b4d.png)

Студенту же достаётся самое сладкое. Он выполняет лабораторную работу
у себя на компьютере, затем загружает получившееся на GitHub и ждёт
прохождения тестов. Как только появляются зелёные галочки в интерфейсе
GitHub’а рядом с последним коммитом, студент может посылать письмо с
данными о себе и ссылкой на работу на почту преподавателя. После чего
студенту либо приходит автоматический ответ о неверно указанной
информации в теле письма и ему придётся исправить ошибки и послать
письмо снова, либо все данные верны и будут занесены в Google-таблицу.

Диаграмма последовательности с точки зрения студента:

![image](https://user-images.githubusercontent.com/36998396/193650230-c2878d93-6c9e-4a17-a955-d358957a3147.png)

В данном проекте производится автоматическая проверка лабораторных
работ курса «Операционные Системы» в СПбГУАП, но его можно реализовать
для любых предметов, лабораторные работы которых состоят в написании
кода.

## Примеры работы:

При правильно отправленном сообщении результат заносится в
Google-таблицу. Пример правильного письма и вывода в таблицу:

![image](https://user-images.githubusercontent.com/36998396/193650966-a9aedae3-81b0-48c7-8581-a1f0e996a68a.png)

![image](https://user-images.githubusercontent.com/36998396/193651073-1c07a6c4-c735-4933-bbd6-42d4cf5aabe5.png)

Так же есть обработка ошибок. В случае ошибки номера группы студенту
приходит письмо с соответствующим уведомлением. Пример такого письма:

![image](https://user-images.githubusercontent.com/36998396/193651156-a920e408-4ec6-40f7-8c15-0bc95a3bbb69.png)

При ошибке в ФИО студента автоматическая система отправки сообщений
присылает ему ответ. Пример такого ответа:

![image](https://user-images.githubusercontent.com/36998396/193651233-4a4b14b3-b0e0-400f-aa1f-1726ba727a1c.png)

В случае неправильной ссылки студенту приходит следующий ответ:

![image](https://user-images.githubusercontent.com/36998396/193651329-adfca0d6-0cbe-4bd7-b73b-6be296ce2d27.png)

Когда студент ошибается в номере лабораторной в ссылке, программа
посылает ему сообщение с уведомлением проверить номер работы в ссылке.
Пример письма:

![image](https://user-images.githubusercontent.com/36998396/193651419-72c65788-68c0-455d-8c4c-ec3cc1f5f5f5.png)

## _**Установка и запуск**_

### Клонировать репозиторий:

```
git clone https://github.com/GandraNNA/api_yamdb.git
```

### Обновить pip:

```
python -m pip install --upgrade pip
```

### Установить пакеты:

```
python pip install PyGithub
python pip install gspread
python pip install google-api-python-client
python pip install oauth2client
```

### Запустить:

```
python main.py
```

## Переменные окружения

#### YANDEX_MAIL_PASSWORD

Пароль от почты на Яндексе.

#### APPVEYOR_TOKEN

Токен для доступа к AppVeyor.

#### GITHUB_TOKEN

Токен для доступа к GitHub.

_**Автор: Анна Гандрабура**_
