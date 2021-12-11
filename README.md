# Django todolist

1. django app 만들기

   ```python
   $django-admin startproject todolist
   ```

   → npx create-react-app todolist 와 같은 명령어로 생각하면 된다. (폴더도 새로 만들어줌)

2. 이후에 $django-admin startapp todo라는 명령어를 치면 todo라는 폴더가 내부에 생성되면서 앱을 하나 더 만들 수 있다. 이는 아직 왜 그렇게 하는 지 모르겠다.. ㅜ → 만들다 보니 원래 그렇게 하는 것 같음!! main project에는 views 가 없다!
3. templates folder를 만들어야 한다! ← html 파일이 들어갈 곳!!
4. todolist 폴더의 settings.py에 TEMPLATES 내부 DIR을 설정해준다!

   ```python
   TEMPLATES = [
       {
           'BACKEND': 'django.template.backends.django.DjangoTemplates',
           'DIRS': [BASE_DIR/'templates'], #이 부분!!
           'APP_DIRS': True,
           'OPTIONS': {
               'context_processors': [
                   'django.template.context_processors.debug',
                   'django.template.context_processors.request',
                   'django.contrib.auth.context_processors.auth',
                   'django.contrib.messages.context_processors.messages',
               ],
           },
       },
   ]
   ```

5. todo 폴더 안에 urls.py를 만들어준다!

   ```python
   from django.urls import path
   	from . import views # .은 루트라는 뜻

   urlpatterns = [
     path('', views.index, name='index') #router 개념인 것 같다
   ]
   ```

   path(path, views에 있는 함수, 이름 지정)

6. views 파일 편집해준다

   ```python
   from django.shortcuts import render

   # Create your views here.
   def index(request):
     return render(request, 'index.html')
   ```

7. 다시 django main project folder(todolist)에 가서 코드를 추가해준다 (urls.py)

   ```python
   from django.contrib import admin
   		from django.urls import path, include #include는 원래 없었다

   urlpatterns = [
       path('admin/', admin.site.urls),
       path('', include('todo.urls')) #요렇게 추가
   ]
   ```

8. 서버 실행 명령어 - $python [manage.py](http://manage.py) runserver
9. model 만들기

   ```python
   from django.db import models

   # Create your models here.
   class Todo(models.Model):
     title = models.CharField(max_length=1000)

     def __str__(self):
       return self.title
   ```

10. installed app에 추가해주기! settings in main project

    ```python
    INSTALLED_APPS = [
        'django.contrib.admin',
        'django.contrib.auth',
        'django.contrib.contenttypes',
        'django.contrib.sessions',
        'django.contrib.messages',
        'django.contrib.staticfiles',
        'todo'
    ]
    ```

    → 추가한 이후에 migrate를 해줘야 함

    → python [manage.py](http://manage.py) makemigrations → migrate

11. create superuser → db root임

    9에서 만든 model을 적용하기 위해선 admin 파일에서 register 해줘야 한다.

    ```python
    from django.contrib import admin
    from .models import Todo

    # Register your models here.
    admin.site.register(Todo)
    ```

12. index.html (front-end)에서 backend로 넘겨주는 것 처리 우선 현재 index.html로 오는 request를 views에 index 함수로 처리해주고 있다. 이에 아래와 같이 코드를 짜준다.

    ```python
    from django.shortcuts import render
    from .models import Todo #.으로 시작하는건 루트!!

    # Create your views here.
    def index(request):
      if request.method == 'POST':
    #현재 index.html의 form action=""로 설정돼있기 때문에 같은 함수에서 받아준다
        new_todo = Todo(
          title = request.POST['title']
        )
        print(new_todo) #테스트를 위해 프린트
    		new_todo.save() #db에 저장을 위한 것.. 되게 간단하다!
      return render(request, 'index.html')
    ```

    ```python
    # in the def index
    todo = Todo.objects.all

    #....

    return render(request, 'index.html', {'todo': todo})
    # db 내용 전달
    ```

    1. 삭제하는 법 (db)

       ```python
       def delete(request, id):
         todo = Todo.objects.get(id= id)
         todo.delete()
         return redirect('/')
       ```
# Django_Todolist
