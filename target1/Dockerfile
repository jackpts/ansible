FROM archlinux

RUN pacman -Suyy openssh nano micro ansible sshpass neofetch --noconfirm

RUN echo 'root:123' | chpasswd

RUN sed -i 's/#PermitRootLogin prohibit-password/PermitRootLogin yes/' /etc/ssh/sshd_config

# RUN sed -i 's/# info "Local IP" local_ip/info "Local IP" local_ip/' ~/.config/neofetch/config.conf

RUN echo neofetch >> /etc/bash.bashrc

RUN ssh-keygen -A

EXPOSE 22

CMD ["/usr/sbin/sshd", "-D"]