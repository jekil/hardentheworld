OpenVPN
-------

According to `OpenVPN official website <https://openvpn.net>`_ OpenVPN is *"an open-source software that implements virtual private network (VPN) techniques for creating secure point-to-point connections"*.

.. contents::
   :local:

Additional authentication
^^^^^^^^^^^^^^^^^^^^^^^^^

If possibile, it is suggested to request an additional authentication in addition to a client certificate.
This could protect you in case of certificate loss.
Additional authentication could be configured server side in two ways:

 * Using the *auth-user-pass-verify

Attacks on default gateway
^^^^^^^^^^^^^^^^^^^^^^^^^^

OpenVPN is commonly used to route all traffic or only some subnets through the VPN tunnel. This is
implemented adding wide scope routing rules.
A rogue DHCP server able to push more specific routes could be able to take precedence on the routing
table and route your traffic outside the VPN.
To prevent this kind of attacks it is suggested to configure your DHCP client to ignore classless static
routes.
A rogue DHCP couls also push a subnet mask for an extremely large subnet, so all the traffic could be
routed on the local network and not in the VPN.
This issue has not an easy solution, it depends by your OS, for example in Linux you can use advanced routing
and multiple routing table (see https://www.agwa.name/blog/post/hardening_openvpn_for_def_con).

Custom Port
^^^^^^^^^^^

It is suggested to move OpenVPN from the default port to a custom one.
For example we are setting it on port 10000, edit the server configuration file as follows::

    port 10000

Disable IPv6
^^^^^^^^^^^^

You know, IPv6 could be a security beast. Unless you are using IPv6 in your OpenVPN tunnerl, then all IPv6 traffic from your client will bypass the VPN and egress over the local network.
It is suggested to disable IPv6 support in your OS if you are not using it.

Disable management interface
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The OpenVPN Management interface allows OpenVPN to be remotely administered.
It is suggested to disable or restrict to localhost (or local trusted clients) the management interface.
Edit the server configuration file and comment the *management* option or make sure it is only accessible
via localhost::

    # management 127.0.0.1 8000

DNS management
^^^^^^^^^^^^^^

When you are using a VPN tunnel, you should use only a trusted DNS server.
If an attacker is able to push a rogue DNS server it is a game over for you because he could redirect all
your traffic outside the VPN.
It should take care of your configured DNS servers, unfortunately how DHCP clients manage pushed DNS servers
depends by operating systems. Some systems do it incredibly poorly and it is possible to change your DNS
server, by pushing it via DHCP, after the VPN tunnel startup.
It is suggested to pin your DNS servers to be suere you are always using the right one.

Don not allow certificate re-use
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Certificates should not be shared and each VPN client must have its unique certificate.
Is is suggested to enforce it disabling the *duplicate-cn* in the server configuration file, if present,
commenting or deleting it, as follows::

    # duplicate-cn

Key Size
^^^^^^^^

It is suggested to use a key size of, at least 2048 bits (better 4096 bits), for your certificates.
If you are creating certificates with OpenSSL you have to modify the *default_bits* parameter.

Limit concurrent clients
^^^^^^^^^^^^^^^^^^^^^^^^

It is suggested to restrict the maximum number of concurrent clients to a reasonable number.
Set *max-clients* in the server configuration file, as follows (limited at 100 clients in the example)::

    max-clients 100

Run as unprivileged user
^^^^^^^^^^^^^^^^^^^^^^^^

It is suggested to run OpenVPN process as unprivileged user.
Add the following lines to the configuration file::

    user nobody
    group nobody

Secure Ciphers
^^^^^^^^^^^^^^

It is suggested to use strong symmetric ciphers (at least 256bit).
For example, add to both server and client configuration file the following to use AES-256:

    cipher AES-256-CBC

Secure PKI Management
^^^^^^^^^^^^^^^^^^^^^

OpenVPN authentication, in most cases, is based on PKI and X.509 certificates. Practicing secure PKI management
is mandatory to safeguard, also, OpenVPN.
It is suggested to follow best practices for secure PKI management, for example::

 * Secure management of CA PKI.
 * Generate private keys on the target system and never transport them.
 * Never share private keys.
 * Use certificate passwords if possibile and use a secure password policy.
 * Use a CRL and revoke lost/compromised keys.

Use PSK
^^^^^^^

The *--tls-auth* option uses a static pre-shared key (PSK) shared among all connected peers. 
This is an extra layer of protection to the TLS channel by requiring that incoming connections are correctly HMAC
signed by the PSK key.
This feature could protect your VPN server by DoS attacks aimed to load your CPU load, by port scanning avoiding
service fingerprinting, and act as second line of defense for SSL library vulnerabilities.
Generate a PSK with the command::

    openvpn --genkey --secret ta.key

Add the following line to your server configuration::

    tls-auth ta.key

Add the following line to your server configuration::

    tls-auth ta.key

Beware, the *--tls-auth* key is changed, it must be changed on all peers at the same time, so it could
potentially lead to a network management horror story. It is suggested to use it with care.

Verify CRL
^^^^^^^^^^

It is suggested to erify revoked client certificates, they should not connect or keep a connection alive.
Add *crl-verify* to the server configuration file, as follows::

    crl-verify path/yourcrl.pem

Verify the server certificate
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

It is recommended to check that the server certificate contains a specific key usage and and extended key usage.
Add to the client configuration file the following line::

    remote-cert-tls server

References
^^^^^^^^^^

* https://community.openvpn.net/openvpn/wiki/Hardening
* http://darizotas.blogspot.it/2014/04/openvpn-hardening-cheat-sheet.html
* https://www.agwa.name/blog/post/hardening_openvpn_for_def_con
