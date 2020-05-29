# API-Automation-Postman

API Testing using Postman 

## Areas Covered:
- Create API Tests Using [Postman Scripts](https://learning.postman.com/docs/postman/scripts/test-examples/)
- Run tests using Postman Collection Runner and through [Newman](https://www.npmjs.com/package/newman) CLI tool
- Generate Report using [Newman reporter](https://www.npmjs.com/package/newman-reporter-html)
- Customized Newman Reporter -[htmlextra](https://github.com/DannyDainton/newman-reporter-htmlextra)
- Assertions using [Chai Assertion Library](https://www.chaijs.com/api/)

Main Reference: [DannyDainton: All-Things-Postman](https://github.com/DannyDainton/All-Things-Postman#example-guides)

## Overview:
The solution tests APIs from,

1. [Restful-Booker -Self Hosted](https://github.com/mwinteringham/restful-booker)
2. [Restful-Booker -Public Link](https://restful-booker.herokuapp.com/)


## Prerequisites:
1. Postman Application installed.
2. Newman tool installed.
3. Node and Npm installed.
4. Htmlextra/Newman reporter node packages installed.

## Execution Steps:
1. Clone the repo.
2. Import the collection and environment to postman tool.
3. Run the tests from either Postman Collection Runner or through Newman tool.
4. Report is will get generated for the execution through Newman tool.

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
Ref: [Extended Checks](https://github.com/DannyDainton/All-Things-Postman/blob/master/Examples/08_extendingOurTests.md)

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
