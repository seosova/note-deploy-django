#Django Deploy
---

##Initial settings (new user, sudo and ssh)

1. Buy Centos 7 ( i use infobox.ru )
2. Update Centos 7

        yum clean all && yum update


3. Create user django-user

        adduser django-user
        passwd django-user

4. Add privileges to new users

        gpasswd -a django-user wheel

5. **If you haven't ssh key** create it on your local machine (** local$ command **)

        ssh-copy-id django-user@SERVER_IP_ADDRESS

6. Copy your ssh-key to remote machine (** local$ command **)

        ssh-copy-id django-user@SERVER_IP_ADDRESS

7. Disallow remote ssh acces to the root account

        sudo yum install nano
        sudo nano /etc/ssh/sshd_config

    *on /etc/ssh/sshd_config* 
        
        PermitRootLogin no

8. Reload SSH

        sudo systemctl reload sshd

##DataBase settings (PostgreSQL)