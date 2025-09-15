# Generating JWT Tokens for Authentication
What an amazing progress you've done so far! In previous lessons you learned how to authenticate users using query parameters and how to secure endpoints with dependency injection. While these methods are useful, they have limitations. Specifically, using query parameters to pass credentials is not secure.

That's why in this lesson, we will introduce JSON Web Tokens (JWT) as a more secure and efficient way to handle authentication. By the end of this lesson, you will be able to generate JWT tokens and integrate them into your FastAPI application for authentication.

## Understanding Bearer Tokens
Before we dive into JWT, let's understand Bearer Tokens. A bearer token is like a "movie ticket" that you present to gain entry. Whoever "bears" this token can access certain resources.

Instead of sending your username and password with every request—which is risky—you log in once and receive a bearer token. This token is then included in the headers of future requests to prove your identity.

JWT is a type of bearer token. By using tokens, you improve security and efficiency since your credentials are not repeatedly transmitted, and servers can quickly verify token validity.

## What is JWT?
JWT stands for JSON Web Token, a compact and URL-safe way to transmit information securely between two parties. These tokens are commonly used in modern web applications to maintain secure communication. Instead of passing login credentials through query parameters—which can be easily intercepted—they provide a safer alternative.

A JWT token consists of three parts: a Header, a Payload, and a Signature.

The Header contains metadata about the token, such as the type and hashing algorithm used.
The Payload carries the actual data or claims, like user information and token expiration time.
The Signature ensures the token's integrity by verifying that the content hasn't been tampered with, using a secret key.
By encoding this information, JWT protects user data and offers a reliable way to verify user identities.

## How Does a Token Look Like?
JWTs are Base64Url encoded, making them compact and URL-safe. Here's an example:

Plain text
```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiJ1c2VyMSIsImV4cCI6MTcyMjU0MTEzOX0.NVgxSDjgt1Yh27qvlfxI1YNW5eEqOt4OTrTawb5bMUc
Header: eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9
Payload: eyJzdWIiOiJ1c2VyMSIsImV4cCI6MTcyMjU0MTEzOX0
Signature: NVgxSDjgt1Yh27qvlfxI1YNW5eEqOt4OTrTawb5bMUc
```
The payload can contain various claims, such as the user ID and an expiration time ("exp"). The signature ensures that the token hasn't been tampered with.

## Importing Essential Modules
To work with JWT tokens in FastAPI, we first need to import some essential modules:

Python
Copy to clipboard
from jose import jwt
from datetime import datetime, timedelta
The jose library will help us encode and decode JWT tokens, while datetime and timedelta allow us to manage token expiration times.

## Configuring JWT Settings
Next, let's set up some important configuration options for JWT:

Python
```
# Configuration for JWT
SECRET_KEY = "your_secret_key"
ALGORITHM = "HS256"
ACCESS_TOKEN_EXPIRE_MINUTES = 30
```
SECRET_KEY: A secret key used to encode the token. Keep it safe and secure.

ALGORITHM: The algorithm used for encoding. Here, we use HS256.

ACCESS_TOKEN_EXPIRE_MINUTES: The duration for which the token remains valid.

## Creating the JWT Token
Now, let's create the function to generate JWT tokens. This function will encode user information and set an expiration time for the token:

Python
```
# Function to create JWT token with an expiration time
def create_access_token(data: dict):
    # Copy the provided data to avoid modifying the original
    to_encode = data.copy()
    # Set the token expiration time
    expire = datetime.utcnow() + timedelta(minutes=ACCESS_TOKEN_EXPIRE_MINUTES)
    # Add the expiration time to the token payload
    to_encode.update({"exp": expire})
    # Encode the token with the secret key and algorithm
    return jwt.encode(to_encode, SECRET_KEY, algorithm=ALGORITHM)
```
This function performs several crucial steps:

Copy the Data: The function begins by copying the provided data dictionary. This ensures that the original data is not modified during the token creation process.

Set Token Expiration: It calculates the token expiration time by adding the specified number of minutes (ACCESS_TOKEN_EXPIRE_MINUTES) to the current UTC time. This expiration time ensures that the token is only valid for a limited period.

Update Payload: The expiration time is added to the token's payload. This is done by updating the copied data dictionary (to_encode) to include a new key-value pair with "exp" as the key and the calculated expiration time as the value.

