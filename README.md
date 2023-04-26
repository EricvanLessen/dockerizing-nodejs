# Dockerizing Node.js and PostgreSQL



Docker is a tool used to package and deploy applications in a containerized environment. It allows developers to create a consistent and portable environment for their applications, which can be easily deployed across different systems. In this guide, we will discuss how to dockerize a Node.js application with a PostgreSQL database.

### Prerequisites

Before we start, make sure you have the following tools installed on your system:

- Docker
- Node.js
- PostgreSQL

### Step 1: Create a Node.js Application

The first step is to create a Node.js application. We will use the Express.js framework to create a simple application.

1. Open a terminal window and navigate to the directory where you want to create the application.
2. Run the following command to create a new Express.js application:

```
npx express-generator --no-view myapp
```

This command will create a new Express.js application named `myapp` in the current directory.

3. Navigate to the `myapp` directory:

```
cd myapp
```

4. Install the dependencies:

```
npm install
```

5. Run the application:

```
npm start
```

This will start the application on `http://localhost:3000/`.

### Step 2: Install the PostgreSQL Driver

To connect to the PostgreSQL database, we need to install the `pg` package. Run the following command to install it:

```
npm install pg --save
```

### Step 3: Create a PostgreSQL Database

We will create a PostgreSQL database using the `createdb` command-line tool. Run the following command to create a new database:

```
createdb myapp
```

### Step 4: Configure the Application

To connect to the PostgreSQL database, we need to modify the `app.js` file. Add the following code at the top of the file:

```javascript
const { Pool } = require('pg');
const pool = new Pool({
  connectionString: process.env.DATABASE_URL,
  ssl: {
    rejectUnauthorized: false
  }
});
```

This code creates a new connection pool using the `pg` package and sets the connection string to the `DATABASE_URL` environment variable.

Next, replace the following line of code:

```javascript
var usersRouter = require('./routes/users');
```

with the following:

```javascript
const { getDb } = require('./database');
const usersRouter = require('./routes/users')(getDb());
```

This code loads the `users` router and passes the database connection to it.

### Step 5: Create a Dockerfile

The next step is to create a Dockerfile. The Dockerfile is used to build a Docker image of our application. Create a new file named `Dockerfile` in the root directory of the project and add the following code:

```dockerfile
FROM node:14-alpine

WORKDIR /app

COPY package*.json ./

RUN npm install --production

COPY . .

EXPOSE 3000

CMD ["npm", "start"]
```

This code specifies a base image, sets the working directory, installs the dependencies, copies the application code, exposes the port, and starts the application.

### Step 6: Create a docker-compose.yml file

The next step is to create a docker-compose.yml file. This file is used to define the services that make up our application. Create a new file named `docker-compose.yml` in the root directory of the project and add the following code:

```yaml
version: '3'

services:
  db:
    image: postgres:latest
    environment:
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: postgres
      POSTGRES_DB: myapp
    ports:
      - "5432:5432

  app:
    build: .
    environment:
      DATABASE_URL: postgres://postgres:postgres@db:5432/myapp
    ports:
      - "3000:3000"
    depends_on:
      - db
```

This code defines two services: `db` and `app`. The `db` service uses the official PostgreSQL image and sets the username, password, and database name. The `app` service builds the Docker image using the Dockerfile in the current directory and sets the `DATABASE_URL` environment variable to the URL of the PostgreSQL database.

### Step 7: Start the Application

To start the application, run the following command in the root directory of the project:

```
docker-compose up
```

This command will start the application and the PostgreSQL database. You can access the application at `http://localhost:3000/`.

### Conclusion

In this guide, we have discussed how to dockerize a Node.js application with a PostgreSQL database. Docker provides a simple and portable way to package and deploy applications, making it easier to manage the development and deployment of complex systems.
      
