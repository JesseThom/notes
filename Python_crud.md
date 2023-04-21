# CRUD
## Setup
```
pip install pipenv
```
next installs flast mysql and bcrypt  only install what is needed
```
pipenv install flask PyMySql flask-bcrypt
```
Do not create a virtual environment within another virtual environment
```
pipenv shell
<!-- may need to use this syntax if previous causes error -->
python -m pipenv shell
<!-- next will show what libs are currently installed -->
pip list
```
## server.py
```py
from flask_app import app
from flask_app.controllers import controller_routes, controller_users # import all controllers here

if __name__ == "__main__":
    app.run(debug=True)
```
## \_\_init__.py
```py
from flask import Flask
app = Flask(__name__)
```
## mysqlconnection.py
```py
# a cursor is the object we use to interact with the database
import pymysql.cursors
# this class will give us an instance of a connection to our database
class MySQLConnection:
    def __init__(self, db):
        # change the user and password as needed
        connection = pymysql.connect(host = 'localhost',
                                    user = 'root', 
                                    password = 'root', 
                                    db = db,
                                    charset = 'utf8mb4',
                                    cursorclass = pymysql.cursors.DictCursor,
                                    autocommit = True)
        # establish the connection to the database
        self.connection = connection
    # the method to query the database
    def query_db(self, query, data=None):
        with self.connection.cursor() as cursor:
            try:
                query = cursor.mogrify(query, data)
                print("Running Query:", query)
    
                cursor.execute(query, data) #may have to remove data
                if query.lower().find("insert") >= 0:
                    # INSERT queries will return the ID NUMBER of the row inserted
                    self.connection.commit()
                    return cursor.lastrowid
                elif query.lower().find("select") >= 0:
                    # SELECT queries will return the data from the database as a LIST OF DICTIONARIES
                    result = cursor.fetchall()
                    return result
                else:
                    # UPDATE and DELETE queries will return nothing
                    self.connection.commit()
            except Exception as e:
                # if the query fails the method will return FALSE
                print("Something went wrong", e)
                return False
            finally:
                # close the connection
                self.connection.close() 
# connectToMySQL receives the database we're using and uses it to create an instance of MySQLConnection
def connectToMySQL(db):
    return MySQLConnection(db)
```
## controller_routes.py
### landing page usually index.html
```py
from flask import render_template
from flask_app import app

from flask_app.models.model_users import User

#landing page
@app.route('/')
def landing_page():
    users = User.get_all()
    print(users[0].first_name)
    return render_template("index.html",users=users)
```
## controller"table_name".py
```py
from flask import render_template, redirect, session, request
from flask_app import app

from flask_app.models.model_users import User #TODO import model file here

#route to new user form page
@app.route('/user/new')
def user_new():
    return render_template("user_new.html")

#route to submit create user form
@app.route('/user/create',methods=["post"])
def user_create():
    data = request.form
    user_id = User.create(data)
    if user_id == False:
        print("Failed to create user")
    else:
        print(f"User Created at {user_id} id")
    return redirect('/')

#route to show individual user
@app.route('/user/<int:id>')
def user_show(id):
    data = {'id': id}
    user = User.get_one(data)
    return render_template("user_show.html", user=user)

#route to edit user form
@app.route('/user/<int:id>/edit')
def user_edit(id):
    data = {'id': id}
    user = User.get_one(data)
    return render_template("user_edit.html", user=user)

#route to submit edit form
@app.route('/user/<int:id>/update',methods=['post'])
def user_update(id):
    data = {
        **request.form,
        'id':id
        }
    User.update_one(data)
    return redirect('/')

#delete user route
@app.route('/user/<int:id>/delete')
def user_delete(id):
    data = {'id': id}
    User.delete_one(data)
    return redirect("/")
```
## model"table_name".py
```py
from flask_app.config.mysqlconnection import connectToMySQL

DATABASE = "users_schema"#TODO change schema name 

class User:
    def __init__(self,data:dict):
        #for every column in table from db, must have an attribute
        self.id = data['id']
        self.first_name = data['first_name']
        self.last_name =  data['last_name']
        self.email = data['email']
        self.created_at = data['created_at']
        self.updated_at = data['updated_at']
        self.full_name = f"{self.first_name} {self.last_name}"

#C
    @classmethod
    def create(cls,data):
        #1 query statement
        query = "INSERT INTO users (first_name, last_name, email) VALUES (%(first_name)s,%(last_name)s,%(email)s);"
        #2 contact the data
        user_id = connectToMySQL(DATABASE).query_db(query, data) 
        return user_id
#R
    @classmethod
    def get_one(cls, data):
        query = "SELECT * FROM users WHERE id = %(id)s;"
        #returns list of dictionaries
        results = connectToMySQL(DATABASE).query_db(query,data)
        return cls(results[0])

    @classmethod
    def get_all(cls):
        query = "SELECT * FROM users"
        results = connectToMySQL(DATABASE).query_db(query)
        all_users = []
        for dict in results:
            all_users.append(cls(dict))
        return all_users
#U
    @classmethod
    def update_one(cls,data):
        query = "UPDATE users SET first_name = %(first_name)s,last_name =%(last_name)s,email = %(email)s WHERE id = %(id)s;"
        return connectToMySQL(DATABASE).query_db(query,data)
#D
    @classmethod
    def delete_one(cls,data):
        query = "DELETE FROM users WHERE id = %(id)s;"
        return connectToMySQL(DATABASE).query_db(query,data)
```
## html notes
```html
<!-- route examples -->
<form action="/user/{{user.id}}/update" method="post">
<a href="/user/new">
```
## Bcrypt
in your model file
```py
from flask_bcrypt import Bcrypt        
bcrypt = Bcrypt(app)
```
To generate a hash, provide the password to be hashed as an argument
```py
pw_hash = bcrypt.generate_password_hash(request.form['password'])
```
To compare passwords, provide the hash as the first argument and the password to be checked as the second argument
```py
if not bcrypt.check_password_hash(user_in_db.password, request.form['password']):
            # if we get False after checking the password
        flash("Invalid Email/Password")
        return redirect('/')
```
### Register
controllers/users.py
```py
from flask_app import app
from flask_bcrypt import Bcrypt
bcrypt = Bcrypt(app)
@app.route('/register/user', methods=['POST'])
def register():
    # validate the form here ...
    # create the hash
    pw_hash = bcrypt.generate_password_hash(request.form['password'])
    print(pw_hash)
    # put the pw_hash into the data dictionary
    data = {
        "username": request.form['username'],
        "password" : pw_hash
    }
    # Call the save @classmethod on User
    user_id = User.save(data)
    # store user id into session
    session['user_id'] = user_id
    return redirect("/dashboard")
```
models/user.py
```py
class User:
    @classmethod
    def save(cls,data):
        query = "INSERT INTO users (username, password) VALUES (%(username)s, %(password)s);"
        return connectToMySQL("mydb").mysql.query_db(query, data)
```
### Login
controllers/users.py
```py
from flask_app import app
from flask_bcrypt import Bcrypt
bcrypt = Bcrypt(app)
@app.route('/login', methods=['POST'])
def login():
    # see if the username provided exists in the database
    data = { "email" : request.form["email"] }
    user_in_db = User.get_by_email(data)
    # user is not registered in the db
    if not user_in_db:
        flash("Invalid Email/Password")
        return redirect("/")
    if not bcrypt.check_password_hash(user_in_db.password, request.form['password']):
        # if we get False after checking the password
        flash("Invalid Email/Password")
        return redirect('/')
    # if the passwords matched, we set the user_id into session
    session['user_id'] = user_in_db.id
    # never render on a post!!!
    return redirect("/dashboard")
```
models/user.py
```py
class User:
    @classmethod
    def get_by_email(cls,data):
        query = "SELECT * FROM users WHERE email = %(email)s;"
        result = connectToMySQL("mydb").query_db(query,data)
        # Didn't find a matching user
        if len(result) < 1:
            return False
        return cls(result[0])
```