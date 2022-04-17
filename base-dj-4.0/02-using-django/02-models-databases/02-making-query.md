# Making Queries

## Creating Object
- Instantiate a model using keyword arguments and call `save()` method.
- `save` method does not return anything.
- To create and save an object in a single step, use `create()` method.

## Update Object

## Saving ForeignKey and ManyToMay fields
- Saving ForeignKey is save as saving any other field
- Saving MayToMany field is a little different - you use `add()` method.
  ```python
  john = Author.objects.create(name='john')
  paul = ...
  george = ..
  
  entry.authors.add(john, paul, george)
  ```
 
## Retrieving objects
- Manager (`objects`) is only accessible via Model class instead of model instance

- Chaining filters
  ```python
  Blog.filter(**kwargs).exclude(**kwargs)
  ```

- Slicing is equivalent to SQL `LIMIT` and `OFFSET`. e.g. - `Entry.objects.all()[5:10]`. 
  This gives you a QuerySet instance. Doesn't execute the query.
  Unless, you use step in slicing - `Entry.objects.all()[:10:2]`. This will execute and fetch the entries first.
  
  - Furthermore, filtering or ordering of sliced queryset is prohibited.

- Filter can reference fields on the model - F expression
  ```python
  Entry.objects.filter(number_of_comments__gt=F('number_of_pingbacks') * 2)
  ```
  
  - F support bitwise operations - `F('biz_feature_level').bitand(16)`  
    ```.filter(biz_feature_level=F('biz_feature_level').bitand(16))```
   
  - Expression can reference transforms  - New in django 3.2
    - Entry published in the same year as they were last modified
    ```Entry.objects.filter(pub_date__year=F('mod_date__year'))```

    - To find the earliest year an entry was published 
      ```Entry.objects.aggregate(first_published_year=Min('pub_date__year'))```
    
    - Highest rated entry and the total number of comments on all entries for each year
      ```python
      Entry.objects.values('pub_date__year').annotate(
          top_rating=Subquery(
              Entry.objects.filter(
                  pub_date__year=OuterRef('pub_date__year'),
              ).order_by('-rating').values('rating')[:1]
          )
      )
      ```
## Caching QuerySet
- When queryset was not cached?
  - When evaluating only the part of the queryset.  
    ```python
    queryset = Entry.objects.all()
    queryset[5]  # makes query
    queryset[5]  # makes query
    [i for i in queryset]  # makes query
    queryset[5]  # use cache
    ```
  - Simply printing the queryset will not populate the cache.

## Querying JSON field
```python
from django.db import models

class Dog(models.Model):
    name = models.CharField(max_length=200)
    data = models.JSONField(null=True)

    def __str__(self):
        return self.name
```
- Storing and Querying for None
  - Not recommended but you can store JSON null by - Value('null'). You can use None. Which will be SQL NULL.
  - Whatever you store. Python will consider it as None. This only applies to the top level null.
  - If None is inside a list or json it will be interpreted as JSON null.
  - So, while querying None will be interpreted as JSON null. To query for SQL NULL, use isnull.
  ```python
  Dog.objects.create(name='Max', data=None)  # SQL NULL
  Dog.objects.create(name='Archie', data=Value('null')  # JSON null
  Dog.objects.filter(data=None)  # output Archie
  Dog.objects.filter(data=Value('null'))  # output Archie
  Dog.objects.filter(data__isnull=True) # Max
  Dog.objects.filter(data__isnull=False) # Archie 
  ```
  Better to use null=False in the field. And use default=dict.

- Multiple keys can be chained together
  ```Dog.objects.filter(data__owner__name='Bob')```

- If key is an integer, it will be interpreted as an index transform in an array
  ```Dog.objects.filter(data__owner__other_pets__0__name='Fishy'```

- Query for missing key. use isnull
  ```Dog.objects.filter(data__owner__isnull=True)``` 

- Above lookups explicitly use exact lookup. These can also be chained with icontains, istartswith, ... etc

- **Containment and key lookups**  
  - **contains**  
    ```Dog.objects.filter(data__contains={'owner': 'Bob'})```
  
  - **contained_by**  
    This is inverse of contains. Will return thsoe where key-value pair of objects are a subset of those in the
    value passed.
    ```python
    >>> Dog.objects.create(name='Rufus', data={'breed': 'labrador', 'owner': 'Bob'})
    <Dog: Rufus>
    >>> Dog.objects.create(name='Meg', data={'breed': 'collie', 'owner': 'Bob'})
    <Dog: Meg>
    >>> Dog.objects.create(name='Fred', data={})
    <Dog: Fred>
    >>> Dog.objects.filter(data__contained_by={'breed': 'collie', 'owner': 'Bob'})
    <QuerySet [<Dog: Meg>, <Dog: Fred>]>
    >>> Dog.objects.filter(data__contained_by={'breed': 'collie'})
    <QuerySet [<Dog: Fred>]>
    ```

   - **has_key**  
     Where given key is in the top level of the data
     ```Dog.objects.filter(data__has_key='owner')```
   
   - **has_keys**  
     All keys are on top level of data
     ```Dog.objects.filter(data__has_keys=['breed', 'owner'])```

  - **has_any_keys** 
    ```Dog.objects.filter(data__has_any_keys=['owner', 'breed'])``` 

## Lookup with Q objects  
Lookup argument can mix with keyword argument and Q objects. Those will be "AND"ed together. However Q object must
precede the definition of all keyword arguments. Otherwise, it is invalid.

## Copying model instance
blog.pk = None
blog._state.adding = True
blog.save() # blog.pk == 2

## Updates
- Update doesnt' call save method or invoke pre_save, post_save signals or respect auto_now.
- You can use F object with updates. But you can't use joins inside F. You need to use models field. Unlike filter.

## Custom Related Manager
```python
from django.db import models

class Entry(models.Model):
    #...
    objects = models.Manager()  # Default Manager
    entries = EntryManager()    # Custom Manager

b = Blog.objects.get(id=1)
b.entry_set(manager='entries').all()
b.entry_set(manager='entries').is_published()
```


