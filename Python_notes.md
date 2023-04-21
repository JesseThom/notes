## for loops
```py
#python
num = [1,2,3,4]
for i in num:
    print(i)
#outcome is values
1
2
3
4

string = "hello"
for i in string:
    print(i)
#outcome is values
h
e
l
l
o

#  range(range start, range end(exclusive),iterator)
for i in range(len(nums))
    print(i)
#outcome is index
0
1
2
3
```
```js
//javascript
var num = [1,2,3,4]
for(let i = 0;i < num.length;i++){
    console.log(num[i]);
}
//outcome is values
1
2
3
4

for(let i = 0; i < 4;i++){
    console.log(i);
}
//outcome is index
0
1
2
3
```
##  list
```py
my_list = []
my_list.append("item") # .push() in js
my_list.append(7)
#outcome
my_list["item",7]
#call list
print(my_list[0])
#prints -- "item"
```

##  dictionary
```py
capitals = {} #create an empty dictionary then add values
capitals["svk"] = "Bratislava"
capitals["deu"] = "Berlin"
#outcome
capitols{"svk": "Bratslava","deu": "Berlin"}
#call dictionary
print(capitols["svk"])
#prints -- "Bratslava"
```
# import links for flask
## css
```py
<link rel="stylesheet" type="text/css" href="{{ url_for('static', filename='css/style.css') }}">
```
## javascript
```py
<script type="text/javascript" src="{{ url_for('static', filename='js/script.js') }}"></script>
```
## images
```py
<img src="{{ url_for('static', filename='imgs/my_img.png') }}">
```
## route example
```py
@app.route("/<int:row>/<int:col>")
def multi_row_col(row,col):
    return render_template("multi_row_col.html", row=row, col=col)
```
## redirect/post example
```py
#python
@app.route("/route from form action", methods=["post"])
def submit():
    return redirect("/route to redirect to")
```
```html
<!-- html post req -->
<form action="/route" method="post">
    <button>Click</button>
</form>
```
To use sessions in Flask, we are also required to give our app a secret key:
```py
app = Flask(__name__)
app.secret_key = 'its a secret to everybody' # set a secret key for security purposes
```
# request form data
```html
<!-- html -->
<form action="/my_route" method="post">
    <input type="number" id="num" name = "num"> <!--number example-->
    <input type="text" id="name" name ="name"> <!--text example-->
    <button>Button text</button>
</form>
```
```py
#python
@app.route("/my_route", methods=["post"])
def request():
    print(request.form)
    #from above html schoul print in terminal
    # ImmutableMultiDict([('num', '','name', '')])
    print(request.form['num']) # to retireve data from dictionary
    return redirect("/") # could also be return render_template("example.html")
```
# session
```py
#ask politely if name is in session
if'name' in session:
    #is 'name' exhist in session do something 
    #example
    del session['name'] # deletes the "name' key

#also ask if name is not in session
if 'name' not in session:
    name = 'jesse'
else:
    name = session['name']

#can redirect useing "f" string
return redirect (f'/route/{color}')
```
## Install PyMySQL and flask every project
```
--run in terminal--
...new_project_folder> pipenv install PyMySQL flask
```
## file structure
assignment_folder
1. pipfile
1. pipfile.lock
1. server.py
1. flask_app FOLDER
    - \_\_init\__.py
    - config FOLDER
        - mysqlconnection.py
    - controllers FOLDER
        - controller_routes.py `extra file`
        - `for every table make a controller file`
        - controller_user.py
        - controller_pay_stub.py
    - models FOLDER
        - `for every table make a model file`
        - model_user.py
        - model_pay_stub.py
    - templates FOLDER
        - index.html
    - static FOLDER
        - imgs FOLDER
        - js FOLDER
            - script.js - global
        - css FOLDER
            - style.css


## server.py
```py
from flask import Flask, render_template
# import the model file class from friend.py
from friend import Friend #import model file class here
app = Flask(__name__)
@app.route("/")
def index():
    friends = Friend.get_all()
    print(friends)#prints memory location of users
    print(friends[0].first_name)#prints first users first name
    return render_template("index.html", friends=friends)
            
if __name__ == "__main__":
    app.run(debug=True)
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
## model.py -file thats interacts with database
```py
# import the function that will return an instance of a connection
from mysqlconnection import connectToMySQL
# model the class after the friend table from our database
class Friend:
    def __init__( self , data ):
        self.id = data['id']
        self.first_name = data['first_name']
        self.last_name = data['last_name']
        self.occupation = data['occupation']
        self.created_at = data['created_at']
        self.updated_at = data['updated_at']
    # Now we use class methods to query our database
    @classmethod
    def get_all(cls):
        query = "SELECT * FROM friends;"
        # make sure to call the connectToMySQL function with the schema you are targeting.
        results = connectToMySQL('first_flask').query_db(query)
                                #^^schema name^^
        # Create an empty list to append our instances of friends
        friends = []
        # Iterate over the db results and create instances of friends with cls.
        for dict in results:
            friends.append( cls(dict) )
        return friends # returns a list of objects
            
```
## controller_"table".py
```py
from flask_app import app
from flask import render_template, redirect, request, session
from flask_app.models.model_user import User #class from model"table" file
#change model_user to match file which would match table from mysql

@app.route('/user/new')
def user_new():
    return render_template("user_new.html")

@app.route('/user/create',methods["post"])
def user_create():
    return redirect("/")

@app.route('/user/delete')
def user_delete():
    return render_template("user_delete.html")
```
## RESTful route names
### never render on action route!! 
### CRUD
### Create
'/user/new' -> Display route, render the page that has the form to create a new user<br>
'/user/create' -> Action route, this will process the form from the '/user/new' route
### Read
'/user/\<int:id> -> Display route, displays someones profile<br>
'/user/\<int:id>/edit -> Display route, render the page that has the form to change the users info
### Update
'/user/\<int:id>/update -> Action route, process the form from the edit page<br>
### Delete
'/user/\<int:id>/delete -> Action route, delete user from db at the id that is being passed