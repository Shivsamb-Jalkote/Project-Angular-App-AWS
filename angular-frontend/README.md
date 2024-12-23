## $${\color{blue} \textbf{Set} \textbf{Up}  \ \textbf{Angular} \ \textbf{Frontend}}$$

This guide provides step-by-step instructions for setting up an Angular project from scratch on an Ubuntu machine.
### Install AWS CLI 
````
sudo apt install unzip -y
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install
````
````
aws configure
````

## Step 1: Install Node.js and npm

```bash
sudo apt update
sudo apt install nodejs npm
```
## Step 2: Check Node.js and npm versions

```bash
node -v
npm -v
```
## Step 3: Install Angular CLI globally
```bash
sudo npm install -g @angular/cli@14.2.1
```
## Step 4: Verify Angular CLI installation
```bash
ng version
```
## Step 5: Connect to backend server
````
cd src/app/services
sudo nano worker.service.ts
````
Note: add public-ip of backend server

## Step 6: Install project dependencies (if needed)

```bash
npm install
ng build 
sudo ng serve --host 0.0.0.0 --port=80
```

![Screenshot (652)](https://github.com/user-attachments/assets/bbfdceba-7c06-439c-96cc-3c96a536b44b)


## Step7: Go to browser and hit public-ip of frontend instance


![Screenshot (653)](https://github.com/user-attachments/assets/1991cc82-159e-4328-9cdf-e3525fb00f08)



## Step 8: Deploy the artifact to s3 bucket
````
cd dist/angular-frontend
aws s3 mb s3://angular-frontend-app-buck
aws s3 cp .  s3://angular-frontend-buck --recursive
````
## Step 9: Create Cloudfront Distribution

![Screenshot (655)](https://github.com/user-attachments/assets/f06ff5a5-f543-4a8f-8340-6834df9dfc03)





## Step 10: Create Hosted zone in route:53
![hosted zone](https://github.com/abhipraydhoble/Project-Angular-App/assets/122669982/cc0abb81-4184-4dcd-b159-3b0e35f7a2d3)

## Step 11: ADD NS records in hostinger


## Step 12: Generate SSL certificate and create records in s3

![ssl](https://github.com/abhipraydhoble/Project-Angular-App/assets/122669982/d31be2ab-1dfa-4d1a-949b-7aa2ca82c0e0)

## Step 13: Attach SSL to cloudfront distribution


## Step 14: Create A record in RT-53 and add cloufront distribution

![a-record](https://github.com/abhipraydhoble/Project-Angular-App/assets/122669982/fc902838-ad31-487c-a92d-51316ccd4648)

## Step 15: Final Result

![domain-frontend](https://github.com/abhipraydhoble/Project-Angular-App/assets/122669982/083c3e34-543b-4a9e-a40a-bb06b1beea2a)

# Angular Project Dockerization Guide

This guide provides step-by-step instructions for Dockerizing an existing Angular project. Docker allows you to package your Angular application into a container, making it portable and easily deployable across different environments.

## Prerequisites

- Docker installed on your system. You can download and install Docker Desktop from [here](https://docs.docker.com/engine/install/).

## Dockerfile Setup

Create a `Dockerfile` in the root directory of your Angular project with the following content:

```Dockerfile
# Use official Node.js image as the base image
FROM node:14-alpine as build

# Set the working directory in the container
WORKDIR /usr/src/app

# Copy package.json and package-lock.json (if available)
COPY package*.json ./

# Install project dependencies
RUN npm install

# Copy the rest of the application code
COPY . .

# Build the Angular application
RUN npm run build 

# Use NGINX as the production server
FROM nginx:alpine

# Copy the built artifact from the previous stage to NGINX web server directory
COPY --from=build /usr/src/app/dist/angular-frontend /usr/share/nginx/html

# Expose port 80 to the outside world
EXPOSE 80

# Start NGINX server when the container starts
CMD ["nginx", "-g", "daemon off;"]
