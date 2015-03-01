OpenSSH
-------

According to `OpenSSH official website <http://www.openssh.com/>`_ OpenSSH is used *"OpenSSH is a free version of the
SSH connectivity tools that technical users of the Internet rely on. Users of telnet, rlogin, and ftp may not realize
that their password is transmitted across the Internet unencrypted, but it is. OpenSSH encrypts all traffic
(including passwords) to effectively eliminate eavesdropping, connection hijacking, and other attacks.
Additionally, OpenSSH provides secure tunneling capabilities and several authentication methods, and supports all
SSH protocol versions"*.

.. contents::
   :local:

Configure Idle Log Out Timeout Interval
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Users can login to server via ssh, it is suggested to set an idle timeout interval to avoid unattended ssh session.
Open sshd_config and make sure following values are configured::

    ClientAliveInterval 300
    ClientAliveCountMax 0

Enable a Warning Banner
^^^^^^^^^^^^^^^^^^^^^^^

Set a warning banner by updating sshd_config with the following line::

    Banner /etc/issue

Disable Empty Passwords
^^^^^^^^^^^^^^^^^^^^^^^

You need to explicitly disallow remote login from accounts with empty passwords, update sshd_config with the following
line::

    PermitEmptyPasswords no

Disable SSH forwarding
^^^^^^^^^^^^^^^^^^^^^^

Port forwarding via SSH (SSH tunneling) creates a secure connection between a local computer and a remote
machine through which services can be relayed.
It is suggested to disable this feature, update sshd_config with the following line::

    AllowTcpForwarding no

Sometimes you would enable SSH forwarding just for some users, for example the following lines enable it for
foobar::

    AllowTcpForwarding no
    Match User foobar
    AllowTcpForwarding yes

Limiting brute forcing attempts
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

SSH is a service target of worms, script kiddies, and all kind of brute forcing around.
It's a good idea to limit the maximum amount of login tries for second. This can be achieved with a few iptables
lines or with `DenyHosts <http://denyhosts.sourceforge.net/>`_.

Whitelisting / blacklisting users
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

By default all systems user can login via SSH using their password or public key.
Sometime you create UNIX / Linux user account for ftp or email purpose. However, those user can login
to system using SSH.
To only allow antani and tapioco user to use the system via SSH, add the following to sshd_config::

    AllowUsers antani tapioco

Alternatively, you can allow all users to login via SSH but deny only a few users, with the following line::

    DenyUsers foo bar

You can also configure Linux PAM allows or deny login via the sshd server.
