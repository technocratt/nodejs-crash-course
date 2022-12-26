# NodeJS crash course

### Contents

1. Installation
2. Language blocks - part 1
3. Language blocks - part 2
4. File operations
5. Web API
6. Database
7. Invoking HTTP calls
8. Parallel programming
9. Unit testing

### 1. Installation

- NodeJS: [https://nodejs.org/en/download](https://nodejs.org/en/download)
- VS Code: [https://code.visualstudio.com/download](https://code.visualstudio.com/download)
- Postman: [https://www.postman.com/downloads](https://www.postman.com/downloads)

### 2. Language blocks - part 1

**Greet**
```javascript
console.log('Hello World!!!');
```

**Variables**
Variables defined with var can be redeclared.

```javascript
var x = 'John Doe';
var x = 0;
console.log(x);
```

Variables defined with let cannot be redeclared.
```javascript
let y = 'John Doe';
y = 0;
console.log(y);
```

A const variable cannot be reassigned:
```javascript
const z = 10;
z = 20;
console.log(z);
```

**Operators**

```javascript
var a = 3;
a = (100 + 50) * a;
console.log(a);
```

**Data types**

```javascript
//// Numbers:
let length = 16;
let weight = 7.5;

//// Strings:
let color = "Yellow";
let lastName = "Johnson";

//// Booleans
let x = true;
let y = false;

//// Object:
const person = { firstName: "John", lastName: "Doe" };

//// Array:
const cars = ["Saab", "Volvo", "BMW"];

//// Date:
const date = new Date("2022-03-25");
```

**If-else**

```javascript
var num = Math.floor(Math.random() * 100);

if (num % 2 == 0) {
    console.log(`The number ${num} is even`);
} else {
    console.log(`The number ${num} is odd`);
}

var numType = num % 2 == 0 ? 'even' : 'odd';

console.log(`The number ${num} is ${num % 2 == 0 ? 'even' : 'odd'}`);
```

**Switch**

```javascript
var day = '';
switch (new Date().getDay()) {
    case 0:
        day = "Sunday";
        break;
    case 1:
        day = "Monday";
        break;
    case 2:
        day = "Tuesday";
        break;
    default:
        day = "Others";
}

console.log(day);
```

**Loops**
```javascript
const numbers = [45, 4, 9, 16, 25];

//// Index
for (let n in numbers) {
    console.log(n);
}

//// Data
numbers.forEach(n => {
    console.log(n);
});
```

### 3. Language blocks - part 2

**Classes & Objects**

```javascript
class Car {
    constructor(name, year) {
        this.name = name;
        this.year = year;
    }
}

let myCar = new Car("Ford", 2014);
console.log(myCar.year);
```

**Node Package Manager (npm)**

```sh
npm install lodash
```

**Data operations using lodash**

```javascript
const util = require('lodash');

var data = {
    "id": "0001",
    "type": "donut",
    "name": "Cake",
    "ppu": 0.55,
    "batters":
    {
        "batter":
            [
                { "id": "1001", "type": "Regular" },
                { "id": "1002", "type": "Chocolate" },
                { "id": "1003", "type": "Blueberry" },
                { "id": "1004", "type": "Devil's Food" }
            ]
    },
    "topping":
        [
            { "id": "5001", "type": "None" },
            { "id": "5002", "type": "Glazed" },
            { "id": "5005", "type": "Sugar" },
            { "id": "5007", "type": "Powdered Sugar" },
            { "id": "5006", "type": "Chocolate with Sprinkles" },
            { "id": "5003", "type": "Chocolate" },
            { "id": "5004", "type": "Maple" }
        ]
};

console.log(extractFromJson(data));

function extractFromJson(data) {
    var batterCollection = util.get(data, 'batters.batter');
    var myBatter = util.filter(batterCollection, ['id', '1004']);

    var toppingCollection = util.get(data, 'topping');
    var myTopping = util.filter(toppingCollection, ['id', '5002']);

    var result = util.union(myBatter, myTopping);

    return result;
}
```

### 4. File operations

```javascript
const fs = require('fs');
const file = './readme.txt';
const data = Math.floor(Math.random() * 101).toString() + '\n';
```

**Write**
```javascript
fs.writeFileSync(file, data);
```

**Append**
```javascript
fs.appendFileSync(file, data);
```

**Read**
```javascript
var fileContents = fs.readFileSync(file, 'utf-8');
```

### 5. Web API

```sh
npm install express
```

```javascript
const express = require('express');
const app = express();
const port = 3000;

app.get('/', (req, res) => {
  res.send('Hello World!')
});

app.listen(port, () => {
  console.log(`Example app listening on port ${port}`)
});
```

### 6. Database

```sh
npm install pg
npm install knex
```

**dataAccess.js**

```javascript
class dataAccess {
    constructor() {
        this.dbClient = require('knex')({
            client: 'pg',
            connection: 'postgres://username:password@host/database',
            useNullAsDefault: true
        });

        this.query = null;
    }

    getStudents() {
        return this.dbClient
                .select()
                .from('student');
    }

    getStudentById(id) {
        return this.dbClient
                .select()
                .from('student')
                .where('id', id);
    }

    saveStudents(records) {
        return this.dbClient('student')
                .returning('*')
                .insert(records);
    }
}

module.exports.dataAccess = dataAccess;
```

**index.js**

```javascript
const express = require('express');
const axios = require('axios');
const { dataAccess } = require('./dataAccess.js');

const app = express();
const port = 3000;
const db = new dataAccess();

app.use(express.json());
app.listen(port, () => {
    console.log(`App is running on port: ${port}`);
});

app.get('/student', (req, res) => {
    db.getStudents()
        .then((result) => {
            res.send(result);
        })
        .catch((err) => {
            res.send(err);
        });
});

app.get('/student/:id', (req, res) => {
    db.getStudentById(req.params.id)
        .then((result) => {
            res.send(result);
        })
        .catch((err) => {
            res.send(err);
        });
});

app.post('/student', (req, res) => {
    db.saveStudents(req.body.records)
        .then((result) => {
            res.send(result);
        })
        .catch((err) => {
            res.send(err);
        });
});
```

### 7. Invoking HTTP Calls

```sh
npm install axios
```

```javascript
axios.get('https://httpbin.org/uuid')
    .then((result) => {
        console.log(result);
    })
    .catch((err) => {
        console.log(err);
    });
```

### 8. Parallel programming

```sh
npm install async
```

```javascript
const axios = require('axios');
const async = require('async');

const collection = ['a', 'b', 'c', 'd', 'e'];
const responses = new Array();

console.time();

async
    .forEachOfLimit(collection, 2, (alphabet, index, callback) => {
        setTimeout(() => {
            axios.get('https://httpbin.org/uuid')
                .then((result) => {
                    console.log(`Executing index: ${index} with alphabet: ${alphabet}`);
                    responses.push({
                        index: index,
                        alphabet: alphabet,
                        uuid: result.data.uuid
                    });
                    callback(null);
                })
                .catch((err) => {
                    callback(err);
                });
        }, 1000);
    })
    .then(() => {
        console.log(responses);
        console.timeEnd();
    })
    .catch((err) => {
        console.log(err);
    });
```

### 9. Unit testing

```sh
npm install mocha
```

**test\test.js**

```javascript
var assert = require('assert');

describe('array-len-test', function () {
    it('should have more than 0 records', function () {
        //// Assume
        let expectedLength = 3;

        //// Actual
        let arr = [1, 2, 3];

        //// Assert
        assert.equal(arr.length > 0, true);
    });

    it('should have more 5 records', function () {
        //// Assume
        let expectedLength = 5;

        //// Actual
        let arr = [1, 2, 3];

        //// Assert
        assert.equal(arr.length, expectedLength, 'Count mismatch');
    })
});
```

**package.json**

```javascript
"scripts": {
    "test": "mocha"
  }
```

**Run tests**

```sh
npm test
```
