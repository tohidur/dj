# File Uploads
- File data is placed in `request.FILES`

## Basic File Uploads
```python
from django import forms

class UploadFileForm(forms.Form):
    title = forms.CharField(max_length=50)
    file = forms.FileField()
```

- FileField only contains data if..
  - request is POST
  - at least one file field as actually posted
  - <form> request has the attribute `enctype="multipart/form-data".
  
```python
# views.py

from django.http import HttpResponseRedirect
from django.shortcuts import render
from .forms import UploadFileForm

from somewhere import handle_uploaded_file

def upload_file(request):
    if request.method == 'POST':
        form = UploadFileForm(request.POST, request.FILE)
        if form.is_valid():
            handle_uploaded_file(request.FILES['file'])
            return HttpResponseRedirect('/success/url/')
    else:
        form = UploadFileForm()
    return render(request, 'upload.html', {'form': form})
```

```python
# somewhere.py

def handle_upload_file(f):
    with open('some/file/name.txt', 'wb+') as destination:
        for chunk in f.chunks():
            destination.write(chunk)
```

- Looping over `UploadFile.chunks()` instead of `read()` ensures that large
  files don't overwhelm your system's memory.


## Handling uploaded files with a model
- If you are using file upload with a model then `ModelForm` makes it much
  easier.
- File will be uploaded to the location specified by `upload_to` argument
  of the FileField. When calling form.save().
- If you are constructing an object manually you assign file object.
  ```python
  instance = ModelWithFieldField(file_field=request.FILES['file'])
  instance.save()
  ```
## Uploading multiple files
- Set `multiple` HTML attribute of field's widget.
  ```python
  class FileField(forms.Form):
      file_field = forms.FileField(widget=forms.ClearableFileInput(
          attrs={'multiple': True}))
  ```
  
  - Override the post method of yoru FormView subclass.
  ```python
  from django.views.generic.edit import FormView
  from .forms import FileFieldForm
  
  class FileFieldFormView(FormView):
      form_class = FileFieldForm
      template_name = 'upload.html'
      success_url = ''
      def post(self, request, *args, **kwargs):
          form_class = self.get_form_class()
          form = self.get_form(form_class)
          files = request.FILES.getlist('file_field')
          if form.is_valid():
              for f in files:
                  ....
              return self.form_valid(form)
          else:
              return self.form_invalid(form)
  ```

## Upload Handlers
- When user uploads a file, Django passes off the file data to an upload handler.A
- Upload handlers are initially defined in the `FILE_UPLOAD_HANDLERS` which
  defaults to:
  ```
  ['django.core.files.uploadhandler.MemoryFileUploadHandler',
   'djanog.core.files.uploadhandler.TemporaryFileUploadHndler']
  ```
  Django reads small files into memory and large files into disk.
  - till 2.5 mb in memory.
  - For large on a file somewhere - `/tmp/tmpzfp6I6.upload`

- You can write custom upload handler for various things like:  
  - user-level quotas
  - compress data on the fly
  - render progress bars
  - send data to remote storage location.

  See writing custom upload handler in APIReference.

## Modify upload handlers on the fly.  
- Sometimes you need different upload behaviour just for a particular view.
- You can change the upload handler on pre-request basis by modifying
  `request.upload_handlers`.
  
  ```python
  request.upload_handlers.insert(0, ProgressBarUploadHandler(request))
  
  # OR

  request.upload_handlers = [ProgressBarUploadHandler(request)]
  ```
- If you are trying to change handler after accessing `request.POST`
  or `requet.FILES` django will throw an error.
  
  - `request.POST` is accessed by `CsrfViewMiddleware`. This means you
    will need to use `csrf_exempt()` you will then need to use
    `csrf_protect()` which actually processes the request.
    
    If you are using classed based views you will need to use `csrf_exempt()`
    on it's dispatch method and `csrf_protect` to the mehtod which actually
    processes it.
    

```python
from django.views.decorators.csrf import csrf_exempt, csrf_protect

@csrf_exempt
def upload_file_view(request):
    request.upload_handlers.insert(0, ProgressBarUploadHandler(request))
    return _upload_file_view(request)

@csrf_protect
def _upload_file_view(request):
    ... # process request
```

```python
from django.utils.decorators import method_decorator
from django.views import View
from django.views.decorators.csrf import csrf_exempt, csrf_protect

@method_decorator(csrf_exempt, name='dispatch')
class UploadFileView(View):
    def setup(self, request, *args, **kwargs):
        request.upload_handlers.insert(0, ProgressBarUploadHandler(request))
        super().setup(request, *args, **kwargs)

    @method_decorator(csrf_protect)
    def post(self, request, *args, *kwargs):
        ... # Process request
```








