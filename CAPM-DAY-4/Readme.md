## CAPM - Day 4 - Generic Handlers

### Creating OData services to our app 

</br>
</br>

Will continue today from yesterdays application 
</br> - will focus on creating services on top of data model we created on previous day 
</br> - OData services 
 
</br>

Go to our application SRV folder and create a file (CatalogService.cds) as shown below 
</br>

```cds
// importing data models and views to our service
using {dan.db} from '../db/datamodel';
using {dante.cds} from '../db/CDSViews';

// so in cap services odata will trim tha name when there is upper case in the word
// example MyName will be dispalyed as My the part (Name) will be removed
// to avoid this we use @(path:<service-name>) annotation

service CatalogService @(path: 'CatalogService') {

    entity BusinessPartnerSet as projection on db.master.businesspartner;
    entity AddressSet         as projection on db.master.address;
    entity EmployeeSet        as projection on db.master.employees;
    entity PurchseOrderItems  as projection on db.transaction.poitems;

    entity POs                as
        projection on db.transaction.purchaseorder {
            *,
            Items : redirected to PurchseOrderItems
        };

    entity CProductValuesView as projection on cds.CDSViews.CProductValuesView;

}

```
</br>
<img src="./files/capmd4-1.png" >
</br>
</br>

After creation of the file deploy it and run (best practice) 

#### if you find cds-serve in package.json - it takes care automatically this is new feature from 2024
#### (service creation - no need to deploy, DB and View creation need to deploy) - if you find cds-serve in package.json
#### (instead if you find CDS watch) then it takes care automatically

<img src="./files/capmd4-2.png" >
</br>
</br>

