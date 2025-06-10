# Redis memory limit + flush on reboot

## Limit Redis max memory

Redis can have a significant drain on system resources. You can limit it by editing:

```
sudo nano /etc/redis/redis.conf
```

Then set the following config:

```
maxmemory 512mb
```

Then also set the eviction policy:

```
maxmemory-policy allkeys-lru
```

## Flush Redis on boot

This is a systemd unit that will copmpletely flush Redis on system boot.

Install dependencies:

```
sudo apt install redis-cli
```


```
sudo nano /etc/systemd/system/redis-flushall.service
```

```
[Unit]
Description=Runs FLUSHALL on Redis server on boot
After=network.target redis.service
Requires=redis.service

[Service]
Type=oneshot
ExecStart=/usr/bin/redis-cli flushall
RemainAfterExit=true

[Install]
WantedBy=multi-user.target
```

Apply the changes:

```
sudo systemctl daemon-reload
sudo systemctl enable redis-flushall
```

This will flush all Redis DBs on system boot. You can manually trigger the flush anytime via:

```
sudo systemctl restart redis-flushall
```