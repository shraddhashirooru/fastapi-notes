# <p align="center">Practice4</p>

Great job on setting up the basic user authentication! Now, let’s build on that foundation.

In this exercise, your task is to fill in the missing parts of the code. You need to complete the function that checks if a user exists in the mock database and raises an HTTP 401 Unauthorized error if the credentials are incorrect.

Let's dive in!
```
from fastapi import FastAPI, HTTPException, Depends
from fastapi.security import OAuth2PasswordRequestForm

app = FastAPI()

# Mock database with usernames and passwords
users_db = {"user1": "pass1", "user2": "pass2"}


# Function to check if user exists in the database
def authenticate_user(username: str, password: str):
    # TODO: Check if user exists and password matches
        # TODO: If true return a dictionary with the username 
    # TODO: Raise an HTTP 401 Unauthorized error if credentials are incorrect


# Login endpoint
@app.post("/login")
async def login(form_data: OAuth2PasswordRequestForm = Depends()):
    # Call the function to authenticate the user
    return authenticate_user(form_data.username, form_data.password)
