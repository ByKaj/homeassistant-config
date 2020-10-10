# Home Assistant Configuration
![Maintenance](https://img.shields.io/maintenance/yes/2020?style=for-the-badge)
![GitHub last commit](https://img.shields.io/github/last-commit/ByKaj/homeassistant-config?style=for-the-badge)
[![GitHub](https://img.shields.io/github/license/ByKaj/homeassistant-config?style=for-the-badge)](/LICENSE)

This is my configuration for Home Assistant (www.home-assistant.io).

## Installation
Steps and information tailored to Home Assistant installed on HassOS.

### Prerequisites
1. Installed Supervisor add-ons:
	* [Git Pull](https://github.com/home-assistant/hassio-addons/tree/master/git_pull)
	* [SSH & Web Terminal](https://github.com/hassio-addons/addon-ssh)


### SSH & Web Terminal configuration
The RSA key pair used below is created on your local PC. The private key is added to your local `ssh-agent`, the public key is used on the Home Assistant server.

```yaml
ssh:
  username: <username>
  password: ''
  authorized_keys: ['ssh-rsa    <key>   <comment>']
  sftp: false
  compatibility_mode: false
  allow_agent_forwarding: false
  allow_remote_port_forwarding: false
  allow_tcp_forwarding: false
zsh: true
share_sessions: false
packages: []
init_commands: []
```

You can also use an username and password _(less secure!)_ instead of a SSH key. Change the configuration as follows:
```yaml
ssh:
  username: <username>
  password: <password>
  authorized_keys: []
...
```

### Backup configuration to GitHub
1. Go to https://github.com/new and create a new repository (eg. `homeassistant-config`). Initialize with `readme: no` and `.gitignore: none`.
3. Navigate to your `config` directory. For HassOS it should be in `/root/home/config`.
5. Run `wget https://raw.githubusercontent.com/<username>/homeassistant-config/main/.gitignore` to get the `.gitignore` file from your repo (replace the link to match your repository). You can add things to your `.gitignore` file that you do not want to be uploaded.
6. Next, we need to add SSH keys to your GitHub account.
    * Run `ssh-keygen -t rsa -b 4096 -C "<email address>"`. If you want to enter a passphrase, that's up to you. If you do, you'll have to enter that passphrase any time you want to update your changes to github. If you do not want a passphrase, leave it blank and just hit `Enter`.
    * Save the key in the default location (press `Enter` when it prompts for location).
    * When you're finished, run `ls -al ~/.ssh` to confirm that you have both `id_rsa` and `id_rsa.pub` files.
    * Go to https://github.com/settings/keys and click `New SSH key` button at top right. Title: `Home Assistant Server` (or whatever you want, really...it's just for you to know which key it is).
    * Run `cat id_rsa.pub` in the SSH session and copy/paste the output to that GitHub page.
    * Then click `Add SSH key` button.
7. Go back to your repo page on GitHub. It'll be something like `https://github.com/<username>/homeassistant-config`. Click the green `Clone or download` button, and then click `Use SSH`.
8. You should see something like this in the textbox: `git@github.com:<username>/homeassistant-config.git`. Copy that to your clipboard.
9. Now you are ready to upload the files to GitHub.
    * Navigate to your configuration files `cd ~/config` and:
		```bash
		git init
		git add .
		git commit -m 'Initial commit'
		```
	* If you get an error about `*** Please tell me who you are.`, run `git config --global user.email "your@email.here"` and `git config --global user.name "Your Name"`
    * After that commit succeeds, run: `git remote add origin git@github.com:<username>/homeassistant-config.git`.
    * Just to confirm everything is right, run `git remote -v` and you should see:
      ```bash
      origin  git@github.com:arsaboo/homeassistant-config.git (fetch)
      origin  git@github.com:arsaboo/homeassistant-config.git (push)
      ```
    * Finally, run `git push origin main`.

10. For subsequent updates:
    ```bash
    git add .
    git commit -m 'Your commit message'
    git push origin main
	```
11. To manual restore from your GitHub repository:
    ```bash
    git clone git@github.com:<username>/homeassistant-config.git ~/config
    ```

Source used: [HASS Cheatsheet](https://github.com/arsaboo/homeassistant-config/blob/master/HASS%20Cheatsheet.md) by @arsaboo


### Git Pull configuration
Configuration with auto restart of Home Assistant and auto pulling your GitHub repository for changes enabled. 

> **WARNING:** Only activate this add-on when your repository holds your initial commit. Otherwise it'll wipe your configuration and you probably don't want that... 

The deployment key is the private key in `id_rsa` created in step 6 of the previous backup procedure. Take note of the key format.

```yaml
deployment_key:
  - '-----BEGIN OPENSSH PRIVATE KEY-----'
  - b3BlbnNzaC1rZXktdjEAAAAABG5vbmUAAAAEbm9uZQAAAAAAAAABAAACFwAAAAdzc2gtcn
  - NhAAAAAwEAAQAAAgEAs+gC1QehlpEymiGPSnhVMwZZJi2nljLlu644G2h9tE7FVn2TG0XR
  - ...
  - KvxjOuF7exeHN2WaW2zkUE9oP6aeaCfSk6XxxAq9YQVQD6BjY83zQPNaB8mk/jmkHH1C6V
  - x/ph2cL0aoo1AAAAE2dpdGh1YkBrYWp2aXNzZXIubmwBAgMEBQYH
  - '-----END OPENSSH PRIVATE KEY-----'
deployment_key_protocol: rsa
deployment_user: ''
deployment_password: ''
git_branch: main
git_command: pull
git_remote: origin
git_prune: false
repository: 'git@github.com:<username>/homeassistant-config.git'
auto_restart: true
restart_ignore:
  - ui-lovelace.yaml
  - .gitignore
  - appdaemon/
  - dashboards/
  - docs/
repeat:
  active: true
  interval: 300
```


## License
[MIT](LICENSE)
