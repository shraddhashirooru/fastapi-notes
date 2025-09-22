# <p align="center"> Exploring Basic Status Codes with FastAPI</p>

Welcome to another course of FastAPI. In this lesson, we will focus on understanding and implementing common HTTP status codes in our API endpoints. Status codes are crucial in API development as they inform clients about the result of their requests.

By the end of this unit, you'll be well-versed in using status codes to indicate the outcome of different API operations, such as fetching, creating, and deleting resources.

## Recap of Setup
Before we dive deeper into status codes, let's quickly recap our FastAPI project setup, which includes a mock database and data validation using Pydantic models.

Here is the initial setup code:

Python
```
from fastapi import FastAPI
from pydantic import BaseModel

app = FastAPI()
# Mock dataset of crew members
crew = [
    {"id": 1, "name": "Cosmo", "role": "Captain"},
    {"id": 2, "name": "Alice", "role": "Engineer"},
    {"id": 3, "name": "Bob", "role": "Scientist"}
]

# Pydantic model for validation
class CrewMember(BaseModel):
    name: str
    role: str
```
In this setup:

We create an instance of FastAPI named app.

We define a mock dataset crew, which is a list of dictionaries representing crew members.

We use the Pydantic library to define a CrewMember model that ensures the incoming data is structured correctly.

## Understanding Status Codes
HTTP status codes are three-digit numbers sent by the server in response to a client's request. They indicate whether a specific HTTP request has been successfully completed. Essentially, these codes are part of the HTTP response and help communicate the outcome of the operation back to the client.

For example, when a client requests data from a server, the server responds with the data along with an HTTP status code. This status code serves as a message to the client, telling it whether the request was successful or if there was some issue that needs attention.

Understanding these status codes is crucial because they allow clients to handle responses appropriately and take necessary actions based on the result of the request.

## Common Status Codes
Here are some basic status codes frequently used in API development:

200 OK: The request was successful, and the server returned the requested data.

201 Created: The request was successful, and a new resource was created.

204 No Content: The request was successful, but there is no content to return.

400 Bad Request: The server could not understand the request due to invalid syntax.

404 Not Found: The server cannot find the requested resource.

500 Internal Server Error: The server encountered an unexpected condition that prevented it from fulfilling the request.

There are many other status codes defined in the HTTP specification, covering a wide range of scenarios. In this unit, we'll focus on some common status codes that indicate success. Don't worry about the unsuccessful status codes just yet; we'll tackle those in later lessons as we progress through the course.

## Default Status Codes
FastAPI assigns default status codes to endpoints based on the type of HTTP method used:

GET requests default to 200 OK if no status_code parameter is specified.

POST requests default to 201 Created if no status_code parameter is specified.

DELETE requests also default to 200 OK if no status_code parameter is specified.

Although these codes are already set by default, we are going to explore some examples of how you can control the status code returned by your endpoints.

## Specifying a Status Code
To pass a status code to an endpoint, you caspecify it using the status_code parameter in the endpoint decorator. FastAPI allows you to use predefined constants from the status module for better readability:

Python
```
from fastapi import status

@app.get("/example", status_code=status.HTTP_200_OK)
def example_endpoint():
    return {"message": "Success"}
```
This approach enhances code readability by using meaningful constants from the status module instead of hard-coded status code numbers. This flexibility allows you to override the default status codes and tailor your API responses to better fit your application's needs.

## Example of GET with 200
Let's implement a GET request for fetching a crew member's details with the status code 200 OK.

Python
```
from fastapi import status

@app.get("/crew/{crew_id}", status_code=status.HTTP_200_OK)
async def read_crew_member(crew_id: int):
    for member in crew:
        if member["id"] == crew_id:
            return member
    return {"message": "Crew member not found"}
```
This code returns a status code 200 OK along with the member's details if the crew member is found. If the crew member is not found, it still returns a status code 200 OK but with a message indicating the absence of the crew member. This approach is not optimal because the same status code is used for both successful and unsuccessful retrievals, which can be misleading for clients.

## Example of POST with 201
Next, let's implement a POST request for adding a new crew member with the status code 201 Created.

Python
```
@app.post("/crew", status_code=status.HTTP_201_CREATED)
async def add_crew_member(crew_member: CrewMember):
    new_id = max(member["id"] for member in crew) + 1 if crew else 1
    new_member = {"id": new_id, **crew_member.dict()}
    crew.append(new_member)
    return {"id": new_id, "crew_member": new_member}
```
When a new crew member is successfully added, the client receives a status code 201 Created along with the details of the new member. This status code clearly communicates that the creation was successful and provides the ID and details of the new crew member. Pydantic handles data validation to ensure that the incoming data is correctly structured, and FastAPI will return appropriate error responses for validation issues.

## Example of DELETE with 204
If you want to implement an endpoint that does not return any data but wants to confirm that an operation worked, such as a DELETE request, using the status code 204 No Content is appropriate.

Python
```
@app.delete("/crew/{crew_id}", status_code=status.HTTP_204_NO_CONTENT)
async def delete_crew_member(crew_id: int):
    for member in crew:
        if member["id"] == crew_id:
            crew.remove(member)
            return
```
In this example, if the crew member is successfully removed, the client receives a status code 204 No Content, indicating that the deletion was successful without returning any content. By not passing any return value, we ensure that the status code 204 No Content accurately reflects that there is no additional content to return to the client.

In this example, the client receives a status code 204 No Content, indicating that the deletion was successful without returning any content. If you do not explicitly set the status code, FastAPI will default to returning 200 OK, which can be misleading as it typically implies the presence of content.

However, this implementation only handles successful cases. If the crew member is not found, the endpoint will still return 204 No Content. We will explore how to handle unsuccessful cases in the next lesson.

## Summary and Next Practices
In this lesson, we explored the importance of status codes in API responses and delved into some basic status codes like 200 OK, 201 Created, and 204 No Content. We demonstrated how these codes can be implemented in GET, POST, and DELETE requests using FastAPI, noting the crucial role they play in communicating the outcome of API operations to clients. We also highlighted the importance of using appropriate status codes for different outcomes to avoid confusion.

Understanding and effectively utilizing these status codes is essential for building robust APIs that provide clear and meaningful feedback to clients. You'll now get hands-on practice implementing these status codes in various exercises, allowing you to solidify your understanding and ensure you can apply these concepts in real-world scenarios.
