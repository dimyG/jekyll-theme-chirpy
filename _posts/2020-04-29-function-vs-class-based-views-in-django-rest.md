---
title: Function vs Class based views in Django Rest Framework 
author: Dimitris Georgoulas
date: 2020-04-29 11:30:00 +0200
categories: [Web-Development, Django]
tags: [django, django-rest-framework]
toc: true
---

## Customization or standardization 
Similarly to django, django rest framework offers you the choice of using class based views. As most things in life 
class based views come with their own pros and cons and their adoption is a matter of preference. But no matter if you are fan of them 
or not, you have to admit that they offer you the possibility to do so many things with just a few lines of code. In a 
nutshell we can say that there are four ways to write a django rest view, beginning from the standard function based view and moving to 
different styles of class based views. 
  
1. Function based views
2. The APIView class
3. The mixin classes
4. The already mixed generic classes

Function based views are usually chosen when we want to highly customize the typical view logic 
since it is easier to follow the sequence of actions they describe. Class based views on the other hand 
give you the possibility to achieve a typical behaviour without much code, almost with no code at all, as will become obvious in a moment. 
This doesn't mean that you can't customize a class based view to any degree of complexity, but someone must be familiar with 
how class based views work behind the scenes, for example which methods are called in what order, in order to fully follow the view's logic. 
By the way, a very useful place to start familiarizing yourself with django class based views is the [Classy Class-Based Views](https://ccbv.co.uk/) 
and the [Classy Django REST Framework](http://www.cdrf.co/) for Django REST framework class based views respectively. 
I highly recommend them. 

In the rest of this post we will show how we can achieve the same result with each one of the four different view styles. 
Our example will focus on the simple `detail` view of an object. In this case the object is a Code Snippet as used in 
the django rest [tutorial](https://www.django-rest-framework.org/tutorial/3-class-based-views/) but 
you can imagine the object being anything you want, for example a Post or a Comment. The object's detail view is responsible 
for retrieving, updating or deleting the object or in other words handling the GET, PUT and DELETE method requests to the detail view's url. 
  
## Function based views
```python
from rest_framework import status
from rest_framework.decorators import api_view
from rest_framework.response import Response
from snippets.models import Snippet
from snippets.serializers import SnippetSerializer

@api_view(['GET', 'PUT', 'DELETE'])
def snippet_detail(request, pk):
    """
    Retrieve, update or delete a code snippet.
    """
    try:
        snippet = Snippet.objects.get(pk=pk)
    except Snippet.DoesNotExist:
        return Response(status=status.HTTP_404_NOT_FOUND)

    if request.method == 'GET':
        serializer = SnippetSerializer(snippet)
        return Response(serializer.data)

    elif request.method == 'PUT':
        serializer = SnippetSerializer(snippet, data=request.data)
        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data)
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)

    elif request.method == 'DELETE':
        snippet.delete()
        return Response(status=status.HTTP_204_NO_CONTENT)
```
Nothing fancy here, the logic flow is straightforward. We select the object from the database and then just use an 
`if` block to check the request's method and proceed accordingly. 

> ***Note***: The 
`@api_view` decorator ensures that the view receives an instance of the django rest framework's `Request` object 
(instead of the standard Django `HttpRequest` object)
and returns an instance of the django rest framework's `Response` object (instead of the standard Django `HttpResponse` object). 
It also provides some conveniences like returning 405 Method Not Allowed responses when appropriate, and handling any ParseError 
exception that occurs when accessing request.data with malformed input.

## The APIView class
```python
from snippets.models import Snippet
from snippets.serializers import SnippetSerializer
from django.http import Http404
from rest_framework.views import APIView
from rest_framework.response import Response
from rest_framework import status

class SnippetDetail(APIView):
    """
    Retrieve, update or delete a snippet instance.
    """
    def get_object(self, pk):
        try:
            return Snippet.objects.get(pk=pk)
        except Snippet.DoesNotExist:
            raise Http404

    def get(self, request, pk, format=None):
        snippet = self.get_object(pk)
        serializer = SnippetSerializer(snippet)
        return Response(serializer.data)

    def put(self, request, pk, format=None):
        snippet = self.get_object(pk)
        serializer = SnippetSerializer(snippet, data=request.data)
        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data)
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)

    def delete(self, request, pk, format=None):
        snippet = self.get_object(pk)
        snippet.delete()
        return Response(status=status.HTTP_204_NO_CONTENT)
```
The `APIView` class subclasses the django's `View` class and is the base of all views in django rest framework. Whenever 
you want to create a class based view you have to subclass the APIView. It is equivalent to the `@api_view` decorator of 
the function based views. It provides 
some standard generic view functionality but makes no assumptions on what you want to do with your view. You still have to 
precisely define your logic which means that it is not that different from the function based view. 
We just have replaced the object selection with the `get_object()` class method and 
the `if` block with distinct get, put and delete methods.

## The mixin classes
```python
from snippets.models import Snippet
from snippets.serializers import SnippetSerializer
from rest_framework import mixins
from rest_framework import generics

class SnippetDetail(mixins.RetrieveModelMixin,
                    mixins.UpdateModelMixin,
                    mixins.DestroyModelMixin,
                    generics.GenericAPIView):
    queryset = Snippet.objects.all()
    serializer_class = SnippetSerializer

    def get(self, request, *args, **kwargs):
        return self.retrieve(request, *args, **kwargs)

    def put(self, request, *args, **kwargs):
        return self.update(request, *args, **kwargs)

    def delete(self, request, *args, **kwargs):
        return self.destroy(request, *args, **kwargs)
```
Here we inherit the core view functionality from the `GenericApiView` (which inherits from the APIView) but we also inherit 
additional functionality from the mixin classes. 
We still have to define the get, put and delete methods but instead of manually writing the logic we just call the 
retrieve, update and destroy methods provided by the respective mixins. The result is exactly the same as before so we can more or less guess what these 
methods do. Another difference is that instead of selecting the object directly, we select a queryset from which the 
object is later retrieved by the mixin methods. The amount of code we need to write has significantly decreased but 
it can be decreased even further.
   
## The already mixed generic classes
```python
from snippets.models import Snippet
from snippets.serializers import SnippetSerializer
from rest_framework import generics

class SnippetDetail(generics.RetrieveUpdateDestroyAPIView):
    queryset = Snippet.objects.all()
    serializer_class = SnippetSerializer
```
Ok, that was quick! 

We only inherit from the `RetrieveUpdateDestroyAPIView` generic class, which offers the exact same standardized functionality 
we want to implement. It can 
properly retrieve, update and delete a specific object. We only need to define the 
queryset from which the object will be selected and the object's serializer. Everything else is handled by the build in 
functionality of the class. Not bad at all!

> ***Note***: The RetrieveUpdateDestroyAPIView can also handle PATCH requests for partially updating a model instance

## A standard approach
### Views
The previous example was dealing with retrieving, updating and destroying a model instance. There is another 
mixed generic class, the `ListCreateApiView`, that deals with listing model instances and also creating a new one. By combining 
RetrieveUpdateDestroyAPIView and ListCreateApiView we implement all possible actions for our Model. 
```python
from snippets.models import Snippet
from snippets.serializers import SnippetSerializer
from rest_framework import generics

class SnippetList(generics.ListCreateAPIView):
    queryset = Snippet.objects.all()
    serializer_class = SnippetSerializer

class SnippetDetail(generics.RetrieveUpdateDestroyAPIView):
    queryset = Snippet.objects.all()
    serializer_class = SnippetSerializer
``` 
With this code we have created the logic for listing all snippets, creating a new one and getting, updating or deleting 
an existing one. 
 
Since the customization code for both views is identical, it can be placed in a custom mixin class.
```python
class SnippetMixin(object):
    queryset = Snippet.objects.all()
    serializer_class = SnippetSerializer

class SnippetList(SnippetMixin, ListCreateAPIView):
    pass

class SnippetDetail(SnippetMixin, RetrieveUpdateDestroyAPIView):
    pass
```  

Notice that we can further abstract this code! This typical logic, can be replaced by the `ModelViewSet` which implements 
by default all methods of RetrieveUpdateDestroyAPIView and ListCreateApiView. A whole bunch of functionality can be 
implemented with a few lines of code. Class based views in all their glory.
```python
class SnippetMixin(object):
    queryset = Snippet.objects.all()
    serializer_class = SnippetSerializer

class SnippetViewSet(SnippetMixin, viewsets.ModelViewSet):
    pass
```

### Urls
To complete the implementation we have to set up the urls. We can do this manually:
```python
from snippets.views import SnippetViewSet, api_root

snippet_list = SnippetViewSet.as_view({
    'get': 'list',
    'post': 'create'
})
snippet_detail = SnippetViewSet.as_view({
    'get': 'retrieve',
    'put': 'update',
    'patch': 'partial_update',
    'delete': 'destroy'
})

urlpatterns = format_suffix_patterns([
    path('', api_root),
    path('snippets/', snippet_list, name='snippet-list'),
    path('snippets/<int:pk>/', snippet_detail, name='snippet-detail')
])
```

But since we use a ViewSet (the ModelViewSet) instead of Views, we can use a django rest framework `Router` class to handle 
the conventions for wiring up resources into views and urls and create the previously defined urls automatically.
```python
from django.urls import path, include
from rest_framework.routers import DefaultRouter
from snippets import views

# Create a router and register our viewsets with it.
router = DefaultRouter()
router.register(r'snippets', views.SnippetViewSet)

# The API URLs are now determined automatically by the router.
urlpatterns = [
    path('', include(router.urls)),
]
```

This way we have created an implementation that can handle the following requests:
- GET request to .../snippets/    -->    Get all snippets
- POST request to .../snippets/   -->   Create a new one
- GET request to .../snippet/{snippet-pk}/   -->   Get this snippet
- PUT request to .../snippet/{snippet-pk}/   -->   Update this snippet
- PATCH request to .../snippet/{snippet-pk}/   --->   Partially update this snippet
- DELETE request to .../snippet/{snippet-pk}/   -->   Delete this snippet