Encode the Token: Finally, the jwt.encode function from the jose library encodes the token. It takes three arguments: the payload (to_encode), the secret key (SECRET_KEY), and the hashing algorithm (ALGORITHM). The result is the encoded JWT token, which can be used for secure authentication in your FastAPI application.

## Setup Recap
Before using any tokens, let's quickly recap what we have set up so far. We've created a FastAPI application with a mock database of users.

Python
```
from fastapi import FastAPI

app = FastAPI()

# Mock database with usernames and passwords
users_db = {"user1": "pass1", "user2": "pass2"}
```
Previously, we had an authentication function (authenticate_user) that checked if a user existed in the database using username and password parameters directly. However, we will now update it to use OAuth2PasswordRequestForm for dependency injection, which provides a more secure and efficient way to handle form data.

## Updated Authentication Function
Here’s the updated authenticate_user function:

Python
```
from fastapi import HTTPException, Depends
from fastapi.security import OAuth2PasswordRequestForm

# Function to check if user exists in the database
def authenticate_user(form_data: OAuth2PasswordRequestForm = Depends()):
    username = form_data.username
    password = form_data.password
    if users_db.get(username) == password:
        return {"username": username}
    raise HTTPException(status_code=401, detail="Incorrect username or password")
```
Now, it uses OAuth2PasswordRequestForm as a dependency, which automatically extracts the form data from the login request. Instead of passing credentials directly, the credentials are accessed from form_data, a convenience provided by OAuth2PasswordRequestForm.

This change allows the authentication function to seamlessly integrate with FastAPI's dependency injection system, enabling secure and structured data handling.

## Creating the Token Response Model
Before we implement a login endpoint, we need to define a response model for the token, which will specify the structure of the response that the client will receive upon successful authentication.

Here's how we define a Token class using Pydantic:

Python
```
from pydantic import BaseModel

# Token class
class Token(BaseModel):
    access_token: str
    token_type: str
```
This class has two fields:

access_token: A string that will hold the JWT token.

token_type: A string indicating the type of the token, usually "bearer".

By using this model, we ensure that the response structure is well-defined and consistent.

## Implementing the Login Endpoint
Now that we've defined the token response model, let's implement the login endpoint that authenticates the user and generates a JWT token.

Python
```
from fastapi import FastAPI, HTTPException, Depends
from fastapi.security import OAuth2PasswordRequestForm

# Login endpoint
@app.post("/login", response_model=Token)
async def login_for_access_token(form_data: OAuth2PasswordRequestForm = Depends()):
    # Check username and password in the database
    user = authenticate_user(form_data)
    # Create an access token
    access_token = create_access_token(data={"sub": user["username"]})
    # Return the access token 
    return {"access_token": access_token, "token_type": "bearer"}
```
When this endpoint is called, the form_data parameter is automatically populated with the form data using the dependency. The authenticate_user function is then called with form_data to verify the user's credentials against the mock database. If the credentials are correct, the function returns the user information.

Next, the create_access_token function is invoked with the authenticated user's username as the data. This function generates a JWT token that includes the user's information and an expiration time. Finally, the endpoint returns the generated token and its type (bearer) in the response. This token can then be used by the client for authenticated requests to other endpoints in the application.

## Expected Response
If the login credentials are correct, the response will have a status code of 200 and look something like this:

JSON
```
{
    "access_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiJ1c2VyMSIsImV4cCI6MTcyMjU0MTEzOX0.NVgxSDjgt1Yh27qvlfxI1YNW5eEqOt4OTrTawb5bMUc",
    "token_type": "bearer"
}
```
This shows that the JWT token has been successfully generated and returned. The client can now use this token to authenticate subsequent requests to secured endpoints by including it in the Authorization header of the HTTP request.

## Summary and Practices
Today you learned about the importance of JWT tokens for secure authentication. We covered the structure of these tokens, how to create them in FastAPI, and how to integrate them into the login endpoint.

As you move on to the practice exercises, remember to review the key points covered in this lesson. These exercises will help reinforce your understanding of generating and using JWT tokens for authentication in FastAPI.

In the next lesson, we'll dive deeper into how to use these JWT tokens to secure other endpoints in your application. Keep up the great work!