Now run the application through terminal and test it as shown below (i didn't deploy) i just run it works 
</br>
<img src="./files/capmd4-3.png" >
</br>
<img src="./files/capmd4-4.png" >
</br>
</br>
</br>
</br>

Now we can check developer tools in browser for identifying OData version as shown below 
</br>
</br>
<img src="./files/capmd4-6.png" >
</br>
</br>
</br>

older version example go to this web page https://services.odata.org/ and select any link 
</br>
</br>
<img src="./files/capmd4-5a.png" >
</br>
</br>
</br>

use <b>(?$format=json) </b> to the link and you will see the following page press f12 and check the version 
</br>
</br>
<img src="./files/capmd4-7.png" >
</br>
</br>

### Backward compatibility - OData V2
</br>
</br>

- default SAP Fiori which comes out of on-prem is OData v2 
- default SAP Fiori which comes out of cloud is OData v4
- SAP applications developed in OData v2 cannot be used for OData v4 - requires (redesign or code changes)
- To avoid this we have an <b>adapter</b> which will use v4 version as v2
- SAP provided this adapters as node module (go to npm site : https://www.npmjs.com/search?q=odata-v2-adapter)
- Search for OData-v2-adapter (pick the proxy one)
- install the adapter using command
- create JS file is (srv) directory (./srv/server.js:) and copy the contents
- run and test the application
  - OData V2 service will be available at http://localhost:4004/v2/
  - OData V4 service will be available at http://localhost:4004/
</br>

```bat
npm install @sap/cds-odata-v2-adapter-proxy -s
```
</br>

### server.js
</br>

```js
const cds = require("@sap/cds");
const cov2ap = require("@sap/cds-odata-v2-adapter-proxy");
cds.on("bootstrap", (app) => app.use(cov2ap()));
module.exports = cds.server;
```

</br>
<img src="./files/capmd4-8.png" >
</br>
<img src="./files/capmd4-9.png" >
</br>
<img src="./files/capmd4-10.png" >
</br>


</br>
</br>

### OData v2 
</br>
it can be identified by representation d in front of result set 
</br>
</br>

</br>
</br>
cloud BAS main page
</br>
<img src="./files/capmd4-10a.png" >
</br>

local VSCode main page
</br>
<img src="./files/capmd4-11a.png" >
</br>

</br> Add v2/ after the cloud link and then navigate from the page 
</br> https://port4004-workspaces-ws-n8568.us10.trial.applicationstudio.cloud.sap/v2/
</br>
</br> SAP cloud BAS
</br> https://port4004-workspaces-ws-n8568.us10.trial.applicationstudio.cloud.sap/v2/odata/v4/CatalogService/BusinessPartnerSet
</br>
</br>
</br>  Add v2/ after the local host link and then navigate from the page
</br> http://localhost:4004/v2/
</br>
</br> VSCode
</br> http://localhost:4004/v2/odata/v4/CatalogService/BusinessPartnerSet

</br>
cloud BAS
<img src="./files/capmd4-10b.png" >
</br>
</br>
VSCode
<img src="./files/capmd4-11b.png" >
</br>
</br>

</br>

### CURDQ Testing for cloud 
</br>
 We cannot test CURD operation through postman on this cloud application it is not supported only local VS code works with postman
</br>
</br>

### A way to test CURDQ in cloud 
</br>
create tester.http (any name of your choice *.http) file under (srv) folder like shown below 
</br>
</br>

determine the correct link before you add it to tester.http file 
</br>
<img src="./files/capmd4-12a.png" >
</br>
<img src="./files/capmd4-12c.png" >
</br>
</br>

click the link (send request) to test
</br>
<img src="./files/capmd4-12b.png" >
</br>
</br>

if the link is correct following output window will appear with right data 
</br>
<img src="./files/capmd4-12d.png" >
</br>
</br>
</br>
</br>


### Contents of tester.http
</br>

```http

GET http://localhost:4004/odata/v4/CatalogService


##### Read Service metadata
GET http://localhost:4004/odata/v4/CatalogService/$metadata


##### GET top 2 employees
GET http://localhost:4004/odata/v4/CatalogService/EmployeeSet?$top=2


##### POST call - creating new employee 
POST http://localhost:4004/odata/v4/CatalogService/EmployeeSet
Content-Type: application/json    
    
    {
      "ID": "02BD2137-0890-1EEA-A6C2-BB55C190999A",
      "nameFirst": "Sunny",
      "nameMiddle": null,
      "nameLast": "Winter",
      "nameInitials": null,
      "sex": "F",
      "language": "E",
      "phoneNumber": null,
      "email": "sunny.winter@Ey.com",
      "loginName": "WINTERS",
      "Currency_code": "USD",
      "salaryAmount": 999989,
      "accountNumber": "9988776655",
      "bankId": "11000358",
      "bankName": "My Bank of San Francisco"
    }

#### GET the employee which got created using POST    
GET http://localhost:4004/odata/v4/CatalogService/EmployeeSet/02BD2137-0890-1EEA-A6C2-BB55C190999A

#### PATCH request for an employee salary 
PATCH http://localhost:4004/odata/v4/CatalogService/EmployeeSet/02BD2137-0890-1EEA-A6C2-BB55C190999A
Content-Type: application/json

{

      "salaryAmount": 1200000,
      "Currency_code": "EUR"

}

```
</br>
</br>

### Testing POST call testing challenges - and troubleshoot 
</br>

When copied the data of some random GET and modified it for posting following corrections carried out 
 - serialization error for bank id error <b>FIX -(change the length) of BANKID</b>
 - Email format error <b>FIX -(we have to modify the REGEX expression in our program)</b>
 </br> (https://www.tutorialspoint.com/checking-for-valid-email-address-using-regular-expressions-in-java)

</br>
</br>

```json

"message": "Deserialization Error: Invalid value 121000358 (string) for property \"bankId\".
The length of the Edm.String value must not be greater than the MaxLength facet value (8)."

```

```json
{
  "error": {
    "code": "400",
    "message": "Value \"sunny.winter@Ey.com\" is not in specified format \"/^\\w+([\\.-]?\\w+)*@\\w+([\\.-]?\\w+)*(\\.\\w{2,3})+$/\"",
    "target": "email",
    "@Common.numericSeverity": 4
  }
```

</br>
</br>

changed the code for email REGEX as mentioned below and tested (commons.cds)
</br>

```cds 
// validating email using REGEX
type Email: String(255)@assert.format : '^[a-zA-Z0-9+_.-]+@[a-zA-Z0-9.-]+$';

// type Email: String(255)@assert.format : '/^\w+([\.-]?\w+)*@\w+([\.-]?\w+)*(\.\w{2,3})+$/';
```
</br>
</br>
When POST system is successfully triggered as shown below 
</br>
</br>
<img src="./files/capmd4-13.png" >
</br>
</br>
<img src="./files/capmd4-13a.png" >
</br>
</br>

Patch request is tested for the app as shown below (same post request key is used)
</br>
</br>
<img src="./files/capmd4-14.png" >
</br>

</br>
</br>
</br>

## Generic handlers in CAP

</br>
The CAPM framework generates most of the code to perform CURDQ operations on the entity.
</br> it does not know what validations to perform on our business data.
</br>
</br> As a developer when we want to attach a pre-check (validations) / post-check (post-processing code) 
</br> on an entity operation, we can use generic handlers. 
</br>
</br> All these operations are performed <b>SYNCHONOUSLY</b>
</br>
</br>

At times we want to 
- Take full control of our implementation <b>(WHEN? -- On, Before, After)</b>
- We want to add some validation before data is inserted or updated <b>(FOR WHOM? - Entity)</b>
- We want to change data before insert/update <b>(WHICH OPERATION? - CREATE, UPDATE, (POST, PATCH) )</b>
- Read some extra information while GET calls <b>(WHAT? Business Logic )</b>

</br>
</br>
</br>

As per above handlers appropriate usage questioning 
</br> lets consider it for a scenario (validate salary greater than 1 million) before updating or creating entry in table 
</br> following question suits as shown below 
</br>

- WHEN? -- <b>validate Before</b> 
- FOR WHOM? - <b>Entity - Employees</b>
- WHICH OPERATION? - CREATE, UPDATE, (POST, PATCH) 
- WHAT Business Logic ? - Check if salary is < (condition value)

</br>
</br>
<img src="./files/capmd4-15.png" >
</br>
</br>

### Generic handlers validation for our app 
</br>
</br>
create a file called <b>(CatalogService.js)</b> in (SRV) folder
</br>
</br>

### CatalogService.js
</br>

```js
// This module block will never change its liek a template 
// -- whats inside this block will cahnge according to business Req

// async - here means run in synchronised manner

module.exports = cds.service.impl(async function () {

    // step 1: get the object of our odata entities
    const { EmployeeSet, POs } = this.entities;

    // step 2:define generic handler for validaiton
    this.before('UPDATE', EmployeeSet, (req, res) => {
        console.log("It came here " + req.data.salaryAmount);
    });

}
);
```
</br>
</br>

now will test the code as shown below using <b>(tester.http)</b>
</br>
</br>
<img src="./files/capmd4-16.png" >
</br>
</br>

Now will add validation to the block to handle the scenario
</br>
</br>
### CatalogService.js (with validation)
</br>

```js
// This module block will never change its liek a template 
// -- whats inside this block will cahnge according to business Req

// async - here means run in synchronised manner

module.exports = cds.service.impl(async function () {

    // step 1: get the object of our odata entities
    const { EmployeeSet, POs } = this.entities;

    // step 2:define generic handler for validation
    this.before('UPDATE', EmployeeSet, (req, res) => {
        console.log("It came here " + req.data.salaryAmount);
        if(parseFloat(req.data.salaryAmount) >= 1000000){
            req.error(500, "Salary must be less than a million for employee");
        }
                
    });

}
);
````
</br>
The testing of validation is shown below 
</br>
</br>
<img src="./files/capmd4-17.png" >
</br>
</br>


</br>
</br>

## Action 
</br>
This doesn't have return value and it is used to perform a calculation or some action some process in a Business Logic
</br> This is used for POST calls - update and create 
</br>
</br>
</br>

## Function
</br>
This has return value and it is used to perform an action and returns the value in a Business logic
</br> This is used for GET calls - only for returning value 
</br>
</br>
</br>


### Will Implement Action and Function in our application will test it using (tester.http) file 
</br>
make changes to tester.http file for getting purchase orders and $expand as shown below 
</br>
</br>
</br>

## tester.http  (Complete File)
</br>
</br>

```http

GET http://localhost:4004/odata/v4/CatalogService


##### Read Service metadata
GET http://localhost:4004/odata/v4/CatalogService/$metadata


##### GET top 2 employees
GET http://localhost:4004/odata/v4/CatalogService/EmployeeSet?$top=2


##### POST call - creating new employee 
POST http://localhost:4004/odata/v4/CatalogService/EmployeeSet
Content-Type: application/json    
    
    {
      "ID": "02BD2137-0890-1EEA-A6C2-BB55C190999A",
      "nameFirst": "Sunny",
      "nameMiddle": null,
      "nameLast": "Winter",
      "nameInitials": null,
      "sex": "F",
      "language": "E",
      "phoneNumber": null,
      "email": "sunny.winter@Ey.com",
      "loginName": "WINTERS",
      "Currency_code": "USD",
      "salaryAmount": 999989,
      "accountNumber": "9988776655",
      "bankId": "11000358",
      "bankName": "My Bank of San Francisco"
    }

#### GET the employee which got created using POST    
GET http://localhost:4004/odata/v4/CatalogService/EmployeeSet/02BD2137-0890-1EEA-A6C2-BB55C190999A


#### PATCH request for an employee salary 
PATCH http://localhost:4004/odata/v4/CatalogService/EmployeeSet/02BD2137-0890-1EEA-A6C2-BB55C190999A
Content-Type: application/json

{

      "salaryAmount": 1200001,
      "Currency_code": "EUR"

}

#### GET Display all Purchase orders 
GET http://localhost:4004/odata/v4/CatalogService/POs


#### GET Single Purchase orders 
GET http://localhost:4004/odata/v4/CatalogService/POs/74867AD200E41EDBA5D8B0C98DC28052


#### GET Main entity and associated entity together $expand
GET http://localhost:4004/odata/v4/CatalogService/POs/74867AD200E41EDBA5D8B0C98DC28052?$expand=Items


#### Boosting a Order - TYPE 1 (ACTION CALL)
POST http://localhost:4004/odata/v4/CatalogService/POs/74867AD200E41EDBA5D8B0C98DC28052/boost
Content-Type: application/json

{}

#### Boosting a Order - TYPE 2 (ACTION CALL)
POST http://localhost:4004/odata/v4/CatalogService/POs('74867AD200E41EDBA5D8B0C98DC28052')/CatalogService.boost
Content-Type: application/json

{}


### Action for POST calls 
### Function is for GET calls (it returns value)

#### GET largest order -- it is a function so () paranthesis is needed (FUNCTION CALL)
GET http://localhost:4004/odata/v4/CatalogService/POs('74867AD200E41EDBA5D8B0C98DC28052')/CatalogService.largestOrder()


#### GET - Custome code GET entity 
GET http://localhost:4004/odata/v4/mysrvdemo/ReadEmployeeSrv


#### GET Custom code - GET entity - with dynamic where 
GET http://localhost:4004/odata/v4/mysrvdemo/ReadEmployeeSrv/02BD2137-0890-1EEA-A6C2-BB55C190999A



```

</br>
</br>


## CatalogService.cds  (Action , Function Definition)
</br>

```cds
// importing data models and views to our service
using {dan.db} from '../db/datamodel';
using {dante.cds} from '../db/CDSViews';

// so in cap services odata will trim tha name when there is upper case in the word
// example MyName will be dispalyed as My the part (Name) will be removed
// to avoid this we use @(path:<service-name>) annotation

service CatalogService @(path: 'CatalogService') {

    entity BusinessPartnerSet as projection on db.master.businesspartner;
    entity AddressSet         as projection on db.master.address;
    entity EmployeeSet        as projection on db.master.employees;
    entity PurchseOrderItems  as projection on db.transaction.poitems;

    entity POs                as
        projection on db.transaction.purchaseorder {
            *,
            Items : redirected to PurchseOrderItems
        } actions {
// Definition Part - need to do implementation part - in JS file             
            action boost();
            function largestorder() returns array of  POs;
        };

    entity CProductValuesView as projection on cds.CDSViews.CProductValuesView;

}


```
</br>
</br>

## CatalogService.js (Action , Function Implementation)
</br>

```js
// This module block will never change its liek a template 
// -- whats inside this block will cahnge according to business Req

// async - here means run in synchronised manner

module.exports = cds.service.impl(async function () {

    // step 1: get the object of our odata entities
    const { EmployeeSet, POs } = this.entities;

    // step 2:define generic handler for validaiton
    this.before('UPDATE', EmployeeSet, (req, res) => {
        console.log("It came here " + req.data.salaryAmount);
        if(parseFloat(req.data.salaryAmount) >= 1000000){
            req.error(500, "Salary must be less than a million for employee");
        }
                
    });

// ACTION
    this.on('boost', async (req,res) => {
        try {
            const ID = req.params[0];
            console.log("Hey Amigo you purcahse order with id " + JSON.stringify(req.params[0]) + " will be boosted");

// (CQL) CDS query language converted from JS to cds 
            const tx = cds.tx(req);
            await tx.update(POs).with({
                GROSS_AMOUNT: { '+=' : 20000 },
                NOTE: 'Boosted!!'
            }).where(ID);
        } catch (error) {
            return "Error" + error.toString();
        }        
    });

// FUNCTION 
    this.on( 'largestOrder', async (req,res) => {
        try {
            const ID = req.params[0];
            const tx = cds.tx(req);
            
// SELECT * UPTO 1 Row from dbtab ORDERBY GROSS_AMOUNT desc
            const reply = await tx.read(POs).orderBy({
                GROSS_AMOUNT: 'desc'
            }).limit(1);

            return reply;
        } catch (error) {
            
        }

    });

}
);


// cds.tx is called CDS Transaction management
// ACID 
// Atomicity - Either completely fail or completley success 
// Consistency - Transction should leave DB in a consistent state
// Isolation - Each transaction is isoalted from each other 
// Durability - data base should be durable enought to perform the transaction else transaction will be rejected 

// it is an api provided by sap for cds transaction - can check the deailed info here https://cap.cloud.sap/docs/node.js/cds-tx
```
</br>
</br>
</br>
</br>

## datamodel.cds (Note field updated in - purchase order entity)
</br>
</br>

```cds
// transaction table 
context transaction {
    entity purchaseorder: commons.Amount{
        key NODE_KEY: commons.Guid;
        PO_ID: String(40);
        PARTNER_GUID: Association to master.businesspartner;
        LIFECYCLE_STATUS: String(1);
        OVERALL_STATUS: String(1);
        NOTE: String(45) default 'null';  // Added this field 
        Items: Association to many poitems on Items.PARENT_KEY = $self;
    }
```

</br>
</br>
</br>

## Testing Action 
</br>

FIRST - Trigger this in tester.http (ACTION call) (POST)
</br>
</br>

```http
#### Boosting a Order - TYPE 1 (CALL)
POST http://localhost:4004/odata/v4/CatalogService/POs/74867AD200E41EDBA5D8B0C98DC28052/boost
Content-Type: application/json

{}
```
</br>
<img src="./files/capmd4-19.png" >
</br>
</br>
</br>

SECOND - Trigger this in tester.http (GET) 
</br>
</br>

```http
#### GET Single Purchase orders 
GET http://localhost:4004/odata/v4/CatalogService/POs/74867AD200E41EDBA5D8B0C98DC28052

{}
```
</br>
<img src="./files/capmd4-20.png" >
</br>
</br>

To check whether this testing is done correctly 
- - pick a POs key with status NULL
- - use it on BOOST (ACTION call)
- - check the status using GET POs it should be updated as BOOSTED 
</br>
</br>


## Testing Function
</br>
This is just a simple get call which returns the purchase order with highest GROSS_AMOUNT value
</br>

Trigger this (Function call)
</br>
```http
#### GET largest order -- it is a function so () paranthesis is needed (FUNCTION CALL)
GET http://localhost:4004/odata/v4/CatalogService/POs('74867AD200E41EDBA5D8B0C98DC28052')/CatalogService.largestOrder()
```
</br>
</br>
</br>



To Control post operation to restrict creating record in an entity use @readonly in catalogservice.cds as shown below 
</br>

## catalogService.cds
</br>

```cds
// I want to restrict CAP from doing post on employee use @readonly 
    @readonly   
    entity EmployeeSet        as projection on db.master.employees;
```

</br>
</br>
<img src="./files/capmd4-18.png" >
</br>


</br>
</br>
</br>

To Control other DB activities like insert and delete sample is shown below
</br>

## catalogService.cds
</br>

```cds
// I want to insert but dont want to delete
    @Capabilities : { Insertable, Deletable: false }
    entity BusinessPartnerSet as projection on db.master.businesspartner;
```

</br>
</br>
</br>

## How to define your own Custom - CURD operations 
</br>
So far we have used - POST, PATCH and GET using standard CAP functionalities to perform CURD operations.
</br> If user wants to take control of defining their own logic of updating, creating, deleting, and reading data
</br> they need to define custom service definition and implementation lets focus on how it can be done.

</br>
</br>

### Create a CDS and JS file of same namespace
</br>
</br>

## MyService.cds
</br>

```cds
using { dan.db.master, dan.db.transaction } from '../db/datamodel';

// I dont want CAP to control CURD operations 
// I want my custom operations for CURD - thats why the following definitions created 

service MyService {

    @readonly
    entity ReadEmployeeSrv as projection on master.employees;
    @insertonly
    entity InsrEmployeeSrv as projection on master.employees;
    @updateonly
    entity UpdateEmployeeSrv as projection on master.employees;
    @Deleteonly
    entity DeleteEmployeeSrv as projection on master.employees;
}
```

</br>
</br>
</br>

## MyService.js
</br>

```js
const cds = require("@sap/cds");
const { employees } = cds.entities("dan.db.master");
const mysrvdemo = function (srv) {

    // Generic handler 
    srv.on("READ", "ReadEmployeeSrv", async (req, res) => {

        var results = [];

        // Example 1 : hardcoded data
        // results.push({
        //         "ID":"56AD5671-9034567-12340-ER89GH-6789",
        //         "nameFirst": "Leonardo",
        //         "nameLast": "davinci"
        // });

        // Example 2 : use Select on DB table 
        // results = await cds.tx(req).run(SELECT.from(employees).limit(10));

        // Example 3 : use Select on DB table 
        // results = await cds.tx(req).run(SELECT.from(employees).limit(10).where({"nameFirst":"Susan"} ) );


        // use /entity/key/

        // Example 4 : Caller will pass the condition like ID
        var whereCondition = req.data;
        console.log(whereCondition);
        if (whereCondition.hasOwnProperty("ID")) {
            results = await cds.tx(req).run(SELECT.from(employees).limit(10).where({ "nameFirst": "Susan" }));
        } else {
            results = await cds.tx(req).run(SELECT.from(employees).limit(1));
        }

        // https://cap.cloud.sap/docs/node.js/cds-ql#where

        return results;

    });

};


module.exports = mysrvdemo;
```

</br>
</br>
</br>

## CatalogService.cds
</br>
</br>
</br>

```cds

// importing data models and views to our service
using {dan.db} from '../db/datamodel';
using {dante.cds} from '../db/CDSViews';

// so in cap services odata will trim tha name when there is upper case in the word
// example MyName will be dispalyed as My the part (Name) will be removed
// to avoid this we use @(path:<service-name>) annotation

service CatalogService @(path: 'CatalogService') {
// I want to insert but dont want to delete
    @Capabilities : { Insertable, Deletable: false }
    entity BusinessPartnerSet as projection on db.master.businesspartner;
    entity AddressSet         as projection on db.master.address;

// I want to restrict CAP from doing post on employee use @readonly 
    @readonly   
    entity EmployeeSet        as projection on db.master.employees;
    entity PurchseOrderItems  as projection on db.transaction.poitems;
    entity POs as projection on db.transaction.purchaseorder {
            *,
// in case if gross amount is showing with extreme decimal value             
            round(GROSS_AMOUNT) as GROSS_AMOUNT: Decimal(10,2),
            Items : redirected to PurchseOrderItems
        } actions {
// Definition Part - need to do implementation part - in JS file             
            action boost();
            function largestOrder() returns array of  POs;
        };

    entity CProductValuesView as projection on cds.CDSViews.CProductValuesView;

}


```

</br>
</br>
</br>

Go through the Node section documents for CDS QUERY LANGUAGE - usage 
</br> https://cap.cloud.sap/docs/node.js/cds-ql#where

</br>
</br>
</br>
</br>

# NEXT ------ CAPM - DAY 5 - Fiori Elements

<p align="center"> 
<a href="https://github.com/Octavius-Dante/Tetra_Proxima/tree/main/CAPM-DAY-5"> CAPM DAY 5 - Fiori Elements</a> 

#### Previous Sessions
</br>

<!--
- [x] <a href="https://github.com/Octavius-Dante/Tetra_Proxima/tree/main/CAPM-DAY-12"> CAPM Day 12 - Extension CI CD</a>
- [x] <a href="https://github.com/Octavius-Dante/Tetra_Proxima/tree/main/CAPM-DAY-11"> CAPM Day 11 - S4HANA Side by Side</a>
- [x] <a href="https://github.com/Octavius-Dante/Tetra_Proxima/tree/main/CAPM-DAY-10"> CAPM Day 10 - Side by Side extension</a>
- [x] <a href="https://github.com/Octavius-Dante/Tetra_Proxima/tree/main/CAPM-DAY-9"> CAPM Day 9 - Serverless Fiori App</a>
- [x] <a href="https://github.com/Octavius-Dante/Tetra_Proxima/tree/main/CAPM-DAY-8"> CAPM Day 8 - CAPM Security XSUAA</a>
- [x] <a href="https://github.com/Octavius-Dante/Tetra_Proxima/tree/main/CAPM-DAY-7"> CAPM Day 7 - HANA and Deployment</a>
- [x] <a href="https://github.com/Octavius-Dante/Tetra_Proxima/tree/main/CAPM-DAY-6"> CAPM Day 6 - Fiori App Draft</a>
- [x] <a href="https://github.com/Octavius-Dante/Tetra_Proxima/tree/main/CAPM-DAY-5"> CAPM Day 5 - Fiori Elements</a>
- [x] <a href="https://github.com/Octavius-Dante/Tetra_Proxima/tree/main/CAPM-DAY-4"> CAPM Day 4 - Generic Handlers</a>
-->
- [x] <a href="https://github.com/Octavius-Dante/Tetra_Proxima/tree/main/CAPM-DAY-3"> CAPM Day 3 - EPM DB and CDS Views</a>
- [x] <a href="https://github.com/Octavius-Dante/Tetra_Proxima/tree/main/CAPM-DAY-2"> CAPM Day 2 - Aspects and Reuse Tables</a>
- [x] <a href="https://github.com/Octavius-Dante/Tetra_Proxima/tree/main/CAPM-DAY-1"> CAPM Day 1 - First CAP App </a>

</br>
</br>
 
</p>

</br>
</br>
</br>
</br>
