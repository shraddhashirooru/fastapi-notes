# Verifying JWT Tokens for Secure Access
Welcome back! In our previous lessons, you learned how to set up basic user authentication using FastAPI. You also explored how to secure endpoints using Dependency Injection and understood the importance of JSON Web Tokens (JWT) for secure authentication.

Today, we’re going to build on those concepts and the progress you've made. Remember how we generated JWT tokens and returned them to the user upon login? Now, let’s take the next step: verifying these tokens to secure our API endpoints.

By the end of this lesson, you'll be able to implement and verify JWT tokens in FastAPI, ensuring only authenticated users can access certain parts of your application. Let's dive in!

# Setup from Previous Lesson
Before we move forward, let's quickly recap what we covered in the previous lesson, where we generated JWT tokens for authentication. Here's a snippet of the essential code you learned:

Python
```
from fastapi import FastAPI, HTTPException, Depends
from fastapi.security import OAuth2PasswordRequestForm
from pydantic import BaseModel
from jose import JWTError, jwt
from datetime import datetime, timedelta

app = FastAPI()

# Mock database with usernames and passwords
users_db = {"user1": "pass1", "user2": "pass2"}

# Configuration for JWT
SECRET_KEY = "your_secret_key"
ALGORITHM = "HS256"
ACCESS_TOKEN_EXPIRE_MINUTES = 30

# Token class
class Token(BaseModel):
    access_token: str
    token_type: str

# Function to create JWT token with an expiration time
def create_access_token(data: dict):
    to_encode = data.copy()
    expire = datetime.utcnow() + timedelta(minutes=ACCESS_TOKEN_EXPIRE_MINUTES)
    to_encode.update({"exp": expire})
    return jwt.encode(to_encode, SECRET_KEY, algorithm=ALGORITHM)
    
# Function to check if user exists in the database
def authenticate_user(form_data: OAuth2PasswordRequestForm = Depends()):
    username = form_data.username
    password = form_data.password
    if users_db.get(username) == password:
        return {"username": username}
    raise HTTPException(status_code=401, detail="Incorrect username or password")

# Login endpoint
@app.post("/login", response_model=Token)
async def login_for_access_token(form_data: OAuth2PasswordRequestForm = Depends()):
    user = authenticate_user(form_data)
    access_token = create_access_token(data={"sub": user["username"]})
    return {"access_token": access_token, "token_type": "bearer"}
```
This code generated JWT tokens upon a successful login, which will be crucial for securing endpoints.

## Understanding Token Verification
To secure API endpoints, it’s essential to verify JWT tokens. When a user logs in and receives a token, every subsequent request to secure endpoints must include this token. We will verify this token to authenticate the user.

The verification process involves:

Decoding the JWT token to extract the payload.
Confirming the token’s validity and expiration.
Extracting user credentials from the token to verify they’re authorized to access the endpoint.
Without verification, anyone with the token could access secure endpoints, compromising your application's security.

## Set Up OAuth2 Scheme
To implement token verification in FastAPI we first need to set up the OAuth2 scheme and create a function to decode the JWT token and extract the user credentials.

Python
```
from fastapi.security import OAuth2PasswordBearer

Define the OAuth2 scheme to be used for token authentication
oauth2_scheme = OAuth2PasswordBearer(tokenUrl="login")
```
The OAuth2PasswordBearer is used to define the scheme for token authentication. This tells FastAPI to expect the token via the "Authorization" header using the Bearer <token> format.

## Passing the Scheme to a Function
Next, we'll create a function that depends on this OAuth2 scheme for token extraction:

Python
```
# Define a function to get the current user from the JWT token
async def get_current_user(token: str = Depends(oauth2_scheme)):
```
The get_current_user function is created as an async function that depends on the oauth2_scheme for extracting the token.

## Verifying the Token
Now, let's dive into the logic of this function, which will decode and verify the JWT token:

Python
```
from jose import JWTError

async def get_current_user(token: str = Depends(oauth2_scheme)):
    try:
        # Decode and verify the JWT token
        payload = jwt.decode(token, SECRET_KEY, algorithms=[ALGORITHM])
        # Extract the username from the token payload
        username: str = payload.get("sub")
        # Check if the username is present in the token payload
        if not username:
            raise HTTPException(status_code=401, detail="Invalid token")
        # Return the username if valid
        return {"username": username}
    # Handle any JWT decoding errors
    except JWTError:
        raise HTTPException(status_code=401, detail="Invalid token")
```
Here's a breakdown of what it does:

### 1. Decode and Verify the Token:

Use jwt.decode to decode and verify the token using your SECRET_KEY and ALGORITHM.
This step extracts the token payload, which includes the user credentials.

### 2. Extract and Validate the Username:

Retrieve the username from the token payload's "sub" (subject) field.
Check if the username exists; if it's missing, the token is invalid, and you should raise an HTTPException with status code 401.

### 3. Handle Decoding Errors:

Use a try-except block to catch any JWTError. If an error occurs during decoding, raise an HTTPException with status code 401, ensuring the security of your application.
By following these steps, we create a reliable method to verify JWT tokens, ensuring only authenticated users can access secure parts of our API.

## Securing an Endpoint
With the get_current_user function in place to verify JWT tokens, securing an endpoint becomes straightforward. Let's define a new endpoint, for example, /secure-message, and use the Depends function with get_current_user to enforce token verification:

Python
```
# Secured endpoint which depends on decoding the token
@app.get("/secure-message")
async def read_secure_message(current_user: dict = Depends(get_current_user)):
    return {"message": "This is a secured message"}
```
Here’s how it works:

The client includes the JWT token in the HTTP Authorization header, using the format Bearer <token>. This indicates that the request includes a token.
read_secure_message executes only if get_current_user successfully verifies the JWT token from the Authorization header.
If the token is valid, current_user contains the extracted user information. If the token is invalid or missing, get_current_user raises an HTTPException with status code 401, blocking access to the endpoint.
This method ensures that only authenticated users with valid JWT tokens can access sensitive data or functionalities in your application.

## Accessing the Secured Endpoint
To access the secured endpoint, clients should:

Login to Get the Token:

Send a request to the /login endpoint with your username and password to receive a JWT token.
The response will include an access_token and token_type.
Use the Token to Access the Secured Endpoint:

Include the JWT token in the Authorization header of the request to the secured endpoint.
If the token is valid, the response will include the secured message.
## Unauthorized Access
If the user attempts to access the secured endpoint without logging in first and obtaining a token, or if they provide an invalid token, the following will happen:

The get_current_user function will not be able to decode a valid token.
An HTTPException with status code 401 (Unauthorized) will be raised:
JSON
Copy to clipboard
{
  "detail": "Not authenticated"
}
The user will be denied access to the secured endpoint until they successfully log in and provide a valid token with their request.
## Summary and Last Practices
In this lesson, you learned how to verify JWT tokens to secure API endpoints in FastAPI. We started by understanding the importance of token verification and implemented it step-by-step. We also created a secured endpoint and discussed testing it to ensure everything works as expected.

Congratulations on completing the final lesson in this path! You've acquired essential skills for implementing secure authentication in your FastAPI applications. Now, test your skills with the practice exercises to solidify your knowledge and apply what you've learned to real-world scenarios. Great job!

