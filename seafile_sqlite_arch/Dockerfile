FROM --platform=${BUILDPLATFORM} manjarolinux/base
LABEL org.opencontainers.image.authors="jiri1@duck.com"
ENV SEAFILE_VERSION=9.0.4

ARG TARGETPLATFORM

RUN pacman-mirrors --country Czech && sudo pacman -Syyu
RUN pacman -S --noconfirm --needed base-devel glibc git
RUN sudo -i echo 'builder  ALL=(ALL:ALL) ALL' >> /etc/sudoers
RUN passwd -d builder
RUN mkdir /home/builder
RUN chown -hR builder:users /home/builder
WORKDIR /home/builder
RUN sudo -u builder git clone https://aur.archlinux.org/pikaur.git
WORKDIR /home/builder/pikaur
RUN sudo -u builder makepkg --noconfirm -fsri
RUN sudo -u builder pikaur --noconfirm -S seafile-server seahub
WORKDIR /usr/share/seafile-server/



CMD ["/bin/bash"]
