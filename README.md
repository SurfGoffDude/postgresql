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

![image](https://github.com/user-attachments/assets/d0e64631-201a-4d59-8e0b-77c1fbf5211b)



Нажимаем Next, проверяем установлены ли все галочки:



Далее вам будет предложено выбрать каталог для установки базы данных. Оставьте каталог данных по умолчанию и нажмите Next.

Затем в этом окне нам нужно придумать пароль для нашего суперпользователя - postgres, с помощью которого мы сможем создавать пользователей и создавать новые базы данных:




Порт мы также оставляем по умолчанию:




Не меняем ничего и идем далее:




После завершения установки мы увидим данное окно:




Снимаем галочку с Stack Builder, это действие является необязательным:




На этом установка нашей БД PostgreSQL на Windows закончена.

Вам также необходимо установить PostgreSQL-адаптер Psycopg 3 для Python. Выполните следующие ниже команды в командной оболочке виртуальной среды, чтобы его установить:

pip install --upgrade pip           # upgrade pip to at least 20.3
pip install "psycopg[binary]"
