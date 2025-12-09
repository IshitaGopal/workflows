- https://www.youtube.com/watch?v=niMybnzmzqc

- https://www.youtube.com/watch?v=e9yMYdnSlUA&t=269s

- https://cookiecutter-data-science.drivendata.org/?utm_source=chatgpt.com

- https://www.youtube.com/watch?v=MaIfDPuSlw8

- https://www.youtube.com/watch?v=mpk4Q5feWaw

- https://ericmjl.github.io/essays-on-data-science/workflow/code-review/


```bash
uv init new_app
# Project types
uv init new_app --app # default
uv init new_app --lib # python package structure


# OR
cd new_app
uv init
```
- `.python-version`: sets the python version the project should use.
- `- pyproject.toml`
    - created when we run `uv init`
    - has basic info about our app
    - lists dependencies. It is an empty when initalized. 
    - running `uv add requests` will update this file and add `requests` to dependencies. Running `uv add requests` will also create a uv.lock file,
- `uv.lock`
    - records the exact versions of all the packages installed, including sub dependencies. 
    - ensures the environment is perfectly reproducible

`uv tree`: will show the dependency tree 
`uv run main.py`: to run script without activating the viirtual envirnment or even when the environment has not been created (since pyroject.toml and uv.lock filea are there to recreate the environment)