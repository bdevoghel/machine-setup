# Machine Setup

When starting to work on a new (dev) machine, this repo gets you started in no time in your beloved and familiar environment.

## Requirements

Commands are described as for RHEL distrubutions, but are supposed to be valid everywhere with minimal changes; eg. `yum` -> `apt-get` for Ubuntu.

This is what you need : 
- a running Linux machine
- credentials to log into this machine

## Initial setup

These are the very basics.

- connect to the machine :

```bash
# on local machine
ssh USER@ADDRESS
```

- configure `ssh` : generate a public/private key pair (choose the default answers for all questions) :
```bash
# on new machine
ssh-keygen -m PEM -t rsa -b 4096
cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
chmod 600 ~/.ssh/authorized_keys
```

- ease access from local machine : donwload the private key and set up a bookmark in the `ssh` targets :
```bash
# on local machine
scp USER@ADDRESS:~/.ssh/id_rsa C:\Users\WINDOWSUSER\.ssh\ADRESS\
```
```bash
# append following lines in C:\Users\WINDOWSUSER\.ssh\config
Host MACHINE
    HostName ADDRESS
    User USER
    IdentityFile C:\Users\WINDOWSUSER\.ssh\ADRESS\id_rsa
    AddKeysToAgent yes
```
You should now be able to connect via `ssh` without typing your passwords.

- install useful tools :
```bash
sudo yum install git-core htop
```

- configure git :
```bash
# append following lines in ~/.gitconfig
[user]
    email = USER_EMAIL
    name = NAME LASTNAME
[credential]
    username = USER_USERNAME
    helper = cache

# add following lines to sign commits with ssh keys
[gpg]
    format = ssh
[commit]
    gpgsign = true
[gpg "ssh"]
    defaultKeyCommand = ssh-add -L
```
```bash
# append following lines in ~/.ssh/config
Host GIT_ADDRESS
    PreferredAuthentications publickey
    IdentityFile ~/.ssh/id_rsa
    Port 8022
    AddKeysToAgent yes
```
-
    - [add an SSH key to your GitLab account](https://docs.gitlab.com/ee/user/ssh.html#add-an-ssh-key-to-your-gitlab-account)
    - [configure Git to sign commits with your SSH key](https://docs.gitlab.com/ee/user/project/repository/signed_commits/ssh.html#configure-git-to-sign-commits-with-your-ssh-key)
    - verify that you can connect with `ssh -p 8022 -T git@GIT_ADDRESS`
    - if you work with containers, make sure to [share git credentials](https://code.visualstudio.com/remote/advancedcontainers/sharing-git-credentials)

Git should now be configured correctly.


- install [Docker](https://www.docker.com/); use the recommended approach in [these instructions](https://docs.docker.com/engine/install/centos/#installation-methods), don't forget the [post-installation instructions](https://docs.docker.com/engine/install/linux-postinstall/) (if not already managed by IT setup, depending from where the machine comes)
- install [brew](https://brew.sh/); also apply the recommended commands that show at the end of the script

You now have the basics to get up and running, installing further dependencies (mande easier to update and maintain thanks to `brew`), etc. ✅🎉

<details>
    <summary>If interested, here are all the commands you should have just run.</summary>

    # these are taken from `.bash_history`
    ls
    df -h
    apt-get
    yum
    ssh-keygen -m PEM -t rsa -b 4096 && cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys && chmod 600 ~/.ssh/authorized_keys
    ls -la .ssh
    sudo yum install -y yum-utils git-core htop
    sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
    sudo yum install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
    sudo systemctl start docker
    sudo docker run hello-world
    sudo groupadd docker
    sudo usermod -aG docker $USER
    sudo reboot
    newgrp docker
    docker run hello-world
    /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
    (echo; echo 'eval "$(/home/linuxbrew/.linuxbrew/bin/brew shellenv)"') >> /home/brieuc.devoghel/.bashrc
    eval "$(/home/linuxbrew/.linuxbrew/bin/brew shellenv)"
    sudo yum groupinstall 'Development Tools'
    brew install gcc
    brew help
</details>

### Personallize shell

If you like, you can use [oh-my-zsh](https://github.com/ohmyzsh/ohmyzsh/wiki) as shell framework. Follow instructions there for installation.

If changing shell did not work properly, try `chsh --shell /bin/zsh USER`, else you can execute a `zsh` shell on `bash` shell startup :
```bash
echo '\n# move to zsh shell with oh-my-zsh\nexec zsh' >> ~/.bashrc
```

A custom `oh-my-zsh` theme I like exists in this repository. Copy it to the appropriate location : 
```bash
mkdir -p ~/.oh-my-zsh/custom/themes/
cp -r utilities/oh-my-zsh/custom/devcontainers.zsh-theme ~/.oh-my-zsh/custom/themes/
```

## Installing tools and utilities

With freshly installed `brew`, install tools and utilities.
<details>
    <summary>Here are details about the packages about to be installed, feel free to filter out some of them.</summary>

    - dependency management :
        - poetry               # python environment and dependencies management
    - git utilities :
        - pre-commit           # git hook scripts for review before submission
        - commitizen           # git standardization
    - kubernetes deployment :
        - minikube             # local Kubernetes cluster for development 
        - helm                 # package manager for Kubernetes
        - k9s                  # terminal UI to interact with Kubernetes
        - gomplate
        - skaffold             # workflow handeling tool for building, pushing and deploying
    - other utilities:
        - navi                 # interactive cheatsheat tool for the CLI

</details>


```bash
brew install poetry pre-commit commitizen minikube helm k9s gomplate skaffold navi
```
> Futher setup commands are expected to be run from this project's main repository (that should be `~/machine-setup/`)
### Setup Navi

[Navi](https://github.com/denisidoro/navi) can help you use CLI commands. A couple of commands are already registered in [it's directory](./utilities/navi/cheats/). Copy it into the local repository : 
```bash
mkdir -p ~/.local/share/navi/
cp -r utilities/navi/cheats/ ~/.local/share/navi/
```

### Connect to private registry

Connect to any private registry :

```bash
docker login https://REGISTRY_ADDRESS
```

Enter your email and password.
If you get a 509 certificate issue, add the certificate to `/etc/docker/certs.d/REGISTRY_ADDRESS`


## Usefull commands

```bash
htop # CPU & RAM usage of the machine
nvtop # GPU usage of the machine
df -h # file system information (disk space, etc.)
docker system prune --all # remove unused Docker data
nohup python main.py & # run python program in background
code doc.txt # open the document doc.txt in the VSCode editor
conda install newpackage # install a new package using conda. You can also replace conda by mamba in this command for improved performance (see `mamba` for more information).
```