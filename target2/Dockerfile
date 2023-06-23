FROM archlinux

RUN pacman -Suyy openssh nano micro ansible sshpass --noconfirm

# RUN mkdir /var/run/sshd

RUN echo 'root:123' | chpasswd

RUN sed -i 's/#PermitRootLogin prohibit-password/PermitRootLogin yes/' /etc/ssh/sshd_config

# RUN sed 's@session\s*required\s*pam_loginuid.so@session optional pam_loginuid.so@g' -i /etc/pam.d/sshd

EXPOSE 22

CMD ["/usr/sbin/sshd", "-D"]