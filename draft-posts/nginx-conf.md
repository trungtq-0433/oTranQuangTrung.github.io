```r
##
upstream puma {
  server unix:/usr/local/rails_apps/indahash-nttd/current/tmp/sockets/puma.sock;
}


# Default server configuration
#
server {
  listen 80;
  server_name localhost default_server;
  root /usr/local/rails_apps/indahash-nttd/current/public;
  client_max_body_size 4G;
  auth_basic            "Cue-prod";
  auth_basic_user_file  "/etc/nginx/.htpasswd";
        access_log /var/log/nginx/production_access.log;
        error_log /var/log/nginx/production_error.log;
        real_ip_header X-Forwarded-For;
        set_real_ip_from 0.0.0.0/0;
  location / {
  try_files $uri @webapp;
  }
  location @webapp {
    proxy_redirect     off;
    proxy_set_header   Host             $host;
    proxy_set_header   X-Real-IP        $remote_addr;
    proxy_set_header   X-Forwarded-For  $proxy_add_x_forwarded_for;
    proxy_read_timeout 10000;
    proxy_pass http://puma;
  }
location /admin {
    proxy_redirect     off;
    proxy_set_header   Host             $host;
#    proxy_set_header   X-Real-IP        $remote_addr;
#    proxy_set_header   X-Forwarded-For  $proxy_add_x_forwarded_for;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
#       real_ip_header X-Forwarded-For;
#       set_real_ip_from 0.0.0.0/0;
    proxy_read_timeout 10000;
    proxy_pass http://puma/admin;
  }

 # Delete after public site
  location /healthcheck {
    auth_basic "off";
    proxy_redirect     off;
    proxy_set_header   Host             $host;
    proxy_set_header   X-Real-IP        $remote_addr;
    proxy_set_header   X-Forwarded-For  $proxy_add_x_forwarded_for;
    proxy_read_timeout 10000;
    proxy_pass http://puma/healthcheck;
  }
}


```
