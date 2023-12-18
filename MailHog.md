# Setup MailHog in WSL2

First install Go (golang)

```
sudo apt install golang
```

Install MailHog

```
GOBIN=/usr/local/bin sudo go install github.com/mailhog/MailHog@latest
```

Create a file at `/etc/systemd/system/mailhog.service` with the following:

```
[Unit]
Description=MailHog
After=network.target
[Service]
ExecStart=/usr/bin/env /usr/local/bin/MailHog > /var/log/maillog.stdout 2>/var/log/maillog.stderr
[Install]
WantedBy=multi-user.target
```

Then enable the unit

```
sudo systemctl daemon-reload
sudo systemctl enable mailhog
sudo systemctl start mailhog
```

This makes MailHog start on boot.

You can view the MailHog UI at: http://localhost:8025/

## Usage in applications

To send mail to MailHog, set the following .env values:

```
MAIL_MAILER=smtp
MAIL_HOST=127.0.0.1
MAIL_PORT=1025
MAIL_USERNAME=null
MAIL_PASSWORD=null
MAIL_ENCRYPTION=null
```