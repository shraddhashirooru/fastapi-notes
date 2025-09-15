With the basic login endpoint now set up, your next step is to execute and test the functionality.

This code includes a mock user database and a function to authenticate users by checking their username and password.

Click on Run to test the /login endpoint by authenticating both a valid user and an invalid user. Observe the responses for each case.

Just a moment while we set up the environment, then you'll be good to go! 🛠️🚀

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
```
