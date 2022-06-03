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
       
    #yum install sqlite-devel
    
    npm install -g configurable-http-proxy

    mkdir /dmtools
    mkdir /dmtools/home

    mkdir /dmtools/notebooks

    adduser \
    --home /dmtools/home \
    pauser

    echo "pauser:pauser" | chpasswd

    usermod -aG wheel pauser

    groupadd dmtools

    usermod -aG dmtools pauser
    chown [OPTIONS] USER[:GROUP] FILE(s)

    chown -R pauser:dmtools /dmtools
   
    cd /dmtools
    
    mkdir jupyterhub
    cd jupyterhub
    
    /usr/local/bin/python3.10 -m venv env
    
    /dmtools/jupyterhub/env/bin/python3.10 -m pip install --upgrade pip
    
    source env/bin/activate
    
    pip install -r requirements.txt
    
    jupyter lab build
    
    xxx yum install python3-mysqldb
    xxx yum install libmysqlclient-dev

    xxx apt-get install -y build-essential python3.10 python3-pip python3-dev

   



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

