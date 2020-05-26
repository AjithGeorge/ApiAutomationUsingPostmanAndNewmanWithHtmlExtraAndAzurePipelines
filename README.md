# API-Automation-Postman

API Trsting using Postman 

## Areas Covered:
- Create API Tests Using [Postman Scripts](https://learning.postman.com/docs/postman/scripts/test-examples/)
- Generate Report using [Newman reporter](https://www.npmjs.com/package/newman-reporter-html)
- Customized Newman Reporter -[htmlextra](https://github.com/DannyDainton/newman-reporter-htmlextra)
- Assertions using [Chai Assertion Library](https://www.chaijs.com/api/)

Main Reference: [DannyDainton:All-Things-Postman](https://github.com/DannyDainton/All-Things-Postman#example-guides)

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
Quick Ref:
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
