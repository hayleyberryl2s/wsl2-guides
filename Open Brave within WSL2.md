# Open Brave within WSL2

```shell
sudo apt install xdg-utils
```

```shell
sudo nano /usr/local/bin/brave-wsl2
```

Paste in the following:

```
#! /bin/bash

/mnt/c/Program\ Files/BraveSoftware/Brave-Browser/Application/brave.exe "$@"
```

```shell
sudo mkdir -p /usr/local/share/applications/
sudo nano /usr/local/share/applications/brave-wsl2.desktop
```

Paste in the following:

```ini
[Desktop Entry]
Name=Brave WSL
Exec=brave-wsl2 %u
Type=Application
Terminal=false
Categories=Network;WebBrowser;WSL2
```

Register the handler:

```shell
xdg-mime default brave-wsl2.desktop x-scheme-handler/http\nxdg-mime default brave-wsl2.desktop x-scheme-handler/https
```

Test:

```shell
xdg-open https://example.com/
```