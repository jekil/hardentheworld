MongoDB                                                                                                                                
-------                                                                                                                                   

According to `MongoDB official website <https://gpgtools.org/>`_ MongoDB is *"a document database that provides high
performance, high availability, and easy scalability"*.

This chapter is dedicated to configuring MongoDB version 2.x.

.. contents::
   :local:

Authentication
^^^^^^^^^^^^^^

Authentication is the process of verifying the identity of a client or a user. MongoDB supports different authentication
mechanisms, it is suggested to always use authentication for all users and clients (with different credentials for each
one).
Even if you have deployed MongoDB servers in a trusted network it is good security practice to enable authentication.
Please refer to MongoDB documentation to understand how create and use users over different authentication mechanisms.

Authorization
^^^^^^^^^^^^^

Authorization is a set of roles to give users permissions that pair resources with allowed operations.
It is suggested to use authorization to fine tune users profiles and let each user access the data or run the
operations it needs.
MongoDB does not enable authorization by default, you can enable authorization using the *--auth* option. Example::

    $ mongod --auth 

Or set it in the configuartion file::

    auth = true

Please refer to MongoDB documentation to understand how to work with authorization mechanisms.

Disable Localhost Exception
^^^^^^^^^^^^^^^^^^^^^^^^^^^

The localhost exception allows you to enable authorization before creating the first user in the system. When active,
the localhost exception allows all connections from the localhost interface to have full access to that instance. The
exception applies only when there are no users created in the MongoDB instance.
To prevent unauthorized access to a cluster’s shards, you must either create an administrator on each shard
or disable the localhost exception. To disable the localhost exception, add setParameter to set the
*enableLocalhostAuthBypass* parameter to 0 during startup. Example::

    $ mongod --setParameter enableLocalhostAuthBypass=0

Or set it in the configuration file::

    setParameter = enableLocalhostAuthBypass=0

Disable server side scripting
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

In some server-side operations (i.e. mapReduce, group, eval, $where), MongoDB supports the execution of JavaScript
code. To mitigate the exploiting of a possible application level vulnerability,  if you do not use these operations,
it is suggested to disable server-side scripting.
To disable server-side scripting add *noscripting* parameter during startup. Example::

    $ mongod --noscripting

Or set it in the configuartion file::

    noscripting = false

Disable status interface
^^^^^^^^^^^^^^^^^^^^^^^^

The status interface is an HTTP server exposing a web page that contains some statistics that may of interest
to system administrators.
It is suggested to disable the status interface to not expose an unused service.
To disable the status interface add *nohttpinterface* argument during startup. Example::

    $ mongod --nohttpinterface

Or set it in the configuartion file::

    nohttpinterface = true

Since version 2.6 MongoDB disables the HTTP interface by default.

Disable the REST interface
^^^^^^^^^^^^^^^^^^^^^^^^^^

The REST interface is s a fully interactive administrative REST interface,
which is disabled by default.
This interface does not support any authentication and you should always restrict access to this interface to only
allow trusted clients to connect to this port.
It is suggested to leave this interface disabled, removing the following arguments by the command line,
if present::

    $ mongod --rest --httpinterface

Or disable it in the configuartion file::

    rest = false

If you have to leave this interface enabled, you should only allow trusted clients
to access this service (using proper firewall rules).

Encryption
^^^^^^^^^^

MongoDB clients can use SSL to encrypt connections to mongo instances.
It is suggested to always use SSL encryption when accessing MongoDB over a network.

Please refer to MongoDB documentation to understand how to setup SSL encryption.

Limit Network Exposure
^^^^^^^^^^^^^^^^^^^^^^

Restriction access to the database service is a critical aspect of service security. It is suggested to do not expose
your database to resources that are not in need to access it.
You can use the *--bind_ip* option on the command line at run time or the *bindIp* in the configuration file to limit the network
accessibility of a MongoDB program. Example::

    $ mongod --bind_ip 127.0.0.1

Or set it in the configuration file::

    bind_ip = 127.0.0.1

If you need fine tuned network access limitation not limited to binding on an interface is suggested to use a firewall
to place custom network traffic ACLs.

Run MongoDB with a dedicated user
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Privilege separation should always be used, it is suggested to run MongoDB processes with a dedicated user account (an
operative system account with the minimum privileges needed to run the service).
Most installers already creates a dedicated user when installing MongoDB.

References
^^^^^^^^^^

* https://docs.mongodb.com/manual/administration/security-checklist/