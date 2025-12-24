# Setup Mailpit in WSL2

[Mailpit](https://github.com/axllent/mailpit) is a local SMTP server with a web interface for viewing sent emails.


## Install Mailpit

*The most up-to-date instructions can be found on the [Mailpit GitHub repository](https://github.com/axllent/mailpit/?tab=readme-ov-file#install-via-script-linux--mac).*

Run the following command in your WSL2 terminal:

```shell
sudo sh < <(curl -sL https://raw.githubusercontent.com/axllent/mailpit/develop/install.sh)```

## Create the systemd service

Create a file at `/etc/systemd/system/mailpit.service` with the following:

```sh
sudo nano /etc/systemd/system/mailpit.service
```


```
[Unit]
Description=Mailpit server
After=network.target

[Service]
ExecStart=/usr/local/bin/mailpit
SyslogIdentifier=mailpit

[Install]
WantedBy=multi-user.target
Alias=mailhog.service
```

Then enable the unit

```
sudo systemctl daemon-reload
sudo systemctl enable mailpit
sudo systemctl start mailpit
```

Mailpit will start automatically on boot. If you want to disable it, run:

```
sudo systemctl disable Mailpit
```

Then run the service manually when needed with:

```
sudo systemctl start Mailpit
```

You can view the Mailpit web interface at [http://localhost:8025/](http://localhost:8025/)

## Usage in applications

To send mail to Mailpit, set the following .env values:

```
MAIL_MAILER=smtp
MAIL_HOST=127.0.0.1
MAIL_PORT=1025
MAIL_USERNAME=null
MAIL_PASSWORD=null
MAIL_ENCRYPTION=null
```