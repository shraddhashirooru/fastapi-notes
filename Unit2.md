# Securing Endpoints with Dependency Injection

Welcome back! In our previous lesson, you started to see how Dependency Injection (DI) is used in FastAPI through the use of the Depends function, particularly in the login endpoint. We didn't dive deeply into the concept at that time, but now it's time to explore it in detail.

In this lesson, we will explain what Dependency Injection is and why it's important in software development. By the end of this lesson, you'll understand how it helps to keep code clean and allows us to secure an endpoint using in FastAPI.

## Understanding Dependency Injection
Dependency Injection might sound complicated, but it's a simple and powerful concept. Think of it like this: instead of a function creating everything it needs by itself, it receives those things from the outside. This way, the function doesn't worry about how to get what it needs; it just uses it. This leads to cleaner, more manageable code.

In FastAPI, we use the Depends function to handle Dependency Injection. Imagine having a function that needs certain pieces of information to work. Using Depends, we can provide those pieces of information from outside the function, making our code more organized and easier to test.

## Taking Back from Where We Stopped
Let's quickly recap what we covered last time. We created a FastAPI app, set up a mock database, and implemented a user authentication function along with a login endpoint.

Here is a quick summary of the code:

Python
```
from fastapi import FastAPI, HTTPException, Depends
from fastapi.security import OAuth2PasswordRequestForm

app = FastAPI()

# Mock database with usernames and passwords
users_db = {"user1": "pass1", "user2": "pass2"}


# Function to check if user exists in the database
def authenticate_user(username: str, password: str):
    if users_db.get(username) == password:
        return {"username": username}
    raise HTTPException(status_code=401, detail="Incorrect username or password")


# Login endpoint
@app.post("/login")
async def login(form_data: OAuth2PasswordRequestForm = Depends()):
    return authenticate_user(form_data.username, form_data.password)
```
## Dependency Injection in the Login Endpoint
In our login endpoint, we briefly used Dependency Injection with the Depends function. Here's how we did it:

Python
```
from fastapi import Depends
from fastapi.security import OAuth2PasswordRequestForm

@app.post("/login")
async def login(form_data: OAuth2PasswordRequestForm = Depends()):
    return authenticate_user(form_data.username, form_data.password)
```
Depends(): In the login endpoint, we used Depends() to get an instance of OAuth2PasswordRequestForm, which contains the username and password.

Parameter Injection: Instead of manually extracting username and password from the request, Depends automatically handled it for us and injected form_data directly into the endpoint function.

Cleaner Code: This allowed our login function to focus on authenticating the user without worrying about how to extract the credentials from the request.

This initial use of Depends made our endpoint simpler and more declarative. Now, we'll build on this concept to secure our endpoints further.

## Securing an Endpoint
Now, let's translate the use of Dependency Injection from our login form data to securing an endpoint. We'll use Depends to ensure only authenticated users can access a certain endpoint.

Python
```
# Secure endpoint that requires user authentication
@app.get("/secure-message")
async def read_secure_message(username: str, password: str, user: dict = Depends(authenticate_user)):
    return {"message": "This is a secured message"}
```
The endpoint function read_secure_message takes three parameters:

username: The username provided by the user as a query parameter

password: The password provided by the user as a query parameter

user: A dictionary that holds the result of the authenticate_user function

## How It Works
In this endpoint, we use Depends(authenticate_user) for Dependency Injection:

Explicit Dependency: By using Depends(authenticate_user), we ensure FastAPI will call authenticate_user using the username and password from the query parameters.

Parameter Passing: When a request is made, FastAPI extracts username and password from the query parameters and passes them to authenticate_user.

Injection: The result of authenticate_user is then injected into the user parameter of the read_secure_message function.

This approach ensures our secure endpoint's user parameter contains the result of the authentication check, allowing only authenticated users access to the endpoint.

## Request and Response Flow
Here's the flow when a request is made to the /secure-message endpoint:

Input Request: The client sends a request with username and password as query parameters.

Dependency Injection: FastAPI uses Depends to call authenticate_user with the provided credentials.

Authentication Process in authenticate_user:

If the credentials match an entry in users_db, it returns {"username": username}.
If not, it raises HTTPException with a 401 status code.
Response Handling: Once authenticate_user returns a value, it's injected into the user parameter of the read_secure_message function. This confirms the user is authenticated, allowing the endpoint to return the secured message.

## Summary and Practice
In this lesson, we introduced Dependency Injection and its benefits. You learned how to use FastAPI's Depends function to secure an endpoint, ensuring only authenticated users can access it. We walked through the complete solution code and explained each part to make the concept clear.

Now, it's time to practice what you've learned! You will work on exercises designed to help you master securing endpoints using Dependency Injection. By now, you should feel confident in implementing basic user authentication and securing endpoints in FastAPI.

## Practice:
```
from fastapi import FastAPI, HTTPException, Depends
from fastapi.security import OAuth2PasswordRequestForm

app = FastAPI()

# Mock database with usernames and passwords
users_db = {"user1": "pass1", "user2": "pass2"}

# Function to check if user exists in the database
def authenticate_user(username: str, password: str):
    # Check if user exists in the mock database
    if users_db.get(username) == password:
        return {"username": username}
    # Raise an HTTP 401 Unauthorized error if credentials are incorrect
    raise HTTPException(status_code=401, detail="Incorrect username or password")

# Login endpoint
@app.post("/login")
async def login(form_data: OAuth2PasswordRequestForm = Depends()):
    # Call the function to authenticate the user
    return authenticate_user(form_data.username, form_data.password)

# TODO: Define a secure endpoint named `/protected-message` that requires user authentication
# - The endpoint should return a JSON with the message: This is a protected message
```
