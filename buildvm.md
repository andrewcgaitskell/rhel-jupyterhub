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

# remove default conf files

    rm /etc/nginx/sites-enabled/default
    rm /etc/nginx/sites-available/default

# add default to conf.d folder

    nano /etc/nginx/conf.d/default.conf
    
    #
    # The default server
    #
    server {
        listen       80;
        server_name  _;

        #access_log  logs/host.access.log  main;

        location / {
            root   /var/www/html;
            index  index.html;
        }

        error_page  404              /404.html;
        location = /404.html {
            root   /var/www/html;
        }

        # redirect server error pages to the static page /50x.html
        #
        }
    

# check nginx config and reload

    sudo nginx -t
    sudo nginx -s reload


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
