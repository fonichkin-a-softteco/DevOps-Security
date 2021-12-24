# Task 2

This project help you easy deploy and setting PostgreSql and Django and connect them.


##1)You should install next software:
### Virtual box:
  sudo apt install virtualbox

### Vagrant:
  sudo apt install vagrant

##2)Create folder and go to this one with your project name: 
   mkdir project && cd project

 
##3)You will need create following file:
__
###Vagrantfile:
Vagrant.configure("2") do |config|

config.vm.define "postgre" do |vm1|

vm1.vm.box = "debian/buster64"

vm1.vm.hostname = "postgre.server"

vm1.vm.network "public_network", ip: "192.168.1.100", bridge: "wlp0s20f3"

vm1.vm.box_check_update = false

vm1.vm.provider "virtualbox" do |vb|

vb.memory = 2048

vb.cpus = 2

end

end

config.vm.define "django" do |vm2|

vm2.vm.box = "debian/buster64"

vm2.vm.hostname = "django.server"

vm2.vm.network "public_network", ip: "192.168.1.110", bridge: "wlp0s20f3"

vm2.vm.box_check_update = false

vm2.vm.provider "virtualbox" do |vb|

vb.memory = 2048

vb.cpus = 2

end

end

end
____

##4)For up 2 virtual machines
- vagrant up

##5)You should use next commands:

###go to postgres virtual machine
- vagrant ssh postgres
####update
- sudo apt update
####install postgresql
- sudo apt install postgresql postgresql-contrib
####install lib for correct work
- sudo apt install libpq-dev
#### go in postgresql
- sudo su - postgres
- psql
#### create database
- create database django_db;
#### create user with password
- create user user_name with password 'password';
#### add privileges to user
- grant all privileges on database django_db to user_name;
#### exit from database
- \q
- exit
#### go to folder
- cd /etc/postgresql/12/main/
#### open file postgresql configuration file, update and save
- sudo nano postgresql.conf
___
listen_addresses = '*'     # what IP address(es) to listen on;
___
___
####open file postgresql configuration file, update and save
-  sudo nano pg_hba.conf
___

ssl_cert_file = '/etc/postgresql/12/main/server.crt'
#### #ssl_crl_file = ''
ssl_key_file = '/etc/postgresql/12/main/server.key'
___
#### # IPv4 local connections:
host    all             all             0.0.0.0/0               md5
___
#### restart postgresql
- sudo systemctl restart postgresql
___
____

###go to django virtual machine
- vagrant ssh django
####update
- sudo apt update
####install pip
- sudo apt install python3-pip
####install Django
- sudo pip3 install Django
####check django-admin
- which django-admin
####create and go to project folder
- mkdir projects && cd projects
####create application
- django-admin startproject test_app
#### go to folder test_app
- cd test_app/
####migrate manage.py
- python3 manage.py migrate
####create super user for application
- python3 manage.py createsuperuser    
####open file postgresql settings.py file, update and save
- nano test_app/settings.py
___
ALLOWED_HOSTS=['192.168.1.110']

DATABASES = {
  'default': {
  'ENGINE': 'django.db.backends.postgresql_psycopg2',
  'NAME': 'django_db',
  'USER' : 'user_name',
  'PASSWORD' : 'password',
  'HOST' : '192.168.1.100',
  'PORT' : '5432',
  }
}
___
####install psycopg2
- sudo apt install python3-psycopg2
####migrate manage.py
- python3 manage.py migrate

##6) Add ssl certification
###go to postgres virtual machine
- vagrant ssh postgres
###Use next command to create certification:
- sudo openssl req -new -x509 -days 365 -nodes -text -out server.crt \
 -keyout server.key -subj "/CN=192.168.1.100"
- sudo chmod og-rwx server.key
- sudo openssl req -new -nodes -text -out root.csr \
  -keyout root.key -subj "/CN=192.168.1.110"
- sudo chmod og-rwx root.key
- sudo openssl x509 -req -in root.csr -text -days 3650 \
  -extfile /etc/ssl/openssl.cnf -extensions v3_ca \
  -signkey root.key -out root.crt
- sudo openssl req -new -nodes -text -out server.csr \
  -keyout server.key -subj "/CN=192.168.1.100"
- sudo chmod og-rwx server.key
- sudo openssl x509 -req -in server.csr -text -days 365 \
  -CA root.crt -CAkey root.key -CAcreateserial \
  -out server.crt
- sudo openssl verify -CAfile ./root.crt -purpose sslclient ./server.crt
- sudo chown postgres: server.* root.*
- sudo chmod 600 server.*
- sudo chmod 600 root.*
####restart postgresql
- sudo systemctl restart postgresql
####copy file root.crt to django server for get access to database
- sudo scp root.crt vagrant@192.168.1.110://home/vagrant/projects/test_app
####for check logs may be used command:
- tail -f /var/log/postgresql/postgresql-12-main.log

###go to django virtual machine
- vagrant ssh django
####migrate manage.py
- python3 manage.py migrate
####start project
- python3 manage.py runserver 0.0.0.0:8000














