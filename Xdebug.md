# Setup Xdebug in WSL2

## On Windows

Install the "PHP Debug" extension. (Link: https://marketplace.visualstudio.com/items?itemName=xdebug.php-debug)

```
code --install-extension xdebug.php-debug
```

In the "Run and Debug" tab in VS Code (Ctrl+Shift+D), click the cog in the top-left to edit the project's launch.json.

Edit the launch.json to match the following. (Or just copy the launch.json in the root of this repo)

Update the `pathMappings` to match the project's location on your WSL2 guest.

```
{
    // Use IntelliSense to learn about possible attributes.
    // Hover to view descriptions of existing attributes.
    // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations": [
        {
            "name": "Listen for xdebug (9000)",
            "type": "php",
            "request": "launch",
            "port": 9000,
            "pathMappings": {
                "${userHome}/Projects/${workspaceFolderBasename}": "${workspaceRoot}" 
            },
        },
        {
            "name": "phpunit",
            "type": "php",
            "request": "launch",
            "program": "${workspaceRoot}/vendor/bin/phpunit",
            "port": 9000,
            "cwd": "${workspaceRoot}",
            "pathMappings": {
                "${userHome}/Projects/${workspaceFolderBasename}": "${workspaceRoot}" 
            }
        },
        {
            "name": "phpunit --stop-on-failure",
            "type": "php",
            "request": "launch",
            "program": "${workspaceRoot}/vendor/bin/phpunit",
            "args": [
                "--stop-on-failure"
            ],
            "port": 9000,
            "cwd": "${workspaceRoot}",
            "pathMappings": {
                "${userHome}/Projects/${workspaceFolderBasename}": "${workspaceRoot}" 
            }
        },
        {
            "name": "phpunit (current file)",
            "type": "php",
            "request": "launch",
            "program": "${workspaceRoot}/vendor/bin/phpunit",
            "args": [
                "${relativeFile}"
            ],
            "cwd": "${workspaceRoot}",
            "port": 9000,
            "pathMappings": {
                "${userHome}/Projects/${workspaceFolderBasename}": "${workspaceRoot}" 
            }
        },
        {
            "name": "phpunit (current directory)",
            "type": "php",
            "request": "launch",
            "program": "${workspaceRoot}/vendor/bin/phpunit",
            "args": [
                "${relativeFileDirname}"
            ],
            "cwd": "${workspaceRoot}",
            "port": 9000,
            "pathMappings": {
                "${userHome}/Projects/${workspaceFolderBasename}": "${workspaceRoot}" 
            }
        },
        {
            "name": "Artisan",
            "type": "php",
            "request": "launch",
            "program": "${workspaceRoot}/artisan",
            "args": [
                "${input:artisanCommand}"
            ],
            "cwd": "${workspaceRoot}",
            "port": 9000,
            "pathMappings": {
                "${userHome}/Projects/${workspaceFolderBasename}": "${workspaceRoot}" 
            }
        }
    ],
    "inputs": [
        {
            "id": "artisanCommand",
            "type": "promptString",
            "description": "The artisan command to run"
        }
    ]
}
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
xdebug.start_with_request=yes
xdebug.discover_client_host=1
xdebug.client_port=9000
```

Apply the same config to PHP CLI

```
cp /etc/php/8.1/fpm/conf.d/30-xdebug-wsl.ini /etc/php/8.1/cli/conf.d/30-xdebug-wsl.ini
```

Restart php-fpm

```
systemctl restart php8.1-fpm
```

## Usage

### Website debugging

To make triggering xdebug easier you can install an extension for your browser:

- Firefox: https://addons.mozilla.org/en-US/firefox/addon/xdebug-helper-for-firefox/
- Edge: https://microsoftedge.microsoft.com/addons/detail/xdebug-helper/ggnngifabofaddiejjeagbaebkejomen
- Chrome: https://chrome.google.com/webstore/detail/xdebug-helper/eadndfjplgieldjbigjakmdgkmoaaaoc

Open the "Run and Debug" tab in VS Code (Ctrl+Shift+D), select "Listen for xdebug (9000)" then click the run button.

Toggle the "Debug" option on the xdebug extension in your browser (or alternatively append ?XDEBUG_SESSION_START=1 to the URL query string), load a page and any breakpoints you set should get hit.

When first setting up a project, I typically just set a breakpoint on the first line of `public/index.php` as an easy validation test.

### PHPUnit debugging

The launch.json defines 3 configurations available:

#### phpunit

This will run all tests in the project. Any breakpoints anywhere in the codebase will be triggered.


#### phpunit (current file)

This will only run the test cases in the current open file.

#### phpunit (current directory)

This will only run the test cases in the directory of the currently open file.

### Artisan debugging

The "Artisan" launch configuration will prompt you for a command (and any optional arguments) to run, and it will run it under the debugger.

## Network/firewall issues

You may need to modify your firewall rules to allow WSL2 to access the host on port 9000. If you are having issues, try adding a rule for 9000. In an admin PowerShell:

```
New-NetFirewallRule -DisplayName "WSL Testing" -InterfaceAlias "vEthernet (WSL)" -Direction Inbound -Protocol TCP -LocalPort 9000 -Action Allow
```

## Increase nginx timeout

If you step through code line-by-line, you will get nginx timeout errors. The PHP code will still be running, but nginx will not be able to serve the response.

Apply a timeout of 600 seconds (10 minutes) via:

```
echo "fastcgi_read_timeout 600;" | sudo sh -c "cat > /etc/nginx/conf.d/l2s-fastcgi-timeout.conf"
```

Then restart nginx:

```
valet restart
```