---
layout: post
title:  "Mock API in Microservices Architecture"
date:   2023-04-07 15:30:12 +0530
categories: microservices
---
## Overview

Setting up the entire stack locally isn't an easy task, especially when there are multiple microservices involved. If this is something you’re already struggling with then you are at the right place. We will discuss the solution by using Mock APIs.

## Challenges

A large application delivered as a network of small microservices requires real-time inter-service communication to function as a single application. As the number of microservices increase, this architecture becomes even more challenging. Here, we will focus only on synchronous communication using HTTP/S APIs. Most likely one would arrive at a situation with the following problems:

1. Setting up individual microservice on a developer’s local machine
    - Developer needs to configure other dependent service’s source code as well and keep pulling the latest code. (It is still okay with small dev team and having 3-5 microservices)
    - On each latest pull, developer needs to make sure it doesn’t break anything
    - Handling DB migrations for other dependent services and synchronizing them is a challenge.
2. Running Integration tests:Testing code changes for dependent features on a local machine is always painful.

## Solutions

There are multiple ways to set up a microservices stack locally.

1. Clone the repos of all the services and run them on the local machine. This is not feasible if you have many microservices.
    - This needs sufficient resources available on the Dev machine to run all the services.
    - If a developer pulls the latest code from other dependent services, the code could break for no fault on their own.
    - DB migrations and synchronizing them is a challenge
2. Have a dedicated DEV environment deployed on the cloud and let your local service communicate with other services.
    - This requires extra conditional code to bypass the authentication and authorization as your local service is now communicating with other services on the Dev environment.
    - It ends with data inconsistency as all the developers are going to use the same DEV environment. It is not possible or cost-effective to provide a separate DEV environment to all developers.
    - Challenge with background workers, where jobs will be picked up by workers running on dev env instead of your local worker.
3. Have a Mock server that will return the mock response for the APIs. This is a better solution than the above two, but again, this will add an extra burden on the Dev team to maintain this server.

We tried all the above solutions and ended up with [Mock Servers using Postman](https://learning.postman.com/docs/designing-and-developing-your-api/mocking-data/setting-up-mock/).This is a very easy and simple solution to simulate the real APIs. It not only solves the local machine setup issue but it is very useful when your application is in early-stage development, where all the microservices are under development. In that case, you can mock the responses for other services and start the development of your service without waiting for others. This also makes it easy for frontend teams to proceed with their feature development by using mock APIs that simulate both success and error conditions.

## Mock APIs

Let’s see how a mock API server works by taking an example of a User service with a simple functionality to list users.

### Request

**GET** https://usersvc.yourdomain.com/v1/users

### Response

```
{
  "data": {
    "users": [
      {
        "id": "98a4b655-9b0c-47f0-bf35-c56e4fb5fe13",
        "name": "FirstUserName"
      },
      {
        "id": "0b19b534-83f3-4072-9a9c-eb282fe2e373",
        "name": "SecondUserName"
      }
    ]
  }
}
```

***Note**: You need to be signed into a Postman account to create a mock server*

1. Create a collection
2. Create your API requests and make sure those are saved to a collection
3. Create a mock server from the collection info menu

    ![image1](https://user-images.githubusercontent.com/9951874/230603197-86f157fb-3593-430c-aacc-b71bafd75de1.png)
    
4. This will generate the mock server URL like this`https://18b77921-cc85-4819-811d-0403eec04251.mock.pstmn.io`
5. Add an example to all the requests
    
   ![image2](https://user-images.githubusercontent.com/9951874/230603282-a63286d7-e8c6-4b46-ac5d-c8f5bdb1ceac.png)

6. Save the expected response JSON with HTTP response code

    ![image3](https://user-images.githubusercontent.com/9951874/230603443-2e97c225-572d-455e-b73e-6fcc6d48ef4f.png)

7. Now just replace the service URL with mock server URL to access the APIs like this`https://18b77921-cc85-4819-811d-0403eec04251.mock.pstmn.io/v1/users`.This will return the list of users saved earlier.
    
    ![image4](https://user-images.githubusercontent.com/9951874/230603484-3e692423-beaf-4685-a1dc-08e0a6eb9d71.png)
    
8. Query params: You can add multiple example responses for query params, Postman Mock server matches the query param and returns the matched response.e.g. We have saved two examples, one for each.
    
    ![image5](https://user-images.githubusercontent.com/9951874/230603539-11dd6e49-7357-4f85-86e2-ea5844e8d291.png)
    
9. Mock server will return the user details as per `user_id` param
    
    ![image6](https://user-images.githubusercontent.com/9951874/230603659-390c2a13-fd9e-436e-995d-5dafdab1b2c3.png)

    
10. Headers: Same as query params, we can have multiple responses as per headers as well. But in case of headers we need to set `x-mock-match-request-body header` to true to enable request body matching and also set `x-mock-match-request-headers` for matching incoming mock request headers while making an API call to the mock server.

Once you have all the API requests added with examples in the collection, you can configure the mock server URL in the service. You can then call any of the APIs using the mock server URL and API request path.

## Conclusion

Postman Mock servers unblock the developer to start the implementation from day one for dependent features. And it simplifies the service configuration on the local machine.
