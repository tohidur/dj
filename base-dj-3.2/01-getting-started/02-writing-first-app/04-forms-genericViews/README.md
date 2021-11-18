### Part 4

- **HTML form**  
  see - detail.html

  - Django's default csrf_token is added to form to protect from CSRF.
  

- **Using generic views**  
  - Change the urls views  
    `path('', views.IndexView.as_view(), name='index'),`
  
  - url var should be named `pk` for detail views
  
  - template name and context variable name is by default same as model name
    with `_template` appended. If you want something different you need
    to specify it on class variable.
