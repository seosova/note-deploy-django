#Django Deploy
---

##Initial settings (new user, sudo and ssh)

1. Update Centos 7

        yum clean all && yum update

2. Create user django-user

        adduser django-user
        passwd django-user

3. Add privileges to new users

        gpasswd -a django-user wheel

4. **If you haven't ssh key** create it on your local machine (** local$ command **)

        ssh-copy-id django-user@SERVER_IP_ADDRESS

5. Copy your ssh-key to remote machine (** local$ command **)

        ssh-copy-id django-user@SERVER_IP_ADDRESS

6. Disallow remote ssh acces to the root account

        sudo yum install nano
        sudo nano /etc/ssh/sshd_config

    *on /etc/ssh/sshd_config* 
        
        PermitRootLogin no

7. Reload SSH

        sudo systemctl reload sshd

##Python3, pip, virtualenv

1. Enable the EPEL

        sudo yum install epel-release

2. Install development tools for isntalling Python3 from source and some packeges

        sudo yum groupinstall -y 'development tools'
        sudo yum install -y zlib-dev openssl-devel sqlite-devel bzip2-devel xz-libs

3. Download Python's 3.4. sources

        cd ~/
        wget https://www.python.org/ftp/python/3.4.3/Python-3.4.3.tar.xz

4. Extract, configuring and installation

        xz -d Python-3.4.3.tar.xz
        tar -xvf Python-3.4.3.tar
        cd Python-3.4.3
        sudo ./configure --prefix=/usr/local --enable-shared LDFLAGS="-Wl,-rpath /usr/local/lib"
        sudo make && sudo make altinstall

5. Update pip

        sudo /usr/local/bin/python3.4 -m pip install -U pip
        
6. Install virtualenv

        sudo /usr/local/bin/python3.4 -m pip install -U pip
        
        
##PostgreSQL and nginx

1. Install PostgreSQL

        sudo yum install postgresql-server postgresql-devel postgresql-contrib gcc nginx
        
2. Initialize PostgreSQL

        sudo postgresql-setup initdb

3. Start PostgreSQL

        sudo systemctl start postgresql
        
4. Configurated PostgreSQL

        sudo nano /var/lib/pgsql/data/pg_hba.conf

    *in /var/lib/pgsql/data/pg_hba.conf set md5*

        # IPv4 local connections:
        host    all             all             127.0.0.1/32            md5
        # IPv6 local connections:
        host    all             all             ::1/128                 md5
        
    *restart service PostgreSQL*

        sudo systemctl restart postgresql
        sudo systemctl enable postgresql

5. Create the PostgreSQL Database and User

        sudo su - postgres
        psql
        CREATE DATABASE django_base;
        CREATE USER django_user WITH PASSWORD 'password';
        GRANT ALL PRIVILEGES ON DATABASE django_base TO django_user;
        \q
        exit

##Create and configure a new Django Project

1. Create directory

        cd ~/
        mkdir django_app/

2. Creating environment

        cd ~/django_app
        virtualenv django_env
        
3. Activating a virtual environment

        source django_env/bin/activate

4. Install Django, Gunicorn and psycorg2

        pip install django gunicorn psycopg2

5. Create Django project in current directory ( dot in the end of command )

        django-admin.py startproject myproject .

