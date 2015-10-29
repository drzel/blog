---
layout: post
title: Open source Ruby on Rails development environment for Lubuntu 15.10 as a guest in [VirtualBox]) 5.0.8 with Vim, tmux and powerline.
---
# Open source Ruby on Rails development environment for [Lubuntu](http://lubuntu.net) 15.10 as a guest in [VirtualBox](http://www.virtualbox.org/) 5.0.8 with [Vim](http://www.vim.org/), [tmux](https://tmux.github.io/) and [powerline](https://github.com/powerline/powerline).

## Install VirtualBox
* Download and install the latest VirtualBox for your OS at https://www.virtualbox.org/.

## Install Lubuntu 15.10.
* Download Lubuntu 15.10 image file from https://www.lubuntu.net/.
* Launch VirtualBox.
* Click New.
* Follow the prompts. I recommend at least 1GB RAM and 16GB HD.
* When prompted select the Lubuntu .iso file you downloaded previously.
* Follow the Lubuntu installation process.
* If it freezes on first boot, in the VirtualBox menu, select Machine > Reset.
* When prompted by Software Updater, install updates.

## Install VirtualBox Guest Additions
* From the VirtualBox menu, select Devices > Insert Guest Additions CD image...
* Launch LXTerminal (Ctrl-Alt-t).

`sudo /media/drzel/VBOXADDITIONS_5.0.8_103449/./VBoxLinuxAdditions.run`

Note: Your VBOXADDITIONS may be a later version.

* Reboot machine.
* From the VirtualBox menu, select Devices > Shared Clipboard > Bidirectional.

## Install packages
* Launch LXTerminal.
```bash
sudo apt-get install build-essential curl git cmake postgresql postgresql-contrib libpq-dev python-dev python-pip python3-dev python3-pip exuberant-ctags imagemagick vim tmux gnome-terminal
```

## Make gnome-terminal default terminal emulator
`sudo update-alternatives --config x-terminal-emulator`

* Select `gnome-terminal.wrapper`.
* Reboot machine.

## Install RVM and Rails
* Launch gnome-terminal (Ctrl-Alt-t).
* Click Edit > Profile Preferences > Command.
* Check 'Run Command as a login shell'. 
* Click Close.

Note: If you are not using gnome-terminal check https://rvm.io/integration for the correct settings for your terminal emulator.
```bash
gpg --keyserver hkp://keys.gnupg.net --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3
\curl -sSL https://get.rvm.io | bash -s stable --rails
```

This will take a while. Once finished:

* Close and then reopen gnome-terminal.

To confirm RVM installed correctly:

`type rvm | head -n 1`

If everything went smoothly you should see `rvm is a function`.

## Configure PostgreSQL
Create a PostgreSQL user with the same username and password as your Lubuntu login.

`sudo -u postgres createuser -s USERNAME`

* Set your account password.

`sudo -u postgres psql`

`postgres=# \password USERNAME`

* Enter and confirm password.

`postgres=# \q`

## Install Node.js
```bash
curl --silent --location https://deb.nodesource.com/setup_4.x | sudo bash -
sudo apt-get install --yes nodejs
```

## Configure Git
```bash
git config --global user.email "YOUR.EMAIL@ADDRESS.COM"
git config --global user.name "YOUR NAME"
```

## Install powerline
```bash
sudo pip install powerline-status
```

### Install powerline fonts
```bash
wget https://github.com/powerline/powerline/raw/develop/font/10-powerline-symbols.conf
mv PowerlineSymbols.otf /usr/share/fonts/
fc-cache -vf /usr/share/fonts/
mv 10-powerline-symbols.conf /etc/fonts/conf.d/
```

### Configure powerline for bash
* Append to `~/.bashrc`
```bash
powerline-daemon -q
POWERLINE_BASH_CONTINUATION=1
POWERLINE_BASH_SELECT=1
. /usr/local/lib/python2.7/dist-packages/powerline/bindings/bash/powerline.sh
```

### Configure powerline for vim
* Append to `~/.vimrc` (create if necessary)
```vim
python from powerline.vim import setup as powerline_setup
python powerline_setup()
python del powerline_setup
set rtp+=/usr/local/lib/python2.7/dist-packages/powerline/bindings/vim
set laststatus=2 " Always show status bar
set timeoutlen=1000 ttimeoutlen=0 " Don't wait for escape key
set noshowmode " Hide -- INSERT -- text in vim status bar
```

### Configure powerline for tmux
* Append to `~/.tmux.conf` (create if necessary)
```
# Use powerline
source /usr/local/lib/python2.7/dist-packages/powerline/bindings/tmux/powerline.conf

# Don't wait on escape
set -sg escape-time 0
```

## Confirm working
```bash
rails new temp-rails-project
cd temp-rails-project
rails s
```

Then point your browser to `localhost:3000`. If you see the Rails 'Welcome aboard' site you're good to go. When you're done you can remove the temp-rails-project folder.

`rm -rf temp-rails-project`
