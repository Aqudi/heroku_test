# Heroku Deploy 실습
base repository : https://github.com/chulhee23/blog_practice  
유용한 링크 : https://dgkim5360.tistory.com/entry/deploy-django-project-on-heroku  
heroku는 aws와 같은 웹 호스팅 사이트로 다음의 경우에 쓰기 좋다.
1. 소규모 개인 프로젝트

2. 새로운 프레임워크를 배우고 그냥 놀고싶다

3. 시스템 관리하고 싶지 않고 빨리 시작

결론 : 무료(부분 유료) 

### 또 다른 호스팅 사이트 목록  
링크 : http://djangofriendly.com/hosts/  
___
## Critical Setting 
- deploy를 할 시 보안상 이슈가 될만한 중요한 설정들
- 이외의 다양한 설정들  Document : https://docs.djangoproject.com/en/2.0/howto/deployment/checklist/

```python
DEBUG = bool(os.environ.get('DJANGO_DEBUG', True))
SECRET_KEY = os.environ.get('DJANGO_SECRET_KEY', 'YOUR SECRET_KEY')
```
___
## Procfile 생성
Django만으로 여는 서버는 TEST를 위한 서버  
운영모드에서 Gunicorn 같은 WSGI(Web Server Gateway Interface) 미들웨어와 연동이 필요하다.
```
pip install gunicorn
```
Procfile 안에 다음 내용을 넣어줍니다.
```
web: gunicorn 프로젝트명.wsgi --log-file -
```     
***
## Database 관련 필요한 모듈들
heroku의 DB는 django의 기본 DB인 sqllite3가 아니여서  
dj-database-url 라이브러리를 사용하여 postgre로 바꿔줘야한다.
```
pip install dj-database-url
```
위의 데이터베이스를 python에서 사용하기 위한 라이브러리
```
pip install psycopg2-binary
```
___
## settings.py파일 수정
```python
# Heroku: Update database configuration from $DATABASE_URL.
import dj_database_url
db_from_env = dj_database_url.config(conn_max_age=500)
DATABASES['default'].update(db_from_env)
```

whitenoise 라이브러리는 static 파일을 collectstatics 명령수행시 지정경로에 파일들을 모아주는 역할을 한다.

```
pip install whitenoise
```
settings.py의 middleware에 다음을 추가
```python
'whitenoise.middleware.WhiteNoiseMiddleware',
```


settings.py의 ALLOWED_HOSTS를 다음과 같이 수정  
```
ALLOWED_HOSTS = [] -> ALLOWED_HOSTS = ["*"]
```
___
## runtime.txt  
다음 내용을 넣어줍니다.
```
python version  
ex) python-3.6.5
```
___
## 나머지 필요한 작업들
```
pip freeze > requirements.txt
```

```
heroku login

heroku create

git push heroku master

heroku run python manage.py migrate
```
___