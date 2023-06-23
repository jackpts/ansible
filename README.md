# Course: Ansible для начинающих + практический опыт:
- https://stepik.org/course/123806/syllabus

### Docker container deploy w/ ssh origin helper doc (for ubuntu image & error in `sed` usage):
- https://www.techrepublic.com/article/deploy-docker-container-ssh-access/

### Build the image and deploy the container
    cd target1/
    docker build -t arch_target1 .

    cd ../target2/
    docker build -t arch_target2 .

### Deploy
    docker run -d -P --name arch_target1 arch_target1
    docker run -d -P --name arch_target2 arch_target2
    docker ps

```text
CONTAINER ID   IMAGE          COMMAND                  CREATED          STATUS          PORTS                                                    NAMES
581d25793897   arch_target2   "/usr/sbin/sshd -D"      5 seconds ago    Up 4 seconds    0.0.0.0:32773->22/tcp, :::32773->22/tcp                  arch_target2
5984d137c941   arch_target1   "/usr/sbin/sshd -D"      55 minutes ago   Up 55 minutes   0.0.0.0:32772->22/tcp, :::32772->22/tcp                  arch_target1
```


### Find container's IP address
    docker inspect --format='{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' arch_target1
    docker inspect --format='{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' arch_target2

### SSH
    # sudo nano /etc/hosts
    sudo micro /etc/hosts

```/etc/hosts
172.17.0.2  target1
172.17.0.3  target2
```
    ssh root@target1
    ssh root@target2

### run inventory
     ansible target1 -m ping -i inventory

> target1 | SUCCESS => {
"ansible_facts": {
"discovered_interpreter_python": "/usr/bin/python3.11"
},
"changed": false,
"ping": "pong"
}



------------------------------------------

### Errors
#### 1. After several deployments:
    ssh root@target1

>  WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!
> Add correct host key in /home/jacky/.ssh/known_hosts to get rid of this message.
Offending ECDSA key in /home/jacky/.ssh/known_hosts:11
Host key for target1 has changed, and you have requested strict checking.
Host key verification failed.

    tail ~/.ssh/known_hosts | grep target1
    ssh-keygen -R target1

> Host target1 found: line 11
> /home/jacky/.ssh/known_hosts updated.
> Original contents retained as /home/jacky/.ssh/known_hosts.old

    ssh root@target1
    > yes, 123

#### 2. Checking every 2nd inventory failed 'cause of ssh host key
    ansible target2 -m ping -i inventory

> target2 | FAILED! => {
"msg": "Using an SSH password instead of a key is not possible because Host Key checking is enabled and sshpass does not support this.  Please add this host's fingerprint to your known_hosts file to manage this host."
}

    sudo micro /etc/ansible/ansible.cfg

```
[defaults]
host_key_checking = false
```

    ansible target2 -m ping -i inventory

> target2 | SUCCESS => {
"ansible_facts": {
"discovered_interpreter_python": "/usr/bin/python3"
},
"changed": false,
"ping": "pong"
}

#### 3. Target1  UNREACHABLE!

     ansible target1 -m ping -i inventory

> target1 | UNREACHABLE! => {
"changed": false,
"msg": "Failed to connect to the host via ssh: @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@\r\n@    WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!     @\r\n@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@\r\nIT IS POSSIBLE THAT SOMEONE IS DOING SOMETHING NASTY!\r\nSomeone could be eavesdropping on you right now (man-in-the-middle attack)!\r\nIt is also possible that a host key has just been changed.\r\nThe fingerprint for the ED25519 key sent by the remote host is\nSHA256:+F8M4fovU1UZ9YYjte489g9Y2PHgB1NOMBgfmRfjo3w.\r\nPlease contact your system administrator.\r\nAdd correct host key in /home/jacky/.ssh/known_hosts to get rid of this message.\r\nOffending ED25519 key in /home/jacky/.ssh/known_hosts:12\r\nPassword authentication is disabled to avoid man-in-the-middle attacks.\r\nKeyboard-interactive authentication is disabled to avoid man-in-the-middle attacks.\r\nUpdateHostkeys is disabled because the host key is not trusted.\r\nroot@172.17.0.2: Permission denied (publickey,password).",
"unreachable": true
}

    tail ~/.ssh/known_hosts
    ssh-keygen -R target1
    ssh-keygen -R 172.17.0.2    // IP for target1
    ansible target1 -m ping -i inventory

> target1 | SUCCESS => {
"ansible_facts": {
"discovered_interpreter_python": "/usr/bin/python3.11"
},
"changed": false,
"ping": "pong"
}

#### 4. 