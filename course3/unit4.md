# <p align="center"> Custom Validation in Models</p>
Welcome back! So far in our course, we've learned a lot about FastAPI and its integration with Pydantic, an essential tool for creating and validating data models. We've also applied Pydantic to handle data in POST requests and applied basic field validations.

As you've surely noticed, data validation is crucial to maintaining the integrity of our data. But what if we need to enforce specific rules that go beyond basic field validation? That's where custom validation comes in handy, allowing us to enforce any rules necessary for our unique application needs.

## What is Custom Validation?
So, what is custom validation? Quite simply, it's a set of rules that we can write ourselves that go beyond what field validation provides. For instance, imagine we want to ensure our captain has at least 5 years of experience. Using custom validation, we can do just that!

Instead of defining these rules in our endpoints, FastAPI allows us to define them right within our Pydantic models. This makes our code cleaner and easier to manage, keeping our validation logic alongside our data models.

## Setup Recap
Before we dive into the implementation, let's quickly revisit our ongoing application setup. Here's our crew dataset and the CrewMember Pydantic model:

Python
```
from fastapi import FastAPI
from pydantic import BaseModel

app = FastAPI()

crew = [
    {"id": 1, "name": "Cosmo", "role": "Captain", "experience": 10},
    {"id": 2, "name": "Alice", "role": "Engineer", "experience": 8},
    {"id": 3, "name": "Bob", "role": "Scientist", "experience": 5}
]

class CrewMember(BaseModel):
    name: str
    role: str
    experience: int
```
In the last lesson, we defined field constraints in our CrewMember Pydantic model as part of data validation. But remember, sometimes we need more complex validation rules.

## Code Implementation
Let's add a custom validation to our CrewMember model that checks if the person assigned as 'Captain' has more than 5 years of experience:

Python
```
from pydantic import model_validator
 
class CrewMember(BaseModel):
    name: str
    role: str
    experience: int

    @model_validator(mode="after")
    def validate_experience_for_captain(cls, values):
        if values.role == 'Captain' and values.experience <= 5:
            raise ValueError('Captain must have more than 5 years of experience')
        return values
```
Here’s a detailed walkthrough of the code:

**Decorator**: @model_validator(mode="after") means this method will run after the normal field validation.
Method Definition: validate_experience_for_captain is the custom validation method. The cls parameter allows access to the class, and values is a dictionary of validated data.
Condition Check: The if statement checks if the role is 'Captain' and the experience is less than or equal to 5 years.

**Raise Error**: If the condition is met, a ValueError is raised with a descriptive error message.
Return Values: If no condition is met, the method returns the validated values.
The @model_validator ensures that your custom validation logic runs after all other field validations, helping you maintain a clean separation between field-level validations and more complex custom rules.

## Response for Invalid Data
If we try to add a captain with 4 years of experience, the API will respond with a 422 Unprocessable Entity error. The response will look like this:

JSON
```
{
    "detail": [
        {
            "type": "value_error",
            "loc": [
                "body"
            ],
            "msg": "Value error, Captain must have more than 5 years of experience",
            "input": {
                "name": "Max",
                "role": "Captain",
                "experience": 4
            },
            "ctx": {
                "error": {}
            }
        }
    ]
}
```
This response indicates that the validation failed because the Captain did not meet the required experience criteria. The error message provides details about which field was invalid and the specific issue, directly reflecting the ValueError we raised in the custom validator.

## Summary & Next Steps
In this lesson, we've discovered how to write custom validation logic for our data models in FastAPI, intensifying our control over data integrity. You're now equipped with the knowledge to employ custom validations in your APIs.

Great job getting here! As always, practice is key to solidifying these skills. So, immerse yourself in the exercises we've got in store, which will challenge you to create and use your own custom validators. Looking forward to seeing you in the next lessons as we continue our journey with FastAPI! Keep going, space cadet!

