[![Build Status](https://dev.azure.com/ajithgeorgethekkel0427/Postman%20API%20Testing/_apis/build/status/Postman-API-Testing-CI?branchName=master)](https://dev.azure.com/ajithgeorgethekkel0427/Postman%20API%20Testing/_build?definitionId=1)


# API-Automation: Using postman and newman with htmlextra reporting and azure pipeline integration

API Testing using Postman 

## Areas Covered:
- Create API Tests Using [Postman Scripts](https://learning.postman.com/docs/postman/scripts/test-examples/)
- Run tests using Postman Collection Runner and through [Newman](https://www.npmjs.com/package/newman) CLI tool
- Generate Report using [Newman reporter](https://www.npmjs.com/package/newman-reporter-html)
- Customized Newman Reporter -[htmlextra](https://github.com/DannyDainton/newman-reporter-htmlextra)
- Assertions using [Chai Assertion Library](https://www.chaijs.com/api/)
- Azure Pipeline Integration [Azure CI/CD](https://dev.azure.com/ajithgeorgethekkel0427/Postman%20API%20Testing/_build)

Main Reference: [DannyDainton: All-Things-Postman](https://github.com/DannyDainton/All-Things-Postman#example-guides)

## Overview:
The solution tests APIs from,

1. [Restful-Booker -Self Hosted](https://github.com/mwinteringham/restful-booker)
2. [Restful-Booker -Public Link](https://restful-booker.herokuapp.com/)
3. Other API endpoints available are: [Random User](https://randomuser.me/api/) and [Httpbin](https://httpbin.org/#/)

Azure CI/CD integration working modal is available at: [ajithgeorgethekkel0427](https://dev.azure.com/ajithgeorgethekkel0427/Postman%20API%20Testing/_build)


## Prerequisites:
1. Postman Application installed.
2. Newman tool installed.
3. Node and Npm installed.
4. Htmlextra/Newman reporter node packages installed.

## Execution Steps:
1. Clone the repo.
2. Import the collection and environment to postman tool.
3. Run the tests from either Postman Collection Runner or through Newman tool.
4. Use appropriate parameter for newman test execution to generate report.

Postman Sandbox [API reference](https://learning.postman.com/docs/postman/scripts/postman-sandbox-api-reference/)
        
## Status Check:
```javascript
        pm.response.to.be.info - Checks 1XX status code
        pm.response.to.be.success - Checks 2XX status code
        pm.response.to.be.redirection - Checks 3XX status code
        pm.response.to.be.clientError - Checks 4XX status code
        pm.response.to.be.serverError - Checks 5XX
        pm.response.to.be.error - Checks 4XX or 5XX
        pm.response.to.be.ok - Status code must be 200
        pm.response.to.be.accepted - Status code must be 202
        pm.response.to.be.badRequest - Status code must be 400
        pm.response.to.be.unauthorized - Status code must be 401
        pm.response.to.be.forbidden - Status code 403
        pm.response.to.be.notFound - Status code of response is checked to be 404
        pm.response.to.be.rateLimited - Checks whether response status code is 429
```
Ref: [Extended Checks](https://github.com/DannyDainton/All-Things-Postman/blob/master/Examples/08_extendingOurTests.md),[Assertion Samples](https://learning.postman.com/docs/postman/scripts/test-examples/#assertion-library-examples)

## Reuse Javascript code using Environment Variable
Setting as environment variable:
```javascript

// Save common tests in a global variable

postman.setGlobalVariable("commonTests", () => {

  // The Content-Type must be JSON

  tests["Content-Type header is set"] = postman.getResponseHeader("Content-Type") === "application/json";

  // The response time must be less than 500 milliseconds

  tests["Response time is acceptable"] = responseTime < 500;

  // The response body must include an "id" property

  var data = JSON.parse(responseBody);

  tests["Response has an ID"] = data.id !== undefined;

});
```
Calling in Test/Pre-Script:
```javascript
// First, run the common tests
eval(globals.commonTests)();

// Then run any request-specific tests
tests["Status code is 200"] = responseCode.code === 200;
```
Reference: [Postman Tips](https://blog.postman.com/api-testing-tips-from-a-postman-professional)

## Schema as environment variable
Setting as environment variable:
```javascript 
const schema = {
    "required": [
        "bookingid",
        "booking"
    ],
    "properties": {
        "bookingid": {
            "type": "integer"
        },
        "booking": {
            "type": "object",
            "required": [
                "firstname",
                "lastname"
            ],
            "properties": {
                "firstname": {
                    "type": "string",
                },
                "lastname": {
                    "type": "string",
            }
        }
    }
}

pm.environment.set("testSchema",schema);; 
```
Comparing from environment variable:
```javascript
var data = pm.response.json()

pm.test('Check Schema from Environment Variable', function () {
    pm.expect(tv4.validate(data, (pm.environment.get("testSchema")))).to.be.true;});
```
Note: The 'Required' field is mandatory else there won't be a strict match done for the field name.

## Dynamic resource/data linking
Instead of hardcoding the resource url (like in case of id) use dynamic linking of the same to reduce error.
- Send a reuest in the pre-script to fetch in the list of resources ids.
- Select a random id from the list (enusre random selection to prevent the selection of the same id every run -displayed script doesn't randomize as the api return was inturn not in a particular set format)
- Set the same as an environment/global variable and consume in the API method.
<img src=https://raw.githubusercontent.com/AjithGeorge/API-Automation-Postman/master/assets/Dynamic%20Id.png>

## Branching and looping of Scripts
Set the request to be executed next:

```javascript
postman.setNextRequest("request_name");
```
Loop over the current request- use the same test in the setNextRequest() method. One should wrap setNextRequest in some logic so as to ensure that the request does not run indefinitely otherwise the collection runner would need to be force closed.

<p>If postman.setNextRequest() is absent in a request, the collection runner defaults to linear execution and moves to the next request.</p>

Stop workflow execution
 
```javascript
postman.setNextRequest(null);
```
postman.setNextRequest() is always executed at the end of the current request. This means that if you put this function before other code blocks anywhere in pre-request or test script, these blocks will still execute.
<p>postman.setNextRequest() has a scope, which is the source of your collection run. If you run a collection, you can jump to any request in the collection (even requests inside folders, using the same syntax). However, if you run a folder, the scope of postman.setNextRequest() is limited to that folder. So you can jump to any request in this folder, but not ones that are outside of the folder. It includes requests inside other folders, and also root-level requests in the collection.</p>

## Single test to iterate over a set of data
Instead of writing the same test script for different endpoints/ids the same test could be used to iterate over the data without any repetition of the code. (DRY -Don't Repeat Your code)
<p> Pre-Script: Handles the initial gathering of ids and setting the variables.</p>
<img src=https://github.com/AjithGeorge/API-Automation-Postman/blob/master/assets/Pre-Script.png>

<p>Test Script: Handles the looping logic and teardown. </p>
<img src=https://github.com/AjithGeorge/API-Automation-Postman/blob/master/assets/Test%20Script.png>

<p> Variables: Through which the data is transferred for subsequent request</p>
<img src=https://github.com/AjithGeorge/API-Automation-Postman/blob/master/assets/Variables.png>


## Tips#
### 1. Check presence of variables:
Have a folder Pre-Condition/Pre-Requisites which will check for the required variables in the environment/global space and if NOT will create them, thereby reducing the chance of error.

Sample: Setting a function to a variable if not already present.
```javascript
if(!pm.variables.has("variableName")){}
```

```javascript
if (pm.variables.get("variableName")===undefined){}
```

### 2. Endpoint availabiltiy check:
Have a folder for Health Check- to check the availability of the endpoint before commencing the remaining tests. There could be a logic added to prevent the execution of the remaining test if the endpoint is not alive.
```javascript
if(!pm.response.to.be.success){

    postman.setNextRequest(null);
}
```
<img src=https://github.com/AjithGeorge/API-Automation-Postman/blob/master/assets/Health%20Check.png>

### 3. Integer validation:
To compare int values don't forget to do implicit conversion if the retrieved value is from any environment/global variable.
Postman stores all variables in string format only.

```javascript
pm.test("15 should equal 15", () => pm.expect(15).to.equal(parseInt(pm.globals.get('another_global_number'))))
```
### 4. CleanUp methods:
Different teardown approaches: 
- Can specifically remove/unset the variables after the test.
```javascript
function cleanup() {
    pm.environment.unset("id");
    pm.globals.unset("counter");
    pm.globals.unset("arrayLength");
    pm.globals.unset("bookingIds");
}

cleanup()
```
- Can remove/unset the varibales based on a preset prefix.
```javascript
function cleanup() {

    const clean = _.keys(pm.environment.toObject())

    _.each(clean, (arrItem) => {

        if (arrItem.startsWith("test")) {

            pm.environment.unset(arrItem)

        }
    })

}

cleanup()
```
- Can remove an entire type of variables. Approach uses variables that are to be retained as one type(say global) and the others as a different type (say environment).
```javascript
pm.globals.clear(); // This will remove all globals, use with caution.
```
- Remove items using a pre-defined array of variables
```javascript
function cleanup() {
    const clean = ['first_name', 'last_name', 'total_price', 'depositPaid', 'check_in', 'check_out', 'additional_needs']
    for(let i = 0; i < clean.length; ++i){
        pm.environment.unset(clean[i])
    }
}
cleanup()
```

## Sample Report
<img src=https://github.com/AjithGeorge/API-Automation-Postman/blob/master/assets/Report-Sample.png>
