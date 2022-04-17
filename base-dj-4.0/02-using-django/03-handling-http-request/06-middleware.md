# Middleware

Middleware is a framework of hooks into Django's request/response processing.
It's a light, low-level 'plugin' system for globally altering Django's input
or output.

## writing your own middleware.A
A middleware factory is a callable that takes a `get_response` callable and
returns a middleware.

```python
def simple_middleware(get_response):
    # One-time configuration and initialization

    def middlware(request):
        # Code before the view is called.

        response = get_response(request)

        # Code after the view is called.
        
        return response
```

```python
class SimpleMiddlware:
    def __init__(self, get_response):
        self.get_response = get_response

    def __call__(self, request):
        response = self.get_response(request)
        return response
```

## Activating middleware
- Add the middleware to the `MIDDLEWARE` list in Django settings file.

- `MIDDLEWARE` list can be empty, Django doesn't need it. But it's
   strongly suggested that you at least use `CommonMiddleware`.

-  The order of middleware matters.
   - Example  
     `AuthenticatoinMiddleware` stores the authenticated user int he session;
     therefore, it must run after `SessionMiddleware`.


## Other middleware hooks  
- **process_view()**  
