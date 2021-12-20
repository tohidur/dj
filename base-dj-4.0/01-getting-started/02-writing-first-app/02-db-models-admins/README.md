### Part 2

#### Database setup

##### Migrate  

##### Creating Models

- Some fields in model has required arguments. Like `max_length` in CharField.
  Others have optional arguments like `default=0`.

- Telling app that polls app installed.  
  ```python
  # on settings.py
  INSTALLED_APPS = [
      'polls.apps.PollsConfig',
      'django.contrib.admin'
  ]
  ```

- Make migrations for polls app
  `python manage.py makemigrations polls`

- SQLMigrate - `sqlmigrate`  
  `python manage.py sqlmigrate polls 0001`
  
  It doesn't run migration on database. It prints SQL query for migration on
  the screen.

- `check` command
  You can run it to see any problems in your project without touching migration.

- Migrate - `migrate`   
  To apply DB schema changes.


##### Django Admin
- **Create Admin User**  
  - `python manage.py createsuperuser`

- **Make polls app modifiable in admins page**  
  - on `polls/admin.py`
