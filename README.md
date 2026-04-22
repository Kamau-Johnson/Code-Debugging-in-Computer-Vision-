## Fix My Code
<p float="center">
    <img src="Fix.png" width="60%" />
</p


### Using The `dotenv` Module

We'll start by importing the needed `os` module and `dotenv` functions.


```python
import os

from dotenv import load_dotenv, set_key, unset_key
```

Environmental variables are conventionally stored in a `.env` file. Environmental variables managed by `dotenv` are typically stored in a file named `.env`. This file is usually placed in the root directory of your project. Let's examine the structure and content of a typical `.env` file:

1. Each line in the file represents a single environment variable.
2. Variables are defined using the format: KEY=VALUE
3. There should be no spaces around the equals sign.
4. Values do not need to be enclosed in quotes unless they contain spaces.
5. Comments can be added using the # symbol.

Let's have a look at the `.env` file in this directory.


```python
!cat .env
```

    # Some values we'll use for training
    MODEL_NAME="CompVis/stable-diffusion-v1-4"
    DATASET_NAME="worldquant-university/maya-dataset-v1"
    OUTPUT_DIR="maya_model_v1"


<div class="alert alert-info" role="alert">
Watch out! There could be sensitive information in the output of the previous cell!

You must clear the output of that cell, or delete the cell entirely, before sharing this notebook with anyone else.
</div>

The `load_dotenv()` function from the `dotenv` library reads the contents of the `.env` file and adds the defined variables to Python's environment.


```python
load_dotenv()
```




    True



We can access all the environment variables in the current Python session using `os.environ`. This includes the environment variables we just loaded from the `.env` file in the current directory. `os.environ` is a dictionary-like object, so you can use typical dictionary operations to interact with it. Let's show all the environment variables.


```python
for key, value in os.environ.items():
    print(f"{key}: {value}")
```

    PATH: /usr/local/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
    HOSTNAME: 24bf7dae5f3c
    PYTORCH_NO_CUDA_MEMORY_CACHING: 1
    DW_CONTAINER_NAME: jupyter-5d786767
    DW_CONTAINER_HOST_NAME: us-east-13.prd.namespace.im
    LANG: C.UTF-8
    GPG_KEY: A035C8C19219BA821ECEA86B64E628F8D684696D
    PYTHON_VERSION: 3.11.0
    PYTHON_PIP_VERSION: 22.3
    PYTHON_SETUPTOOLS_VERSION: 65.5.0
    PYTHON_GET_PIP_URL: https://github.com/pypa/get-pip/raw/66030fa03382b4914d4c4d0896961a0bdeeeb274/public/get-pip.py
    PYTHON_GET_PIP_SHA256: 1e501cf004eac1b7eb1f97266d28f995ae835d30250bec7f8850562703067dc6
    NVIDIA_VISIBLE_DEVICES: 0
    HOME: /root
    JPY_SESSION_NAME: /app/061-fix-my-code.ipynb
    JPY_PARENT_PID: 1
    PYDEVD_USE_FRAME_EVAL: NO
    TERM: xterm-color
    CLICOLOR: 1
    FORCE_COLOR: 1
    CLICOLOR_FORCE: 1
    PAGER: cat
    GIT_PAGER: cat
    MPLBACKEND: module://matplotlib_inline.backend_inline
    MODEL_NAME: CompVis/stable-diffusion-v1-4
    DATASET_NAME: worldquant-university/maya-dataset-v1
    OUTPUT_DIR: maya_model_v1


It's often useful to retrieve the value of a specific environment variable using its key. 


```python
print(os.environ["DATASET_NAME"])
```

    worldquant-university/maya-dataset-v1


### Set And Unset Keys

The `set_key()` function from the `dotenv` library allows you to create or update an environment variable in your `.env` file. Here's how to use it:


```python
set_key(
    dotenv_path=".env", key_to_set="API_KEY", value_to_set="your_sample_api_key_here"
)
```




    (True, 'API_KEY', 'your_sample_api_key_here')




