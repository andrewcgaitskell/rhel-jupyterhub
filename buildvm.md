rhel 8

make sure port forwarding is enabled

# nginx

    sudo su
    yum update
    yum install curl -y
    yum install nginx -y
  

# check nginx commands

    nginx -t
    nginx -s reload

    systemctl stop nginx
    systemctl start nginx
    systemctl status nginx

# install nano

    yum install nano

# add default to conf.d folder

    nano /etc/nginx/nginx.conf
    
    
    events {
      worker_connections  4096;  ## Default: 1024
    }
    http {
     server {
        listen 80;
        listen [::]:80;
        #server_name dev3.dmtools.info;
        server_name dev4.dmtools.info;
    #location / {
    #        rewrite ^ https://$host$request_uri? permanent;
    #    }

    location / {
            root   /var/www/html;
            index  index.html;
        }

    location ~ /.well-known/acme-challenge {
            allow all;
            root /tmp/acme_challenge;
        }

    }
    }

 
# make var/www/html

    cd var
    mkdir www
    cd www
    mkdir html
    cd html
    
    nano index.html
    
        <!DOCTYPE html>
        <html>
        <body>

        <h1>My First Heading</h1>
        <p>My first paragraph.</p>

        </body>
        </html>     

# check nginx config and reload

    sudo nginx -t
    sudo nginx -s reload


# start here

    apt-get -y install openssl
    apt-get -y install sudo
    apt-get install -y apt-utils dialog
    apt-get install -y build-essential python3.10 python3-pip python3-dev
    apt-get -y install nodejs npm
    apt-get -y install python3.10-venv
    apt-get -y install python3-mysqldb
    apt-get -y install libmysqlclient-dev

    npm install -g configurable-http-proxy

    mkdir srv/jupyterhub
    mkdir srv/jupyterhub/home

    mkdir srv/jupyterhub/notebooks

    ARG user=jupyterhub
    ARG home=/srv/jupyterhub/home/$user

    adduser \
    --disabled-password \
    --gecos "" \
    --home /srv/jupyterhub/home/jupyterhub \
    --ingroup docker \
    jupyterhub


#RUN usermod -aG shadow jupyterhub

echo "jupyterhub:jupyterhub" | chpasswd

usermod -aG sudo jupyterhub

groupadd jupyterhub

usermod -aG jupyterhub jupyterhub

chown -R jupyterhub:jupyterhub /srv/jupyterhub

#ensures that /var/run/docker.sock exists
touch /var/run/docker.sock

#changes the ownership of /var/run/docker.sock
chown jupyterhub:jupyterhub /var/run/docker.sock

# Give rhea passwordless sudo access to run the sudospawner mediator on behalf of users:
ADD sudoers /tmp/sudoers
RUN cat /tmp/sudoers >> /etc/sudoers
RUN rm /tmp/sudoers

RUN chown jupyterhub .

USER jupyterhub

ENV VIRTUAL_ENV=/srv/jupyterhub/env
RUN python3 -m venv $VIRTUAL_ENV
ENV PATH="$VIRTUAL_ENV/bin:$PATH"

# Install dependencies:
COPY requirements.txt .
RUN pip install -r requirements.txt

RUN jupyter lab build

RUN pip -q install pip --upgrade
RUN pip install wheel

WORKDIR /srv/jupyterhub

#RUN touch jupyterhub_cookie_secret
#RUN chmod g+rw jupyterhub_cookie_secret
#RUN openssl rand -hex 32 > jupyterhub_cookie_secret
#RUN chmod 600 jupyterhub_cookie_secret

RUN jupyterhub --generate-config

COPY . .
#EXPOSE 8000
CMD . /srv/jupyterhub/env/bin/activate && exec jupyterhub

# install node

# now looking at node and how to connect containers

    apt update
    apt install nodejs npm


# install venv

# activate venv

# letsencrypt

apt install certbot python3-certbot-nginx

https://www.nginx.com/blog/using-free-ssltls-certificates-from-lets-encrypt-with-nginx/

sudo su

cd /etc/nginx/conf.d

nano dev1.dmtools.info.conf


    server {
    listen 80 default_server;
    listen [::]:80 default_server;
    root /var/www/dev1.dmtools.info/html;
    server_name dev1.dmtools.info;

    #charset koi8-r;

    #access_log  logs/host.access.log  main;

    location / {
        root /var/www/dev1.dmtools.info/html;
        index  index.html;
    }

    location /hello/ {
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header Host $host;
        proxy_pass http://127.0.0.1:8080/;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
        # location /overview {
        #     proxy_pass http://127.0.0.1:3000$request_uri;
        #     proxy_redirect off;
        # }
    }


    location /dev/ {
        proxy_pass http://localhost:5050/dev/;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }

    error_page  404              /404.html;
    location = /404.html {
        root   /usr/share/nginx/html;
    }

    # redirect server error pages to the static page /50x.html
    #
    }


certbot --nginx -d dev1.dmtools.info


    location = /dev/hub/oauth_callback {
        proxy_pass http://localhost:5050/dev/hub/oauth_callback;

        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header Host $host;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        # WebSocket support
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection $connection_upgrade;
        proxy_redirect https,http https;

    }
