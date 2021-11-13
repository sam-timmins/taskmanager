# Run a Basic Flask Application

* Install the python packages into the terminal


```
pip3 install Flask-SQLAlchemy psycopg2
```

* Create a ```env.py``` file in the root directory
* Check that the .gitignore file includes the ```env.py``` file
* Within the ```env.py``` file:
    * Set up default environment variables

```py

import os


os.environ.setdefault("IP", "0.0.0.0") 
os.environ.setdefault("PORT", "5000") # For Flask applications
os.environ.setdefault("SECRET_KEY", "any_secret_key") # Any random string
os.environ.setdefault("DEBUG", "True") # Set to True but change to False before submission of a project
os.environ.setdefault("DEVELOPMENT", "True") #  This is used to distinguish between the local environment and the deployed application
os.environ.setdefault("DB_URL", "postgresql:///taskmanager") # Points to the database, when working locally, use the Postgres environment

``` 

* Create a python package (folder) in the root directory, in this case it's called 'taskmanager'
* Within the 'taskmanager' package create the ```__init__.py``` file. This file initializes the taskmanager application as a package, allowing it to be used
in your own imports, as well as any standard imports
* In the ```__init__.py``` file:

```py

import os
from flask import Flask
from flask_sqlalchemy import SQLAlchemy
# Import 'env' if the OS can find an existing file path for the env.py file
if os.path.exists("env.py"):
    import env # noqa


# Create instance of the imported Flask() class
app = Flask(__name__)


# app configuration variables
app.config["SECRET_KEY"] = os.environ.get("SECRET_KEY")
app.config["SQLALCHEMY_DATABASE_URI"] = os.environ.get("DB_URL")


# Create instance of the imported SQLAlchemy() class assigned to a variable 
# and set to the instance of the FLask 'app'
db = SQLAlchemy((app))


# Our own file relies on the db and app variables,
# so must be declared at the end, routes is the file that includes the
# Flask navigation links 
from taskmanager import routes # noqa
``` 


* Create a ```routes.py``` file in the taskmanager package.

```py
from flask import render_template
from taskmanager import app, db

# returns the html page of base
@app.route("/")
def home():
    return render_template("base.html")

```


* Create a ```run.py``` file in the root directory from the terminal
```
touch env.py
```
* Within the ```run.py``` file:

```py
import os
from taskmanager import app


# How and where to run the application
if __name__ == "__main__":
    app.run(
        host=os.environ.get("IP"),
        port=int(os.environ.get("PORT")),
        debug=os.environ.get("DEBUG")
    )

```

## Basic application should be running

* Create a folder within the taskmanager package called 'templates'
* Within templates create a base.html file
* To check in the terminal eter 

```
python3 run.py
```


# Create the database schema
### Defining mnodules

* Create a ```modules.py``` file within the taskmanager package

