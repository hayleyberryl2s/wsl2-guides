# PHP-FPM Configs

Backup the default pool config.

```bash
sudo cp /etc/php/8.2/fpm/pool.d/www.conf /etc/php/8.2/fpm/pool.d/www.conf.old
```

Then place in a pool with sensible defaults for Laravel.

```bash
sudo tee /etc/php/8.2/fpm/pool.d/laravel.conf <<EOF
[laravel]
user = $USER
group = $USER

listen = /run/php/php8.2-fpm.sock

listen.owner = $USER
listen.group = $USER

pm = dynamic
pm.max_children = 4
pm.start_servers = 1
pm.min_spare_servers = 1
pm.max_spare_servers = 4
pm.max_requests = 255

env[APP_ENV] = local
env[APP_DEBUG] = true
env[DB_HOST] = 127.0.0.1
env[DB_PORT] = 3306
env[DB_USER] = dev

php_admin_value[memory_limit] = 512M
php_admin_value[upload_max_filesize] = 64M
php_admin_value[post_max_size] = 64M
php_admin_value[max_execution_time] = 120
EOF
```

Then reload nginx

```bash
sudo nginx -t
sudo systemctl restart nginx
```
