---
title: Django Random Tips 
author: Dimitris Georgoulas
date: 2020-05-04 08:30:00 +0800
categories: [Web-Development, Django]
tags: [django, tips]
toc: true
---

## Avoiding race conditions in django
- Use an `F() expression` when updating a model field’s value relatively to its previous value. For example:
```python
from django.db.models import F
product = Product.objects.get(name='Numenor stone')
# don't do this:
product.number_sold += 1
product.save()
```
We want to avoid loading the _number_sold_ value in memory, doing the calculation (in this case adding 1) and then saving back to the 
database, because while the value is in memory, the actual value in the database might be modified by another application 
instance. This would mean that the first application instance will use an outdated value for the calculation. 
```python
# do this instead
product.number_sold = F('number_sold') + 1
product.save()
```
The F() object generates an SQL expression that describes the required operation at the database level, so you avoid 
loading the value in memory. When product.save() is executed the operation will use the database's value. 

- If we use `get_or_create()` or `update_or_create()` methods, we must enforce **uniqueness** in database level for the given keyword arguments. 
This is a get_or_create() example but the reasoning is identical for update_or_create() too. 
```python
highlander, created = Immortal.objects.get_or_create(
    first_name='Connor', last_name='MacLeod'
)
# get_or_create is equivalent to the following:
try:
    highlander = Immortal.objects.get(first_name='Connor', last_name='MacLeod')
except Immortal.DoesNotExist:
    highlander = Immortal(first_name='Connor', last_name='MacLeod')
    highlander.save()
```
If highlander does not exist and two different application instances run this code at the same time then they will 
both try to create him. If there is nothing to ensure that there can be only one highlander we can end up with two. 
To avoid this we must enforce uniqueness at the database level.    
```python
class Immortal(models.Model):
    first_name = models.Charfield(max_length=42)
    last_name = models.Charfield(max_length=42)

    class Meta:
        # enforce uniqueness to avoid duplicate entries
        unique_together = ('first_name', 'last_name')
```
Now only the faster application instance will actually create the highlander. The second one will just raise 
an IntegrityError. 

- If you want to update an existing model instance, use the `update()` method instead of loading it in memory and then calling save() 
on it, in order to avoid the possibility that the entry is modified (for example deleted) in the short period of time between loading and saving. 
The update() method creates a raw SQL expression to update the model directly in the database. 
```python
# instead of doing this:
captain = Person.objects.get(id=1)
captain.last_name = 'Adama'
captain.save()
# do this:
Person.objects.filter(id=1).update(last_name='Adama')
```
> ***Note***: The update() method does an update at the SQL level and thus, does not call any save() methods on 
>your models. This means that the pre_save() and post_save() signals which are a consequence of calling Model.save() 
>are also not called. If executing the model's save() method or its related signals is necessary then you can't use update(). In this case 
you have to use the select_for_update() queryset method in combination with database transactions. See below.

- Using the `select_for_update()` queryset method in combination with **database transactions**. 
```python
from django.db import transaction
wandering_hobbits = Hobbit.objects.select_for_update().filter(location='Bree')
with transaction.atomic():
    for hobbit in wandering_hobbits:
        hobbit.location = 'Lothlorien'
        hobbit.save()
```
When the queryset is evaluated (for hobbit in wandering_hobbits in this case), all matched hobbits will be locked until the end 
of the transaction block, meaning that other transactions will be prevented from changing or acquiring locks on them. 
> ***Note***: The select_for_update() method locks rows by using a _SELECT ... FOR UPDATE_ SQL statement on supported databases. This 
is not a table level lock, it is a row level lock, so the rest of table's rows can be normally accessed by other 
application instances which means that the overall performance of your service is not reduced.   

