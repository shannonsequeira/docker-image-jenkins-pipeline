FROM ubuntu

RUN apt-get update 
RUN apt-get install -y python3
RUN apt-get install -y python3-pip
RUN apt-get install -y python3-flask

COPY . /opt/source-code

ENTRYPOINT FLASK_APP=/opt/source-code/app.py flask run