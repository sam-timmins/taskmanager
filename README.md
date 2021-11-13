# Flask Folder Structure

* taskmanager (package in the root directory)
    * static
        * css
            * style.css
        * js
            * script.js
        * images
    * templates
        * base.html
        * login.html
    * ```__init__.py```
    * ```modules.py```
    * ```routes.py.py```

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
### Defining modules

* Create a ```modules.py``` file within the taskmanager package

* Create class based tables
```py
from taskmanager import db


class Category(db.Model):
    # schema for the Category model
    id = db.Column(db.Integer, primary_key=True)
    # string with max length of 25, the string has to be unique and can't be empty
    category_name = db.Column(db.String(25), unique=True, nullable=False)
    tasks = db.relationship("Task", backref="category", cascade="all, delete", lazy=True)

    def __repr__(self):
        # __repr__ to represent itself in the form of a string
        return self.category_name


class Task(db.Model):
    # schema for the Task model
    id = db.Column(db.Integer, primary_key=True)
    # string with max length of 50, the string has to be unique and can't be empty
    task_name = db.Column(db.String(50), unique=True, nullable=False)
    # textarea that can't be empty
    task_description = db.Column(db.Text, nullable=False)
    # true/false with default set to false and can't be empty
    is_urgent = db.Column(db.Boolean, default=False, nullable=False)
    # date field that can't be empty
    due_date = db.Column(db.Date, nullable=False)
    # a integer that is a foreign key. The value is the id frim the category table
    # if the category id is deleted, the task associated will be deleted
    category_id = db.Column(db.Integer, db.ForeignKey("category.id", ondelete="CASCADE"), nullable=False)


    def __repr__(self):
        # __repr__ to represent itself in the form of a string
        return "#{0} - Task: {1} | Urgent: {2}".format(
            self.id, self.task_name, self.is_urgent
        )
```


* Within the terminal login to the Postgres CLI by entering
```
psql
```

* Create a database by entering
```
postgres=# CREATE DATABASE taskmanager;
```

* Switch over to that connection 
```
\c taskmanager;

You are now connected to database "taskmanager" as user "gitpod".
taskmanager=# 
```
* Exit from the Postgres CLI by entering
```
\q
```


## Generate and Migrate Models to the database

* Access the python interpreter in the terminal by entering
```
python3
```

* import db from the taskmanager package

```
from taskmanager import db
```

* import db from the taskmanager package

```
from taskmanager import db
```

* populate the database with the tables from ```models.py``` and their relationships
```
db.create_all()
```

* Exit the python interpreter
```
exit()
```

* To check thety exist
```
psql -d taskmanager
```

* Then 
```
\dt
```

* Exit from the Postgres CLI by entering
```
\q
```