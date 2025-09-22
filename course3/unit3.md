# <p align="center"> Validating Data with Pydantic Models</p>
Welcome back! In our previous lessons, we worked with FastAPI and Pydantic models. We learned how to model our data and how to handle POST requests. Today, we move a step further. We're going to talk about a crucial aspect of handling data, particularly incoming data, which is data validation.

Pydantic models not only help us structure our data but also validate it. They ensure that the incoming data follow the model's defined framework and that our application can handle it without trouble.

## Importance of Data Validation
In any application, the data we receive is prone to inconsistencies. For instance, imagine you have a form on a website, and you're expecting a user to enter their name. A name is usually a string of alphabets, maybe a dash or an apostrophe. But what if a user tries to enter a number as their name, or some sort of special character? This input is not ideal and can lead to problems in the application if not handled correctly.

That's where data validation comes in. It's the process of checking if the data provided matches the requirements we set. It ensures that our application data adheres to the defined business rules and logic.

## Data Validation with Pydantic Models
Pydantic allows us to set constraints on our data fields using the Field class, which provides a way to specify additional validation and metadata for model fields.

For example, let’s suppose we have a field for the experience of a crew member, which is an integer. We want to ensure that the value entered for this field is greater than zero. We can easily define this requirement using the Pydantic model:

Python
```
from pydantic import BaseModel, Field

class CrewMember(BaseModel):
    name: str
    role: str
    experience: int = Field(..., gt=0)
```
In this example gt=0 is used to enforce that the experience must be a value greater than zero, while the ... indicates that the experience field is mandatory and must be provided when creating an instance of the CrewMember model. This is a shorthand way in Pydantic to mark a field as required.

Let's explore some additional examples of data validation!

## Validating String Length
Let's say we have a title field that should not exceed 100 characters. We can use max_length to enforce this constraint.

Python
```
from pydantic import BaseModel, Field

class Book(BaseModel):
    title: str = Field(..., max_length=100)
    author: str
    pages: int
```
This ensures that any data provided for the title field will not exceed 100 characters.

## Validating Number Range
Suppose we have a rating field that should be between 1 and 5. We can use both gt (greater than) and lt (less than) constraints.

Python
```
from pydantic import BaseModel, Field

class Review(BaseModel):
    content: str
    rating: int = Field(..., gt=0, lt=6)
```
This ensures that the rating must be a value between 1 and 5, inclusive.

## Validating Email Format
For an email field, we might want to ensure that the provided value is a valid email address. Pydantic's EmailStr type can be used for this purpose.

Python
```
from pydantic import BaseModel, EmailStr

class UserProfile(BaseModel):
    username: str
    email: EmailStr
```
By using EmailStr, we ensure that the email field contains a valid email address.

## What Happens When Validation Fails
When a field fails validation in Pydantic, FastAPI will automatically handle the error and generate a detailed response indicating what went wrong. The response typically includes an HTTP 422 Unprocessable Entity status code along with a JSON body that specifies which field failed validation and why.

For example, if a user tries to submit a value of -5 for the experience field in the CrewMember model, FastAPI will intercept this request and provide a clear error message stating that the value must be greater than zero. Here is an example of the JSON response you might see:

JSON
```
{
    "detail": [
        {
            "type": "greater_than",
            "loc": [
                "body",
                "experience"
            ],
            "msg": "Input should be greater than 0",
            "input": -5,
            "ctx": {
                "gt": 0
            }
        }
    ]
}
```
This immediate feedback helps developers quickly identify and correct data issues, ensuring that only valid data gets processed by the application.

## Summary and Practice Section
Congratulations on bringing another skill under your belt. We have expanded our knowledge of Pydantic models to include data validation in FastAPI. Remember, data validation is a must-have feature in almost every application. It helps us ensure that we're getting the right kind of data, and in the right format.

Up next, we'll continue our exploration of FastAPI. Remember to apply what you've learned here in the exercises coming up — it's how you'll get the most from these lessons! So keep going, you're doing great!

