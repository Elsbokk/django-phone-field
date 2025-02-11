NOTE: The only difference in this fork is that the widget no longer includes a field for an extension.

# django-phone-field
Lightweight model and form field for phone numbers in Django

* View and edit phone numbers in forms and the Django admin in pretty format: `(415) 123-4567`
* Store all phone numbers canonically in the database for easy searching & uniqueness: `+14151234567`
* Built-in support for simple extensions: `(415) 123-4567, press 88`
* Doesn't choke on international numbers (but doesn't format them, either)
* Automatically format phone numbers in templates

This package is simple, lightweight, and without dependencies. However, it doesn't attempt to solve fancy
problems or deal with international phone numbers. For a full-featured phone number package, take a look at
[`django-phonenumber-field`](https://github.com/stefanfoulis/django-phonenumber-field).

## Installation

This package is designed for Python 3 and Django 1.10+. Install via:

```
pip install django-phone-field
```

Then add `'phone_field'` to your `INSTALLED_APPS` setting.

## Usage

In your `models.py`:

```
from django.db import models
from phone_field import PhoneField


class MyModel(models.Model):
    name = models.CharField(max_length=128)
    phone = PhoneField(blank=True, help_text='Contact phone number')
```

`PhoneField` accepts standard options for a Django `CharField`. By default it sets `max_length=31`. Feel
free to override this, set `blank=True`, etc. as you would otherwise.

There is one special argument, `E164_only=False`, which adds a form validator to only accept numbers in
the E164 format (currently, only supported for US phone numbers).

In your template:

```
User {{ obj.name }} has phone number {{ obj.phone }}
```

Result:

```
User Ted has phone number (415) 123-1233
```

## Database representation

`PhoneField` attempts to coerce all phone numbers to the following format:

```
+[country code][number]x[extension]
+12223334444x55
```

where the extension part is optional. If the input phone number can't be coerced to this
format, `PhoneField` gives up and simply stores it as-is.

Because all phone numbers are stored without formatting, you can set this field to be unique
on a Django model and it will actually work.

## Extras

Use the `|phone` template filter to attempt to display a formatted phone number from arbitrary text. Use
the `|raw_phone` template filter to display the raw, un-formatted value.

Use property `.is_E164` to check if a PhoneNumber object is in E164 format.

Also provided are `.is_standard` (E164 but with extensions allowed) and `.is_usa`.
