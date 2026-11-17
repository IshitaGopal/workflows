# Computed Fields
- Used when you want fields that are calculated/computed from other fields and you want them to be included when you serialize the model to a dictionary or json
- Use a decorator @computed_field.
- We can combine it with the @property decorator.

```python
from typing import Annotated
from pydantic import (
    BaseModel, 
    Field, 
    EmailStr,
    HttpUrl, 
    SecretStr, 
    computed_field
)

class User(BaseModel):
    username: Annotated[str, Field(min_length=3, max_length=20)]
    password: SecretStr
    website: HttpUrl | None = None
    first_name: str = "" 
    last_name: str = "" 
    follower_count: int = 0

    # Now this will be included when we serialize out model
    @computed_field 
    @property
    def display_name(self) -> str:
        if self.first_name and self.last_name:
            return f"{self.first_name} {self.last_name}"
        return self.username
    
    @computed_field 
    @property
    def is_influencer(self) -> bool:
        return self.follower_count >= 10000

```

# Nested Models 
- You can use other Pydantic models as field types, i.e., you can have models within models.
- Pydantic will automatically validate everything recursively

```python

class Comment(BaseModel):
    content: str
    another_email: EmailStr
    likes: int = 0


class BlogPost(BaseModel):
    title: Annotated[str, Field(min_length=10, max_length=200)]
    content: Annotated[str, Field(min_length=10)]
    author_id: str | int  

```

## What if we wanted to store the full author information instead of just the author id?

- We can use the User model as the type 
- Below we replace ``author_id`` with ``author`` and set the type as ``User``. So data will have to pass the User validation checks.
- The full User object will be nested in the blogpost. 
- We can also set the comment that we created above. So the each comment will need to pass the Comment validation checks. 

```python
class BlogPost(BaseModel):
    title: Annotated[str, Field(min_length=10, max_length=200)]
    content: Annotated[str, Field(min_length=10)]
    author: User

    # List of Comment 
    comments: list[Comment] = Field(default_factory=list)

post_data = {
    "title":
    "content":
    "author":{
        "username": "coreyms",
        "age": 39,
        "password": "secret1234"
    }
    "comments":[
        {
            "content": "I think I understand nested models now!",
            "author_email": "student@xyz.com",
            "likes": 25
        },
        {
            "content": "Can you cover FastAPI next?",
            "author_email": "viewer123@example.com",
            "likes": 15
        }
    ]
}

# Creating a blogpost from the post_data:
# Unpacking the dictionary with ** and validating recursively
post = BlogPost(**post_data) 

# Same as using model_validate()
BlogPost.model_validate(post_data)

```

# Advanced serialization and working with json 

- Working with APIs
    - External the external representation may use different field names  form internal Python names 
    - You may not want some fields serialized 
    - Maybe you have some internal fields that you dont want exposed when serialized. -> ConfigDict 
    - Allows you do configure our model further 
    - populate_by_name: tells pydantic to accept both a fieldname and an alias when loading daa 
        - accept id not just uid
        - We dont have to change the whole model. Alias allows us to be a litle more flexible when loading/exporting data.


# ConfigDict
```python
from pydantic import ConfigDict

class User(BaseModel):
    model_config = ConfigDict(populate_by_name=True)

    # This will accept data not just with uid but also if the field is named id

    uid: UUID = Field(alias="id", default_factory=uuid4)
    username: Annotated[str, Field(min_length=3, max_length=20)]
    password: SecretStr
    website: HttpUrl | None = None
    first_name: str = "" 
    last_name: str = "" 
    follower_count: int = 0

    # Now this will be included when we serialize out model
    @computed_field 
    @property
    def display_name(self) -> str:
        if self.first_name and self.last_name:
            return f"{self.first_name} {self.last_name}"
        return self.username
    
    @computed_field 
    @property
    def is_influencer(self) -> bool:
        return self.follower_count >= 10000


user_data = {
    "id":"3bc4567...",
    "username":"corey_shaffer",
    "age":"39",
}

# Will still display as "uid" not "id"
user = User.model_validate(user_data)
print(user.model_dump_json(indent=2))

# Instead of "uid" we will get "id"
print(user.model_dump_json(indent=2, by_alias=True))

# Exclude the password 
print(user.model_dump_json(indent=2, by_alias=True, exclude={"password"}))

# Include only specific fields 
print(user.model_dump_json(indent=2, by_alias=True, include={"username", "email"}))

# Load from json data or a json file 
User.model_validate_json(json_data)

```


## Other ConfigDict Options 

```python 
# Controling type coersion and making it strict mode Be very specific about type 
class User(BaseModel):
    model_config = ConfigDict(
        populate_by_name=True, 
        strict=True
    )
    ...
    ...
    ...

# Handle extra fields 
# By default 
# Allows extra fields 
class User(BaseModel):
    model_config = ConfigDict(
        populate_by_name=True, 
        strict=True,
        extra = "allow"
    )
    ...
    ...
    ...

# Forbid extra fields and raise an error 
class User(BaseModel):
    model_config = ConfigDict(
        populate_by_name=True, 
        strict=True,
        extra = "forbid"
    )
    ...
    ...
    ...

# Extra fields are accepted but silently ignored
# This is the default 
class User(BaseModel):
    model_config = ConfigDict(
        populate_by_name=True, 
        strict=True,
        extra = "ignore"
    )
    ...
    ...
    ...

```

## 
```python 
# Revalidates anytime we make changes to values which it does not do by default. 
class User(BaseModel):
    model_config = ConfigDict(
        populate_by_name=True, 
        strict=True,
        extra = "ignore",
        validate_assignment = True, 
    )

    ....
    ....
```

## Frozen models
- Make models immutable 
- Cant make changes to it once it is created  
- data integrity 
- configuratons 
```python 
class User(BaseModel):
    model_config = ConfigDict(
        populate_by_name=True, 
        strict=True,
        extra = "ignore",
        validate_assignment = True, 
        frozen = True 
    )

    ....
    ....

```
