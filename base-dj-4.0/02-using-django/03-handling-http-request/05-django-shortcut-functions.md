# Django Shortcut functions
- **render()**
  - Combines a given template with a given context dictionary and returns
    a HttpResponse.

- **redirect()**  
  - Arguments
    - A model: the model's `get_absolute_url()` function will be called.
    - A view name, possibly with arguments: `reverse()`.
    - An absolute or relative url.
  
  - By default issues a temporary redirect; pass `parmanent=True` to
    issue a permanent redirect.

- **get_object_or_404()**  
  - Raises `Http404` instead of `DoesNotExist`.
  - You can pass Model, a Manager or a QuerySet or a reverse relation.

- **get_list_or_404()**  
  - If list is empty then raises `Http404`.
  