```python
# The new key-value pair must be reload from .env
load_dotenv()

# Use the key to find the value
print(os.environ["API_KEY"])
```

    your_sample_api_key_here


It's a best practice to keep only the required environmental variables in `.env`. Let's remove the previous example from `.env` with the `unset_key` function.


```python
unset_key(dotenv_path=".env", key_to_unset="API_KEY")
```




    (True, 'API_KEY')



Key points about `unset_key()`:

1. It removes the specified variable from the `.env` file.
2. If the variable doesn't exist, it does nothing (no error is raised).
3. It only affects the `.env` file, not the current `os.environ`.



### Forgetting To Reload Changes to `.env`

One of the most common errors is forgetting to reload and updated version of the `.env`.

**Task 6.1.1:** Load the updated version of `.env` to get access to the `PASSWORD` value.


```python
set_key(dotenv_path=".env", key_to_set="PASSWORD", value_to_set="qwerty")

load_dotenv()

print(os.environ["PASSWORD"])
```

    qwerty



```python
# Remove example key
unset_key(dotenv_path=".env", key_to_unset="PASSWORD")
```




    (True, 'PASSWORD')



### Spaces in Environment Variable

It's a best practice to avoid spaces in environment variable names and values, this can prevent many errors in your code. Note that `load_dotenv()` will gracefully handle errors caused by spaces but the associated variable will not be loaded.

**Task 6.1.2:** Rename the environmental variable to be a valid environmental variable.


```python
from dotenv import set_key, load_dotenv
import os

set_key(
    dotenv_path=".env",
    key_to_set="DEBUG",
    value_to_set="True", 
)

load_dotenv()

print(os.environ["DEBUG"])
```

    python-dotenv could not parse statement starting at line 5
    python-dotenv could not parse statement starting at line 7
    python-dotenv could not parse statement starting at line 5
    python-dotenv could not parse statement starting at line 7


    True


We should remove the variable with a space from `.env`. That can't be done with `dotenv` but can do in Python.


```python
# Open the .env file
with open(".env", "r") as file:
    lines = file.readlines()

# Filter out the line with 'DATABASE URL'
with open(".env", "w") as file:
    for line in lines:
        if not line.startswith("DATABASE URL"):
            file.write(line)
```


```python
# Remove example keys with dotenv
# Recall trying to remove non-existing keys is okay
unset_key(dotenv_path=".env", key_to_unset="DATABASE_URL")
```




    (True, 'DATABASE_URL')



### Environment Variable Values As Strings

A common error is not defining environment variable values as strings. Environment variables are 
expected to be represented as strings to ensure compatibility across systems.

**Task 6.1.3:** Change the `value_to_set` to be a string.


```python
set_key(
    dotenv_path=".env",
    key_to_set="DEBUG",
    value_to_set="True"
)
```




    (True, 'DEBUG', 'True')



Once you're done, remember to unset the key:


```python
# Remove example key
unset_key(dotenv_path=".env", key_to_unset="DEBUG")
```




    (True, 'DEBUG')



### Gracefully Handling A Missing Key

Recall that `os.environ` is a dictionary-like object that represents environment variables as key-value pairs. For standard Python dictionaries, key look-up can be done with either square bracket notation (`[]`) or the `get()` method. For environment variables, key look-up can be performed using either square bracket notation (`os.environ[]`) or the `os.environ.get()` function.

In both cases, using the `get` version (`dict.get()` or `os.environ.get()`) is preferred when you want to avoid raising a `KeyError` for non-existent keys and optionally provide a default value / message. 

**Task 6.1.4:** Replace `os.environ[]` with `os.environ.get()`, including a helpful message, for non-existent key look-up.


```python
load_dotenv()
print(os.environ.get("PORT"))
```

    None


To recap, we managed environment variables in Python using the `dotenv` library. We were able to load variables from `.env` and create and delete variables in that file. `os.environ` is a dictionary-like object for accessing environment variables. Environment variables store potentially sensitive information so they must be carefully handled.

---
&#169; 2024 by [WorldQuant University](https://www.wqu.edu/)
