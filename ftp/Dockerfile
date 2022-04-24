FROM bogem/ftp
RUN apt -y update && apt -y upgrade
RUN apt -y install ssh
RUN echo 'root:test' | chpasswd
RUN sed -i 's/PermitRootLogin prohibit-password/PermitRootLogin yes/' /etc/ssh/sshd_config
