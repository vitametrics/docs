# Installation

Follow these steps to get Vitametrics set up on your local machine.

## Prerequisites

- Node.js and npm installed
- MongoDB installed and running
- Docker (optional)

## Steps


1. **Clone the respository**


```bash
git clone https://github.com/vitametrics/vitametrics.git
cd vitametrics
```


2. **Set up environment variables:**


Copy your .env.save to .env: 
```bash

cp .env.save .env

```
Then, open that .env file in a text editor of your choosing. Your .env should look something like this:


```sh
NODE_ENV='production'
WEB_PORT='5173'
API_PORT='7970'
DB_PORT='32265'
MONGODB_URI='mongodb://vitametrics:vitametrics@vitametrics-mongo:27017/vitametrics'
FITBIT_CLIENT_ID='fitbitclientidhere'
FITBIT_CLIENT_SECRET='fitbitclientsecrethere'
BASE_URL='https://yoursite.com'
API_URL='https://yoursite.com/api'
REDIRECT_URI='https://yoursite.com/api/callback'
SESSION_SECRET='sessionsecrethere'
SENDGRID_API_KEY='apikeyhere'
SENDGRID_FROM='sender@example.com'
ADMIN_EMAIL='email@example.com'
```
Here is a walkthrough of which variables are important.

- **WEB_PORT**: This is the port that will be mapped to the frontend on the host machine. (This is the port that the website "runs on"). Can be ignored if not using Docker.

- **API_PORT**: This is the port that will be mapped to the backend (API) on the host machine. (This is the port that the API "runs on"). Can be ignored if not using Docker.

- **DB_PORT**: This is the port that will be mapped to the database on the host machine. (This is the port that teh database "runs on"). Can be ignored if not using Docker.

- **MONGODB_URI**: This must be a valid MongoDB connection string. This is what Vitametrics will use to store data. If you are using Docker, there is no need to change this value.

- **FITBIT_CLIENT_ID, FITBIT_CLIENT_SECRET, REDIRECT_URI**: These are values that Vitametrics will use to fetch data from the Fitbit API. It will be necessary to create a new application with FitBit using [this](https://dev.fitbit.com/apps) link. You must ensure that the redirect URI that is set within the FitBit application matches exactly with the URI used in your .env here. Vitametrics also requires that you have Intraday API access which can be applied to using [this](https://dev.fitbit.com/build/reference/web-api/intraday/) link.

- **BASE_URL**: This is the URL that your website will run. (Example: https://vitametrics.org)

- **API_URL**: This is the URL that your API will run. (Example: https://vitametrics.org/api)

- **SESSION_SECRET**: This is a secure random string that will be used for user authentication. **CHANGE THIS VALUE**

- **SENDGRID_API_KEY, SENDGRID_FROM**: These can be configured if you would like to use [SendGrid](https://sendgrid.com/en-us) for email notifications. **Currently the app will error and crash if these are not set!**

- **ADMIN_EMAIL**: This is the email for the instance owner, used for notifications and initial account creation. If this is not set, you will be unable to access your instance. **CHANGE THIS VALUE**


## **Docker**

If you plan on using docker to deploy Vitametrics, follow these steps.

Once your .env is configured, ensure that [Docker](https://docs.docker.com/engine/install/) is installed and running on your machine.

```bash
cd /wherever/you/installed/vitametrics
```

Then run:

```bash
docker compose up --build
```

Or, alternatively,

```bash
docker compose up --build -d
```

## **Not Docker**

If you plan on using Vitametrics without docker, follow these steps.

From wherever you clone Vitametrics, run the following commands:

```bash
npm install
```
This will install all necessary dependencies for Vitametrics.

To use Vitametrics in a production setting, it is recommended to deploy it using something like [pm2](https://pm2.keymetrics.io/) or another process manager to ensure that your uptime is stable. Additionally, if you are not using Docker, you will need to configure your **MONGODB_URI** to match that of your database.


## NGINX Configuration

To use Vitametrics, a valid TLD is required. This means that for your Vitametrics to work, you must have a site that you can visit on the internet. If you do not have a site setup like that and Vitametrics is unable to communicate with Fitbit, you will be unable to get any data from Fitbit's servers.

If you are using NGINX, now would be a good time to configure your proxy setup. We will be assuming that you are using a Linux distribution. The commands shown will be targeting Debian 11. We will also assume that you know how to install and set up and [SSL certificate](https://www.digitalocean.com/community/tutorials/how-to-secure-nginx-with-let-s-encrypt-on-debian-11) for your domain.

Here is an example NGINX configuration: 

```conf
server {
    listen 80;
    listen 443 ssl;
    listen [::]:443 ssl;
    server_name example.com;

    ssl_certificate /path/to/your/ssl/cert/site.pem;
    ssl_certificate_key /path/to/your/ssl/cert/site.key.pem;

    location / {
        proxy_pass http://localhost:YOUR_WEB_PORT_HERE/;
        proxy_http_version 1.1;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto https;
    }

    location /api/ {
        proxy_pass http://localhost:YOUR_API_PORT_HERE/;
        proxy_http_version 1.1;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto https;
    }
}
```

