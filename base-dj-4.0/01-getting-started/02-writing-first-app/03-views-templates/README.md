### Part 3

#### Write views

- Write urls on polls/urls.py
  - For detail
  - For results
  - For voting

- Add views for each url


#### Templates

- Create directory named `templates` inside polls directory.
- By convention DjangoTemplates looks for a `templates` directory in each
  of the installed apps.

- A shortcut - `render`
  `render(request, 'polls/index.html', context)`

- A shortcut - `get_object_or_404`
  - There is also `get_list_or_404`, raises if list is empty.

- Removing hardcoded url
  `{% url 'detail' question.id %}` 
  
- Namespacing URL names  
  Add an app_name to set the application namespace on `polls/urls.py`  
  `app_name = 'polls'`
