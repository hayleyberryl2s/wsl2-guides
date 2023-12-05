# Setup XDebug in WSL2

## On Windows

Install the "PHP Debug" extension. (Link: https://marketplace.visualstudio.com/items?itemName=xdebug.php-debug)

```
code --install-extension xdebug.php-debug
```

In the "Run and Debug" tab in VS Code (Ctrl+Shift+D), click the cog in the top-left to edit the project's launch.conf.

Add a new block to the `configurations` array. Update the `pathMappings` to match the project's location on your WSL2 guest.

```
"configurations": [
    {
        "name": "Listen for xdebug (9000)",
        "type": "php",
        "request": "launch",
        "port": 9000,
        "pathMappings": {
            "/home/hayley/Projects/quik-gift": "${workspaceRoot}" 
        },
    },
    ...
```

## In WSL2

Install xdebug

```
apt install php8.1-xdebug
```

Create the config file

```
nano /etc/php/8.1/fpm/conf.d/30-xdebug-wsl.ini
```

With the following:

```
xdebug.mode=debug
xdebug.start_with_request=trigger
xdebug.discover_client_host=1
xdebug.client_port=9000
```
Restart php-fpm

```
systemctl restart php8.1-fpm
```

## Usage

To make triggering xdebug easier you can install an extension for your browser:

- Firefox: https://addons.mozilla.org/en-US/firefox/addon/xdebug-helper-for-firefox/
- Edge: https://microsoftedge.microsoft.com/addons/detail/xdebug-helper/ggnngifabofaddiejjeagbaebkejomen
- Chrome: https://chrome.google.com/webstore/detail/xdebug-helper/eadndfjplgieldjbigjakmdgkmoaaaoc

Open the "Run and Debug" tab in VS Code (Ctrl+Shift+D), select "Listen for xdebug (9000)" then click the run button.

Toggle the "Debug" option on the xdebug extension in your browser (or alternatively append ?XDEBUG_SESSION_START=1 to the URL query string), load a page and any breakpoints you set should get hit.

When first setting up a project, I typically just set a breakpoint on the first line of `public/index.php` as an easy validation test.

### Network/firewall issues

You may need to modify your firewall rules to allow WSL2 to access the host on port 9000. If you are having issues, try adding a rule for 9000. In an admin PowerShell:

```
New-NetFirewallRule -DisplayName "WSL Testing" -InterfaceAlias "vEthernet (WSL)" -Direction Inbound -Protocol TCP -LocalPort 9000 -Action Allow
```
