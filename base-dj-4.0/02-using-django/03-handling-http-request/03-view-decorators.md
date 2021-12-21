# View Decorators

- Allow HTTP methods
   - In `django.views.decorators.http`. It will return `django.http.HttpResponseNotAllowed`
     if conditions are not met.
     
     ```python
     from django.views.decorators.http import require_http_methods
     
     @require_http_methods(['GET', 'POST'])
     def my_view(request):
        pass
     ```
   
   - `require_GET()`
   - `require_POST()`
   - `require_safe()`
      To only accept GET and HEAD methods.

- Conditional view processing  
  Can be used to control caching behaviour of particular views.
  Can be used to generate ETag and Last-Modified headers.
  
  - `condition(etag_func=None, last_modified_func=None`
  - `egat(etag_func)`
  - `last_modified(last_modified_func`
  
- GZip compression  
  - `django.views.decorators.gzip`
    - `gzip_page()`

- Vary Headers
  - `django.views.decorators.vary`  
  - Can be used to control caching based on specific request headers.
  - `vary_on_cookie(func)`
  - `vary_on_headers(*headers)`
  - The Vary header defines which request headers a cache mechanism should
    take into account when building its cache key.

- Caching
  - Decorator - `django.views.decorators.cache`.  
  - `cache_control(**kwargs)`
    - Patches the response's `Cache-Control` header by adding all of the keywordf
      argument to it. See `patch_cache_control()`.
  - `never_cache(view_func`
    - Add `Expires` header to current date/time.
    - Adds a `Cache-Control: max-age=0, no-cache, no-store, must-revalidate, private`

- Common
  - Decorator - `django.views.decorators.common`
  - Allow per-view customization of `CommonMiddleware` behavior.
  - `no_append_slash()`