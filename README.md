# Course: Ansible для начинающих + практический опыт:
- https://stepik.org/course/123806/syllabus

### Docker container deploy w/ ssh origin doc:
- https://www.techrepublic.com/article/deploy-docker-container-ssh-access/

### Build the image and deploy the container
    cd target1/
    # sudo docker build -t sshd_ubuntu .
    docker build -t arch_target1 .

    cd ../target2/
    # sudo docker build -t sshd_debian .
    docker build -t arch_target2 .

### Deploy
    docker run -d -P --name arch_target1 arch_target1
    docker run -d -P --name arch_target2 arch_target2
    # docker run -d -P --name test_sshd sshd_ubuntu
    # docker run -d -P --name test_sshd2 sshd_debian
    docker ps

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
#### After several deployments:
    ssh root@target1

> Add correct host key in /home/jacky/.ssh/known_hosts to get rid of this message.
Offending ECDSA key in /home/jacky/.ssh/known_hosts:18
Host key for 172.17.0.2 has changed, and you have requested strict checking.
Host key verification failed.

    tail ~/.ssh/known_hosts | grep target1
    ssh-keygen -R target1

> Host target1 found: line 11
> /home/jacky/.ssh/known_hosts updated.
> Original contents retained as /home/jacky/.ssh/known_hosts.old

    ssh root@target1
    > yes, 123

#### 