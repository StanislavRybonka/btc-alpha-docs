BTC-Alpha's API V1
==================

**Introduction**
----------------
           The BTC-Alpha API’s are designed to allow access to all of the features of the BTC-Alpha platform.
           The end goal is to allow people to potentially recreate the entire platform on their own.If you
           would like to suggest changes to the documentation, please see the github at "Some link"....

**API Access**
--------------
          In order to access the parts of the API which require authentication, you must generate
          an API key and an API secret using this page

REST
====

**General**
-----------

**Authentication**
------------------
    Authentication is done using an API key and a secret.To generate this pair, go to the API Access page.
     As an example of how to authenticate, we can look at the “account_infos” endpoint. Take the example payload
     to the right.

**Parameters**
--------------
   Requests parameters for POST requests (authenticated):

    /v1/call/?parameter=2

   Requests parameters for GET requests (non-authenticated) are GET parameters, appended to the URL being called as follows:

    /v1/call/?parameter=1


