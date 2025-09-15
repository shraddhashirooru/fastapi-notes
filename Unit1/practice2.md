# <p align="center">Practice 2</p>
Nice work getting through the setup! Now, let's reinforce what you've learned with a practical task.

This task focuses on using OAuth2PasswordRequestForm with Depends in the login function to handle form data parsing and dependency injection.

Fill in the missing part where indicated to complete the login endpoint.

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

# TODO: Use OAuth2PasswordRequestForm and Depends to handle the request body
@app.post("/login")
async def login(form_data: _______________): 
    # Call the function to authenticate the user
    return authenticate_user(form_data.username, form_data.password)
```
