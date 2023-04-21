- create project folder
- create server folder inside project folder
- open terminal 
- cd to server folder

- npm init -y
- npm install express mongoose cors
- mkdir config controllers modules routes


- open folder in vscode
- create server.js file

```js
const express = require("express");
const cors = require("cors")
const port = 8000;

const app = express();
app.use(express.json(), express.urlencoded({ extended: true }));
app.use(cors())

require("./config/mongoose.config");
require("./routes/routes")(app); // may need to adjust routes here

// must be at the bottom
app.listen(port, () => console.log(`Listening on port: ${port}`))
```

create mongoose.config.js file inside config folder
```js
const mongoose = require('mongoose');

mongoose.connect('mongodb://127.0.0.1:27017/my_db', {//change database name here
    useNewUrlParser: true,
    useUnifiedTopology: true
})
    .then(() => console.log('Established a connection to the database'))
    .catch(err => console.log('Something went wrong when connecting to the database ', err));
```
create model file in model folder
```js
const mongoose = require('mongoose')
//change schema name
const ShelterSchema = new mongoose.Schema({
        name: {
        type: String,
        required: [true, "Name is required"],
        minlength: [3, "Name must be at least 3 characters long"]
    }
    },{timestamps: true});
//change next line where it says shelter to match schema name
module.exports.Shelter = mongoose.model("Shelter", ShelterSchema);
```
create routes.js file in routes folder
```js
const Controller = require('../controllers/controller');

//in the routes change shelter to make sence for your project
module.exports = (app) => {
    app.get('/api', Controller.index)//test route
    app.get('/api/shelter/:id', Controller.findOne)//find one
    app.get('/api/shelters', Controller.findAll)//find all
    app.post('/api/shelter/new', Controller.create)//create route
    app.put('/api/shelter/edit/:id', Controller.updateOne)//update one route
    app.delete('/api/shelter/:id', Controller.deleteOne)//delete one route
}
```
create controller.js file in controllers folder
```js
const { model } = require('mongoose');
//change shelter to what make sence for your project 17 times on this page
const { Shelter } = require('../modules/model')

//test route
module.exports.index = (req, res) => {
    res.json({
        message: "Hello from shelters"
    })
}

//create one
module.exports.create = (req,res)=> {
    Shelter.create(req.body)
    .then(shelter => res.json(shelter))
    .catch(err => res.status(400).json(err))
}

//find all
module.exports.findAll = (req, res) => {
    Shelter.find()
        .then((shelters) => {
            res.json(shelters)
        })
        .catch((err) => {
            res.json({ message: 'Something went all wrong', error: err })
        });
}

//find one
module.exports.findOne = (req, res) => {
    Shelter.findOne({ _id: req.params.id })
        .then(shelter => {
            res.json({ shelter })
        })
        .catch((err) => {
            res.json({ message: 'Something went one wrong', error: err })
        });
}

//update one
module.exports.updateOne = (req, res) => {
    Shelter.findOneAndUpdate(
        { _id: req.params.id },
        req.body,
        { new: true, runValidators: true }
    )
        .then(updatedShelter => {
            res.json({shelter: updatedShelter})
        })
        .catch((err) => {
            res.status(400).json(err)
        });
}

//delete one
module.exports.deleteOne = (req, res)=> {
    Shelter.deleteOne({ _id: req.params.id })
    .then(result => {
        res.json({ result: result })
    })
    .catch((err) => {
        res.json({ message: 'Something went  wrong', error: err })
    });
}
```
- run nodemon to test server in cmd promt does not work in powershell
- nodemon server.js
- CTRL+C to end nodemon
- cd back into project folder then install react

- cd ..
- npx create-react-app client
- cd client
- npm install axios bootstrap react-router-dom

open client/src/index.js
wrap app with browser router
```js
//before
  <React.StrictMode>
    <App />
  </React.StrictMode>
//after
import { BrowserRouter } from 'react-router-dom';


  <React.StrictMode>
    <BrowserRouter>
    <App />
    </BrowserRouter>
  </React.StrictMode>
```
- nodemon server.js -- from server folder
- npm run start -- from client folder