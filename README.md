# PostgreSQL installing for Mac

Download brew:

```
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```
or
```
brew update
```

Install postgresql:

```
brew install postgresql
```

Start postgresql:

```
brew services start postgresql
psql postgres
```

Stop postgresql:

```
brew services stop postgresql
```

If you want to leave postgresql use ```\q```


# PostgreSQL installing for Windows

Для установки базы данных PostgreSQL в Windows перейдем по ссылке https://www.postgresql.org/download/windows/ и нажмем нажмём на ссылку "Download the installer certified by EDB for all supported PostgreSQL versions".

Cкачаем 15 версию, в нашем случае это будет postgresql-15.5-1-windows-x64.exe и приступим к её установке:

![image](https://ucarecdn.com/0a89a9e2-d7d9-4dff-9a02-8c9762caee15/)

Нажимаем Next, проверяем установлены ли все галочки:

![image](https://ucarecdn.com/3b1a67ab-6a13-4fd0-89c0-660962b76a25/)

Далее вам будет предложено выбрать каталог для установки базы данных. Оставьте каталог данных по умолчанию и нажмите Next.

Затем в этом окне нам нужно придумать пароль для нашего суперпользователя - postgres, с помощью которого мы сможем создавать пользователей и создавать новые базы данных:

![image](https://ucarecdn.com/826334f5-f4b2-4330-8b42-8c0a79fbcd5d/)

Порт мы также оставляем по умолчанию:

![image](https://ucarecdn.com/fb78e673-ef7a-4d5a-8d12-631104134545/)

Не меняем ничего и идем далее:

![image](https://ucarecdn.com/c3ca9063-c701-4d21-bee1-a618f9b479e8/)

После завершения установки мы увидим данное окно:

![image](https://ucarecdn.com/d9e07bed-040f-46a0-9d5c-b7f9634acb4e/)

Снимаем галочку с Stack Builder, это действие является необязательным:

![image](https://ucarecdn.com/11c1fa19-9948-4103-ac2d-84e17523bfa6/)

На этом установка нашей БД PostgreSQL на Windows закончена.

Чтобы начать использовать psql, необходимо в меню Пуск -> Все Приложения найти папку PostgreSQL 15
и запустить SQL Shell (psql):

![image](https://ucarecdn.com/64d8b570-ef39-48a6-9b62-3a937575de30/)


# Создание базы данных и подключение к Django

Вам также необходимо установить PostgreSQL-адаптер Psycopg3 для Python. Выполните следующие ниже команды в командной оболочке виртуальной среды, чтобы его установить:

```
pip install --upgrade pip           # upgrade pip to at least 20.3
pip install "psycopg[binary]"
```

Введите следующую ниже команду, чтобы создать пользователя, который может создавать базы данных:

```
CREATE USER blog WITH PASSWORD 'xxxxxx';
```

Замените xxxxxx на желаемый пароль и выполните команду. Вы увидите такой результат:

```CREATE ROLE```

Пользователь был создан. Теперь давайте создадим базу данных blog и передадим права на владение этой базой данных только что созданному пользователю.

Для этого исполните следующую ниже команду:

```
CREATE DATABASE blog OWNER blog ENCODING 'UTF8';
```

Этой командой мы сообщаем PostgreSQL, что нужно создать базу данных с именем blog, мы передаем право на владение базой данных blog её пользователю, которого мы создали ранее, и указываем, что для новой базы данных должна использоваться кодировка UTF8. Вы увидите следующий ниже результат:

```CREATE DATABASE```

Мы успешно создали пользователя PostgreSQL и базу данных.

 

## Выгрузка существующих данных

Перед переключением на другую базу данных необходимо выгрузить существующие в проекте Django данные из базы данных SQLite. Сначала мы экспортируем данные, потом переключим базу данных проекта на PostgreSQL, а затем импортируем данные в новую базу данных.

Django предоставляет простой способ переноса данных между базой данных и файлами через механизм фикстур, поддерживающий сохранение и восстановление состояния данных. При работе с фикстурами в Django вы можете использовать любой из поддерживаемых форматов: JSON, XML или YAML. Мы собираемся создать фикстуру со всеми данными, содержащимися в базе данных.

Данные, выгружаемые командой dumpdata, по умолчанию сериализуются в формате JSON и выводятся в стандартный вывод. Полученная структура данных включает в себя всю необходимую информацию о модели и ее полях, что позволяет Django корректно загрузить эти данные в базу данных при импорте фикстур.

Выгружаемые данные можно ограничивать моделями приложения, предоставив команде имена приложений либо указав отдельные модели для вывода данных, используя формат app_name.ModelName. Также можно указать формат выгружаемых данных, использовав для этого флаг --format.

По умолчанию команда dumpdata выводит сериализованные данные в стандартный вывод, что означает, что вы увидите их в терминале. Однако, используя флаг --output, можно указать выходной файл. Флаг --indent позволяет указывать отступ.

Дополнительную информацию об опциях команды dumpdata можно получить, выполнив команду python manage.py dumpdata --help.

Исполните следующую ниже команду из командной оболочки:

```
python -Xutf8 manage.py dumpdata --indent=2 --output=mysite_data.json
```

Вы увидите результат, аналогичный приведенному ниже:

![image](https://ucarecdn.com/ec240ecd-dbb4-45ea-bd97-106237f4f5e7/)

Все существующие данные были экспортированы в формат JSON в новый файл с именем mysite_data.json.

Содержимое файла можно просмотреть, чтобы увидеть структуру JSON, которая включает в себя разнообразные объекты данных различных моделей установленных вами приложений.


## Переключение базы данных

Для начала скорректируйте значение параметра DATABASES в конфигурационном файле settings.py вашего проекта, чтобы оно соответствовало следующему виду:

```
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': 'blog',
        'USER': 'blog',
        'PASSWORD': 'xxxxxx',
        'HOST': '127.0.0.1',
        'PORT': '5432',
    }
}
```

Вместо xxxxxx укажите свой пароль, который вы задали для пользователя blog при создании базы данных.

Ваша новая база данных blog создана, но пока не содержит таблиц. Чтобы подготовить ее к использованию, выполните следующую команду для применения миграций:

```
python manage.py migrate
```

Вывод этой команды будет содержать отчёт обо всех примененных миграциях, как показано в примере ниже:

```
Operations to perform:
  Apply all migrations: admin, auth, blog, contenttypes, sessions, sites, taggit
Running migrations:
  Applying contenttypes.0001_initial... OK
  Applying auth.0001_initial... OK
  Applying admin.0001_initial... OK
  Applying admin.0002_logentry_remove_auto_add... OK
  Applying admin.0003_logentry_add_action_flag_choices... OK
  Applying contenttypes.0002_remove_content_type_name... OK
  Applying auth.0002_alter_permission_name_max_length... OK
  Applying auth.0003_alter_user_email_max_length... OK
  Applying auth.0004_alter_user_username_opts... OK
  Applying auth.0005_alter_user_last_login_null... OK
  Applying auth.0006_require_contenttypes_0002... OK
  Applying auth.0007_alter_validators_add_error_messages... OK
  Applying auth.0008_alter_user_username_max_length... OK
  Applying auth.0009_alter_user_last_name_max_length... OK
  Applying auth.0010_alter_group_name_max_length... OK
  Applying auth.0011_update_proxy_permissions... OK
  Applying auth.0012_alter_user_first_name_max_length... OK
  Applying taggit.0001_initial... OK
  Applying taggit.0002_auto_20150616_2121... OK
  Applying taggit.0003_taggeditem_add_unique_index... OK
  Applying taggit.0004_alter_taggeditem_content_type_alter_taggeditem_tag... OK
  Applying taggit.0005_auto_20220424_2025... OK
  Applying blog.0001_initial... OK
  Applying blog.0002_remove_post_blog_post_publish_bb7600_idx_and_more... OK
  Applying blog.0003_comment... OK
  Applying blog.0004_post_tags... OK
  Applying sessions.0001_initial... OK
  Applying sites.0001_initial... OK
  Applying sites.0002_alter_domain_unique... OK
 ```

## Загрузка данных в новую базу данных

Для загрузки данных из файла фикстуры в базу данных PostgreSQL, выполните такую команду:

```
python -Xutf8 manage.py loaddata mysite_data.json
```

В выводе команды будет указано сколько объектов было добавлено в базу данных:

![image](https://ucarecdn.com/81e92b4c-551d-43a5-b4e2-96aadb63cb7e/)

Число добавленных объектов может отличаться в зависимости от количества пользователей, постов, комментариев и других объектов, которые были созданы в базе данных ранее.


В случае возникновения ошибки django.db.utils.IntegrityError: Problem installing fixture, необходимо запустить интерактивную оболочку Python:

```
python manage.py shell
```

И в ней необходимо выполнить следующие команды:

```
from django.contrib.contenttypes.models import ContentType
ContentType.objects.all().delete()
```

После этого пробуем загрузить данные еще раз.