- And a small bonus. A post describing the cumbersome process of 
[Fixing a race condition](https://medium.com/in-the-weeds/fixing-a-race-condition-c8b475fbb994). It does it in a 
ruby on rails context but the fundamentals are identical. 

## select_related() and prefetch_related()
select_related() and prefetch_related() can greatly improve the performance of 
your code. They return a QuerySet that will automatically retrieve, in a single batch, 
related objects for each of the specified lookups. This would become more clear with an example 
where data relationships span across many models and if select_related() and 
prefetch_related() were not used, the impact on performance would be unbearable.
```python
class TotalBet(Model):
    user = ForeignKey(User, on_delete=CASCADE, related_name='total_bets')
    bets = ManyToManyField(Bet)

class Bet(Model):
    bet_events = ManyToManyField(BetEvent)

class BetEvent(Model):
    event = ForeignKey(Event, on_delete=CASCADE, related_name='bet_events')
    market_type = ForeignKey(MarketType, on_delete=CASCADE, related_name='bet_events')

class Event(Model):
    home_team = ForeignKey(Team, on_delete=CASCADE, related_name='events')

total_bets = TotalBet.objects.filter(status=won)

for total_bet in total_bets:  # database hit
    user = total_bet.user  # database hit
    for bet in total_bet.bets.all():  # database hit
        for bet_event in bet.bet_events.all():  # database hit
            market_type = bet_event.market_type  # database hit 
            home_team = bet_event.event.home_team  # database hit
```
You can significantly improve performance by fetching all necessary 
data at once, in a single batch, using select_related() and prefetch_related() 
and then loop through them
```python
total_bets = TotalBet.objects.filter(status=won).select_related('user').\
        prefetch_related('bets__bet_events', 'bets__bet_events__market_type',
                          'bets__bet_events__event__home_team', 
                          'bets__bet_events__event__away_team')
for total_bet in total_bets:
    ...
```

The related data is stored in prefetched querysets, so instead of hitting the database for expensive joins 
many times during the for loop, we hit it only once, performing some super expensive queries but in one batch 
greatly improving the performance.

> ***Note***: The difference between select_related and prefetch_related lies in how they work under the hood. 
>The result is, that select_related() is limited to single-valued relationships (foreign key and one-to-one) 
>while prefetch_related() on the other hand, can prefetch many-to-many and many-to-one objects.

## Django forms validation in a nutshell
Form validation is normally executed when you call the `Form.is_valid()` method. 
There are other things that can also trigger cleaning and validation (accessing the errors attribute 
or calling full_clean() directly), but normally they won’t be needed. During Form validation the following 
actions are executed: 

1. Field.clean()  
It runs for each field of the form and invokes the following three methods:
- `to_python()`: It coerces the raw input value to a correct python datatype depending on the form field type.
- `validate()`: It handles field-specific validation that is not suitable for a validator.
- `run_validators()`: It runs all of the field’s validators and aggregates all the errors into a single ValidationError. 
A validator is merely a callable object or function that takes a value and simply returns nothing if the value is 
valid or raises a ValidationError if not. You pass them to the form’s field in the form class definition. 
> ***Note***: The Field.clean() method returns the clean data, which is then inserted into the `Form.cleaned_data` dictionary.   

2. Field.clean_\<fieldname\>()  
It runs for each field of the form and does any cleaning that is specific to that particular field, unrelated to the type 
of field that it is. For example check if the value of a charfield is unique.
 
3. Form.clean()  
It runs after the individual field methods have run, so it has access to their errors too, available in `Form.errors` attribute. 
It can perform validation that requires access to multiple form fields. Errors raised by this method are accessed by 
the `Form.non_field_errors` attribute. 

## Clearing the session store
In general you can use sessions in four ways:
- Using database-backed sessions (the session data is stored in the database)
- Using cached sessions (the session data is stored in the cache but also in the database if you want)
- Using file-based sessions (the session data is stored in a filesystem)
- Using cookie-based sessions (the session data is stored in a cookie)

As users create new sessions on your website, session data can accumulate in your session store. 
If you’re using the database backend, the django_session database table will grow. If you’re using the file backend, 
your temporary directory will contain an increasing number of files.
To understand this problem, consider what happens with the database backend. When a user logs in, Django adds a row 
to the django_session database table. Django updates this row each time the session data changes. If the user logs 
out manually, Django deletes the row. But if the user does not log out, the row never gets deleted. A similar process 
happens with the file backend.
Django does not provide automatic purging of expired sessions. Therefore, it’s your job to purge expired sessions 
on a regular basis. Django provides a clean-up management command for this purpose: `clearsessions`. It’s recommended 
to call this command on a regular basis, for example as a **daily cron job**.
> ***Note***: The cache backend (without persistence to the database) isn’t vulnerable to this problem, because caches automatically delete stale data. 
Neither is the cookie backend, because the session data is stored by the users’ browsers.

## Signals
- Signals are not executed asynchronously. This means that the `post_save` signal will have to wait for the `pre_save` signal to finish.
- Signals are part of Transactions. If there is an exception in the functions executed in the signal the transaction is rolled back. 
The signal is considered part of the transaction block.
- The pre_save or post_save signals are a consequence of calling Model.save() method
- Many of django signals are sent by various model methods like \_\_init\_\_() or save() that you can override in your own code. 
If you override these methods on your model, you must call the parent class’ methods for these signals to be sent.

## Queryset.update() and Model.save()
- The queryset.update() method does an update at the SQL level and thus, does not call any save() methods on your models 
nor does it emit the pre_save or post_save signals (which are a consequence of calling Model.save()). 
- Model DateFields with auto_now argument (used for tracking when was the latest update of a model) are not updated 
with queryset.update().
- queryset.bulk_create() also doesn’t use the model's save() method. 
- If you want to avoid these side effects of the queryset.update() then loop over the entries of the queryset and call 
save() on each one of them.  
```python
elves = Elf.objects.all()
# instead of doing this:
elves.update(weapon='Elven Sword')
# do this:
for elf in elves:
    elf.weapon = 'Elven Sword'
    elf.save()
```
> ***Note***: If your service uses concurrent application instances, then looping through entries, loading in 
>memory and then saving might cause race conditions. To avoid them see
> [avoiding race conditions in django]({% post_url 2020-05-04-django-tips %}#avoiding-race-conditions-in-django).  

## Url design  
When deciding whether to use the URL path or the query parameters for passing information, the following may help:
1. use the URL path for uniquely identifying resources, for example /blog/post/15/ not /blog/posts/?id=15
2. use query parameters for changing the way the resource is displayed, for example /blog/django/2020/?sort_by=date&direction=desc
3. to make human friendly URLs, avoid using ID numbers and use dates, categories or slugs, for example /blog/django/2020/09/30/django-urls/  

> ***Note***: The url query parameters are available in request.GET dictionary, for example: request.GET.get(“sort_by”). 

## Number of entries in a queryset   
When you only want the number of entries in a queryset use the queryset's count() method `queryset.count()` instead of the len 
function `len(queryset)`. The len function will evaluate the queryset, which means that it will fetch all the entries from the 
database and store them in memory, while the count method will just return the number of entries without returning the 
actual entries. Under the hood you have a `SELECT * FROM table` vs a `SELECT COUNT(*) FROM table` SQL query. If you intend 
to iterate over the entries though, you better use the len() function in order to avoid the double hit to the database (one for 
the count operation and one for the select all operation). 

## Callable as a model field’s default value
The default value of a field can be a callable object. In that case it will be called every time a new object is created.
```python
from datetime import datetime, timedelta

def get_deadline():
    return datetime.today() + timedelta(days=20)

class Bill(models.Model):
    deadline = models.DateField(default=get_deadline)
```
> ***Note***: lambdas can’t be used for field options like default because they can’t be serialized by migrations.

## Extendable views  
If you intent to build a django app for other developers, you would like to make it as customizable as possible. One way 
to customize a 3rd party django app is to override its views. You can make your app views easier to customize by adding some 
standard arguments to them, arguments like extra_context or upload_form and of course *args and **kwargs. For example 
with an `extra_context` argument you give others the possibility to easily update your view's context. The modified 
context can then be rendered by slightly modifying the respective template in order to use it.  
```python
def original_view(request, extra_context=None, next_override=None, 
                upload_form=UploadForm, primary_form=PrimaryForm, 
                *args, **kwargs):
    ...
    if extra_context is None:
        extra_context = {}
    context = {'the_context': the_context}
    context.update(extra_context)  # update the context with the extra_context
    template_name = settings.MYAPP_TEMPLATE or 'myapp/my_template.html'
    return render(request, template_name, context)
```
```python
def my_override_view(request):
    # do stuff before the original view is called
    extra_context = {'new_context_item': item}
    # call the original view
    response = views.original_view(request, extra_context=extra_context)
    # do stuff after the original view is called
    return response
```

## Miscellaneous
- List of ids   
When you want a list of the ids or a list of any other field of a django model you can use the values_list() method.
```python
MyModel.objects.values_list('id', flat=True).order_by('id')
# <QuerySet [1, 2, 3, ...]>
```

- Evaluating querysets  
An (unevaluated) queryset doesn't hold any data, it just represents a specific SQL query to the database. It 
only holds data when it is evaluated. A queryset is evaluated when:
1. You iterate over it
2. You slice it
3. You pickle it
4. You call repr(), len(), list() on it
5. Testing it in a boolean context using it in an if, or, and statement or calling bool() on it.   

- Django template comments vs html comments  
Django template comments are marked with `{# comment #}` while html native comments are `<!-- comment -->`. 
The difference is that django comments are not part of the rendered html, for example if you view page source in 
the browser you will not see them.
