### Models  

- Subclass of django.db.models.Model. 

- Fields 
  - `id` fields added automatically by default.
  - To use models app needs to be specified on `INSTALLED_APPS`.
  - Choose field names which is not conflicting with model APIs, clean, save
    or delete.

- Field Types
  - Check model field reference (API Reference).
  - You can write custom model fields also (Check How-to's).

- Field Options
  - `max_length`, `null`, `blank`  
  - `choices`  
    - Sequence of 2 tuples, first element of tuple will be store in DB.
      Second element is displayed by the field's form widget.
    - Display value for a field can be accessed by - `get_FOO_display()`. 
    - You can also use enumerate classes to define choices in a concise way.
      ```python
      from django.db import models


      class Runner(models.Model):
          MedalType = models.TextChoices('MedalType', 'GOLD SILVER BRONZE')
          medal = models.CharField(blank=True, choices=MedalType.choices,
                                   max_length=10)
      ```
  - `default`  
    This can be a callable also.

  - `help_text`  

  - `primary_key`  
     - Value True
     - If you don't specify to any field then Django will automatically create
       on IntegerField (`id`).
     - Primary key field is read-only, if you update one primary key then 
       a new object will be created alongside the old one.

  - `unique`  

- Verbose field names  
  - Each field takes a optional first argument - verbose name. Except few
    fields. If not passed Django will create one from field name.

  - Except `ForeignKey`, `ManyToManyField` and `OneToOneField`. For these
    first argument is a model class. So you have pass `verbose_name` keyword
    argument. 
    ```python
    poll = models.ForeignKey(
        Poll,
        on_delete=models.CASCADE,
        verbose_name="the related poll",
    )
    ```

- Relationships  
  
  - ManyToOne  
    - Use django.db.models.ForeignKey.

  - ManyToMany  
    - It suggested that many-to-many field should be plural.
    - It doesn’t matter which model has the ManyToManyField, but you should
      only put it in one of the models – not both.

  - Extra fields on ManyToMany  
    - Using `through` argument on `ManyToManyField` class.
      ```python
      from django.db import models

      class Person(models.Model):
          name = models.CharField(max_length=128)

          def __str__(self):
              return self.name
      

      class Group(models.Model):
          name = models.CharField(max_length=128)
          members = models.ManyToManyField(Person, through='Membership')

          def __str__(self):
              return self.name


      class Membership(models.Model):
          person = models.ForeignKey(Person, on_delete=models.CASCADE)
          group = models.ForeignKey(Group, on_delete=models.CASCADE)
          date_joined = models.DateField()
          invite_reason = models.CharField(max_length=64)
      ```
  - Restrictions  
    - Your intermediate model must contain one - and only one 
      foreign key to both models.  

      Or you must explicitly specify the foreign keys Django should use for
      the relationship using ManyToManyField.through_fields.

    - For self reference many to many if there are only two FK's to same model
      then it's permitted, if more than 2 then you need to specify
      `through_fields`.
  
    - Use of through defaults for any required param.
      ```python
      ringo = Person.objects.create(name="Ringo Starr")
      paul = Person.objects.create(name="Paul McCartney")
      beatles = Group.objects.create(name="The Beatles")
      m1 = Membership(person=ringo, group=beatles, date_joined=date(1962, 8, 16),
                      invite_reason="Needed a new drummer.")
     
      beatles.members.add(john, through_defaults={'date_joined': date(1960, 8, 1)})
      beatles.members.create(name="George Harrison", through_defaults={'date_joined': date(1960, 8, 1)})
      beatles.members.set([john, paul, ringo, george], through_defaults={'date_joined': date(1960, 8, 1)})
      
      beatles.members.clear()
      ```

  - **One-to-One Relationship
    - Use `models.OneToOneField`.
    - Also accepts optional `parent_link` argument.
    - Deprecation warning
      `OneToOneField` used to used to automatically become the primary key
      on the model. This is no longer true, although you can manually pass
      `primary_key` argument. Thus it's not possible to have multiple
      `OneToOneField` on a single model.
      
#### Models across files
```
from django.db import models
from geography.models import ZipCode

class Restaurant(models.Model):
    # ...
    zip_code = models.ForeignKey(
        ZipCode,
        on_delete=models.SET_NULL,
        blank=True,
        null=True,
    )
```

#### Field name restrictions
Django doesn't allow some field names
- Python reserved keywords - pass.
- `foo__bar` - For Django lookup syntax.
- Can't end with an underscore, for the same reason

#### Meta options
```
class Ox(models.Model):
    horn_length = models.IntegerField()

    class Meta:
        ordering = ["horn_length"]
        verbose_name_plural = "oxen"
```

#### Model attributes
- objects
  Manager - interface through which database query operations are provided.

#### Model Methods
- **Override predefined model**  
  ```
  class Blog(models.Model):
    def save(self, *args, **kwargs):
      do_something()
      super().save(*args, **kwargs)
      do_something_else()
  
  // You can also perfent saving
  def save(self, *args, **kwargs):
    if not condition:
        return
    else:
        super().save(*args, **kwargs)
  ```

- delete method is not necessarily called when deleting objects in bulk on
  QuerySet. you can use pre_delete and post_delete signals.
  
  Unfortunately, there isn't any workaround when creating or updating objects
  in bulk, since none save(), pre_save and post_save are called.
  
#### Model inheritance

- Three styles
  1. `Abstract base class` - To hold common information that you don't
     want to have to type out for each child model.

  2. `Multi-table inheritance` - Subclassing an existing model (perhaps
     something from another application entirely) and want each model
     to have it's own database table.
     
  3. `Proxy modles` - If you want to modify the Python-level behaviour
     of a model. Without changing the models fields in any way.
     
##### Abstract base class
- `abstract=True` - in `Meta` class.

- Will not create a database table.

- Child will inherit parents `Meta` class. You can override it also.
  ```
  class Meta(CommonInfo.Meta):
      db_table = 'student_info'
  ```

- Child class copies all attributes of `Meta` class from parent except
  `abstract=True`. On child it will be `False`.

- Some attribute doesn't make sense to add on child Meta class like - `db_table`.

- Be careful with `related_name` and `related_query_name`.
  These names should be unique per table so adding these on Abstract
  model will create duplicates.
  
  To work around this problem, when you are using related_name or
  related_query_ame in an abstract base class (only), part of the value
  should contain '%(app_label)s' and '%(class)s'.
  
  If you forgot to mention then django will throw an error at the time
  of system check or run migrations.

##### Multi-table inheritance
- Each models corresponds to its own database table an can be queried
  and created individually.

- Inheritance relationship happens through OneToOneField.

- The automatically-created `OneToOneField` on `Restaurant` that links
  it to `Place` looks like this:
  ```
  place_ptr = models.OneToOneField(
    Place,
    on_delete=models.CASCADE,
    parent_link=True,
    primary_key=True,
  )
  ```
  You can override that field by declaring your own OneToOneField with
  parent_link=True on Restaurant.

- Inheritance and reverse relation 
  If you have ForeignKey or ManyToManyField in the table you must specify
  `related_name` as reverse relation name is used up by inheritance.
  ```
  class Supplier(Place):
     customers = models.ManyToManyField(Place)
  ```

  ```
  Reverse query name for 'Supplier.customers' clashes with reverse query
  name for 'Supplier.place_ptr'.
  ```
  
  - Solution
    ```
    Adding related_name to the customers field as follows would resolve the error: models.ManyToManyField(Place, related_name='provider').
    ```
    
##### Proxy models
```
from django.db import models

class NewManager(models.Manager):
    pass

class MyPerson(Person):
    objects = NewManager()
    
    class Meta:
        proxy = True
```

- If you want existing models and don't want all original table columns
  use Meta.managed=False. Not under the control of Django.
  
- Wanting to change python only behaviour and keep all the same fields
  as original, use `Meta.proxy=True`.
  

##### Field name 'hiding' is not permitted.
You can not override fields on child class. Doesn't apply on model
fields inherits from abstract model.


##### Organizing models in a package
```
# myapp/models/__init__.py

from .organic import Person
from .synthetic import Robot
```