# Course: Ansible для начинающих + практический опыт
- https://stepik.org/course/123806/syllabus

## Docker container doc start
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
    #docker run -d -P --name test_sshd2 sshd_debian
    docker ps

### Find container's IP address
    docker inspect --format='{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' arch_target1
    docker inspect --format='{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' arch_target2

### SSH
    sudo nano /etc/hosts

```hosts
172.17.0.2  target1
172.17.0.3  target2
```
    ssh root@target1
    ssh root@target2

### run inventory
     ansible target1 -m ping -i inventory


# Errors
1) 
    ssh root@172.17.0.2
> Add correct host key in /home/jacky/.ssh/known_hosts to get rid of this message.
Offending ECDSA key in /home/jacky/.ssh/known_hosts:18
Host key for 172.17.0.2 has changed, and you have requested strict checking.
Host key verification failed.

    ssh-keygen -R 172.17.0.2

2) 