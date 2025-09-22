# <p align="center"> Handling General Exceptions with Exception Handlers</p>
Welcome to the lesson on handling general exceptions with exception handlers in FastAPI. In the real world, no matter how carefully we code, unexpected errors can occur. These can range from unforeseen logic errors to unexpected user inputs.

This lesson is critical because it teaches you how to manage these unexpected exceptions gracefully. By the end of this lesson, you'll be able to implement a general exception handler in FastAPI that catches all unhandled exceptions and provides a meaningful error message to the client.

## What Are Exception Handlers?
In FastAPI, an exception handler is a function that manages errors occurring during request processing. These handlers enable your API to deal with unexpected issues gracefully, ensuring users receive clear and meaningful feedback. By using a general exception handler, you can catch unforeseen errors and maintain the stability and user-friendliness of your API, preventing the return of confusing or poorly formatted error messages.

Common situations where a general exception handler proves useful include:

- Unhandled logic errors.
- Unexpected data from external APIs.
- Failures in dependent services.
  
## Implementing a General Exception Handler
Let’s see how to implement a general exception handler in our FastAPI application.

Python
```
# Import Required Modules
from fastapi import Request
from fastapi.responses import JSONResponse

# Define the General Exception Handler
@app.exception_handler(Exception)
async def general_exception_handler(request: Request, exc: Exception):
    return JSONResponse(
        status_code=500,
        content={"detail": "An unexpected error occurred, sorry for the inconvenience"}
    )
```
In this code:

**Decorating the Function**: @app.exception_handler(Exception) tells FastAPI to use this function to handle all exceptions. (Exception is the base class for all built-in exceptions in Python).

**Function Parameters**:
request provides context about the request that caused the exception.
exc is the exception that was raised.

**Returning a Response**: We can choose what we want to return. Here, we are returning a JSONResponse with:
status_code=500 to indicate an internal server error.
A custom message in the content informing the client of the error.

## How to Use It?
Once the general exception handler is defined, it does not need to be explicitly added to each endpoint. FastAPI will automatically use this handler to catch and manage any unexpected exceptions that occur while processing requests. This means you can focus on writing your endpoint logic without worrying about handling unforeseen errors at the endpoint level—your general exception handler will take care of it.

## Summary
In this lesson, we covered how to handle general exceptions in FastAPI using a custom exception handler. Here are the key takeaways:

**Handling Unexpected Errors**: We understood the importance of using a general exception handler to catch unforeseen errors and provide meaningful feedback.

**Implementation**: We implemented a general exception handler and integrated it with our existing endpoints.
Now that you have a solid understanding of handling general exceptions in FastAPI, you are ready to try it out yourself in the practice exercises! Good luck and happy coding!

