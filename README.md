Trionyx Ansible
===============

An Ansible role to setup a complete [Trionyx](https://github.com/krukas/Trionyx) environment on a clean Ubuntu server.

Setup an Ansible project that uses this role with:

```shell
trionyx create_ansible <domain> <repo>
```

Setup your host
~~~~~~~~~~~~~~~

    cat >setup.sh <<EOL
    #!/usr/bin/env bash
    
    useradd -m -s /bin/bash ansible
    echo "ansible ALL=(ALL) NOPASSWD: ALL" > /etc/sudoers.d/99-ansible
    mkdir /home/ansible/.ssh/
    chmod 700 /home/ansible/.ssh/
    touch /home/ansible/.ssh/authorized_keys
    
    read -p 'Ansible public ssh key: ' ansible_public_key
    echo $ansible_public_key > /home/ansible/.ssh/authorized_keys
    
    chmod 600 /home/ansible/.ssh/authorized_keys
    chown ansible -Rf /home/ansible/.ssh
    
    sudo apt-get update
    sudo apt-get --assume-yes install python python-pip
    
    echo "Change SSH port to 6969 and restart ssh"
    sudo sed -i "s/#Port 22/Port 6969/" /etc/ssh/sshd_config
    sudo sed -i -r 's/#?PasswordAuthentication.*/PasswordAuthentication yes/' /etc/ssh/sshd_config
    sudo sed -i -r 's/#?PermitEmptyPasswords.*/PermitEmptyPasswords no/' /etc/ssh/sshd_config
    sudo systemctl restart sshd
    EOL
    chmod +x setup.sh
    . setup.sh