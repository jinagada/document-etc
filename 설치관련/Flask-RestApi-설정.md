# 목차
- [사전 준비사항 및 주의사항](#user-content-사전-준비사항-및-주의사항)
- [추가 패키지 설치](#user-content-추가-패키지-설치)
- [Flask Rest Api 메인 파일 생성](#user-content-flask-rest-api-메인-파일-생성)
- [uWSGI 메인 파일 생성](#user-content-uwsgi-메인-파일-생성)
- [uWSGI 설정 파일 생성](#user-content-uwsgi-설정-파일-생성)
- [uWSGI Service 파일 생성](#user-content-uwsgi-service-파일-생성)
- [uWSGI Service 등록 및 실행](#user-content-uwsgi-service-등록-및-실행)
- [Nginx 설정](#user-content-nginx-설정)
- [Nginx 재실행](#user-content-nginx-재실행)
- [uWSGI Logrotate 설정](#user-content-uwsgi-logrotate-설정)

# 사전 준비사항 및 주의사항
- 가상 환경을 만들자.
  - anaconda 또는 virtualenv 를 이용하여 가상환경을 만들자.
- 아래 모든 스크립트는 가상환경에 접속을 하고 실행하자.
```shell
# Anaconda3 가상환경 생성
~$ anaconda3/bin/conda create -n sample_restapi python=3.5 anaconda
 
# 가상환경 접속
~$ source ~/anaconda3/bin/activate sample_restapi
 
# 가상환경 접속 해제
~$ source ~/anaconda3/bin/deactivate
 
# Source 디렉토리 생성
~$ mkdir ~/anaconda3/envs/sample_restapi/script
```

# 추가 패키지 설치
```shell
# 가상환경 접속
~$ source ~/anaconda3/bin/activate sample_restapi
 
# 추가 패키지 설치 : pip 업데이트
(sample_restapi) ~$ conda install -c anaconda msgpack-python
(sample_restapi) ~$ pip install --upgrade pip
# 추가 패키지 설치 : Kafka 모듈 설치
(sample_restapi) ~$ conda install -c conda-forge kafka-python
# 추가 패키지 설치 : Flask-RESTful 모듈 설치
(sample_restapi) ~$ pip install Flask-RESTful
# 추가 패키지 설치 : elasticsearch 모듈 설치
(sample_restapi) ~$ pip install elasticsearch
# 추가 패키지 설치 : uWSGI 모듈 설치
(sample_restapi) ~$ conda install -c conda-forge uwsgi
(sample_restapi) ~$ conda install -c conda-forge libiconv
```

# Flask Rest Api 메인 파일 생성
```shell
# 디렉토리 이동
~$ cd ~/anaconda3/envs/sample_restapi/script
 
# app.py 파일 생성
script$ vi app.py
```
```python
from flask import Flask, g
from flask_restful import Resource, Api, reqparse
from werkzeug.local import LocalProxy
from elasticsearch import Elasticsearch
from random import randint
from io import BytesIO
import logging
import json
import pycurl
 
app = Flask(__name__)
api = Api(app)

# 소스 내용은 생략
 
if __name__ == '__main__':
    app.run()
```

# uWSGI 메인 파일 생성
```shell
# 디렉토리 이동
~$ cd ~/anaconda3/envs/sample_restapi/script
 
# wsgi.py 파일 생성
script$ vi wsgi.py
```
```python
# wsgi.py # app.py와 같은 위치
from app import app
 
if __name__ == '__main__':
    app.run()
```

# uWSGI 설정 파일 생성
```shell
# 디렉토리 이동
~$ cd ~/anaconda3/envs/sample_restapi/script
 
# sample_restapi.ini 파일 생성
script$ vi sample_restapi.ini
```

**※ project, username 부분은 디렉토리 및 사용 계정에 맞게 잘 확인하여 생성 할 것!!**
```ini
[uwsgi]
project=sample_restapi
username=yourid
base=/home/%(username)/anaconda3/envs
chdir=%(base)/%(project)/script
home=%(base)/%(project)
module=wsgi
callable=app
master=true
processes=5
uid=%(username)
socket=/run/uwsgi/%(project).sock
chown-socket=%(username):nginx
chmod-socket=660
vacuum=true
 
logto=/var/log/uwsgi/%(project).log
```

# uWSGI Service 파일 생성
```shell
# uWSGI 서비스 파일 작성
~$ sudo vi /etc/systemd/system/sample-api.service
```

**※ 생성한 파일의 디렉토리 경로 및 계정을 잘 확인 하여 설정 할 것!!**
```ini
[Unit]
Description=Sample Restapi
[Service]
ExecStartPre=/usr/bin/bash -c 'mkdir -p /run/uwsgi; chown yourid:nginx /run/uwsgi'
ExecStart=/home/yourid/anaconda3/envs/sample_restapi/bin/uwsgi --ini /home/yourid/anaconda3/envs/sample_restapi/script/sample_restapi.ini
Restart=always
KillSignal=SIGQUIT
Type=notify
NotifyAccess=all
[Install]
WantedBy=multi-user.target
```

# uWSGI Service 등록 및 실행
```shell
# uWSGI 서비스 등록
(sample_restapi) ~$ sudo systemctl enable sample-api
 
# uWSGI 서비스 실행
(sample_restapi) ~$ sudo systemctl start sample-api
```

# Nginx 설정
```shell
# sample_restapi.conf 파일 생성
~$ sudo vi /etc/nginx/conf.d/sample_restapi.conf
```
```shell
server {
    listen 80;
    server_name api_04 192.168.56.104;
    root /home/yourid/anaconda3/envs/sample_restapi/script;
 
    location / {
        include uwsgi_params;
        uwsgi_pass unix:/run/uwsgi/sample_restapi.sock;
        autoindex on;
        autoindex_exact_size off;
    }
    location /nginx_status {
        stub_status;
        allow 127.0.0.1;
        deny all;
    }
}
```

# Nginx 재실행
```shell
# Nginx 재실행
~$ sudo systemctl restart nginx
```

# uWSGI Logrotate 설정
```shell
~$ sudo vi /etc/logrotate.d/uwsgi
```
```shell
/var/log/uwsgi/*.log {
    copytruncate
    daily
    rotate 15
    missingok
    notifempty
    compress
    dateext
    dateformat -%Y%m%d_%s
}
```
