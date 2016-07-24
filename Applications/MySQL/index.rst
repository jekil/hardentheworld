MySQL Server
------------

According to `MySQL official website <https://www.mysql.com/>`_ MySQL is *"open-source relational database management system (RDBMS)"*.

.. contents::
   :local:

Connection Encryption
^^^^^^^^^^^^^^^^^^^^^

By default MySQL connections are not encrypted and everything flows over network in open text.
If you are using MySQL over a network it is suggested to use encryption, refer to MySQL documentation to understand how
to configure an encryption mechanism.

Connection Error Limit
^^^^^^^^^^^^^^^^^^^^^^

It is suggested to apply host ban to clients with many unsuccessful authentications.
As stated in `MySQL documentation <http://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_max_connect_errors>`_:

*If there are more than this number of interrupted connections from a host, that host is blocked from further connections. You can unblock blocked hosts with the FLUSH HOSTS statement.
If a connection is established successfully within fewer than max_connect_errors attempts after a previous connection was interrupted, the error count for the host is cleared to zero. However, once a host is blocked, the FLUSH HOSTS statement is the only way to unblock it.*

Edit the configuration file *my.cnf* and set *max_connect_errors*::

    max_connect_errors = 3

Disable LOAD DATA LOCAL INFILE
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The LOAD DATA LOCAL INFILE command allows users, or an attacker, to read local files and even access other files on the operating system.
It is also a common command used by attackers exploiting by methods such as SQL injection. 
It is suggested to disable the command, edit the configuration file *my.cnf* and set *local-infile*::

    local-infile=0

Disable SHOW DATABASES
^^^^^^^^^^^^^^^^^^^^^^

SHOW DATABASES is a command used by users, or attackers, to list all databases available.
Stripping remote attackers of their information gathering capabilities is critical to a secure security posture.
It is suggested to disable the command, edit the configuration file *my.cnf* and add *skip-show-database* to the [mysqld] section ::

    [mysqld]
    skip-show-database

Hardening Script
^^^^^^^^^^^^^^^^

MySQL comes with an hardening script to check database server security and remove some default settings.
You can run it with the command::

    mysql_secure_installation

It will ask you for your desired hardening level through some questions.

Interface Binding
^^^^^^^^^^^^^^^^^

If you don't need to access your database from another machine it is suggested to bind MySQL service
on localhost only, edit the configuration file *my.cnf* and set *bind-address*::

    bind-address = 127.0.0.1

You can also disable networking if not used with *skip-networking* option.

Privilege Hardening
^^^^^^^^^^^^^^^^^^^
You should carefully manager users and privileges, it is suggested to follow at least these best practices:

 * Each application that uses MySQL should have its own user that only has limited privileges and only has access to the databases it needs to run.
 * Never use ALL TO *.*.
 * Never use % for a hostname
 * Application user permissions should be restrictive as possible
 * Only allow super privileges to dba accounts, and localhost
 * Never ever give users global privileges, except for root, backup user, monitoring user, replication user
 * Take extra caution when granting SUPER or FILE privileges: SUPER can modify runtime configuration and become other users, FILE allows reading or writing files as MySQL process

Rename root User
^^^^^^^^^^^^^^^^

It is suggested to change the root login name. If an attacker is trying to access the root MySQL login, they will need to perform the additional step of finding the username.

The root login can be changed with the following SQL commands::

    RENAME USER 'root'@'localhost' TO 'foobar'@'localhost';
    FLUSH PRIVILEGES;


References
^^^^^^^^^^

* https://www.owasp.org/index.php/OWASP_Backend_Security_Project_MySQL_Hardening
