# Django

## Создание виртуального окружения

1) В терминале при помощи cd заходим в интересующую папку:

2) В папке для создания папки с виртуальной средой прописываем команду: 
python -m venv djvenv 

3) В появившейся папке djvenv заходим в папку Scripts и запускаем файл activete.bat
![[Untitled.png]]
![Untitled](Django%2075b3de05990b43259a6c22d06093c499/Untitled.png)

*Чтобы выйти из виртуального окружения в глобальное окружение нужно прописать команду deactivate

4) В папку с проектом устанавливаем django необходимой версии pip install django==4.2.1

## Создание django-проекта

1) Открываем пустой репозиторий
2) Пишем в терминале команду: django-admin startproject <название проекта>

## Создаем приложение:

1) В терминале пишем команду: python manage.py startapp <название в нижнем регистре>

2) После создания приложения его необходимо зарегистрировать в файле [setting.py](http://setting.py) в списке `INSTALLED_APPS = [<приложение>]` 

3) Далее необходимо зарегистрировать прописать маршрут к файлу [urls.py](http://urls.py) приложения из файла [urls.py](http://urls.py)  проекта (в [settings.py](http://settings.py) прописано, что этот файл с URLами является главным)

Для этого импортируем функцию include()

```python
from django.contrib import admin
from django.urls import path, include # импортируем метод

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('main.urls')) # Ссылка на главную страницу
]
```

4) После этого нужно создать файл [urls.py](http://urls.py) в папке с приложением и прописать в нем ссылку на интересующую страницу:

```python
from django.urls import path
from . import views # Импортируем вьюхи из папки с приложением

urlpatterns = [
    path('', views.index), # Главная страница
    path('about', views.about) # Страница о проекте
]
```

5) чтобы на страницах был какой-то контент необходимо в [urls.py](http://urls.py) импортировать файл views (from . import views) и внутри него написать методы, которые будут возвращать контент:

```python
from django.shortcuts import render
from django.http import HttpResponse

def index(request):
    return HttpResponse('<h4>Мощнейший заголовок<h4>')

def about(request):
    return HttpResponse('<h4>О проекте<h4>')
# Важно: в методах должен быть обязательный параметр "request"
```

## Отирсовка html страниц:

1) Внутри папки с приложением создаем папку templates, внутри папки templates создаем папку с наименованием приложения:
![[Untitled 1.png]]
![Untitled](Django%2075b3de05990b43259a6c22d06093c499/Untitled%201.png)

2) В папке templates/main создаем файл c html разметкой, например, index.html

3) В файле [views.py](http://views.py) обращаемся к созданному файлу с html разметкой из метода, используя встроенную функцию render():

```python
from django.shortcuts import render
from django.http import HttpResponse

def index(request):
    return render(request, 'main/index.html') 
    # Представляем, что находимся в папке templates
```

*чтобы создать шаблон в html файле необходимо написать восклицательный знак и нажать клавишу tab, получим шаюлон:

```html
<!doctype html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport"
          content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>
<body>

</body>
</html>
```

## Шаблонизатор html-файлов Jinja

0) Чтобы добавить коммент при помощи шаблонизатора Jinja нужно использовать такую конутрукцию: {# #}

1) Для начала создаем шаблон html-файла

2) В html-файле добавляем заглушки {% block content %}{% endblock %} для фрагментов, которые будут изменяться:

```html
<!doctype html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport"
          content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>{% block title %}{% endblock %}</title>
</head>
<body>
    {% block content %}
    {% endblock %}
</body>
</html>
```

3) Создаем файл с наполнением для созданных заглушек, например index.html

4) В файле index.html указываем, какой файл мы расширяем: {% extends '<путь к файлу>' %}

5) Далее прописываем контент, который должен отображаться в шаблоне

```html
{% extends 'main/layout.html' %}

{% block title %}Главная страница{% endblock %}

{% block content %}
    <h1>Главная страница</h1>
    <p>Не стелять, блять!</p>
{% endblock %}
```

6) Также можно вставлять в шаблон какие-то фрагменты html-разметки посредством директивы: {% include '<путь к файлу>' %}

