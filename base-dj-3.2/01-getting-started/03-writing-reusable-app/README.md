### How to write reusable apps  

#### Packaging polls app  
- For a directory like polls to form a package, it must contain a special file
  `__init__.py`, even if this file is empty.

- We will use setuptools to do packaging. We setup installation using pip.
  So install pipa

- **How to**  
  - Create a parent directory for `polls` outside your Django project.
    Call the directory `django-polls`.

  - Move the polls directory into the Django polls directory.

  - Create a file django-polls/README.rst. Check the content in that file.

  - Create a LISENSE file.

  - Create `setup.cfg` and `setup.py` files.  

    Details how to build and install an app.
    TO-DO: To know how to use setup tools fully go to the docs
           https://setuptools.pypa.io/en/latest/

  - Add MANIFEST.in file

  - Add `docs` directory and add docs.

  - Try building your package with python setup.py sdist (run from inside
    `django-polls`). This creates a directory called `dist` and builds your
    new package, django-polls-0.1.tar.gz

  - More on packaging - [Python packaging docs](https://packaging.python.org/tutorials/packaging-projects/)

- **Using package**  
  - 

