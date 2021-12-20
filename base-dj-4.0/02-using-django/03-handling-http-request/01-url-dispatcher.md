# URL Dispatcher

- You can urlconf attribute from middleware, it's value will be used in
  place of the `ROOT_URLCONF` setting.
  
## Example
```
from django.url import path
from . import views

urlpatterns = [
    path('articles/2003/', views.special_case_2003),
    path('articles/<int:year>/', views.year_archive),
    path('articles/<int:year>/<int:month>/', views.month_archive),
    path('articles/<int:year>/<int:month>/<slug:slug>/',
         views.article_detail),
]
```

## Path converters
- str

- int

- slug

- uuid  
  Returns UID instance. Prevent multiple URLs from mapping to the same page.
  dashes must be included and letters must be lowercase.

- path  
  Matches any non-empty string, including the path separator `/`.
  Match against complete URL path rather than a segment of URL path as with str.

## Register custom path converter
Converter is a class that includes the following 
- A `regex` class attribute as string
- A `to_python(self, value)` method. convert matching string to a type which
  will be passed to the view function.
- A `to_url(self, value)` method which handles converting the Python type into a
  string to be used int he URL. a ValueError here means no match and `reverse()`
  will raise `NoReverseMatch` unless another URL pattern matches.
  
```
class FourDigitYearConverter:
    regex = '[0-9]{4}'

    def to_python(self, value):
        return int(value)

    def to_url(self, value):
        return '%04d' % value
```

```
from django.urls import path, register_converter

from . import converters, views

register_converter(converters.FourDigitYearConverter, 'yyyy')

urlpatterns = [
    path('articles/2003/', views.special_case_2003),
    path('articles/<yyyy:year>/', views.year_archive)
]
```

## Using regular expressions
**re_path()**

- re_path instead of path
- Use when path converter sequence isn't sufficient
- Syntax - `(?P<name>pattern>)`

```
from django.urls import path, re_path
from . import views

urlpatterns = [
    re_path(r'^articles/(?P<year>[0-9]{4})/$', views.year_archive),
    re_path(r'^articles/(?P<year>[0-9]{4})/(?P<month>[0-9]{2})/(?P<slug>[\w-]+)/$',
            views.article_detail)
]
```

- Based on what you use, `path` or `re_path` the type of arguments on view
  functions will change.

## Nested arguments
```
from django.urls import re_path

urlpatterns = [
    re_path(r'^blog/(page-(\d+)/)?$', blog_articles), # bad

    re_path(r'^comments/(?:page-(?P(<page_number>\d+)/)?$', comments)  # good
]
```

- First one will have two positional arguments: `page-2` and `2`.
- Second pattern will match with keyword argument `page_number` set to 2.
  The outer argument is in this case is a non-capturing argument `(?:....)`

## Default for view arguments
```
urlpattern = [
    path('blog/', views.page),
    path('blog/page<int:num>/', views.page),
]

def page(request, num=1):
    pass
```

## Error handling views
- For unmatched urls or on an raised exception django invokes error handling view.
- views for these are specified by four variables
- these variables are customizable (later).
- such values can be set in your root URL conf. Setting these on other URLConf
  will have no effect.
- variables must be callable
- Variables
  - handler400 - See `django.conf.urls.handler400`
  - handler403 - See `django.conf.urls.handler403`
  - handler404 - See `django.conf.urls.handler404`
  - handler500 - See `django.conf.urls.handler500`
  
## Include other URLconfs
```
urlpatterns = [
    path('community/', include('arrgegators.urls')),
]
```

- Another way  
  ```
  urlpatterns = [
    path('<page_slug>-<page_id>/', include([
        path('history/', views.history),
        path('edit/', views.edit),
        path('discuss/', views.discuss),
        path('permissions/', views.permissions),
    ]))
  ]
  ```

## Passing extra options to view functions  
```
from django.urls import path
from . import views
urlpattern = [
    path('blog/<int:year>/', views.year_archive, {'foo': 'bar'}),
]
```
- This technique is used in the `syncication framework` to pass metadata
  and options to view function.
 
- If there is a conflict with urls named keyword argument then dictionary
  value will be used.

## Reverse resolution of URLs
Tools for url reversing
- In templates: Using the `url` template tag.
- In Python code: Using the `reverse()` function.
- In higher level code related to handling of URLs of Django model instances:
  The `get_absolute_url()` method.

```
urlpattern = [
    path('articles/<int:year>/', views.year_archive, names='news-year-archive'),
]
```

- In template
  ```
  <a href="{%url 'news-year-archive' 2021 %}">2021 Archive</a>
  ```

- In python code:  
  ```
  from django.http import HttpResponseRedirect
  from django.urls import reverse
  
  def redirect_to_year(request):
      year = 2006
      return HttpResponseRedirect(reverse('news-year-archive', args=(year,)))
  ```
  
- In some scenarios where views are of a generic nature a many to many
  relationship may exist between views and urls. In these cases view name isn't
  good enough to identify. Check next section.
  
  
## Naming URL patterns
- Clash can happen with other apps.
  - One way to solve is to put prefixes `myapp-comment` instead of `comment`.


## URL namespaces

- Namespaces can also be nested
  `sports:polls:index`

- Reversing namespaced URLs
  ```
  # urls.py

  from django.urls import include, path

  urlpatterns = [
    path('author-polls/', include('polls.urls', namespace='autor-polls')),
    path('publisher-polls/', include('polls.urls', namespace='publisher-polls')),
  ]
  ```
  
  ```
  # polls/urls.py
  
  from django.url import path
  from . import views
  
  app_name = 'polls'
  
  urlpatterns = [
    path('', views.IndexView.as_view(), name='index'),
    path('<int:pk>/', views.DetailView.as_view(), name='detail'),
  ]
  ```
  
  - In method
    `reverse('polls:index', current_app=self.request.resolver_match.namespace`
    
  - In template
    `{% url 'polls:index' %}`

  - If we are rendering the page somewhere else in the site and there is no
    current instance. then the last one will be picked - `publisher-polls`

  - You can use 
    `author-polls:index`
    
## URL namespaces and included URLconfs

# TO-DO: Get back here and re-read it again.
