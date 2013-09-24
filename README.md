<img src="http://ebmedia.eventbrite.com/s3-s3/eventlogos/6576803/1238025969-14.jpg" alt="Logo nginx" width="120" style="margin:2px 43px" align="right">

nginx-configuration-recipes 
===========================

<a href="http://nginx.org/">
<img src="http://upload.wikimedia.org/wikipedia/de/thumb/2/2c/Nginx_Logo.svg/200px-Nginx_Logo.svg.png" alt="Logo nginx" align="right"></a>

With this [nginx](http://nginx.org/) configuration recipes you are able to setup a new virtual host in less than a minute. Take a choice of prepered configuration files for the cms or framework of your choice - include and you are done. 

## Example

### using nginx-configuration-recipes

```bash

server {
		server_name		.typo3.org;
		rewrite ^(.*) 	http://typo3.org$1 permanent;
}

server {
		listen			80;

		set $basename 	'typo3org';
		set $baseuser 	'dev';

		access_log		/var/log/nginx/dev/typo3.access.log;
		error_log		/var/log/nginx/dev/typo3.error.log;

		server_name		typo3.org;

		include			conf/base.conf;
		include			conf/php/fastcgi.conf;
		include			conf/php/cms/typo3.conf;
}
```

### traditional way
```bash

server {
    listen   *:80;
    root /var/www/typo3.org/htdocs/;
    index index.html index.htm index.php;
    server_name typo3.org;

    access_log /var/log/nginx/typo3.access.log;
    error_log  /var/log/nginx/typo3.error.log;

	# Rewrite to none-www
    if ($http_host != "typo3.org") {
                 rewrite ^ http://typo3.org$request_uri permanent;
       }

    index index.php index.html;
    
    # Static files
    location = /favicon.ico {
        log_not_found off;
        access_log off;
        expires max;
    }
    location = /robots.txt {
        allow all;
        log_not_found off;
        access_log off;
    }

    # Deny all attempts to access hidden files such as .htaccess, .htpasswd, .DS_Store (Mac).
    location ~ /\. {
        deny all;
        access_log off;
        log_not_found off;
    }
    location ~*  \.(jpg|jpeg|png|gif|css|js|ico)$ {
        expires max;
        log_not_found off;
    }

    location ~* \.(cur|ico|gif|png|jpe?g|css|js|swf|woff)((\?\d\d\d\d\d\d\d\d\d\d)|(\?s=\d\d\d\d\d\d\d\d\d\d))$ {
        expires max;
        log_not_found off;
    }
    location ~* \.(cur|ico|gif|png|jpe?g|css|js|swf|woff)(\?v\d\d?\.\d\d?\.\d\d?)$ {
        expires max;
        log_not_found off;
    }
    location ~* ^(/typo3/sysext|/typo3conf/ext).*\.(cur|ico|gif|png|jpe?g|css|js|swf|woff) {
        expires max;
        log_not_found off;
    }
    location = /clear.gif {
        empty_gif;
        expires max;
    }
    location ^~ /typo3/gfx {
        expires max;
    }
    location ^~ /typo3temp/compressor {
        expires max;
    }
    location ~* \.(sql|htaccess|htpasswd|tpl|html5|xhtml) {
        deny all;
    }
    location / {
        if ($query_string ~ ".+") {
            return 405;
        }
        # pass requests from logged-in users to PHP
        if ($http_cookie ~ 'nc_staticfilecache|be_typo_user' ) {
            return 405;
        } # pass POST requests to PHP
        if ($request_method !~ ^(GET|HEAD)$ ) {
            return 405;
        }
        if ($http_pragma = 'no-cache') {
            return 405;
        }
        if ($http_cache_control = 'no-cache') {
            return 405;
        }
        error_page 405 = @nocache;
        # serve requested content from the cache if available, otherwise pass the request to PHP
        try_files /typo3temp/tx_ncstaticfilecache/$host${request_uri}index.html @nocache;
    }
    location @nocache {
        try_files $uri $uri/ /index.php?$args;
    }
    location ^~ /typo3temp/tx_ncstaticfilecache {
        expires 43200;
        charset utf-8;
    }
    location ~ \.php$ {
        try_files $uri =404;
        include /etc/nginx/fastcgi_params;
        fastcgi_pass 127.0.0.1:9000;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        fastcgi_index index.php;
    }
}
```

As you can see, the way of incorporating of prepared configuration files is easier, nicer, cleaner, shorter and simply faster then every time to write anything new.

## Contains configurations for ...

1. **Basics**
   - Basic configuration  
   - Rewrite www to none-www
   - Rewrite none-www to www

- **PHP**
  - _CMS_
    - DokuWiki
    - Drupal
    - TYPO3 CMS
    - TYPO3 FLOW
    - Wordpress
  - _Framework_
    - Symfony1
    - Symfony2
    - Zend Framework 1
    - Zend Framework 2


## Credentials
Read more about on related links:

- [http://nginx.org/](http://nginx.org/)‎
- [http://wiki.nginx.org/WordPress](http://wiki.nginx.org/WordPress)‎
- [http://wiki.nginx.org/Dokuwiki](http://wiki.nginx.org/Dokuwiki)
- [http://wiki.nginx.org/Drupal](http://wiki.nginx.org/Drupal)
- [http://wiki.nginx.org/Typo](http://wiki.nginx.org/Typo3)
- [http://wiki.nginx.org/Symfony](http://wiki.nginx.org/Symfony)

### Thank you
 - .. to Igor Sysoev, the founder of nginx.
 - .. to the nginx community were agrabed the scripts.