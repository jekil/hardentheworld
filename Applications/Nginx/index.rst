Nginx
-----

According to `Nginx official website <http://nginx.org/>`_ Nginx is *"is an HTTP and reverse proxy server, a mail
proxy server, and a generic TCP proxy server, originally written by Igor Sysoev. For a long time, it has been running
on many heavily loaded Russian sites including Yandex, Mail.Ru, VK, and Rambler."*.

.. contents::
   :local:

Catch all deny virtualhost
^^^^^^^^^^^^^^^^^^^^^^^^^^

A catch all virtual host is the website server when your website is accessed by IP address and not by hostname.
It is usually used only by bots and attackers, so it is suggested to setup a virtual host listening on your IP website
and deny all requests.
Create a virtualhost with the following configuration::

    server {
       listen 80 default;
       server_name _;
       deny all;
    }

Enable HTTP Strict Transport Security
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

If it apply to your website, it is suggested to consider enabling HSTS (HTTP Strict Transport Security) mechanism,
which let browsers to communicate with your websites only over HTTPS protocol.
This mechanism is designed to reduce man in the middle attacks (MiTM).
In order to enable HSTS on Nginx, you should need to add this code to your virtual host or server block of your site::

    add_header Strict-Transport-Security max-age=15768000;

Example of a redirect virtual host with HSTS enabled::

    server {
        listen 80;
        add_header Strict-Transport-Security max-age=15768000;
        return 301 https://www.example.com$request_uri;
    }

Deny access to some resources
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Sometimes critical data are published during application deploy. It is suggested to deny access to sensitive resources,
for example the *.git* folder, adding a location statement to deny access to *.git* or another resource::

    server {
        location ~ /\.git {
            deny all;
        }
    }

Deny illegal Host headers
^^^^^^^^^^^^^^^^^^^^^^^^^

Malicious bots or vulnerability probing usually sends also requests with an
improper or empty Host header.
The default technique to block this kind of attempts is to use a "Catch all
virtualhost", but in some cases, for example if your website is SSL/TLS
encrypted, you can't use a default virtualhost.
It is suggested to block all requests with an illegal Host header with the
following configuration (example.com is your website in this example)::

    server {
        # Deny illegal Host headers.
        if ($host !~* ^(example.com|www.example.com)$ ) {
            return 444;
        }
    }

The returned HTTP error code 444 is used in Nginx logs to indicate that the
server has returned no response to the client and closed the connection (useful
to block malicious requests).

Disable Autoindex
^^^^^^^^^^^^^^^^^

It is suggested to disable the autoindex module, disabling the directive *autoindex* in your command location block::

    autoindex off;

Disable Server Signature
^^^^^^^^^^^^^^^^^^^^^^^^

By default Nginx sends banner with version number, it is suggested to disable server banner disabling *server_tokens* in
global configuration file::

    server_tokens off;

For more information see: http://wiki.nginx.org/HttpCoreModule#server_tokens
