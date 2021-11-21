### Writing your first patch

- Installing git.  
  [Link](https://git-scm.com/download)
- Fork Django code.  
  - [Fork](https://github.com/django/django/fork)
  - [Download](git clone https://github.com/YourGitHubName/django.git)  
    Add `--depth 1` to reduce the size.
- Install on virtual environment.  
  `python3 -m venv ~/.virtualenvs/djangodev`
- [Creating a project with local copy of django](
    https://docs.djangoproject.com/en/3.2/intro/contributing/  
    #creating-projects-with-a-local-copy-of-django)
- Running Django's test suit for the first time.
  - Install dependencies cd-ing into the Django `tests/`.
    `python -m pip install -r requirements/py3.txt`
  - Run test.
    `./runtests.py`