Вставляем скрипт из файла test.html (можно вставлять сколько угодно раз)

```html
<!doctype html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport"
          content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>{% block title %}{% endblock %}</title>
</head>
<body>
    {% block content %}
    {% endblock %}
    {% include 'main/test.html'%}
    {% include 'main/test.html'%}
</body>
</html>
```

## Статические файлы (Bootstrap framework)

1) Забираем отсюда ссылку на CSS файл: [https://www.bootstrapcdn.com/](https://www.bootstrapcdn.com/)

2) Вставляем ссылку в html-шаблон layout.html:

```html
<head>
    <meta charset="UTF-8">
    <meta name="viewport"
          content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>{% block title %}{% endblock %}</title>
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/css/bootstrap.min.css">
</head>
```

3) В папке с приложением создаем папку static для хранения статических файлов (css-стили, картинки итд), внутри папки static создаем папку по наименованию приложения (по аналогии с templates). В этой папке создаем папки для каждого вида статичных файлов:
![[Untitled 2.png]]
![Untitled](Django%2075b3de05990b43259a6c22d06093c499/Untitled%202.png)

4) Далее необходимо подключить папку со статичными объектами к html-шаблону, для этого внутри html-шаблона прописываем команду: {% load static %}. Затем прокидываем ссылку на css файл через jinga в html-файл:

```html
{% load static %}
<!doctype html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport"
          content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>{% block title %}{% endblock %}</title>
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/css/bootstrap.min.css">
    <link rel="stylesheet" href="{% static 'main/css/main.css' %}">
</head>
<body>
    {% block content %}
    {% endblock %}
    {% include 'main/test.html'%}
</body>
</html>
```

5) В файле [settings.py](http://settings.py) указываем специальные настройку для static файлов:

```powershell
STATIC_URL = 'static/'

STATICFILES_DIRS = [
    BASE_DIR / "static",
]
```

6) В файл [urls.py](http://urls.py) импортируем библиотеки для взаимодействия со статичными файлами, а также к списку урлов добавляем настройку для статичных файлов

```powershell
from django.contrib import admin
from django.urls import path, include

from django.conf import settings
from django.conf.urls.static import static

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('main.urls')) # Ссылка на главную страницу
] + static(settings.STATIC_URL, document_root=settings.STATIC_ROOT)
```

7) Внутри css-файла main.css прописываем стиль:

```css
body {
    background: yellow;
}
```

## Добавление иконок и картинок

1) Чтобы добавить иконки в html-шаблон, необходимо сперва добавить ссылку на сайт с иконками, например: [https://cdn.jsdelivr.net/npm/bootstrap-icons@1.11.1/font/bootstrap-icons.css](https://cdn.jsdelivr.net/npm/bootstrap-icons@1.11.1/font/bootstrap-icons.css) , а затем добавить иконки с сайта в разметку:

```html
{% load static %}
<!doctype html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport"
          content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>{% block title %}{% endblock %}</title>
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/css/bootstrap.min.css">
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap-icons@1.11.1/font/bootstrap-icons.css">
    <link rel="stylesheet" href="{% static 'main/css/main.css' %}">
</head>
<body>
    <aside>
        <img src="{% static 'main/img/bee-svgrepo-com.svg' %}" alt="Логотип">
        <span class="logo">HellBee</span>
        <h3>Навигация</h3>
        <ul>
            <a href=""><li><i class="bi bi-house"></i>Главная</li></a>
            <a href=""><li><i class="bi bi-info-square"></i>О проекте</li></a>
            <a href=""><li><i class="bi bi-person-lines-fill"></i>Контакты</li></a>
        </ul>
    </aside>
    <main>
        {% block content %}
        {% endblock %}
        {% include 'main/test.html'%}
    </main>

</body>
</html>
```

2) Чтобы добавить картинку, ее сперва нужно положить в папку static/main/img , а потом добавить в разметку при помощи jinja:

