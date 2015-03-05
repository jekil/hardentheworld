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

Change port number
^^^^^^^^^^^^^^^^^^

SSH default port (22/tcp) is a service target of worms, script kiddies, and all kind of brute forcing around.
It is suggested to edit *sshd_config* file (usually located in */etc/ssh/sshd_config*) to run the SSH daemon on a non default
port, using the *Port* option::

    Port 34567

Configure Idle Log Out Timeout Interval
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Users can login to server via ssh, it is suggested to set an idle timeout interval to avoid unattended ssh session.
Open *sshd_config* (usually located in */etc/ssh/sshd_config*) and make sure following values are configured::

    ClientAliveInterval 300
    ClientAliveCountMax 0

Enable a Warning Banner
^^^^^^^^^^^^^^^^^^^^^^^

Set a warning banner by updating *sshd_config* with the following line::

    Banner /etc/issue

Disable Empty Passwords
^^^^^^^^^^^^^^^^^^^^^^^

You need to explicitly disallow remote login from accounts with empty passwords, update *sshd_config* with the following
line::

    PermitEmptyPasswords no

Disable SSH forwarding
^^^^^^^^^^^^^^^^^^^^^^

Port forwarding via SSH (SSH tunneling) creates a secure connection between a local computer and a remote
machine through which services can be relayed.
It is suggested to disable this feature, update *sshd_config* with the following line::

    AllowTcpForwarding no

Sometimes you would enable SSH forwarding just for some users, for example the following lines enable it for
foobar::

    AllowTcpForwarding no
    Match User foobar
    AllowTcpForwarding yes

Do not use SSH Agent Forwarding
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

SSH Agent Forwarding is as an easy way to connect to a host with your SSH key and from there connect to another host with the same key.
For example this is used when you cannot connect directly to the second host from your workstation.
To enable SSH Agent Forwarding from comand line you have to use ssh -A from command line or edithe the AgentForward option in
your SSH configuration file.
It is suggested to not use SSH Agent Forwarding because it comes at cost of a security issue: a port-forwarding will be set up to
connect you to the second host, so anyone with sufficient permission on the first host could be able to use that socket to connect
to and use your local ssh-agent.
It is recommended to never use SSH Agent Forwarding, if it is really needed by your use case it is suggested to use the option
ProxyCommand instead.

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
To only allow antani and tapioco user to use the system via SSH, add the following to *sshd_config*::

    AllowUsers antani tapioco

Alternatively, you can allow all users to login via SSH but deny only a few users, with the following line::

    DenyUsers foo bar

You can also configure Linux PAM allows or deny login via the sshd server.

References
^^^^^^^^^^

* https://heipei.github.io/2015/02/26/SSH-Agent-Forwarding-considered-harmful/
