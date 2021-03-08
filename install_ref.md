

## backed Server Saleor 설치

# Installing python and other essentials
https://www.digitalocean.com/community/tutorials/how-to-install-python-3-and-set-up-a-programming-environment-on-an-ubuntu-18-04-server

sudo add-apt-repository ppa:deadsnakes/ppa
sudo apt-get update
sudo apt-get -y upgrade

sudo apt-get install python3.8 libpython3.8-dev python3-all-dev


sudo apt install virtualenv
sudo apt install postgresql
sudo apt install uwsgi
sudo apt-get install build-essential python3-dev

# Required for 2.11 and above.
sudo apt-get install libpangocairo-1.0-0

sudo apt-get install apache2-dev
pip3 install mod_wsgi


### Setup Node
 ### insatalling node.
 sudo apt install npm

 ## installing NVM
 curl https://raw.githubusercontent.com/creationix/nvm/master/install.sh | bash

 source ~/.profile 
 source ~/.bashrc
 nvm install v12






### Setup Code 
 mkdir -p codes/backend
 cd codes/backend
 virtualenv -p python3.8 venv
 source venv/bin/activate
 
 ## git clone
 git clone -b 2.10 https://github.com/sncap/saleor.git
 
 cd saleor
 #git fetch origin 2.10:2.10 && git checkout 2.10
 pip install -r requirements.txt

### settiing up postgres user.

 sudo su - postgres
 psql


 CREATE ROLE saleor WITH LOGIN PASSWORD 'saleor';
 CREATE DATABASE saleor;
 ALTER USER saleor WITH SUPERUSER;

 GRANT ALL PRIVILEGES ON DATABASE saleor TO saleor;
 ALTER USER saleor CREATEDB;


# Drop table
# DROP DATABASE saleor;

postgres=# \q
 \q


 
postgres@mall-server:~$ ougout
 logout

## Export these variables to os.
 # setup (public IP) EXT-IP 34.64.87.109, INT-IP 10.178.0.46
 # ValueError: Cannot add amount in 'KRW' to 'USD'

export ALLOWED_HOSTS=34.64.87.109
export ALLOWED_CLIENT_HOSTS=34.64.87.109
export DEBUG=True
export SECRET_KEY=123456
export INTERNAL_IPS=127.0.0.1,34.64.87.109,
export DEFAULT_COUNTRY=KO
export DEFAULT_CURRENCY=USD
export ALLOWED_HOSTS=34.64.87.109,*



### Applying migrations
 python manage.py migrate

## create dummy data
 python manage.py populatedb
 
## Create superuser
 python manage.py createsuperuser


## Account 설정
email : admin@samsung.com
password : 123456

## runserver
python manage.py runserver 0.0.0.0:8000


### browser 접속
http://34.64.87.109:8000/graphql/

# graphql Test (DB Test)
#https://docs.saleor.io/docs/developer/pagination


```
query {
  products(first: 10) {
    edges {
      cursor
      node {
        id
        name
      }
    }
  }
}
```

# 결과가 보이면 설치 서버 성공


## Front  Saleor 설치
cd ~/saleor_setup/codes/backend/
source venv/bin/activate

## git clone code
# 2.11 Error 2.10 로 재테스트
git clone -b 2.10 https://github.com/sncap/saleor-storefront.git storefront


cd storefront/

#git checkout -b 2.10 remotes/origin/2.10

#version 확인 
git branch
* 2.10


# Installing the dashboard
npm install


# package.json에 host 주소 변경 기본 (127.0.0.1 로되어 있어 접속이 안됨)
# --host 0.0.0.0 추가 

vim package.json
```

"start": "cross-env NODE_ENV=develop webpack-dev-server --history-api-fallback --watch --host 0.0.0.0 --port 3000 --mode development --hotOnly",
```


# Running the Front 
# 설치서버 URL 설정  34.64.87.109
export API_URI=http://34.64.87.109:8000/graphql/
export BACKEND_URL='http://34.64.87.109:8000/'


npm start

# front 접속
http://34.64.87.109:3000/


------------------------------------------

# Setup Dashboard

cd ~/codes 

git clone -b 2.11 https://github.com/sncap/saleor-dashboard.git dashboard

cd dashboard
git branch
* 2.11

vi package.json 
# --host 0.0.0.0 추가 
"start": "webpack-dev-server -d --host 0.0.0.0"


# dashboard 실행
export API_URI=http://34.64.87.109:8000/graphql/
npm start


# Dashboard 접속
http://34.64.87.109:9000/

#login
email : admin@samsung.com
password : 123456

====================================================

# Node.js의 프로세스 매니저 PM2
https://engineering.linecorp.com/ko/blog/pm2-nodejs/


# PM2 설치
npm install -g pm2@latest

# 설치 확인
pm2

usage: pm2 [options] <command>

pm2 -h, --help             all available commands and options
pm2 examples               display pm2 usage examples
pm2 <command> -h           help on a specific command

Access pm2 files in ~/.pm2


# scrit 생성
add
vi ~/.bashrc
```
export ALLOWED_HOSTS=34.64.87.109,*
export ALLOWED_CLIENT_HOSTS=34.64.87.109
export DEBUG=True
export SECRET_KEY=123456
export INTERNAL_IPS=127.0.0.1,34.64.87.109,
export DEFAULT_COUNTRY=KO
export DEFAULT_CURRENCY=USD
export APP_MOUNT_URI=/dashboard/
export API_URI=http://34.64.87.109:8000/graphql/
```


cd ~/codes/backend/
vi run_api.sh
```
source ~/codes/backend/venv/bin/activate
cd ~/codes/backend/saleor/

python manage.py runserver 0.0.0.0:8000
```



vi run_storefront.sh
```
cd ~/codes/storefront

export API_URI=http://34.64.87.109:8000/graphql/
export BACKEND_URL='http://34.64.87.109:8000/'

npm start
```



vi run_dashboard.sh
```
cd ~/codes/dashboard/

export API_URI=http://34.64.87.109:8000/graphql/
npm start
```

chmod 775 *.sh

-------------------------

# 실행
pm2 start run_api.sh
pm2 start run_storefront.sh
pm2 start run_dashboard.sh


# 상태 확인 
pm2 status

# show log
# pm2 show {id}
pm2 show 0

#pm2에서 log를 관리함.

# pm2 명령어
$ pm2 start   config.js 
$ pm2 restart config.js # 정의된 프로세스 재시작
$ pm2 reload  config.js # 정의된 프로세스 설정 다시 불러오기
$ pm2 delete  config.js # 정의된 프로세스 삭제

# pm2 프로세스 관리
$ pm2 stop all # 모든 프로세스 멈추기
$ pm2 restart all # 모든 프로세스 재시작
$ pm2 reload all # 모든 프로세스의 다운타임을 0으로 설정후 다시로드

$ pm2 stop 0 # 프로세스 0번을 멈추기
$ pm2 restart 0 # 프로세스 0번을 재시작
$ pm2 delete 0 # 프로세스 0번을 삭제
$ pm2 delete all # 프로세스 전부 삭제

$ pm2 ping # pm2가 사용가능한지 핑확인
$ pm2 updatePM #pm2 업데이트
$ pm2 reset <process> # 리셋 후 메타데이터 리로드

#로그관리
$ pm2 logs #모든 로그 출력
$ pm2 ilogs #자세한 로그 출력
$ pm2 flush #모든 로그 비우기
$ pm2 reloadLogs #로그 다시불러오기


# pm2 list
