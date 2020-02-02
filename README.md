# ddr
- backend: Django
- frontend: React
- environment: Docker

## folder structure
- /backend  --> django folder
- /backend/hello-world --> main app in django
- /backend/hello-world/settings --> production and dev settings
- /backend/word-count  --> word-count app in django
- /frontend  --> react folder
- /

## First build each
### backend: Django
create Dockerfile in backend
```
# Use an official Python runtime as a parent image
FROM python:3.6

# Adding backend directory to make absolute filepaths consistent across services
WORKDIR /app/backend

# Install Python dependencies
COPY requirements.txt /app/backend
RUN pip3 install --upgrade pip -r requirements.txt

# Add the rest of the code
COPY . /app/backend

# Make port 8000 available for the app
EXPOSE 8000

# Be sure to use 0.0.0.0 for the host within the Docker container,
# otherwise the browser won't be able to find it
CMD python3 manage.py runserver 0.0.0.0:8000
```

run in a terminal
```
docker build -t backend:latest backend
docker run -v $PWD/backend:/app/backend backend:latest django-admin startproject hello_world .
docker run -v $PWD/backend:/app/backend -p 8000:8000 backend:latest
```


### frontend: React
Create Dockerfile in frontend
```
# Use an official node runtime as a parent image
FROM node:8

WORKDIR /app/

# Install dependencies
# COPY package.json yarn.lock /app/

# RUN npm install

# Add rest of the client code
COPY . /app/

EXPOSE 3000

# CMD npm start
```

run in a terminal
```
docker build -t frontend:latest frontend
docker run -v $PWD/frontend:/app frontend:latest npx create-react-app hello-world
mv frontend/hello-world/* frontend/hello-world/.gitignore frontend/ && rmdir frontend/hello-world
docker run -v $PWD/frontend:/app -p 3000:3000 frontend:latest npm start
```

## docker-compose: Combine the two into one
### Setup
create docker-compose.yml
```
version: "3.2"
services:
  backend:
    build: ./backend
    volumes:
      - ./backend:/app/backend
    ports:
      - "8000:8000"
    stdin_open: true
    tty: true
    command: python3 manage.py runserver 0.0.0.0:8000
  frontend:
    build: ./frontend
    volumes:
      - ./frontend:/app
      # One-way volume to use node_modules from inside image
      - /app/node_modules
    ports:
      - "3000:3000"
    environment:
      - NODE_ENV=development
    depends_on:
      - backend
    command: npm start
```

run in a terminal
```
docker-compose up
```

### Install axios in frontend
```
docker-compose run --rm frontend npm add axios
```

### Install an app in backend
```
docker-compose run --rm backend python3 manage.py startapp char_count
```

## Build App
```
docker-compose down
docker-compose up --build
```

## Run and Stop App
```
docker-compose up
```
In another terminal
```
docker-compose stop
```

In browser for backend
```
localhost:8000
```
another brower for frontend
```
localhost:3000
```

## Environment Variables
- SECRET_KEY
- PRODUCTION_HOST

## Deployment
### Install Heroku CLI
Make sure you installed Heroku CLI. Otherwise do the following
on Mac
```
$ brew install heroku/brew/heroku
```
on Ubuntu
```
$ sudo snap install heroku --classic
```

### Create an app in Heroku

```
$ heroku login
$ heroku create
https://vast-escarpment-1234.herokuapp.com
$ heroku config:set PRODUCTION_HOST=vast-escarpment-1234.herokuapp.com SECRET_KEY=<your secret key> DJANGO_SETTINGS_MODULE=hello_world.settings.production
```

### Deploy the app to Heroku
```
$ heroku stack:set container
$ git push heroku master
```

## Reference
- https://dev.to/englishcraig/docker-django-react-building-assets-and-deploying-to-heroku-24jh