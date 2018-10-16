LEIA Services
=============

Introduction
^^^^^^^^^^^^
The LEIA Services architecture is designed to enable rapid deployment and ease-of-use for a variety of systems and knowledge resources needed for various experimentation.

The architecture is built around the premise of “micro-services”, that is, each individual component is a self-contained service that may speak to other such services on the same machine as needed.  Each service can be enabled or disabled independently, and in fact, multiple versions of the same service can be run simultaneously if desired.

Service Stack
^^^^^^^^^^^^^
The architecture stack consists primarily of two parts:

1. Service Layer - this is any given running service; it will be running on a machine, likely the local one, on a particular port, and will have a series of endpoints that can be reached to request data or processing.  Services for static knowledge will also have both a manager and an editor (see below).  Finally, some services may have requirements (such as databases, etc.) that will be considered part of their deployment.
2. API Wrapper Layer - for any given service, there will also be a common library of python methods that “wrap” the service, obfuscating the network connections into simple function calls.  This should be considered the “SDK” - development that needs something from a service should use the appropriate method in the wrapper to accomplish the task, rather than sending a message directly to the service.

Running a Service and Using Docker
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Services themselves are built as python web applications.  They will have a main method which activates a web service (either using Flask or web.py).  Each service can be run on a given machine by simply running the main method - however, doing so does not guarantee that either the required resources (such as databases) are present, nor does it guarantee that the service won’t collide with another service (shared ports, for example).

To manage these problems, we use Docker.  Docker is a system that allows services to be bundled up into “images” (a self-contained snapshot of what the service has - code and resources), and each image can be “turned on” by running it in a container (an isolated virtual machine).

To support Docker, each service has a Docker compose file - this is essentially a script that tells Docker how to use the image, and how to set up an environment for that image to run safely in.  The compose file allows for required resources to be enabled, and environment variables to be mapped in the virtual machine (such as port numbers).

As such, while running the service directly from the code is generally fine, and does allow for rapid changes to the service codebase itself, if the intent of a service on a machine is to consume it (use a knowledge editor, ask for processing etc.) it is far preferred to run the service via Docker with the associated compose file.

Static Knowledge Services
^^^^^^^^^^^^^^^^^^^^^^^^^
Services that specifically encapsulate a static knowledge resource will necessarily have at least one required resource (a database).  The service will expose an API for searching the knowledge resource (and a wrapper library will be provided for accessing the API).

These services will also make use of a knowledge manager.  The manager is a light-weight user interface external to the knowledge editor.  The manager allows for the knowledge supplied by the service to be versioned and shared using a central hosted repository for that knowledge type.

Accessing the Wrappers and Docker Compose Files
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
For ease of access, all of the API Wrappers, and a wide collection of Docker Compose files are all found in the LEIAServices SVN repository.  Each compose file found has a description at the top as to what exactly it sets up.  There is one for each atomic service, and there are some that enable multiple services simultaneously.
