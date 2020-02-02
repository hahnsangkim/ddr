# ddr
- backend: Django
- frontend: React
- environment: Docker

## Installation

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