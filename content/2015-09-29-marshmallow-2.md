title: Marshmallow 2.0 Released
tags: python, marshmallow
description: pip install -U marshmallow
category: programming

One alpha, five betas, and two release candidates after marshmallow's last 1.x release, marshmallow 2.0 is published to the PyPI.

## Benefits

Here are a few immediate benefits of upgrading to 2.0:

* Dump-only and load-only fields (analagous to "read-only" and "write-only" in the context of a CRUD app).
* More consistent treatment of [null](https://marshmallow.readthedocs.org/en/latest/upgrading.html#deserializing-none) and [missing values](https://marshmallow.readthedocs.org/en/latest/upgrading.html#default-values).

* Powerful, user-friendly pre- and post-processing API.

```python
from marshmallow import Schema, fields, pre_load, post_dump, post_load

class UserSchema(Schema):
    name = fields.Str()
    email = fields.Email()

    @staticmethod
    def get_envelope_key(many):
        """Helper to get the envelope key."""
        return 'users' if many else 'user'

    @pre_load(pass_many=True)
    def unwrap_envelope(self, data, many):
        key = self.get_envelope_key(many)
        return data[key]

    @post_dump(pass_many=True)
    def wrap_with_envelope(self, data, many):
        key = self.get_envelope_key(many)
        return {key: data}

    @post_load
    def make_user(self, data):
        return User(**data)
```

* More consistent default error messages and a better API for overriding error messages.

```python
errors = schema.validate(invalid_data)
# {
#     'str_field': ['Not a valid string.'],
#     'bool_field': ['Not a valid boolean.'],
#     'int_field': ['Not a valid integer.'],
#     'decimal_field': ['Not a valid number.'],
#     'float_field': ['Not a valid number.']
#     'datetim_field': ['Not a valid datetime.'],
#     'email_field': ['Not a valid email address.'],
#     'url_field': ['Not a valid URL.'],
#     'list_field': ['Not a valid list.'],
# }
```

* Better error bundling when validating multiple objects.
* Improved ``strict`` mode.
* Field and schema validators can be written as methods.

## Ecosystem

A number of supporting libraries emerged since the last major release.

* [marshmallow-jsonapi](https://github.com/marshmallow-code/marshmallow-jsonapi) provides [JSON API 1.0](http://jsonapi.org) formatting.
* [marshmallow-sqlalchemy](https://github.com/marshmallow-code/marshmallow-sqlalchemy) automatically generates marshmallow Schemas from [SQLAlchemy](http://www.sqlalchemy.org/) models and tables.
* [django-rest-marshmallow](https://github.com/tomchristie/django-rest-marshmallow) allows you to use marshmallow with the [Django REST Framework](http://www.django-rest-framework.org/).
* [webargs](https://github.com/sloria/webargs), a cross-framework request parsing library, now uses marshmallow for validation, much for the better.
* [Flask-RESTful](https://github.com/flask-restful/flask-restful) is planning to remove its own request parsing and marshalling modules in favor of marshmallow. See the relevant thread [here](https://github.com/flask-restful/flask-restful/issues/335).
* [Flask-Smore](https://github.com/jmcarp/flask-smore) uses webargs and marshmallow for request input and response output and automatically generates Swagger documentation for Flask-powered APIs.
* [hug](https://github.com/timothycrosley/hug), a new Python HTTP API framework, has built-in support for marshmallow.

## What will happen to marshmallow 1?

I will continue to merge bug fixes into the 1.2 release line. No new 1.x feature releases are planned. New libraries in the [marshmallow-code](https://github.com/marshmallow-code) GitHub organization will likely support marshmallow>=2.0 only.


## More

 Several refinements were made to the marshmallow API. For details, see the [changelog](https://marshmallow.readthedocs.org/en/latest/changelog.html#changelog). For a guide on upgrading from marshmallow 1, see the [upgrading guide](https://marshmallow.readthedocs.org/en/latest/upgrading.html#upgrading).

 Special thanks to everyone who contributed and reported issues; you made this release awesome.
