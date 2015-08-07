#Django Deploy
---

##Initial settings (new user, sudo and ssh)

1. Update Centos 7 ( use centos minimal without apache )

        yum clean all && yum update && yum install sudo

2. Create user django-user

        adduser django-user
        passwd django-user

3. Add privileges to new users

        gpasswd -a django-user wheel

4. Copy your ssh-key to remote machine (** local$ command **)

        ssh-copy-id django-user@SERVER_IP_ADDRESS

5. Disallow remote ssh acces to the root account

        sudo yum install nano
        sudo nano /etc/ssh/sshd_config

    *on /etc/ssh/sshd_config* 
        
        PermitRootLogin no

6. Reload SSH

        sudo systemctl reload sshd


##NGINX

1. Install

        sudo yum install epel-release && sudo yum groupinstall -y 'development tools' && sudo yum install -y zlib* openssl-devel sqlite-devel bzip2-devel xz-libs readline-devel gcc nginx git

        
2. Stop Apache if it working ( use minimal os withaout preinstall apache )

        sudo systemctl stop httpd && sudo systemctl disable httpd

4. Start Nginx

        sudo service nginx start && sudo systemctl enable nginx

##Python3, pip, virtualenv

1. Download Python's 3.4. sources

        cd ~/ && wget https://www.python.org/ftp/python/3.4.3/Python-3.4.3.tar.xz && xz -d Python-3.4.3.tar.xz && tar -xvf Python-3.4.3.tar && cd Python-3.4.3 && sudo ./configure --prefix=/usr/local --enable-shared LDFLAGS="-Wl,-rpath /usr/local/lib" && sudo make && sudo make altinstall

2. Isntall or Update pip

   *install*
   
        wget https://bootstrap.pypa.io/get-pip.py
        sudo /usr/local/bin/python3.4 get-pip.py
        
   *update*
   
        sudo /usr/local/bin/python3.4 -m pip install -U pip
        
3. Install virtualenv

        sudo /usr/local/bin/python3.4 -m pip install virtualenv
        
        
##PostgreSQL

1. All comand

        sudo yum install postgresql-server postgresql-devel postgresql-contrib && sudo postgresql-setup initdb && sudo systemctl start postgresql 
        
       
2. Configurated PostgreSQL

        sudo nano /var/lib/pgsql/data/pg_hba.conf

    *in /var/lib/pgsql/data/pg_hba.conf set md5*

        # IPv4 local connections:
        host    all             all             127.0.0.1/32            md5
        # IPv6 local connections:
        host    all             all             ::1/128                 md5
        
    *restart service PostgreSQL*

        sudo systemctl restart postgresql
        sudo systemctl enable postgresql

3. Create the PostgreSQL Database and User

        sudo su - postgres
        psql
        CREATE DATABASE django_base;
        CREATE USER django_user WITH PASSWORD 'password';
        GRANT ALL PRIVILEGES ON DATABASE django_base TO django_user;
        \q
        exit

##Create and configure a new Django Project

        cd ~/ && mkdir django-app/ && cd ~/django-app && virtualenv django-env && source django-env/bin/activate && pip install django gunicorn psycopg2 && django-admin.py startproject myproject .
        


##Settings your project

1. Settings project's database for PostgreSQL

        nano myproject/settings.py

    in myproject/settings.py
    
        DATABASES = {
            'default': {
                'ENGINE': 'django.db.backends.postgresql_psycopg2',
                'NAME': 'myproject',
                'USER': 'myprojectuser',
                'PASSWORD': 'password',
                'HOST': 'localhost',
                'PORT': '',
            }
        }
        
    in myproject/settings.py add to last line
    
        STATIC_ROOT = os.path.join(BASE_DIR, "static/")

##Complite project setup


        source django-env/bin/activate && cd ~/myproject && ./manage.py makemigrations && ./manage.py migrate && ./manage.py createsuperuser
        ./manage.py collectstatic
      

        
    