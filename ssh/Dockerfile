FROM rastasheep/ubuntu-sshd

RUN useradd -u 1000 --no-user-group --shell=/bin/bash -m --system devuser \
 && echo 'devuser:devuser' | chpasswd
