# <p align="center"> Data Modeling with Pydantic and FastAPI</p>
Welcome to another step in our journey with FastAPI! After getting your hands dirty with the basics of FastAPI and creating advanced endpoints, we're now ready to dive into data models. These concepts form the backbone of structured data handling within FastAPI applications, and this will be our focus in this lesson.

FastAPI leverages the power of Python's dataclasses for data validation, serialization, and documentation synchronization through a powerful library — Pydantic. Our goal for today's lesson is to get comfortable with defining Pydantic models and using them in our FastAPI application responses. Resources are already in place to help you practice and understand these concepts more concretely. So, let's dive straight in!

## What is Pydantic?
Pydantic is a data validation library that provides a way for us to create and handle complex data structures with built-in validation. But how does Pydantic fit into FastAPI?

In FastAPI, we use Pydantic models to build easy-to-understand, predictable data structures, with incorporated type-checking and validation. When we design powerful APIs, structuring, validating, serializing, and documenting our data is crucial, and Pydantic models are the perfect tool to help us accomplish this.

## The Role of Pydantic Models in FastAPI
Pydantic models allow us to create and validate complex data structures and forms, ensuring data integrity throughout our application. These models establish a clear contract of what kind of data is expected in our endpoints' requests and responses.

In addition to this, FastAPI leverages Pydantic's models for automatic documentation. So, it's not only about defining our application's data. It's also about developing clean, self-documented APIs without extra effort.

## Recap of Our Setup
Before diving deeper, let's recap our application setup. We have a FastAPI application with a mock database of crew members. Each crew member has an id, name, and role. Now, we are introducing a new field, experience, to our mock database.

Here is the updated mock database setup:

Python
```
from fastapi import FastAPI

# Initialize FastAPI app
app = FastAPI()

# Mock database of crew members
crew = [
    {"id": 1, "name": "Cosmo", "role": "Captain", "experience": 10},
    {"id": 2, "name": "Alice", "role": "Engineer", "experience": 8},
    {"id": 3, "name": "Bob", "role": "Scientist", "experience": 5}
]
```
## Constructing a Pydantic Model
Creating Pydantic models is similar to defining Python classes. Each attribute of the class represents a field in the model, and its type annotation determines what kind of data it can hold.

First, we need to import BaseModel from Pydantic. BaseModel is the core class of Pydantic that provides the foundation for defining data models with validation. It ensures that the data adheres to the specified types and constraints.

Here's how to define a Pydantic model to represent a crew member in our application:

Python
```
from pydantic import BaseModel

class CrewMember(BaseModel):
    name: str
    role: str
    experience: int
```
In this code, the CrewMember class inherits from BaseModel and includes name, role, and experience fields, each with a specified data type. This ensures that any data assigned to a CrewMember instance is validated against these annotations, enhancing the robustness of our application.

## Incorporating Pydantic Model in Response
We can now use our CrewMember Pydantic model in our FastAPI endpoints' responses. Here is how we define a GET endpoint to fetch information using our model as a response model:

Python
```
@app.get("/crew/{crew_id}", response_model=CrewMember)
async def read_crew_member(crew_id: int):
    # ... remaining code ...
```
As you can see, FastAPI makes it simple to incorporate Pydantic models with the response_model parameter. This parameter tells FastAPI to use the CrewMember model for shaping the response sent to the client after processing the request.

## Understanding Response Model and Pydantic
Using the response_model with Pydantic in FastAPI changes the way the response data is handled and returned to the client. Here's a breakdown of what happens when we use a response_model:

**Data Validation**: Before sending the response, FastAPI validates the data against the specified Pydantic model. This ensures that the data meets the expected structure and types.

**Data Shaping**: FastAPI automatically converts the response data to match the structure of the Pydantic model. If the model defines specific fields, only those fields will be included in the response, ensuring a clean, consistent output.

**Automatic Documentation**: With response_model, FastAPI automatically generates documentation that clearly shows the expected response format. This helps in understanding the API better through tools like Swagger UI.

**Error Handling**: If the data does not conform to the Pydantic model, FastAPI will raise a validation error. This helps catch issues early and provides consistent error messages.

So, using response_model ensures that the response data is valid, well-structured, and properly documented, making the API more reliable and easier to work with.

## Example of Implementation
Let's dissect an example to understand how it all comes together.

Python
```
from fastapi import FastAPI
from pydantic import BaseModel

app = FastAPI()

# Mock database of crew members
crew = [
    {"id": 1, "name": "Cosmo", "role": "Captain", "experience": 10},
    {"id": 2, "name": "Alice", "role": "Engineer", "experience": 8},
    {"id": 3, "name": "Bob", "role": "Scientist", "experience": 5}
]

# Defining a Pydantic model for the crew member
class CrewMember(BaseModel):
    name: str
    role: str
    experience: int

# GET endpoint to read a crew member details using response model
@app.get("/crew/{crew_id}", response_model=CrewMember)
async def read_crew_member(crew_id: int):
    # Finding member in crew
    for member in crew:
        if member["id"] == crew_id:
            return member
```
In this example, we have a GET endpoint, /crew/{crew_id}, which returns the details of a crew member by their id. The response_model=CrewMember parameter in the endpoint definition instructs FastAPI to use the CrewMember Pydantic model to format its responses.

## Possible Responses
In the example above, the response will always use the response_model defined by FastAPI. Here are the possible responses depending on whether the crew member is found:

**Crew Member Found**: If a crew member with the specified id is found in the mock database, the endpoint will return the corresponding data validated against the CrewMember model.

Plain text
```
{
    "name": "Cosmo",
    "role": "Captain",
    "experience": 10
}
```
**Crew Member Not Found**: If no crew member with the specified id is found, FastAPI will attempt to return a response conforming to the CrewMember model. Since no data is returned, this leads to validation errors.

Plain text
```
1 validation errors:
{'type': 'model_attributes_type', 'loc': ('response',), 'msg': 'Input should be a valid dictionary or object to extract fields from', 'input': None}
```
These validation errors indicate that the input returned is not valid for creating a CrewMember instance, as FastAPI is attempting to validate the response against the CrewMember model.

## Review and Preparing for the Practice
Congratulations on completing this lesson! You've learned the role of Pydantic models in FastAPI, how to define and use them in endpoint responses. Now, you're set to apply this knowledge in the practice session. Mastering these basics will be invaluable for advancing in your FastAPI journey. Great work!

