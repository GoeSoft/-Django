

---

## ✅  Пошаговый гайд с использованием виртуального окружения

---

## **1. Создание и активация виртуального окружения**

Откройте терминал в папке, где хотите создать проект (например, `~/projects` или `C:\projects`).

### Создайте виртуальное окружение:
```bash
python -m venv myblog_env
```

> Это создаст папку `myblog_env` с изолированным Python-окружением.

---

### Активируйте виртуальное окружение:

#### ➤ Windows (cmd):
```cmd
myblog_env\Scripts\activate
```

#### ➤ Windows (PowerShell):
```powershell
myblog_env\Scripts\Activate.ps1
```

> Если PowerShell блокирует выполнение скриптов, запустите от администратора:
> ```powershell
> Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
> ```
> Затем снова активируйте.

#### ➤ macOS / Linux:
```bash
source myblog_env/bin/activate
```

✅ После активации в командной строке вы должны увидеть что-то вроде:
```bash
(myblog_env) $
```

---

## **2. Установка Django**

Теперь, находясь в активированном окружении, установите Django:

```bash
pip install django
```

> ✅ Проверьте установку:
```bash
python -m django --version
```
(Должна отобразиться версия, например: `5.0.x`)

---

## **3. Создание проекта "MyBlog"**

```bash
django-admin startproject MyBlog
cd MyBlog
```

---

## **4. Создание приложения "blog"**

```bash
python manage.py startapp blog
```

---

## **5. Подключение приложения в `settings.py`**

Откройте `MyBlog/settings.py` и добавьте `'blog'` в `INSTALLED_APPS`:

```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'blog',  # ← добавлено
]
```

---

## **6. Создание модели Post**

Откройте `blog/models.py` и добавьте:

```python
from django.db import models

class Post(models.Model):
    title = models.CharField(max_length=200, verbose_name="Заголовок")
    content = models.TextField(verbose_name="Содержание")
    created_at = models.DateTimeField(auto_now_add=True, verbose_name="Дата создания")

    def __str__(self):
        return self.title

    class Meta:
        verbose_name = "Запись"
        verbose_name_plural = "Записи"
        ordering = ['-created_at']
```

---

## **7. Создание и применение миграций**

```bash
python manage.py makemigrations
python manage.py migrate
```

---

## **8. Настройка маршрутов**

### ➤ В `MyBlog/urls.py`:

```python
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('blog.urls')),
]
```

### ➤ Создайте файл `blog/urls.py`:

```python
from django.urls import path
from . import views

urlpatterns = [
    path('', views.PostListView.as_view(), name='post_list'),
]
```

---

## **9. Представление (views.py)**

Используем `ListView`:

```python
# blog/views.py

from django.views.generic import ListView
from .models import Post

class PostListView(ListView):
    model = Post
    template_name = 'blog/post_list.html'
    context_object_name = 'posts'
    ordering = ['-created_at']
```

---

## **10. Шаблон**

Создайте структуру папок:
```
blog/
└── templates/
    └── blog/
        └── post_list.html
```

Содержимое `blog/templates/blog/post_list.html`:

```html
<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <title>Мой блог</title>
</head>
<body>
    <h1>Записи блога</h1>
    <hr>
    {% for post in posts %}
        <div>
            <h2>{{ post.title }}</h2>
            <p><small>Создано: {{ post.created_at|date:"d.m.Y H:i" }}</small></p>
            <hr>
        </div>
    {% empty %}
        <p>Записей пока нет.</p>
    {% endfor %}
</body>
</html>
```

---

## **11. Админка (опционально, но рекомендуется)**

В `blog/admin.py`:

```python
from django.contrib import admin
from .models import Post

admin.site.register(Post)
```

Создайте суперпользователя:

```bash
python manage.py createsuperuser
```

Запустите сервер:

```bash
python manage.py runserver
```

Зайдите:
- Главная: `http://127.0.0.1:8000/`
- Админка: `http://127.0.0.1:8000/admin/` — добавьте несколько записей для теста.

---

## 💡 Полезные советы

- **Всегда активируйте окружение перед работой с проектом**.
- **Не коммитьте папку `myblog_env` в Git** — добавьте её в `.gitignore`.
- Для фиксации зависимостей проекта выполните:

```bash
pip freeze > requirements.txt
```

Тогда любой другой разработчик сможет воссоздать окружение:

```bash
python -m venv new_env
source new_env/bin/activate  # или activate.bat на Windows
pip install -r requirements.txt
```

---

## ✅ Готово!

Теперь ваш проект **полностью изолирован в виртуальном окружении**, соответствует лучшим практикам и готов к дальнейшей разработке и масштабированию.

Если нужно — могу помочь с `.gitignore`, `requirements.txt`, Docker-файлом или деплоем.
