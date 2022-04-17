## Cheat Sheet
```python
Book.objects.all().aggregate(Avg('price'))
Book.objects.all().aggregate(Max('price'))

# Difference between the highest priced book and the average price of all books.
from django.db.models import FloatField
Book.objects.aggregate(price_diff=Max('price', output_field=FloatField()) - Avg('price'))

# Each publisher, each with a count of books as a "num_books" attribute.
pubs = Publisher.objects.annotate(num_books=Count('book'))

# Each publisher, with a separate count of books with a rating above and below 5
above_5 = Count('book', filter=Q(book__rating__gt=5))
below_5 = Count('book', filter=Q(book__rating__lte=5))
pubs = Publisher.objects.annotate(below_5=below_5).annotate(above_5=above_5)

# Top 5 publishers
pubs = Publisher.objects.annotate(num_books=Count('book')).order_by('-num_books')[:5]

# Generating over the entire queryset
Book.objects.aggregate(Avg('price'), Max('price'), Min('price'))

# Generating over each object - annotate
q = Book.objects.annotate(Count('authors'))
q[0].authors__count
q = Book.objects.annotate(num_authors=Count('authors'))  # override default name
q[0].num_authors
```