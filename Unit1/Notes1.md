
# <p align="center"> Introduction to Basic User Authentication</p>

Welcome to another FastAPI course, where we will delve into creating a robust authentication system. Authentication is vital in any web application, as it helps ensure that only authorized users can access certain features or data. Today, we'll set up a basic login endpoint to validate user credentials, giving us a solid foundation to build more advanced authentication systems in future lessons.

## Understanding User Authentication
User authentication is the process of verifying the identity of a user attempting to access a system. The most basic form involves checking whether a submitted username and password match those stored in a database. For this lesson, we will use a simple dictionary as our user database. This allows us to focus on the concepts without worrying about database setup.

## Setting Up our Application
Before we proceed with building our authentication system, let's set the groundwork for our FastAPI project. To kick things off, we’ll create a FastAPI app instance and set up a mock database to store user credentials.

Python

```
from fastapi import FastAPI

app = FastAPI()

# Mock database with usernames and passwords
users_db = {"user1": "pass1", "user2": "pass2"}
```
We begin by importing the necessary components from FastAPI. Next, we instantiate our FastAPI app and define users_db, a simple dictionary that acts as our mock database containing usernames and passwords.

## Creating the Authentication Function
Next, we need to create a function to authenticate users by verifying their username and password.

Let's define the function:

Python
```
from fastapi import HTTPException

# Function to check if user exists in the database
def authenticate_user(username: str, password: str):
    # Check if user exists in the mock database
    if users_db.get(username) == password:
        return {"username": username}
    # Raise an HTTP 401 Unauthorized error if credentials are incorrect
    raise HTTPException(status_code=401, detail="Incorrect username or password")
```
This function, authenticate_user, accepts a username and a password. It checks if the provided username exists in our users_db and if the password matches. If they do, it returns a dictionary with the username.

If the credentials are incorrect, it raises an HTTPException with a 401 status code, indicating that the authentication failed. Remember, a 401 Unauthorized status code means that the request lacks valid authentication credentials to proceed.

## Implementing the Login Endpoint
Now, let's create an endpoint to handle user login. For this, we'll use OAuth2PasswordRequestForm to parse the form data submitted by the user.

Here's the code:

Python
```
from fastapi import Depends
from fastapi.security import OAuth2PasswordRequestForm

# Login endpoint
@app.post("/login")
async def login(form_data: OAuth2PasswordRequestForm = Depends()):
    # Call the function to authenticate the user
    return authenticate_user(form_data.username, form_data.password)
```
Let's break down what each part does!

## 1. Defining the Login Endpoint
Python
```
@app.post("/login")
```
This line defines a POST route at /login. It means that the endpoint will handle HTTP POST requests at this path.

## 2. Defining the Function with OAuth2PasswordRequestForm
Python

```
from fastapi.security import OAuth2PasswordRequestForm
```
async def login(form_data: OAuth2PasswordRequestForm ...):
If you're familiar with JSON request bodies, form data is another way to structure input, especially for HTML forms. OAuth2PasswordRequestForm, which we imported from fastapi.security, helps extract the username and password fields from form data, making it easy for FastAPI to handle traditional login submissions.

## 3. Using Depends to Call OAuth2PasswordRequestForm
Depends() is a function that tells FastAPI to use another function or class to provide a value for form_data. In this case, it tells FastAPI to call the OAuth2PasswordRequestForm to retrieve the form data.

Python
```
from fastapi import Depends
from fastapi.security import OAuth2PasswordRequestForm

async def login(form_data: OAuth2PasswordRequestForm = Depends()):
```
Essentially, it ensures that before the login function is called, FastAPI will process and validate the incoming form data, and then pass it to the function as form_data.

## 4. Authenticating the User
Inside the login function, we call our previously defined authenticate_user() function with form_data.username and form_data.password to verify the user's credentials. Here are the possible outcomes:

Python
```
return authenticate_user(form_data.username, form_data.password)
```
If the credentials are correct, authenticate_user() will return a dictionary with the username:

Plain text
```
Status Code: 200
{"username": "user1"}
```
Othewise, it will raise a 401 Unauthorized error:

Plain text

```
Status Code: 401
{detail="Incorrect username or password"}
```
## Why Use Form Data for Login?
When handling user login, we often deal with HTML forms. Forms are a common and traditional way to collect user input and send it to the server.

Form Data in Authentication: Login forms typically collect a username and password. While using JSON in the request body is common in many web applications, form data is a standard for authentication.

Using OAuth2PasswordRequestForm: The OAuth2PasswordRequestForm class provided by FastAPI simplifies the process of handling form data. It automatically extracts the necessary fields like username and password.

Benefits of Depends: The Depends() function in FastAPI allows for dependency injection. This makes your code cleaner and more modular by letting FastAPI handle parsing and validation of the form data automatically.

By using OAuth2PasswordRequestForm and Depends(), we streamline form data handling and ensure our login endpoint is efficient, adhering to established web development practices.

## Summary and Next Steps
We've delved into the essentials of user authentication, created a function to verify user credentials, and set up a login endpoint using FastAPI and OAuth2PasswordRequestForm.

You're now equipped with the foundational knowledge to set up basic user authentication in a FastAPI app. Dive into the practice exercises to cement what you've learned and get ready for even more exciting topics ahead!




