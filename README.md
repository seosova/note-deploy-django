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
        
7. Creating environment

        cd ~/
        virtualenv django_app
        
8. Activating a virtual environment

        source django_app/bin/activate

9. Deactivating a virtual environment

        deactivate 
        
        