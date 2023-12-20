# docker-nginx

## Example configuration 

```
server {
    listen 443 ssl;
    server_name www.hello.local;
    charset utf-8;

    ssl_certificate /etc/certs/self-signed.crt;
    ssl_certificate_key /nginx/certs/self-signed.key;

    access_log /var/log/nginx/hello.local-access.log;
    error_log /var/log/nginx/hello.local-error.log error;

    root /var/www/hello.local/;
    index index.php index.html index.htm;

    error_page   500 502 503 504  /50x.html;

    location = /50x.html { root   /usr/share/nginx/html; }
    location = /favicon.ico { access_log off; log_not_found off; }
    location = /robots.txt { access_log off; log_not_found off; }
    location = /sitemap.xml { access_log off; log_not_found off; }

    location = /system/storage/download/(.*) {
        rewrite ^(.*)$ /index.php?route=error/not_found break; 
    }

    location /admin/ {
        rewrite ^/admin/(.+)$ /index.php$1 last;
    }

    location / {
        try_files $uri @opencart;
    }

    location @opencart {
        rewrite ^/(.+)$ /index.php?_route_=$1 last;
    }

    location ~ \.php$ {
        # try_files $uri $uri/ /index.php$is_args$args =404;
        fastcgi_intercept_errors on;
        fastcgi_index  index.php;
        include        fastcgi_params;
        fastcgi_connect_timeout 300;
        fastcgi_send_timeout 300;
        fastcgi_read_timeout 300;
        fastcgi_buffer_size 32k;
        fastcgi_buffers 8 16k;
        fastcgi_busy_buffers_size 32k;
        fastcgi_temp_file_write_size 32k;
        fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
        fastcgi_split_path_info ^(.+\.php)(/.+)$;
        fastcgi_pass php73:9000;
    }

    location ~* \.(js|css|png|jpg|jpeg|gif|ico|xml|txt)$ {
        expires max;
        access_log off;
        log_not_found off;
    }

    location ~* \.(engine|inc|info|ini|install|log|make|module|profile|test|po|sh|.*sql|theme|tpl(\.php)?|xtmpl)$|^(\..*|Entries.*|Repository|Root|Tag|Template)$|\.php_ {
        deny all;
    }

    location ~ ~$ {
        access_log off;
        log_not_found off;
        deny all;
    }

    location ~* /(?:cache|logs|image|download)/.*\.php$ {
        deny all;
    }

    location ~* \.(eot|otf|ttf|woff)$ {
        access_log off;
        add_header Access-Control-Allow-Origin *;
    }

    location ~ /\.ht {
        deny all;
    }

    location ~ /(LICENSE\.txt|composer\.lock|composer\.json|nginx\.conf|web\.config|htaccess\.txt|\.htaccess|config.php) {
        return 404;
        deny all;
    }

    location ~* /(catalog|ie_pro|image|system)/.*\.(txt|xml|md|html|yaml|yml|php|pl|py|cgi|twig|sh|bat)$ {
        return 404;
        deny all;
    }
}
```


## Config logrotate

```
# Create file: /etc/logrotate.d/nginx
/home/ubuntu/docker-nginx/logs/nginx/*.log {
    dateext
    daily
    rotate 31
    nocreate
    missingok
    notifempty
    nocompress
    postrotate
        /usr/bin/docker exec con_nx_nginx /bin/bash -c '/usr/sbin/nginx -s reopen > /dev/null 2>/dev/null'
    endscript
}
```

- Logrotate uses crontab to work. It's scheduled work, not a daemon, so no need to reload its configuration.
- When the crontab executes logrotate, it will use your new config file automatically.
- If you need to test your config you can also execute logrotate on your own with the command:

    ```
    ## Syntax:
    # logrotate /etc/logrotate.d/your-logrotate-config

    ## Example:
    logrotate /etc/logrotate.d/nginx
    ```

- If you want to have a debug output use argument -d

    ```
    ## Syntax:
    # logrotate -d /etc/logrotate.d/your-logrotate-config

     ## Example:
    logrotate -d /etc/logrotate.d/nginx
    ```

## Create self certificates

```
openssl req -x509 -nodes -days 36500 -newkey rsa:2048 -keyout self-signed.key -out self-signed.crt
```