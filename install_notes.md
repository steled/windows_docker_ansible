## VMware Tools Installation
```bash
sudo yum install open-vm-tools
```

## Add sudo privileges
```bash
usermod -aG wheel <username>
```

## Set hostname
```bash
hostnamectl set-hostname <hostname>
hostnamectl status
```

## Update all installed packages
```bash
sudo yum update
```

## Python PIP Installation
```bash
sudo rpm -iUvh http://dl.fedoraproject.org/pub/epel/7/x86_64/Packages/e/epel-release-7-11.noarch.rpm
sudo yum update
sudo yum install python-pip
sudo pip install --upgrade pip
```

# Ansible Master Installation
- Cent OS 7 - Minimal Installation
- [VMware Tools Installation](#vmware-tools-installation)
- [Add sudo privileges](#add-sudo-privileges)
- [Set hostname](#set-hostname)
- [Update all installed packages](#update-all-installed-packages)

# Management Master Installation
- Cent OS 7  - Server with GUI
- [VMware Tools Installation](#vmware-tools-installation)
- [Add sudo privileges](#add-sudo-privileges)
- [Set hostname](#set-hostname)
- [Update all installed packages](#update-all-installed-packages)
- [Python PIP Installation](#python-pip-installation)

## Installation

### VNC-Server Installation
Source: https://www.howtoforge.com/vnc-server-installation-on-centos-7

check that vnc-server is installed or not
```bash
rpm -q tigervnc-server
```

install vnc-server
```bash
sudo yum install tigervnc-server
```

configure vnc-server
```bash
sudo cp /lib/systemd/system/vncserver@.service /etc/systemd/system/vncserver@:1.service
sudo vi /etc/systemd/system/vncserver@:1.service
```

replace <USER> strings with vncuser's username
```bash
User=<USER>
PIDFile=/home/<USER>/.vnc/%H%i.pid
```

add firewall rule
```bash
sudo firewall-cmd --permanent --zone=public --add-service vnc-server
sudo firewall-cmd --reload
```

start vnc service
```bash
su - <username>
vncserver
```

make service enabled after reboot
```bash
su -
systemctl daemon-reload
systemctl enable vncserver@:1.service
reboot
```

### VirtualBox Installation
Source: https://wiki.centos.org/HowTos/Virtualization/VirtualBox

```bash
cd /etc/yum.repos.d
sudo wget http://download.virtualbox.org/virtualbox/rpm/rhel/virtualbox.repo
sudo yum install gcc
sudo yum install kernel-devel
yum search VirtualBox
sudo yum install VirtualBox-x.x
sudo usermod -a -G vboxusers <username>
```

### Vagrant Installation
Source: https://www.tecmint.com/how-to-install-vagrant-on-centos-7/

```bash
sudo yum -y install https://releases.hashicorp.com/vagrant/x.x/vagrant_x.x_x86_64.rpm
cd ~/
mkdir vagrant-home
```

### Packer Installation
Source: https://www.packer.io/intro/getting-started/install.html

```bash
cd ~/
mkdir packer
wget https://releases.hashicorp.com/packer/x.x/packer_x.x_linux_amd64.zip
unzip packer_x.x_linux_amd64.zip
export PATH=$PATH:/home/<username>/packer
cd /usr/bin/
sudo ln -s ~/packer/packer packer.io
```

### Git Installation
Source: https://git-scm.com/book/en/v2/Getting-Started-Installing-Git

```bash
sudo yum install git
git config --global user.name "<username>"
git config --global user.email <e-mail>
```

### Ansible Installation
```bash
sudo yum install ansible
sudo pip install pywinrm
```

## Configuration

clone repository
```bash
cd ~/
git clone https://github.com/jonashackt/ansible-windows-docker-springboot.git
mkdir iso
```

creating windows-vagrant-box by packer
```bash
cd ~/ansible-windows-docker-springboot/step0-packer-windows-vagrantbox/
packer.io build -var iso_url=../../iso/14393.0.161119-1705.RS1_REFRESH_SERVER_EVAL_X64FRE_EN-US.ISO windows_server_2016_docker.json
vagrant init windows_2016_docker_virtualbox.box
```

starting windows-vagrant-box
```bash
cd ~/ansible-windows-docker-springboot/step0-packer-windows-vagrantbox/
vagrant up
```

test working configuration
```bash
cd ~/ansible-windows-docker-springboot/step0-packer-windows-vagrantbox/ansible-windows-simple
ansible windows-dev -i hostsfile -m win_ping
```

install firefox
```bash
cd ~/ansible-windows-docker-springboot/step0-packer-windows-vagrantbox/ansible-windows-simple
ansible-playbook -i hostsfile windows-playbook.yml --extra-vars "host=windows-dev"
```

install docker
```bash
cd ~/ansible-windows-docker-springboot/step1-prepare-docker-windows/
ansible-playbook -i hostsfile prepare-docker-windows.yml --extra-vars "host=ansible-windows-docker-springboot-dev"
```