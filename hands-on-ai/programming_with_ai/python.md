# First steps in Programming with Python

## Intro 

We'll work with our favorite LLM-System. Example:

```
How to get started in programming with python? I have python installed and a virtual environment with miniconda.
```

Next to some explainations this request returns for example code like this:

```python
# hello.py
def greet(name):
    return f"Hello, {name}!"

names = ["Alice", "Bob", "Charlie"]

for name in names:
    print(greet(name))
```

Save it as `hello.py` and run it in VSCode/VSCodium.

Continue with questions like

```
Generate code that explains the concept of variables.
```

or use suggestions like 

```
Can you give me an example of a simple function in Python?
```

## Image manipulation

```
Write a function that takes an image as input and performs a image manipulation before displaying the new image.
```

Imagine more complex image manipulations and write functions for that. 

Try to create pipelines: feed an image through multiple image manipulation functions.
