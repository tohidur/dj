- On INSTALLED_APP use `polls.apps.AppConfig` instead of `polls`

- Always redirect after post

- Generic Views

### Using Django
#### Making queries
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

### Important
- Upgrading pre-Django-style middleware
