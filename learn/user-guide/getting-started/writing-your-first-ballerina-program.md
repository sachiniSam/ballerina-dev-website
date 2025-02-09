---
layout: ballerina-left-nav-pages-swanlake
title: Writing Your First Ballerina Program
description: Let's create a simple Ballerina HTTP service and also an HTTP client to invoke it.
keywords: ballerina, quick tour, programming language, http service
permalink: /learn/user-guide/getting-started/writing-your-first-ballerina-program/
active: writing-your-first-ballerina-program
intro: Let's create a simple Ballerina HTTP service and also an HTTP client to invoke it.
redirect_from:
  - /learn/user-guide/getting-started/writing-your-first-ballerina-program
---

## Writing a Simple Service

Write a simple HTTP service as shown below in a file with the `.bal` extension.

***hello_service.bal***
```ballerina
import ballerina/http;

# A service representing a network-accessible API
# bound to port `9090`.
service /hello on new http:Listener(9090) {

    # A resource representing an invokable API method
    # accessible at `/hello/sayHello`.
    #
    # + return - A string payload which eventually becomes 
    #            the payload of the response
    resource function get sayHello() returns string {
        return "Hello Ballerina!";
    }
}
```

## Running the Service

In the CLI, navigate to the location in which you have the `hello_service.bal` file and run the service by executing the command below.

```bash
bal run hello_service.bal
```

You get the following output.

```bash
[ballerina/http] started HTTP/WS listener 0.0.0.0:9090
```

This means your service is up and running. 

> **Note:** You can test the service by invoking it using an already-available HTTP client. For example, execute the command below in a new CLI tab to use [cURL](https://curl.haxx.se/download.html) as the client.

```bash
curl http://localhost:9090/hello/sayHello
```

You get the following response.

```bash
Hello Ballerina!
```

Alternatively, you can create a Ballerina HTTP client and use that to invoke the service as follows.

## Creating an HTTP Client to Invoke the Service

A Ballerina client is a component, which interacts with a network-accessible service. It aggregates one or more actions that can be executed on the network-accessible service and accepts configuration parameters related to the network-accessible service.

Create a Ballerina client as a Ballerina program with a `main` function as follows to invoke the `hello` service.   

> **Note**: Returning `error?` from the `main` function allows you to use the `check` keyword to avoid handling errors explicitly. This is only done to keep the code simple. However, in real production code, you may have to handle those errors explicitly.

***hello_client.bal***
```ballerina
import ballerina/http;
import ballerina/io;

public function main() returns @tainted error? {
    // Add the relevant endpoint URL to perform the invocation.
    http:Client helloClient = check new("http://localhost:9090/hello");

    // Perform a `GET` request to the `hello` service. If successful, 
    // the remote call would return an `http:Response` or the payload 
    // (if the `targetType` defaultable parameter is configured).
    // Otherwise an `error` on failure.
    http:Response helloResp = check helloClient->get("/sayHello");

    // Retrieve the payload as a `string` and print it if the 
    // response of the remote call is successful.
    io:println(check helloResp.getTextPayload());
}
```

## Invoking the Service Using the Client 

In a new tab of the CLI, navigate to the location in which you have the `hello_client.bal` file and execute the command below to run the `hello_client.bal` file containing the `main` function (of the client), which invokes the `hello` service.

> **Tip:** Make sure the `hello` service is [up and running](#running-the-service).

```bash
bal run hello_client.bal
```

This would produce the following output.


```bash
Hello Ballerina!
```

## What's Next?

Now, that you have written your first Ballerina program, you can explore more about writing [Ballerina Packages](/learn/user-guide/structuring-ballerina-code/).

<style> #tree-expand-all, #tree-collapse-all, .cTocElements {display:none;} .cGitButtonContainer {padding-left: 40px;} </style>
