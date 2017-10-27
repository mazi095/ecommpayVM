FROM nginx:latest

ENV LANG=en_US.UTF-8 \
    LANGUAGE=en_US.UTF-8 \
    LC_ALL=en_US.UTF-8 \
    TERM=xterm \
    TZ=Europe/Moscow\
    PORT=80

### Curl install
RUN apt-get update && apt-get install -y curl

### Install NodeJS
RUN groupadd --gid 1001 node \
  && useradd --uid 1001 --gid node --shell /bin/bash --create-home node

RUN curl -sL https://deb.nodesource.com/setup_7.x | bash - && apt-get install -y nodejs && npm install npm -g


### Install Angular CLI
RUN npm install -g @angular/cli

### Install supervisor
RUN apt-get update && apt-get install -y supervisor \
    && mkdir -p  /var/log/supervisor


CMD ["/usr/bin/supervisord"]
