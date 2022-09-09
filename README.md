# ProShop eCommerce Platform

> eCommerce platform built with the MERN stack & Redux.

This is the course project for my [MERN eCommerce From Scratch](https://www.udemy.com/course/mern-ecommerce) course

![screenshot](https://github.com/bradtraversy/proshop_mern/blob/master/uploads/Screen%20Shot%202020-09-29%20at%205.50.52%20PM.png)

## Features

- Full featured shopping cart
- Product reviews and ratings
- Top products carousel
- Product pagination
- Product search feature
- User profile with orders
- Admin product management
- Admin user management
- Admin Order details page
- Mark orders as delivered option
- Checkout process (shipping, payment method, etc)
- PayPal / credit card integration
- Database seeder (products & users)

## Note on Issues

Please do not post issues here that are related to your own code when taking the course. Add those in the Udemy Q/A. If you clone THIS repo and there are issues, then you can submit

## Usage

### ES Modules in Node

We use ECMAScript Modules in the backend in this project. Be sure to have at least Node v14.6+ or you will need to add the "--experimental-modules" flag.

Also, when importing a file (not a package), be sure to add .js at the end or you will get a "module not found" error

You can also install and setup Babel if you would like

### Env Variables

Create a .env file in then root and add the following

```
NODE_ENV = development
PORT = 5000
MONGO_URI = your mongodb uri
JWT_SECRET = 'abc123'
PAYPAL_CLIENT_ID = your paypal client id
```

### Install Dependencies (frontend & backend)

```
npm install
cd frontend
npm install
```

### Run

```
# Run frontend (:3000) & backend (:5000)
npm run dev

# Run backend only
npm run server
```

## Build & Deploy

```
# Create frontend prod build
cd frontend
npm run build
```

There is a Heroku postbuild script, so if you push to Heroku, no need to build manually for deployment to Heroku

### Seed Database

You can use the following commands to seed the database with some sample users and products as well as destroy all data

```
# Import data
npm run data:import

# Destroy data
npm run data:destroy
```

```
Sample User Logins

admin@example.com (Admin)
123456

john@example.com (Customer)
123456

jane@example.com (Customer)
123456
```

# Docker Architecture Diagram

![Docker Architecture Diagram](https://github.com/taufiqpsumarna/proshop_mern_dockerized/blob/master/uploads/Docker.png)

In this project we create replicas for backend and frontend each two of them, you can modified replicas count or disable it by commented out in docker-compose.yml file:

```

...
#deploy:
#      replicas: 2
...

```

# Docker Configuration

Docker instruction created by Taufiq 😁 shout-out to @bradtraversy for the application !

``These instructions use Docker version 20.10.17, build 100c701 and Docker Compose version v2.7.0 may be different approach in future if using newer version of docker engine and docker compose.``

### Pre requirerites

1. Make sure you has created .env file on this root directory, follow instruction above or use .env.example
2. You have some knowledge about Docker *just kidding you can use this repo as your learning material

# Build Docker Images

You may want to build Docker images manually or simply skip this step for using the docker image that already uploaded to my dockerhub registry

### Build Frontend Docker Images

```
cd frontend
docker build -t <your_dockerhub_username>/proshop-frontend .
```

### Build Backend Docker Images

```
cd backend
docker build -t <your_dockerhub_username>/proshop-backend .
```

# Update docker-compose.yml

### Docker Compose Frontend Services, change to your docker image name

```
...
proshop-frontend-app:
    image: <your_dockerhub_username>/proshop-frontend:latest
...
```

### Docker Compose Frontend Services, change to your docker image name

```
...
proshop-backend-app:
    image: <your_dockerhub_username>/proshop-backend:latest
...
```

# Firing up docker container

```
#Running container foreground
docker compose up 

#Running container background
docker compose up -d
```

# How To Seed Database On Backend Container

Make sure all docker container is running up then you can access the backend container like this:

```
docker ps
docker exec -it <backend-container-name> sh

# Import data
node seeder

# Destroy data
node seeder -d
```

# SSL Nginx Reverse Proxy Guide

1. Create DNS record using Public IP docker server.
2. Update server name in nginx/nginx.conf
```
server {
    listen 80;
    server_name {domain}; #Change with your domain or subdomain ex: shop.example.com
    ...
}
...
...
...
server {
    listen 443 ssl;
    server_name {domain};  #Change with your domain or subdomain ex: shop.example.com
}
```
3. Request new certificate
    - First you may need to remove HTTPS nginx config block in nginx/nginx.conf

    ```
    ...
    #HTTPS Access Start
    ...
    ...
    ...
    #HTTPS Access End
    ...
    ```
    - Dry run certificate request ``` docker compose run --rm  certbot certonly --webroot --webroot-path /var/www/certbot/ --dry-run -d {domain} ``` replace with your own domain and you should get a success message like "The dry run was successful".
    - Then run command for request new certificate ``` docker compose run --rm certbot certonly --webroot --webroot-path=/var/www/certbot --email {email} --agree-tos --no-eff-email -d {domain} ``` replace with your own email and domain
    - After request new certificate we bring back HTTPS nginx config block in nginx/nginx.conf (Don't forget to change {domain} for nginx loading ssl certificates)

    ```
    ...
    #HTTPS Access Start
    ssl_certificate /etc/letsencrypt/live/{domain}/fullchain.pem; #Replace with your domain name
    ssl_certificate_key /etc/letsencrypt/live/{domain}/privkey.pem; #Replace with your domain name
    #HTTPS Access End
    ...
    ```

4. Fire up your application ``` docker-compose up -d ```

### Troubleshooting

- Make sure you have firewall allowing access to port 80 and port 443
- Check if there any typos in configuration files
- Run docker container with ``` docker-compose up ``` it make easier for debugging

# Notes

You may need to check the docker-compose.yml before start up the docker container, and look to docker architecture diagram first.

- If you need connect to mongodb just uncomment the following line in the docker-compose.yml file

```
...
  mongodb:
    image: mongo:4.4.11
    container_name: mongodb-srv
    volumes:
    - ./proshop-app-data:/data/db
    #Enable port if you need to connect the mongodb
--> #ports:          <--
--> #- "27017:27017" <--
    networks:
      - proshop-net
...
```

- If you want to deploy this application to your VPS or cloud VM, make sure you have updated the nginx.conf in the nginx folder before building or using the frontend image

```
...
server {
    listen 80;
    server_name localhost;  #Change with your domain or subdomain ex: shop.example.com
...
```

For more details about docker you can access the official documentation:
<https://docs.docker.com/>

## Reference

### Docker Nginx Certbot

- <https://mindsers.blog/post/https-using-nginx-certbot-docker/>
- <https://www.agiksetiawan.com/posts/how-to-setup-nginx-free-ssl-using-lets-encrypt-with-certbot-in-docker/>

## License

The MIT License

Copyright (c) 2020 Traversy Media <https://traversymedia.com>

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in
all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN
THE SOFTWARE.
