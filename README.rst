=================
Django URL Filter
=================

Django URL Filter provides a safe way to filter data via human-friendly URLs.

Overview
--------

The main goal of Django URL Filter is to provide an easy URL interface
for filtering data. It allows the user to safely filter by model
attributes and also allows to specify the lookup type for each filter
(very much like Django's filtering system in ORM).

For example the following will retrieve all items where the id is
``5`` and title contains ``"foo"``::

    example.com/listview/?id=5&title__contains=foo

In addition to basic lookup types, Django URL Filter allows to
use more sophisticated lookups such as ``in`` or ``year``.
For example::

    example.com/listview/?id__in=1,2,3&created__year=2013

Installing
----------

Easiest way to install this library is by using ``pip``::

    $ pip install django-url-filter

Usage Example
-------------

To make example short, it demonstrates Django URL Filter integration
with Django REST Framework but it can be used without DRF (see below).

::

  class UserViewSet(ModelViewSet):
      queryset = Waiter.objects.all()
      serializer_class = UserSerializer
      filter_backends = [DjangoFilterBackend]
      filter_fields = ['username', 'email']

Alternatively filteset can be manually created and used directly
to filter querysets::

  class UserFilterSet(ModelFilterSet):
      class Meta(object):
          model = User

  query = QueryDict('email__contains=gmail&joined__gt=2015-01-01')
  fs = UserFilterSet(data=query, queryset=User.objects.all())
  filtered_users = fs.filter()

Features
--------

* **Human-friendly URLs**

  Filter querystring format looks
  very similar to syntax for filtering in Django ORM.
  Even negated filters are supported! Some examples::

    example.com/users/?email__contains=gmail&joined__gt=2015-01-01
    example.com/users/?email__contains!=gmail  # note !

* **Related models**

  Support related fields so that filtering can be applied to related
  models. For example::

    example.com/users/?profile__nickname=foo

* **Decoupled filtering**

  How URLs are parsed and how data is filtered is decoupled.
  This allows the actual filtering logic to be decoupled from Django
  hence filtering is possible not only with Django ORM QuerySet but
  any set of data can be filtered (e.g. SQLAlchemy query objects)
  assuming corresponding filtering backend is implemented.

* **Usage-agnostic**

  This library decouples filtering from any particular usage-pattern.
  It implements all the basic building blocks for creating
  filtersets but it does not assume how they will be used.
  To make the library easy to use, it ships with some integrations
  with common usage patterns like integration with Django REST Framework.
  This means that its easy to use in custom applications with custom
  requirements (which is probably most of the time!)