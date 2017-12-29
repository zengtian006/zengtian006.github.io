---
title: Building RESTFul APIs with Django REST framework
layout: post
date: '2017-12-29 03:50:45'
tags: Python
card-image: http://www.django-rest-framework.org/img/logo.png
post-card-type: image
---

## Preparation
1. Install [Python](https://www.python.org/downloads/)
2. Install  [Django](https://github.com/django/django)

	`pip install django`

	*If you are using Python 3.0+ please input the following command*

    `pip3 install django`
3. Install [Django-REST-framework](http://www.django-rest-framework.org/)

    `pip install djangorestframework` (change to `pip3` if you are using Python 3.0+)

## Creating an API with Django-rest-framework

1. Create a Django project and an app. I created it with a name django-tutorial and music

	```
$ django-admin startproject django-tutorial
$ cd django-tutorial
$ python manage.py startapp music
```

2. Add rest_framework and the app name to the installed apps in settings.py

	```
    INSTALLED_APPS = (
    ...
    'rest_framework',
    'music',
    ...
    )
	```

3. Create Model and schema for database. (Django using SQLite as default, you can change other database you prefered in setting.py)

    in music/models.py

    ```
    from django.db import models

		# Create your models here.
    class Music(models.Model):
        song = models.TextField()
        singer = models.TextField()
        last_modify_date = models.DateTimeField(auto_now=True)
        created = models.DateTimeField(auto_now_add=True)
        class Meta:
            db_table = "music"
    ```

4. Create database in SQLite (change to `python3` if you are using Python 3.0+)

		```
		python manage.py makemigrations
		python manage.py migrate
		```

5. Create a music/serializers.py which should look like the below way.

	  ```
    from rest_framework import serializers
	  from musics.models import Music
    class MusicSerializer(serializers.ModelSerializer):
        class Meta:
            model = Music
            fields = ('id', 'song', 'singer', 'last_modify_date', 'created')
	  ```

6. Create a music/view.py and enable CRUD

	  ```
    from musics.models import Music
    from musics.serializers import MusicSerializer
    from rest_framework import viewsets

		# Create your views here.
    class MusicViewSet(viewsets.ModelViewSet):
        queryset = Music.objects.all()
        serializer_class = MusicSerializer
	  ```

7. Update the root urls.py

	  ```
		from django.conf.urls import url, include
		from django.contrib import admin
		from rest_framework.routers import DefaultRouter
		from musics import views

		router = DefaultRouter()
		router.register(r'music', views.MusicViewSet)

		urlpatterns = [
				url(r'^admin/', admin.site.urls),
				url(r'^api/', include(router.urls))
		]
	  ```

8. Finally start server, the default port is 8000 (change to `python3` if you are using Python 3.0+)

		` python manage.py runserver [port] `

## Let's test our API now
* Create new music

	POST: http://127.0.0.1:8000/api/music/

	BODY:

		```
		{
		    singer:
		    song:
		}
		```

* Get all music info

	GET: http://127.0.0.1:8000/api/music/

* Update info for specify music (PATCH: update part of info)

	PUT: http://127.0.0.1:8000/api/music/{id}

	BODY:

		```
		{
		    singer:
		    song:
		}
		```

* Delete specify music

	DELETE: http://127.0.0.1:8000/api/music/{id}
