# Custom Validators 
- Sometimes the built constraints in validation are not enough, and you may want to add your own logic.
- We can easily do this in Pydantic with decorators.
- *After validation*
    - Default 
    - Runs after pydantic has already done its typechecking and basic validation
    - Ex below: ``validate_username`` 
- *Before validation*
    - Used when you want to preprocess data before the main validation happens. 

## @field_validator
- In the example, ``validate_username``:
    - We valdate that username only contain alphanumeric characters and underscores + normalize them to lowercase
    - This is an *after* validation
- In the example, ``add_https``:
    - This is an example of *before* validation
    - We do some preprocessing and run some custom validation and modification for the website *before* it does the HttpUrl validation above
    - It checks if the website start with http:// or https://. If not, it adds it to the beginning. 
    - Then the HttpUrl validation runs 


```python
from uuid import UUID, uuid4
from typing import Annotated
from pydantic import (
    BaseModel, 
    Field, 
    EmailStr, 
    HttpUrl, 
    SecretStr, 
    ValidationError,
    field_validator, 
    model_validator, 
    ValidationError
    )

class User(BaseModel):
    uid: UUID = Field(default_factory=uuid4)
    username: Annotated[str, Field(min_length=3, max_length=20)] 
    password: SecretStr
    website: HttpUrl | None = None
    age: Annotated[int, Field(ge=13, le=130)]

    # Pass in the field we are validating : username
    @field_validator("username")
    @classmethod
    def validate_username(cls, v: str) -> str:
        if not v.replace("_", "").isalnum():
            raise ValueError("Username must be alphanumeric (underscores allowed)"
        return v.lower()

    # Mode = "before"
    @field_validator("website", mode="before")
    @classmethod
    def add_https(cls, v: str | None) -> str | None:
        if v and not v.startswith(("http://", "https://")):
            return f"https://{v}"
    return v 


# Website validation 

User (
    username = .. 
    email=..., 
    age =39, 
    password ='secret123',
    website = 'coreyms.com') # this will give an error if mode it not set to before because the website string does not start with http or https

```

## @model_validator
- Useful when you want to validate multiple fields together or when you want to validate the complete model. EX: Confirming if the password and confir password fields match.
- Instead of receiving athe value, it receives *self* = whole model instance. This allows you to access all the fields to do validation.
- This runs after all the fields have been validated individually. This is why we do not usee @classmethod.

```python
class USerRegistration(BaseModel):
    email: EmailStr
    password: str
    confirm_password: str

    @model_validator(mode="after"):
    def passwords_match(self) -> "UserRegistration":
        if self.password != self.confirm_password:
            raise ValueError("Passwords do not match")
        raise self

```

## Best Practices for validators
- Always return the values even if you are not modeifying it or raise an error
- You usually want to raise a ValueError for validation errors. - Pydantic converts it into a ValidationError automatially.
- If you are going to raise an error, don't mutate the value first. Either return the modified value or raise an error, but not both.