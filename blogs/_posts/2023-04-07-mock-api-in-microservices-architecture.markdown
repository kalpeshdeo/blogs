---
layout: post
title:  "Mock API in Microservices Architecture"
date:   2023-04-07 15:30:12 +0530
categories: microservices
---
## **Overview**

Setting up the entire stack locally isn't an easy task, especially when there are multiple microservices involved. If this is something you’re already struggling with then you are at the right place. We will discuss the solution by using Mock APIs.

## Challenges

A large application delivered as a network of small microservices requires real-time inter-service communication to function as a single application. As the number of microservices increase, this architecture becomes even more challenging. Here, we will focus only on synchronous communication using HTTP/S APIs. Most likely one would arrive at a situation with the following problems:

1. Setting up individual microservice on a developer’s local machine
    - Developer needs to configure other dependent service’s source code as well and keep pulling the latest code. (It is still okay with small dev team and having 3-5 microservices)
    - On each latest pull, developer needs to make sure it doesn’t break anything
    - Handling DB migrations for other dependent services and synchronizing them is a challenge.
2. Running Integration tests:Testing code changes for dependent features on a local machine is always painful.

## # Solutions

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

    
    ![image.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/3c2d387c-6553-4a3e-a046-b4c2cd59c405/image.png)
    
4. This will generate the mock server URL like this`https://18b77921-cc85-4819-811d-0403eec04251.mock.pstmn.io`
5. Add an example to all the requests
    
    [https://lh5.googleusercontent.com/waCW7XA9PDichJSIXAbmQN1fgJzpS5EeRhCCTGNvRUE3DFlKqogxT9tfbFLt7irztSfIV0HL848bzp7IqWYeOkvwKIToX0vyfWK15mssP_WKi4czx3mxCVo-QeOfVMhgIan3veyT](https://lh5.googleusercontent.com/waCW7XA9PDichJSIXAbmQN1fgJzpS5EeRhCCTGNvRUE3DFlKqogxT9tfbFLt7irztSfIV0HL848bzp7IqWYeOkvwKIToX0vyfWK15mssP_WKi4czx3mxCVo-QeOfVMhgIan3veyT)
    
6. Save the expected response JSON with HTTP response code
    
    [https://lh4.googleusercontent.com/5jwAJyhqnXcfE7DSdvZGTKmyIYi1sPktEPpEOazML0rvAZf4YYcU9PXQ491yzPil9tJTCLUUm6QluFjbni4y9JD2CPtp7tBqyYKeSS_qmRp9X3wUbCgZk9X4rrS-PB-s-c5tFO-5](https://lh4.googleusercontent.com/5jwAJyhqnXcfE7DSdvZGTKmyIYi1sPktEPpEOazML0rvAZf4YYcU9PXQ491yzPil9tJTCLUUm6QluFjbni4y9JD2CPtp7tBqyYKeSS_qmRp9X3wUbCgZk9X4rrS-PB-s-c5tFO-5)
    
7. Now just replace the service URL with mock server URL to access the APIs like this`https://18b77921-cc85-4819-811d-0403eec04251.mock.pstmn.io/v1/users`.This will return the list of users saved earlier.
    
    [https://lh5.googleusercontent.com/Ho-zek1vHqjzVQ_oUhOXv0MZVu_EjHzYZCRFeDYPBsAYjT5tZJHbHpH6L2wli6IzOIuatSZ4To_8ZkpIB2PorPOwyypFWtJSobeGrB51DN6x2D3_9aCecG49g2Be-YJYu1Eb_2Le](https://lh5.googleusercontent.com/Ho-zek1vHqjzVQ_oUhOXv0MZVu_EjHzYZCRFeDYPBsAYjT5tZJHbHpH6L2wli6IzOIuatSZ4To_8ZkpIB2PorPOwyypFWtJSobeGrB51DN6x2D3_9aCecG49g2Be-YJYu1Eb_2Le)
    
8. Query params: You can add multiple example responses for query params, Postman Mock server matches the query param and returns the matched response.e.g. We have saved two examples, one for each.
    
    [https://lh6.googleusercontent.com/Aszm2TEsiC0r424leJ6Z3d6HTZgthZVNKEZs-bgCX8vz8Fw574yNiUA55tx5_OsMD8XHDduNk-mWfWJ-n0tuB-um_FSj7-DvLs5fKchAI7-PxdF9m7SW--EE0p2mY2bO0lV8tKpy](https://lh6.googleusercontent.com/Aszm2TEsiC0r424leJ6Z3d6HTZgthZVNKEZs-bgCX8vz8Fw574yNiUA55tx5_OsMD8XHDduNk-mWfWJ-n0tuB-um_FSj7-DvLs5fKchAI7-PxdF9m7SW--EE0p2mY2bO0lV8tKpy)
    
9. Mock server will return the user details as per `user_id` param
    
    [https://lh5.googleusercontent.com/zvLWDiOxf2_y4SCwqFJpaQ0o9cl3C3W1CC3d3_pMHAUDXf46yGHB8Lo_l0wcAA6XugfRVQiP5TBapkaGk3qfIBae55-erYZ4wuGCRlELHzjoQUnAaMpS9Bj3X5RxL_AzR4LZa9Ta](https://lh5.googleusercontent.com/zvLWDiOxf2_y4SCwqFJpaQ0o9cl3C3W1CC3d3_pMHAUDXf46yGHB8Lo_l0wcAA6XugfRVQiP5TBapkaGk3qfIBae55-erYZ4wuGCRlELHzjoQUnAaMpS9Bj3X5RxL_AzR4LZa9Ta)
    
10. Headers: Same as query params, we can have multiple responses as per headers as well. But in case of headers we need to set `x-mock-match-request-body header` to true to enable request body matching and also set `x-mock-match-request-headers` for matching incoming mock request headers while making an API call to the mock server.

Once you have all the API requests added with examples in the collection, you can configure the mock server URL in the service. You can then call any of the APIs using the mock server URL and API request path.

## Conclusion

Postman Mock servers unblock the developer to start the implementation from day one for dependent features. And it simplifies the service configuration on the local machine.