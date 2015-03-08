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

Disable .rhosts Files
^^^^^^^^^^^^^^^^^^^^^

SSH can be configured to emulate the behavior of the obsolete rsh command honoring .rhosts files.
This is historically unsafe and it is suggested to disable it, edit *sshd_config* file and disable *IgnoreRhosts*::

    IgnoreRhosts yes

Disable Empty Passwords
^^^^^^^^^^^^^^^^^^^^^^^

You need to explicitly disallow remote login from accounts with empty passwords, update *sshd_config* with the following
line::

    PermitEmptyPasswords no

Disable Host-Based Authentication
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

It is suggested to disable host-based authentication, as .rhost based authenticaiton, it is not rock solid authentication.
To disable host-based authentication,  edit *sshd_config* file and disable *HostbasedAuthentication*::

    HostbasedAuthentication no

Disable Password Authentication
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

By default SSH can use keys or password to provide authentication, passwords are prone to brute force attacks.
It is suggested to use keys only and completely disable password-based logins.
To stop password based authentication, edit *sshd_config* file and disable *PasswordAuthentication*::

    PasswordAuthentication no

Disable Protocol 1
^^^^^^^^^^^^^^^^^^

The legacy SSH protocol 1 is not secure: it suffers of man-in-the-middle attacks and it has a myriad of vulnerabilities;
it should be disabled although in most cases it already is
by default.
It is suggested to edit *sshd_config* file and add the following line to use only SSH protocol version 2::

    Protocol 2

Disable Root Logins
^^^^^^^^^^^^^^^^^^^

It is suggested to not enable root login via SSH, this account has high privileges and it is usually target of attacks.
A good practice is to login with a normal user, the root account is still available by using *su* and *sudo* tools.
To disallow logins with user root, edit */sshd_config* file and make sure you have the following entry::

    PermitRootLogin no

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
To enable SSH Agent Forwarding from command line you have to use ssh -A from command line or edithe the AgentForward option in
your SSH configuration file.
It is suggested to not use SSH Agent Forwarding because it comes at cost of a security issue: a port-forwarding will be set up to
connect you to the second host, so anyone with sufficient permission on the first host could be able to use that socket to connect
to and use your local ssh-agent.
It is recommended to never use SSH Agent Forwarding, if it is really needed by your use case it is suggested to use the option
ProxyCommand instead.

Increase Key Strength
^^^^^^^^^^^^^^^^^^^^^

It is suggested to use a length more than the default one.
The following command instructs ssh-keygen with *-b* argument to generate a 4096-bit key::

    $ ssh-keygen -b 4096 -t rsa -f ~/.ssh/id_rsa

Feel free to increase this to your desired key length although remember to use powers of two.

Limiting brute forcing attempts
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

SSH is a service target of worms, script kiddies, and all kind of brute forcing around.
It's a good idea to limit the maximum amount of login tries for second. This can be achieved with a few iptables
lines or with `DenyHosts <http://denyhosts.sourceforge.net/>`_.

Restrict IP Listen Address
^^^^^^^^^^^^^^^^^^^^^^^^^^

If you are in a multi homed setup (with multiple network interfaces) it is suggested to avoid having SSH listening on
all interfaces, unless it is really needed. For example only a specific IP should be used for SSH.
To specify on which IP to listen, edit */sshd_config* file use *ListenAddress* option, for example to listen only on the
interface with IP 192.168.0.1::

    ListenAddress 192.168.0.1

Reduce Grace Time
^^^^^^^^^^^^^^^^^

It is suggested to lower the default grace time for authenticating a user, it is only necessary if you are on a very
slow connection otherwise it will hold unauthenticated connections open for some time.
To reduce the gracetime to 30 seconds, edit */sshd_config* file use *LoginGraceTime* option::

    LoginGraceTime 30

Use PAM
^^^^^^^

By default, OpenSSH uses PAM for the authentication of users.
PAM (Pluggable Authentication Modules) is a powerful framework for managing authentication of users.
Using PAM you can enforce rules during the authentication (i.e. limiting access based on login count).
It is suggested to use PAM for SSH authentication too, edit */sshd_config* file and enable *UsePAM*::

    UsePAM yes

Use privilege separation
^^^^^^^^^^^^^^^^^^^^^^^^

It is a good practice to never run processes as root, if yoi enable SSH privilege separation, the SSHd process has a
tiny footprint running as root and it drops privileges as soon as possible to run as unprivileged process.
It is suggested to enable privilege separation (usually it is enabled by default), edit */sshd_config* file and
enable *UsePrivilegeSeparation*::

    UsePrivilegeSeparation yes

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
