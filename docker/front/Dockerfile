FROM nginx:latest

ENV LANG=en_US.UTF-8 \
    LANGUAGE=en_US.UTF-8 \
    TERM=xterm \
    TZ=Europe/Moscow

### Curl install
RUN apt-get update && apt-get install -y curl

### Install NodeJS
RUN groupadd --gid 1000 web \
  && useradd --uid 1000 --gid web --shell /bin/bash --create-home web

RUN curl -sL https://deb.nodesource.com/setup_7.x | bash - && apt-get install -y nodejs && npm install npm -g


### Install Angular CLI
RUN npm install -g @angular/cli

### Install supervisor
RUN apt-get update && apt-get install -y supervisor \
    && mkdir -p  /var/log/supervisor


CMD ["/usr/bin/supervisord"]
