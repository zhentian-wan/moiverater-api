# moiverater-api

## Step by Step set up

a. Download python 3.6

b. Make alias for python3 and pip3 in `~/.bashrc` file
```bash
alias python =python3
alias pip=pip3
```

c. Make project folder
```bash
mkdir moiverater-api
cd moiverater-api
```

d. Create a virtualenv to isolate our package dependencies locally
```bash
virtualenv env
source env/bin/activate
```

e. Install Django and Django REST framework into the virtualenv
```bash
pip install django
pip install djangorestframework
```

f. Set up a new project with a single application
```bash
django-admin.py startproject moiverater .
cd moiverater
django-admin.py startapp api
cd ..
python manage.py migrate
```

g. `moiverater/api/serializers.py`:
```python
from django.contrib.auth.models import User

from rest_framework import serializers


class UserSerializer(serializers.HyperlinkedModelSerializer):
    class Meta:
        model = User
        fields = ('username', 'email')

```

h. change `moiverater/api/views.py`
```python
from django.shortcuts import render

# Create your views here.
from django.contrib.auth.models import User
from rest_framework import viewsets
from moiverater.api.serializers import UserSerializer


class UserViewSet(viewsets.ModelViewSet):
    """
    API endpoint that allows users to be viewed or edited.
    """
    queryset = User.objects.all().order_by('-date_joined')
    serializer_class = UserSerializer

```

i. Add 'rest-frameworks' to `moiverater/settings.py`:
```python
INSTALLED_APPS = [
    ...
    'rest_framework',
]
```

j. `moiverater/urls.py`:
```python
"""moiverater URL Configuration

The `urlpatterns` list routes URLs to views. For more information please see:
    https://docs.djangoproject.com/en/2.0/topics/http/urls/
Examples:
Function views
    1. Add an import:  from my_app import views
    2. Add a URL to urlpatterns:  path('', views.home, name='home')
Class-based views
    1. Add an import:  from other_app.views import Home
    2. Add a URL to urlpatterns:  path('', Home.as_view(), name='home')
Including another URLconf
    1. Import the include() function: from django.urls import include, path
    2. Add a URL to urlpatterns:  path('blog/', include('blog.urls'))
"""
from django.conf.urls import url, include
from rest_framework import routers
from moiverater.api import views

router = routers.DefaultRouter()
router.register(r'users', views.UserViewSet)

# Wire up our API using automatic URL routing.
# Additionally, we include login URLs for the browsable API.
urlpatterns = [
    url(r'^', include(router.urls)),
    url(r'^api-auth/', include('rest_framework.urls', namespace='rest_framework'))
]

```