# How to add local Valet certificates to trusted store

If you get SSL issues from curl and/or Guzzle when trying to request HTTPS URLs from your Valet projects, you can solve this by adding the certificates to the trusted store:

```shell
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

COUNT=0

for PROJECT in "${PROJECTS[@]}"; do
    CERT=/etc/ssl/certs/"$PROJECT".test.crt
    TARGET=/usr/local/share/ca-certificates/"$PROJECT".test.crt
    
    if [ ! -f "$CERT" ]; then
        echo -e "[\e[91mERROR\e[0m] Certificate for $PROJECT not found at $CERT, skipping..."
        continue
    fi

    if [ -f $TARGET ]; then
        echo -e "[\e[33mSKIPPED\e[0m] Certificate for $PROJECT already exists in trusted store, skipping..."
        continue
    fi

    echo "[\e[32mOK\e[0m] Adding $PROJECT certificate to trusted store..."
    sudo cp "$CERT" /usr/local/share/ca-certificates

    COUNT=$((COUNT + 1))
done

sudo update-ca-certificates
```
