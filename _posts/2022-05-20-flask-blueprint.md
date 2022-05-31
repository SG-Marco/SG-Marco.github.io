---
titie : 'Flask-Blueprint'
---

Flask를 모듈화 하여 사용할때 Blueprint를 쓴다.

### Basic usage

```
yourapp/
    __init__.py
    admin/
        __init__.py
        views.py
        static/
        templates/
    home/
        __init__.py
        views.py
        static/
        templates/
    control_panel/
        __init__.py
        views.py
        static/
        templates/
    models.py
```
이런 식의 구조로 원하는 부분만을 모듈화 하여 별개의 폴더로 구성할 수 있다.
폴더 안에 __init__.py 파일을 넣어두면 마치 패키지 처럼 import 하여 사용하면 된다.

code1   
```python
# facebook/views/profile.py

from flask import Blueprint, render_template

profile = Blueprint('profile', __name__,
                    template_folder='templates',
                    static_folder='static')

@profile.route('/<user_url_slug>')
def timeline(user_url_slug):
    # Do some stuff
    return render_template('profile/timeline.html')

@profile.route('/<user_url_slug>/photos')
def photos(user_url_slug):
    # Do some stuff
    return render_template('profile/photos.html')

@profile.route('/<user_url_slug>/about')
def about(user_url_slug):
    # Do some stuff
    return render_template('profile/about.html')
```
이런 식으로 파일 구성을 하면 된다.
Blueprint 라는 클래스를 생성하고 그 안에 사용할 폴더를 넣는다.

```python
# facebook/__init__.py

from flask import Flask
from .views.profile import profile

app = Flask(__name__)
app.register_blueprint(profile)
```

### Using a dynamic URL prefix

prefix는 두 가지 방법으로 지정 가능하다
```python
# facebook/views/profile.py

from flask import Blueprint, render_template

profile = Blueprint('profile', __name__, url_prefix='/<user_url_slug>')

# [...]
```
```python
# facebook/__init__.py

from flask import Flask
from .views.profile import profile

app = Flask(__name__)
app.register_blueprint(profile, url_prefix='/<user_url_slug>')
```
top level 에서 모듈 이동을 용이하게 하기 위해선 registration에 prefix를 주는것이 좋다고 한다. (아래)

그러면 code1을 아래와 같이 적을 수 있다. (짧은 endpoint)
```python
# facebook/views/profile.py

from flask import Blueprint, render_template, g

from ..models import User

# The prefix is defined on registration in facebook/__init__.py.
profile = Blueprint('profile', __name__)

@profile.url_value_preprocessor
def get_profile_owner(endpoint, values):
    query = User.query.filter_by(url_slug=values.pop('user_url_slug'))
    g.profile_owner = query.first_or_404()

@profile.route('/')
def timeline():
    return render_template('profile/timeline.html')

@profile.route('/photos')
def photos():
    return render_template('profile/photos.html')

@profile.route('/about')
def about():
    return render_template('profile/about.html')
```

아래는 아직 이해 못함  
We’re using the g object to store the profile owner and g is available in the Jinja2 template context.   
This means that for a barebones case all we have to do in the view is render the template.   
The information we need will be available in the template.   
```python
{% raw %}
{# facebook/templates/profile/photos.html #}

{% extends "profile/layout.html" %}

{% for photo in g.profile_owner.photos.all() %}
    <img src="{{ photo.source_url }}" alt="{{ photo.alt_text }}" />
{% endfor %}
{% endraw %}
```

