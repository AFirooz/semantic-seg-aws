> Under dev

## Notes

- The region you create your notebook instance will be insulated from other regions. In other words, you can only access (and find) the notebook instance from that region.
- Make sure that your files are in this path `/home/ec2-user/SageMaker/your_notebook_files`.
- To be able to open the notebook locally on VSCode, you need to setup a vscode tunnel in the AWS Notebook instance EACH TIME you start the notebook.
- In addition, to be able to pull / push from Github, you need to config an ssh key EACH TIME we start the notebook instance. A better way to deal with this is by creating a Lifecycle script that automatically run every time the notebook instance starts.

## My basic AWS LifeCycle script

> Make sure to change the variables to your own.

```bash

GIT_USER=<value>
GIT_EMAIL=<value>
GITHUB_SSH_KEY_NAME=<value>
GITHUB_SSH_KEY_PRIVATE=<value>

# ---

export HOME=/home/ec2-user

### config bashrc for new terminals
echo 'eval "$(ssh-agent -s)"' >> .bashrc
echo 'ssh-add -k ~/.ssh/$GITHUB_SSH_KEY_NAME' >> .bashrc
echo 'alias c="clear"' >> .bashrc
echo 'alias e="exit"' >> .bashrc
echo 'alias p="pwd -L"' >> .bashrc
echo 'alias ll="ls -XlAh --group-directories-first 2> /dev/zero || ls -XlAh"' >> .bashrc

### setting up github
echo -e "$GITHUB_SSH_KEY_PRIVATE" > "~/.ssh/$GITHUB_SSH_KEY_NAME"
sudo chmod 600 "~/.ssh/$GITHUB_SSH_KEY_NAME"
eval "$(ssh-agent -s)"

ssh-add -k "~/.ssh/$GITHUB_SSH_KEY_NAME"
git config --global user.name "$GIT_USER"
git config --global user.email "$GIT_EMAIL"
git config --global credential.helper cache
git config --global credential.helper "cache --timeout=86400"

### Install and enable Git LFS
curl -s https://packagecloud.io/install/repositories/github/git-lfs/script.rpm.sh | sudo bash
sudo yum install git-lfs -y 
git lfs install

### setting up vscode tunnel
curl -Lk 'https://code.visualstudio.com/sha/download?build=stable&os=cli-alpine-x64' --output ~/vscode_cli.tar.gz
tar -xf ~/vscode_cli.tar.gz
# Start VS Code tunnel in the background and save logs for the login device code
nohup ~/code tunnel --accept-server-license-terms --no-sleep --name aws_notebook > ~/vscode_tunnel.log 2>&1 &
```

This script will run the VSCode tunnel in the background. You will have to open a terminal of your Notebook Instance (each time you start it) and run `cat ~/vscode_tunnel.log` to log into your GitHub account (where the tunnel will be set). Use `~/code tunnel status` to see the status of the tunnel. If the tunnel is not configured as it should, run `~/code tunnel` to start it manually.
