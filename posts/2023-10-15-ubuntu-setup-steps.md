---
title: Consider these steps when fresh-installing Ubuntu
description: A quick reference for getting an Ubuntu set-up set up.
date: 2023-10-15
tags:
  - ubuntu
  - python
  - node
  - git
  - checklist
extraWideMedia: true
opengraph:
  image: /assets/images/ubuntu-setup-steps/00.jpg
---

Whether freshly installing Ubuntu on a new bit of hardware or within WSL, there are a few steps I like to follow to get the basics set up nicely.

What's here is taken direct from others' prior work but having them here in a single page makes for a helpful _aide-mémoire_.

## Software/tools

### Python

#### virtualenvwrapper

```bash
sudo apt install python3-virtualenvwrapper
```

Then add the following to `~/.bashrc`:

```bash
export WORKON_HOME=$HOME/.virtualenvs
export PROJECT_HOME=$HOME/code
export VIRTUALENVWRAPPER_PYTHON=/usr/bin/python3
source /usr/share/virtualenvwrapper/virtualenvwrapper.sh
```

### Node.js

Here, just following the [guidance steps from The Odin Project](https://www.theodinproject.com/lessons/foundations-installing-node-js).

#### Node Version Manager

```bash
sudo apt update && sudo apt upgrade
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.5/install.sh | bash
# Verify nvm is installed:
command -v nvm
```

#### Node

```bash
nvm install --lts
nvm use --lts
# Verify node is installed
node -v
```


## Configuration

### Bash prompt

I like the minimalism offered by this [simple and effective Bash prompt](https://code.mendhak.com/simple-bash-prompt-for-developers-ps1-git/), which is a result of this addition to `~/.bashrc`:

```bash
function parse_git_dirty {
  [[ $(git status --porcelain 2> /dev/null) ]] && echo "*"
}
function parse_git_branch {
  git branch --no-color 2> /dev/null | sed -e '/^[^*]/d' -e "s/* \(.*\)/ (\1$(parse_git_dirty))/"
}

export PS1="\n\t \[\033[32m\]\w\[\033[33m\]\$(parse_git_branch)\[\033[00m\] $ "
```

### Git

Configure user name and e-mail, and some aliases for nicer git logging:

```bash
git config --global user.name "bob.loblaw"
git config --global user.email "bob.loblaw@users.noreply.github.com"

# Git log graph view, all branches, commits ordered by graph topology
# In this view, commit ordering from top to bottom is not by time, but subject to the graph topology, i.e. the dependencies between commits can be viewed easily.
git config --global alias.l "log --format='%C(yellow)%h%C(reset) %C(auto)%d%C(reset) %s %C(magenta)(%an)%C(reset) %C(cyan)(%ar)%C(reset)' --all --graph --topo-order"

# Git log graph view, all branches, commits ordered by commit date
# In this view, a commit graph is displayed showing the dependencies between commits, but the ordering of the commits if by time. This leads to longer graph edges in some cases, but it is sometimes more intuitive to see commits in order of their creation time.
git config --global alias.ld "log --format='%C(yellow)%h%C(reset) %C(auto)%d%C(reset) %s %C(magenta)(%an)%C(reset) %C(cyan)(%ar)%C(reset)' --all --graph --date-order"
```

## WSL-specific steps

### Serve SSH keys to WSL2 from KeepassXC on the host

Again, thanks to the efforts of others who've done the hard work to [make this a simple setup script](https://code.mendhak.com/wsl2-keepassxc-ssh/#all-together-in-one-script)!

```bash
cd ~

echo "Get npiperelay"
wget https://github.com/jstarks/npiperelay/releases/latest/download/npiperelay_windows_amd64.zip
unzip -o npiperelay_windows_amd64.zip -d npiperelay
rm npiperelay_windows_amd64.zip

echo "Install socat"
sudo apt -y install socat

echo "Add to .bashrc"
cat << 'EOF' >> ~/.bashrc
export SSH_AUTH_SOCK=$HOME/.ssh/agent.sock

ss -a | grep -q $SSH_AUTH_SOCK
if [ $? -ne 0 ]; then
    rm -f $SSH_AUTH_SOCK
    (setsid socat UNIX-LISTEN:$SSH_AUTH_SOCK,fork EXEC:"$HOME/npiperelay/npiperelay.exe -ei -s //./pipe/openssh-ssh-agent",nofork &) >/dev/null 2>&1
fi
EOF

echo "Reload ~/.bashrc"
exec bash

echo "Done"
```