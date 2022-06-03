# check nginx commands

    nginx -t
    nginx -s reload

    systemctl stop nginx
    systemctl start nginx
    systemctl status nginx


# editing conf.d folder

    
    nano /etc/nginx/nginx.conf
    

 
# make home file in /usr/share/nginx/html

   cd /usr/share/nginx/html
    
   nano index.html
    
        <!DOCTYPE html>
        <html>
        <body>

        <h1>DM Tools Home</h1>
        <p>My first paragraph.</p>

        </body>
        </html>     

# check nginx config and reload

    sudo nginx -t
    sudo nginx -s reload


# start here
    
    yum install nodejs npm
    
    
    upload sql file to server
    
    make RubyDB Database
    
    load backup file
    
    mysql -h localhost -u root -p -D RubyDB < 20211104_dmtools_backup.sql;
       
    yum install sqlite-devel
    
    npm install -g configurable-http-proxy

    mkdir /srv/jupyterhub
    mkdir /srv/jupyterhub/home

    mkdir /srv/jupyterhub/notebooks

    ARG user=jupyterhub
    ARG home=/srv/jupyterhub/home/$user

    adduser \
    --disabled-password \
    --gecos "" \
    --home /srv/jupyterhub/home/jupyterhub \
    --ingroup docker \
    jupyterhub
    
    cd /home/andrew_gaitskell/
    
    mkdir jupyterhub
    cd jupyterhub
    
    /usr/local/bin/python3.10 -m venv env
    
    /home/jupyterhub/env/bin/python3.10 -m pip install --upgrade pip
    
    pip install -r requirements.txt
    
    jupyter lab build
    
    xxx yum install python3-mysqldb
    xxx yum install libmysqlclient-dev

    xxx apt-get install -y build-essential python3.10 python3-pip python3-dev

   


#RUN usermod -aG shadow jupyterhub

echo "jupyterhub:jupyterhub" | chpasswd

usermod -aG wheel jupyterhub

groupadd jupyterhub

usermod -aG jupyterhub jupyterhub

chown -R jupyterhub:jupyterhub /srv/jupyterhub


#RUN chown jupyterhub .

#USER jupyterhub

#ENV VIRTUAL_ENV=/srv/jupyterhub/env
#RUN python3 -m venv $VIRTUAL_ENV
#ENV PATH="$VIRTUAL_ENV/bin:$PATH"

# Install dependencies:
#COPY requirements.txt .
#RUN pip install -r requirements.txt

#RUN jupyter lab build

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
