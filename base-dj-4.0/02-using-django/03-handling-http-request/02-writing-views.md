# Writing Views

A python function takes request and returns response.
The convention is to write it on views.py file

```
from django.http import HttpResponse

def my_view(request):
    # ...
    return HttpResponse(status=201)
```

- When you return an error such as `HttpResponseNotFound`, you're responsible
  for defining the HTML for the resulting error page.
  
  Django provides an Http404 exception. If you raise it Django will catch it
  and return the standard error page for your application.

  For custom page you can define the HTML named 404.html and place it in the top
  level of your template tree.
  
## Customizing error views

- Specify the handlers as below in your URLconf (root), will not effect elsewhere.
  ```
  handler404 = 'mysite.views.my_custom_page_not_found_view'
  handler505 = 'mysite.views.my_custom_error_view'
  ```

## Async views
- Use Python's `async def` to define asynchronous views.
- You will need to use an async server based on ASGI to get their performance
  benefits.
```
import datetime
from django.http import HttpResponse

async def current_datetime(request):
    now = datetime.datetime.now()
    html = '<html><body>It is now %s.</body></html>' % now
    return HttpResponse(html)
```
