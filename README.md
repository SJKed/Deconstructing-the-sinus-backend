#A Deconstruction of the SINUS webshop backend for the SINUS project
In this repository, we will go over the technical details of the SINUS webshop backend. The backend is known as what's called a RESTful API. Being a RESTful API just means that it is a web service that can be accessed via HTTP requests. We will be going over the technical details being utilized, the order of operations and the architecture of the applicaiton as a whole.

## Getting familiar
Starting off, a good entry point is always the [package.json](sinus-backend/package.json) file. As it contains all the relevant libraries and dependencies that the application utilizes. What's the most interesting to us are the "scripts", which are the command line commands that are used to run the application. As well as the "dependencies" which are the libraries that are used by the application.
### Scripts
```
"scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "setup": "node database/setup.js",
    "seed": "node database/seed.js",
    "start": "node app.js",
    "dev": "nodemon app.js"
  },
```
The scripts segment contains 5 different commands, 4 of which are of actual relevence. The "setup" and "seed" commands are telling us that is the highest likelyhood, that the database requires us to first initialize the database structure; usually with a "setup" script. Then yield it basic information using a seed script. The "start" and "dev" commands are bascially the same thing, with the exception that "dev" will run a nodemon command, which will automatically restart the application when changes are made to the code.
### Dependencies
```
"dependencies": {
    "bcryptjs": "^2.4.3",
    "cors": "^2.8.5",
    "dotenv": "^8.2.0",
    "express": "^4.17.1",
    "express-fileupload": "^1.3.1",
    "express-validator": "^6.14.0",
    "jsonwebtoken": "^8.5.1",
    "sequelize": "^6.16.1",
    "sqlite3": "^5.0.2",
    "uuid": "^3.3.3"
  },
```
The dependencies segment contains all the external libraries that the application uses. 
* [bcryptjs](https://www.npmjs.com/package/bcryptjs): Used for hashing passwords.
* [cors](https://www.npmjs.com/package/cors): Used for allowing cross-origin requests.
* [dotenv](https://www.npmjs.com/package/dotenv): Used for loading environment variables.
* [express](https://www.npmjs.com/package/express): A Fast, unopinionated, minimalist web framework for node.
* [express-fileupload](https://www.npmjs.com/package/express-fileupload): Used for uploading files.
* [express-validator](https://www.npmjs.com/package/express-validator): Used to validate server requests.
* [jsonwebtoken](https://www.npmjs.com/package/jsonwebtoken): Used for creating and verifying tokens.
* [sequelize](https://www.npmjs.com/package/sequelize): A Node.js ORM for Postgres, MySQL, MariaDB, SQLite and Microsoft SQL Server.
* [sqlite3](https://www.npmjs.com/package/sqlite3): Used for interacting with the SQLite database.
* [uuid](https://www.npmjs.com/package/uuid): Used for generating unique identifiers.

---

Next up we'll take a look at the main app.js file to get a better understanding of the application. 
```
const express = require('express');
const routes = require('./routes');
const cors = require('cors');
const Logger = require('./middleware/Logger');
const _404 = require('./routes/404');
const {errorHandler} = require('./errors/errorHandler')

// Initialize Models
require('./models')()

const app = express();

// Middleware
app.use( cors() )
app.use( Logger )
app.use( express.static('public') )
app.use( express.json() );
app.use( express.urlencoded({ extended: true }) );

// Routes
app.use('/api', routes.auth);
app.use('/api/items', routes.products);
app.use('/api/orders', routes.orders);
app.use('/api/images', routes.images)
app.use( errorHandler )
app.use( _404 )


const PORT = process.env.PORT || 5000;
app.listen(PORT, () => console.log(`SINUS API running on port ${PORT}`));
```
At the top of the file we see that it's importing the express and cors library, as well as some custom made middleware, routes, and error handlers.
We'll take a closer look at these when they come up. Initally the program  starts by requiring the models.js file, which is where we initialize the database [models](sinus-backend/models/index.js), which is the connector between the tables that we will be using in the application. Next up we're declaring an express instance and assigning it to the app variable.
Next we're telling the app which middlewares we want to use. Here we're telling it to use the [cors](https://www.npmjs.com/package/cors) and [Logger](sinus-backend/middleware/Logger.js) middlewares. The logger middleware is a simple function that console.logs the request and response information. 
Following we're telling the app to use the [express.static](https://expressjs.com/en/4x/api.html#express.static) middleware to serve static files, [express.json](https://expressjs.com/en/4x/api.html#express.json) to parse(analyse) the request body as JSON, and [express.urlencoded](https://expressjs.com/en/4x/api.html#express.urlencoded) to parse the request body as a URL encoded string.

Next up we're declaring our routes. The routes are the endpoints that the application will respond to. The routes are defined in the [routes](sinus-backend/routes/index.js) file.