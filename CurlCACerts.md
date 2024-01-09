# How to add local Valet certificates to trusted store

If you get SSL issues from curl and/or Guzzle when trying to request HTTPS URLs from your Valet projects, you can solve this by adding the certificates to the trusted store:

```
sudo cp ~/.valet/Certificates/*.crt /usr/local/share/ca-certificates
sudo update-ca-certificates
```