```html
{% load static %}
<!doctype html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport"
          content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>{% block title %}{% endblock %}</title>
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/css/bootstrap.min.css">
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap-icons@1.11.1/font/bootstrap-icons.css">
    <link rel="stylesheet" href="{% static 'main/css/main.css' %}">
</head>
<body>
    <aside>
        <img src="{% static 'main/img/bee-svgrepo-com.svg' %}" alt="Логотип">
        <span class="logo">HellBee</span>
        <h3>Навигация</h3>
        <ul>
            <a href=""><li><i class="bi bi-house"></i>Главная</li></a>
            <a href=""><li><i class="bi bi-info-square"></i>О проекте</li></a>
            <a href=""><li><i class="bi bi-person-lines-fill"></i>Контакты</li></a>
        </ul>
    </aside>
    <main>
        {% block content %}
        {% endblock %}
        {% include 'main/test.html'%}
    </main>

</body>
</html>
```

## Вывод данных на экран (в том числе списки  и словари)

1) Все данные, которые передаются в шаблоне html должны быть оформлены внутри метода в файле views.py виде словаря
![[Untitled 3.png]]
![Untitled](Django%2075b3de05990b43259a6c22d06093c499/Untitled%203.png)

После этого можно вывести элемент словаря в html-шаблоне через конструкцию {{ }} по ключу:

```html
<p>{{ dishes }}</p>
```

2) Шаблонизатор Jinja позволяет фильтовать html-теги по определенному правилу:

```html
{% filter upper %}
  <h1>Главная страница</h1>
{% endfilter %}
```

3) Перебор эллементов итерируемого объекта при помощи цикла for в Jinja:

(списки и словари перебираются одинаково)

```html
{% for el in dishes %}
    <p>{{ el }}</p>
{% endfor %}
```

4) При помощи jinja можно отбирать значения по условию:

```html
{% for el in dishes %}
    {% if el == 'Чизкейк' %}
        <p>{{ el }}</p>
    {% endif %}
{% endfor %}
```

## Django ORM (cоздание таблиц в БД)

1) В файле [models.py](http://models.py) создаем класс для таблицы и внутри него перечисляем перечень полей таблицы с указанием их типов:

```python
from django.db import models

class Articles(models.Model):
    title = models.CharField('Название', max_length=100)
    review = models.TextField('Отзыв')
    date = models.DateTimeField('Дата публикации')

    def __str__(self):
        return self.title
```

2) Чтобы таблица нормально отражалась на панели адиминистратора, внутри класса с таблицей создаем класс с переименованием:
![[Untitled 4.png]]
![Untitled](Django%2075b3de05990b43259a6c22d06093c499/Untitled%204.png)

3) В терминале прокидываем команду для создания файла миграций, после этого создастся файл миграции:

```powershell
python manage.py makemigrations

```

4) Далее запускаем миграции при помощи команды в терминале

```powershell
python manage.py migrate

```

5) Для вывода таблицы на страницу добавляем ее в метод в файле views.py:
![[Untitled 5.png]]
![Untitled](Django%2075b3de05990b43259a6c22d06093c499/Untitled%205.png)

6) Для того чтобы войти в консоль для выполнения CRUD-операций используется команда 
python manage.py shell_plus --print-sql
или
python manage.py shell , если не установлены пакеты django-extensions и ipython

## Панель администратора

1) Чтобы перейти в панель администратора, переходим по ссылке: [http://127.0.0.1:8000/admin/](http://127.0.0.1:8000/admin/)

2) Чтобы изменить язык в панели администратора, меняем настройку в файле settings.py:

```python
LANGUAGE_CODE = 'ru'
```

3) Чтобы зарегистрировать пользователя, необходимо прокинуть в терминале команду

```powershell
python manage.py createsuperuser
```

4) Чтобы таблица появилась на панели администратора, ее необходимо зарегистрировать в файле admin.py
![[Untitled 6.png]]
![Untitled](Django%2075b3de05990b43259a6c22d06093c499/Untitled%206.png)

## Форма для внесения записей в базу

Команда для локального запуска сайта:

```powershell
python [manage.py](http://manage.py/) runserver

```