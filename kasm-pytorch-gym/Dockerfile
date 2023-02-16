FROM kasmweb/core-cuda-focal:1.12.0


ENV DEBIAN_FRONTEND noninteractive

USER root

ENV HOME /home/kasm-default-profile
ENV STARTUPDIR /dockerstartup
WORKDIR $HOME

RUN ARCH=$(arch | sed 's/aarch64/arm64/g' | sed 's/x86_64/x64/g') && \
    wget -q https://update.code.visualstudio.com/latest/linux-deb-${ARCH}/stable -O vs_code.deb && \
    apt-get update && \
    apt-get install -y ./vs_code.deb

RUN mkdir -p /usr/share/icons/hicolor/apps && \
    wget -O /usr/share/icons/hicolor/apps/vscode.svg https://kasm-static-content.s3.amazonaws.com/icons/vscode.svg && \
    sed -i '/Icon=/c\Icon=/usr/share/icons/hicolor/apps/vscode.svg' /usr/share/applications/code.desktop && \
    sed -i 's#/usr/share/code/code#/usr/share/code/code --no-sandbox##' /usr/share/applications/code.desktop && \
    cp /usr/share/applications/code.desktop $HOME/Desktop && \
    chmod +x $HOME/Desktop/code.desktop && \
    chown 1000:1000 $HOME/Desktop/code.desktop && \
    rm vs_code.deb && \
    apt-get autoclean && \
    rm -rf \
    /var/lib/apt/lists/* \
    /var/tmp/* \
    /tmp/*

RUN apt update -y && \
    apt install -y \
    nano \
    python-is-python3 

RUN $STARTUPDIR/set_user_permission.sh $HOME

ENV HOME /home/kasm-user
WORKDIR $HOME
RUN mkdir -p $HOME && chown -R 1000:0 $HOME

COPY mamba-init.sh $HOME/Desktop/mamba-init.sh

RUN chmod +x $HOME/Desktop/mamba-init.sh

RUN chown -R 1000:0 $HOME

USER 1000

RUN wget https://github.com/conda-forge/miniforge/releases/latest/download/Mambaforge-pypy3-Linux-x86_64.sh && \
    /bin/bash Mambaforge-pypy3-Linux-x86_64.sh -b 

RUN $HOME/mambaforge-pypy3/bin/mamba install -vy pytorch torchvision torchaudio pytorch-cuda=11.6 -c pytorch -c nvidia

RUN $HOME/mambaforge-pypy3/bin/mamba install pip 

RUN $HOME/mambaforge-pypy3/bin/mamba run -n base /bin/bash -c "pip install gymnasium nes-py gym-super-mario-bros"




