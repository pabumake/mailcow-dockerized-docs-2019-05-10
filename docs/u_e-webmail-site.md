To create a subdomain `webmail.example.org` and redirect it to SOGo, you need to create a new Nginx site. Take care of "CHANGE_TO_MAILCOW_HOSTNAME"!

**nano data/conf/nginx/webmail.conf**
``` hl_lines="17"
server {
  ssl_certificate /etc/ssl/mail/cert.pem;
  ssl_certificate_key /etc/ssl/mail/key.pem;
  index index.php index.html;
  client_max_body_size 0;

  include /etc/nginx/conf.d/listen_plain.active;
  include /etc/nginx/conf.d/listen_ssl.active;
  server_name webmail.example.org;

  location ^~ /.well-known/acme-challenge/ {
    allow all;
    default_type "text/plain";
  }

  location / {
    return 301 https://CHANGE_TO_MAILCOW_HOSTNAME/SOGo;
  }
}
```

Save and restart Nginx: `docker-compose restart nginx-mailcow`.

Now open `mailcow.conf` and find `ADDITIONAL_SAN`.
Add `webmail.example.org` to this array, don't use quotes!

```
ADDITIONAL_SAN=webmail.example.org
```

Run `docker-compose up -d`. See "acme-mailcow" and "nginx-mailcow" logs if anything fails.
