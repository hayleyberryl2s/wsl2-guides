# Nginx configs

Copy [generate-nginx-config](generate-nginx-config) to /usr/local/bin/generate-nginx-config

```bash
sudo cp generate-nginx-config /usr/local/bin/generate-nginx-config
sudo chmod +x /usr/local/bin/generate-nginx-config
```

## Generate Nginx configs for all projects

This generates certifgicates and Nginx configs for all projects in the `PROJECTS` array.

```bash
PROJECTS=(
    quik-gift
    link-module
    simfoni
    inspireddeck-admin
    chooza-gift-app
    inspireddeck-support
    inspireddeck-store
    inspireddeck-portal
    simfoni-retail
    inspireddeck-server
    inspireddeck-public
)

for project in "${PROJECTS[@]}"; do
  DOMAIN="${project}.test"
  sudo openssl req -x509 -nodes -days 3650 -newkey rsa:2048 \
      -keyout "/etc/ssl/private/${DOMAIN}.key" \
      -out "/etc/ssl/certs/${DOMAIN}.crt" \
      -subj "/C=GB/ST=Merseyside/L=Liverpool/O=Love2Shop/CN=${DOMAIN}" \
      -addext "subjectAltName=DNS:${DOMAIN}"

  /usr/local/bin/generate-nginx-config $project | sudo tee /etc/nginx/sites-available/$DOMAIN.conf

  if [[ ! -L /etc/nginx/sites-enabled/$DOMAIN.conf ]]; then
    sudo ln -s /etc/nginx/sites-available/$DOMAIN.conf /etc/nginx/sites-enabled/$DOMAIN.conf
  fi

  sudo nginx -t
done

sudo systemctl restart nginx
```
