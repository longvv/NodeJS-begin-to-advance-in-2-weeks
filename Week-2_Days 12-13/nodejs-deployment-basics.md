# Node.js Deployment Basics: Environment Variables, PM2, Docker, and Cloud Platforms

## 1. Environment Variables

Environment variables are used to configure your application for different environments (development, staging, production) and to store sensitive information.

### Setting Up Environment Variables

1. Install dotenv:
   ```bash
   npm install dotenv
   ```

2. Create a `.env` file in your project root:
   ```
   PORT=3000
   DATABASE_URL=mongodb://localhost:27017/myapp
   API_KEY=your-secret-api-key
   ```

3. Load environment variables in your app:
   ```javascript
   require('dotenv').config();

   const app = express();
   app.listen(process.env.PORT, () => {
     console.log(`Server running on port ${process.env.PORT}`);
   });
   ```

4. Use environment variables:
   ```javascript
   const dbConnection = mongoose.connect(process.env.DATABASE_URL);
   ```

### Best Practices
- Never commit `.env` files to version control
- Use different `.env` files for different environments (e.g., `.env.production`, `.env.development`)
- Provide a `.env.example` file with dummy values for reference

## 2. Process Managers (PM2)

PM2 is a process manager for Node.js applications that helps you keep your app running forever, reload it without downtime, and manage log files.

### Setting Up PM2

1. Install PM2 globally:
   ```bash
   npm install -g pm2
   ```

2. Start your application with PM2:
   ```bash
   pm2 start app.js --name "my-app"
   ```

3. Other useful PM2 commands:
   ```bash
   pm2 list                  # List all processes
   pm2 stop my-app           # Stop an application
   pm2 restart my-app        # Restart an application
   pm2 logs my-app           # Display logs
   pm2 monit                 # Monitor CPU/Memory usage
   ```

### Using PM2 Configuration File

Create a `ecosystem.config.js` file:

```javascript
module.exports = {
  apps: [{
    name: "my-app",
    script: "./app.js",
    instances: "max",
    env: {
      NODE_ENV: "development",
    },
    env_production: {
      NODE_ENV: "production",
    }
  }]
}
```

Start your app using this configuration:
```bash
pm2 start ecosystem.config.js
```

## 3. Containerization with Docker

Docker allows you to package your application and its dependencies into a container for easy deployment and scaling.

### Creating a Dockerfile

1. Create a `Dockerfile` in your project root:

```Dockerfile
FROM node:14

WORKDIR /usr/src/app

COPY package*.json ./

RUN npm install

COPY . .

EXPOSE 3000

CMD [ "node", "app.js" ]
```

2. Build your Docker image:
   ```bash
   docker build -t my-node-app .
   ```

3. Run your Docker container:
   ```bash
   docker run -p 3000:3000 -d my-node-app
   ```

### Using Docker Compose

Create a `docker-compose.yml` file for multi-container applications:

```yaml
version: '3'
services:
  app:
    build: .
    ports:
      - "3000:3000"
    environment:
      - NODE_ENV=production
    depends_on:
      - db
  db:
    image: mongo
    volumes:
      - ./data:/data/db
```

Start your application:
```bash
docker-compose up -d
```

## 4. Cloud Platforms

### Heroku

1. Install the Heroku CLI and login:
   ```bash
   heroku login
   ```

2. Create a Heroku app:
   ```bash
   heroku create my-node-app
   ```

3. Add a `Procfile` to your project root:
   ```
   web: node app.js
   ```

4. Deploy your app:
   ```bash
   git push heroku main
   ```

5. Set environment variables:
   ```bash
   heroku config:set NODE_ENV=production
   ```

### AWS Elastic Beanstalk

1. Install the AWS EB CLI and configure it.

2. Initialize your EB project:
   ```bash
   eb init
   ```

3. Create an environment:
   ```bash
   eb create my-node-env
   ```

4. Deploy your application:
   ```bash
   eb deploy
   ```

### DigitalOcean App Platform

1. Create an account on DigitalOcean and install `doctl` CLI.

2. Authenticate `doctl`:
   ```bash
   doctl auth init
   ```

3. Create an app:
   ```bash
   doctl apps create --spec app-spec.yaml
   ```

4. Deploy your app:
   ```bash
   doctl apps update <app-id> --spec app-spec.yaml
   ```

## Best Practices for Deployment

1. Use a CI/CD pipeline for automated testing and deployment.
2. Implement logging and monitoring solutions.
3. Use a reverse proxy like Nginx in production.
4. Implement proper error handling and graceful shutdowns.
5. Regularly update dependencies and apply security patches.
6. Use SSL/TLS for all production traffic.
7. Implement database migrations for schema changes.
8. Use a CDN for serving static assets.

By following these deployment basics, you can ensure a smoother, more reliable deployment process for your Node.js applications across various platforms and environments.
