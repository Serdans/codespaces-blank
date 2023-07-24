FROM archlinux/archlinux:latest

ARG BUILD_DATE

ARG UUID=1000
ARG GUID=$UUID
ENV USERNAME=metabee
ENV TZ="Europe/Amsterdam"

RUN sed -e '/NoProgressBar/ s/^#*/#/' -i /etc/pacman.conf ;\
    sed -e '/Color/ s/^#//' -i /etc/pacman.conf

RUN pacman -Syyu --noconfirm --needed reflector base-devel git sudo

RUN groupadd -g "${GUID}" "${USERNAME}" ;\
    useradd -ms /bin/bash "${USERNAME}" -u "${UUID}" -g "${USERNAME}" -g wheel ;\
    echo "ALL ALL=(ALL) NOPASSWD:ALL" > "/etc/sudoers.d/${USERNAME}" ;\
    chmod 0440 "/etc/sudoers.d/${USERNAME}"

RUN pacman -S --noconfirm rustup nodejs pnpm python3 curl wget python-pip openssl-1.1

RUN set -eu ;\
    wget "https://github.com/cargo-bins/cargo-binstall/releases/latest/download/cargo-binstall-x86_64-unknown-linux-musl.tgz" -O "cargo-binstall.tgz" ;\
    tar zxvf "cargo-binstall.tgz" ;\
    mv "cargo-binstall" "/usr/bin/cargo-binstall" ;\
    rm -rf "cargo-binstall.tgz"

USER $USERNAME

RUN pushd /tmp ;\
    git clone "https://aur.archlinux.org/paru-bin.git" paru ;\
    pushd paru ;\
    makepkg -si --noconfirm ;\
    popd -2 ;\
    rm -rf paru

RUN rustup default stable

RUN cargo binstall cargo-nextest cargo-watch cargo-udeps --no-confirm

RUN paru -S --noconfirm httpie gitui mprocs-bin zip unzip zellij neovim zoxide lunarvim-git

RUN /usr/share/lunarvim/init-lvim.sh

RUN git config --global init.defaultBranch main

RUN echo 'eval "$(zoxide init bash)"' >> ~/.bashrc

RUN echo 'eval "$(zellij setup --generate-auto-start bash)"' >> ~/.bashrc
