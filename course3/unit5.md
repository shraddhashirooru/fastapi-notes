# <p align="center"> Nested Models for Complex Data Structures</p>
Welcome to another lesson. This time we will explore how to handle intricate data relationships using nested Pydantic models within FastAPI. You will learn how to define nested data structures, create corresponding Pydantic models, and use these models in your FastAPI endpoints for validation and data manipulation.

## Understanding the Importance of Nested Models
Nested models are crucial for representing complex data structures in a manner similar to how relational databases handle related tables. In a real database, you would have tables with foreign keys to represent relationships.

Similarly, in FastAPI, nested models let us encapsulate these relationships within a single model, streamlining data validation and manipulation. This way, our application can handle complex data interactions just as efficiently as a well-structured database.

## Setting Up Nested Data in FastAPI
To work effectively with nested data structures, we'll represent the complexity using Pydantic models in our FastAPI application. Let's start by creating a FastAPI app with a mock dataset that includes nested relationships.

Here's an example of a mock database of crew members, each with nested equipment data:

Python
```
from fastapi import FastAPI
from pydantic import BaseModel

app = FastAPI()

# Mock database of crew members with nested equipment data
crew = [
    {
        "id": 1,
        "name": "Cosmo", "role": "Captain", "experience": 10,
        "equipment": [
            {"name": "Helmet", "status": "Good"},
            {"name": "Suit", "status": "Needs Repair"}
        ]
    },
    {
        "id": 2,
        "name": "Alice", "role": "Engineer", "experience": 8,
        "equipment": [
            {"name": "Toolkit", "status": "Good"}
        ]
    },
]
```
In this structure, each crew member has a list of equipment objects associated with them. Now, let's define Pydantic models to represent these nested data structures.

## Creating the Equipment Model
First, let's define the Equipment model. This model will represent the equipment associated with each crew member. Here's the code:

Python
```
class Equipment(BaseModel):
    name: str
    status: str
```
The Equipment model has two fields: name for the equipment's name and status for its current status.

## Nesting Equipment in the CrewMember Model
Next, let's define the CrewMember model, which will include a list of nested Equipment objects. This structure allows each crew member to have multiple pieces of equipment. Here's the code:

Python
```
class CrewMember(BaseModel):
    name: str
    role: str
    experience: int
    equipment: list[Equipment]
```
The CrewMember model now includes the equipment field, which is a list of Equipment objects.

## Validating and Handling Nested Data with Pydantic
Pydantic makes it straightforward to validate and manipulate nested data structures. It automatically validates the nested models based on the defined schemas. For example, if you send the following JSON to a POST endpoint:

JSON
```
{
    "name": "Bob",
    "role": "Navigator",
    "experience": 5,
    "equipment": [
        {"name": "Binoculars", "status": "New"},
        {"name": "Map", "status": "Worn"}
    ]
}
```
Pydantic will ensure the nested data conforms to the CrewMember and Equipment models' definitions. If any field is missing or incorrect, it will raise a validation error.

## Summary and Practices
You have now learned how to define and use nested Pydantic models within FastAPI to manage complex data structures effectively. We covered the importance of nested data models and how to create nested models with Pydantic.

This knowledge will be invaluable as you move on to practice exercises to reinforce what you've learned. Keep practicing to master the use of nested models in FastAPI, and feel confident in applying these skills to your own projects.

