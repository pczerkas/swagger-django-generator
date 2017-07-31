[![Build Status](https://travis-ci.org/praekelt/swagger-django-generator.svg?branch=master)](https://travis-ci.org/praekelt/swagger-django-generator)

# swagger-django-generator
Convert Swagger specifications into Django code

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**  *generated with [DocToc](https://github.com/thlorenz/doctoc)*

- [Introduction](#introduction)
- [Getting started](#getting-started)
- [Examples](#examples)
  - [A demo application](#a-demo-application)
  - [Generated files](#generated-files)
- [Notes](#notes)
- [Todo](#todo)
- [Why?](#why)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## Introduction
This utility parses a Swagger specification and generates Django-specific
definitions, which allows for easy integration into Django projects.
In particular, the following files are currently generated:
* `urls.py`, for routing requests,
* `views.py`, for handling requests, performing validation, etc., and
* `schemas.py`, containing the JSONSchema definitions of parameters and
  request body arguments.
* `utils.py`, containing utility functions that can be tweaked, if necessary.

## Getting started
A virtual environment can be set up by running
```
make virtualenv
./ve/bin/python manage.py develop  # Installs package so that templates can be found
```
This utility is self-documenting. To get an idea of what is supported, simply
run the utility with the `--help` flag:
```
└─▪ ./ve/bin/python swagger_django_generator/generator.py --help
Usage: generator.py [OPTIONS] SPECIFICATION

Options:
  --verbose / --no-verbose
  --output-dir DIRECTORY
  --module-name TEXT         The name of the module where the generated code
                             will be used, e.g. myproject.some_application
  --urls-file TEXT           Use an alternative filename for the urls.
  --views-file TEXT          Use an alternative filename for the views.
  --schemas-file TEXT        Use an alternative filename for the schemas.
  --utils-file TEXT          Use an alternative filename for the utilities.
  --stubs / --no-stubs       Generate a stub file as well.
  --stubs-file TEXT          Use an alternative filename for the utilities.
  --help                     Show this message and exit.
```

At the time of writing the ulity expects you to implement your logic in a `stubs.py` file.
The name of this file will be made configurable in future releases.
Both `yaml` and `json` specifications are supported.

## Examples

### A demo application
For a quick demonstration of how to get started, run `make demo`. This will create a demo Django application that uses files generated by this application. The command does the following in the back:
```bash
# Create a new Django project
./ve/bin/django-admin startproject demo
# Generate files for the petstore API (including stubs) straight into the project
./ve/bin/python swagger_django_generator/generator.py tests/resources/petstore.json --output-dir demo/demo/ --module-name demo --stubs
```
You can then start the server:
```
./ve/bin/python demo/manage.py runserver
```

### Generated files

The following links references files that were generated using this utility based on the popular `PetStore` Swagger definition.

* the [schemas](generated/schemas.py) file contains global schema definitions
* the [stubs](generated/stubs.py) file is where the abstract base class for your code will live, along with a mocked implementation
* the [urls](generated/urls.py) file is where the routing takes place
* the [utils](generated/utils.py) file contains utility functions
* the [views](generated/views.py) handles security and validation

## Notes
* All generated API calls are CSRF exempt
* API calls that have any form of security defined on them will require a logged in user or basic HTTP auth.
* At this stage there are **limited tests**.

## Todo
* We can look at using the `swagger-tester` library.
* Investigate using [warlock](https://github.com/bcwaldon/warlock) or [python-jsonschema-objects](https://github.com/cwacek/python-jsonschema-objects) to generate models for ease of use.
* Look at using some of the Python libs [here](https://swagger.io/open-source-integrations/)

## Why?
Using the [Swagger/OpenAPI](https://swagger.io/) specification enables the use of a tremendous amount of tooling:

* the generation of documentation
* creating a UI to play around with the API
* [importing into Runscope](https://blog.runscope.com/posts/new-import-feature-support-for-swagger-postman) for automated test creation
* client code generation for various languages
* server code generation for various application servers

When we have to write applications that provide APIs, it will typically have to form part of a Django application. Unfortunately the "official" code generation tool only supports code generation for Tornado and Flask when it comes to the Python language.

This tool is intended to plug the gap for Django. We can focus on getting the spec right, quickly generate server code and integrate with testing frameworks like Runscope. The generated code takes care of the routing, input and output validation and common error handling.

At a minimum it allows us to get some working code up and running in very little time.
