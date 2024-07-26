FROM ubuntu:22.04
WORKDIR  /data/workspace

#* Basic setup
ARG DEBIAN_FRONTEND=noninteractive
ARG DEBCONF_NONINTERACTIVE_SEEN=true
RUN apt-get update
RUN apt-get -yq upgrade

RUN apt-get install -yq software-properties-common apt-utils tzdata moreutils
RUN apt-get install -yq curl wget 
RUN apt-get install -yq git 
RUN apt-get install -yq vim nano tmux
RUN apt-get install -yq python3 nodejs npm python3-pip
RUN apt-get install -yq gdal-bin 
RUN apt-get install -yq jq jo
RUN apt-get install -yq iputils-ping
RUN apt-get install -yq postgresql-client

# updating node.js
RUN npm cache clean -f; \
	npm install -g n; \
	n stable

ENV LANG C.UTF-8
ENV LC_ALL C.UTF-8

# installing node-red
RUN npm install --unsafe-perm node-red --location=global
RUN npm install pm2 --location=global

#* Installing jupyter
RUN pip3 install jupyterlab
RUN pip3 install notebook
# node.js kernel
RUN npm install -g ijavascript
RUN ijsinstall
# bash kernel
RUN pip3 install bash_kernel
RUN python3 -m bash_kernel.install
# adding postgresql kernel
RUN apt-get install -yq libpq-dev
RUN pip3 install psycopg2
RUN pip3 install postgres_kernel

#* Creating user admin

# Define ARG with default values
ARG USER_ID=1000

RUN apt-get install -y sudo
RUN useradd -m -u ${USER_ID} -g 0 admin && echo "admin:admin" | chpasswd && adduser admin sudo
RUN echo 'admin ALL=(ALL) NOPASSWD:ALL' >> /etc/sudoers

#* Configuratin files
ADD ./conf/vim/vimrc /etc/vim/vimrc
ADD ./conf/tmux/tmux.conf /home/admin/.tmux.conf
ADD ./pm2.json /home/admin/pm2.json

#* Configuring timezone
ENV TZ=America/Sao_Paulo
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

#* cmd
USER admin

CMD npm install --prefix /data/workspace/node-red ; \
    pm2 start "/home/admin/pm2.json" --no-daemon

