# django-sample
simple web app that uses docker compose to build django web app and has a test using selenium.

The first thing to do is to create a dockerfile which will have the details of the image that we are creating

FROM python:3
RUN apt-get update && apt-get upgrade -y && apt-get autoremove && apt-get autoclean
RUN apt-get install -y \
    libffi-dev \
    libssl-dev \
    libmysqlclient-dev \
    libxml2-dev \
    libxslt-dev \
    libjpeg-dev \
    libfreetype6-dev \
    zlib1g-dev \
    net-tools \
    vim
# Project Files and Settings
ARG PROJECT=django-dev
ARG PROJECT_DIR=/var/www/${PROJECT}
RUN mkdir -p $PROJECT_DIR
WORKDIR $PROJECT_DIR
COPY requirements.txt .
RUN pip install -r requirements.txt
# Server
EXPOSE 8000
STOPSIGNAL SIGINT
ENTRYPOINT ["python", "manage.py"]
CMD ["runserver", "0.0.0.0:8000"]


The next step is to create a requirements.txt file that will show the details of what the app requires 

django>=2.1
psycopg2


The next step is now step up our docker-compose.yaml file 

version: "2"
services:
  django:
    container_name: django_server
    build:
      context: .
      dockerfile: Dockerfile
    image: docker_django_test
    stdin_open: true
    tty: true
    volumes:
      - .:/var/www/django-dev
    ports:
      - "8000:8000"
    links:
      - db
    environment:
      - DATABASE_URL=mysql://root:itsasecret@db:3306/docker_django_test_db
  db:
    container_name: mysql_database
    image: mysql/mysql-server
    ports:
      - "3306:3306"
    environment:
      - MYSQL_ROOT_PASSWORD=itsasecret
    volumes:
      - /Users/work/Development/data/mysql:/var/lib/mysql
  selenium_hub:
    container_name: selenium_hub
    image: selenium/hub
    ports:
      - "4444:4444"
  selenium_chrome:
    container_name: selenium_chrome
    image: selenium/node-chrome-debug
    environment:
      - HUB_PORT_4444_TCP_ADDR=selenium_hub
      - HUB_PORT_4444_TCP_PORT=4444
    ports:
      - "5900:5900"
    depends_on:
      - selenium_hub
  selenium_firefox:
    container_name: selenium_firefox
    image: selenium/node-firefox-debug
    environment:
      - HUB_PORT_4444_TCP_ADDR=selenium_hub
      - HUB_PORT_4444_TCP_PORT=4444
    ports:
      - "5901:5900"
    depends_on:
      - selenium_hub
      
     
  This file also has a sample test in selenium for the build app
