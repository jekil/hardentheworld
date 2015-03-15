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

Enable strict mode
^^^^^^^^^^^^^^^^^^

Using strict mode you can enforce some checks on important files inside users' home directory have the proper privileges
and ownership, SSH daemon will only allow a remote user to log on if checks pass.
It is suggested to enable strict mode editing *sshd_config* file and enabling *StrictModes*::

    StrictModes yes

Enable a Warning Banner
^^^^^^^^^^^^^^^^^^^^^^^

Set a warning banner by updating *sshd_config* with the following line::

    Banner /etc/issue

Disable .rhosts Files
^^^^^^^^^^^^^^^^^^^^^

SSH can be configured to emulate the behavior of the obsolete rsh command honoring *.rhosts* files.
This is historically unsafe and it is suggested to disable it, edit *sshd_config* file and disable *IgnoreRhosts*::

    IgnoreRhosts yes

Disable Empty Passwords
^^^^^^^^^^^^^^^^^^^^^^^

You need to explicitly disallow remote login from accounts with empty passwords, update *sshd_config* with the following
line::

    PermitEmptyPasswords no

Disable gateway for forwarded ports
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

SSH binds local port forwardings to the loopback address only, as default.
This is a security feature to prevent other remote hosts from connecting to forwarded ports.
The *GatewayPorts* option can be used to specify if this is the expected behaviour.
It is suggested to disable *GatewayPorts*, it is already disabled by default in most distributions, edit *sshd_config*
file and disable *IgnoreRhosts*::

    GatewayPorts no

Disable Host-Based Authentication
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

It is suggested to disable host-based authentication, as *.rhost* based authenticaiton, it is not rock solid authentication.
To disable host-based authentication, edit *sshd_config* file and disable *HostbasedAuthentication*::

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

Disable TCP forwarding
^^^^^^^^^^^^^^^^^^^^^^

SSH supports "traffic tunneling", it is used to forward TCP traffic over SSH channel.
If you are not using this feature it is suggested to disable it.
To disable TCP forwarding, edit *sshd_config* file and disable *AllowTcpForwarding*::

    AllowTcpForwarding no

Disable user environment
^^^^^^^^^^^^^^^^^^^^^^^^

Users logging via SSH are usually able to set environment options and potentially bypass some access restrictions.
It is suggested, if this feature is not needed, to remove this permission, edit *sshd_config* file and disable
*PermitUserEnvironment*::

    PermitUserEnvironment no

Disable X11 forwarding
^^^^^^^^^^^^^^^^^^^^^^

SSH supports X display forwarding, so X11 applications started on the remote system via SSH have their display shown on
the client.
If this feature is not used it is suggested to disable it, although it is disabled by default in most distributions.
To disable X11 forwarding, edit *sshd_config* file and disable *X11Forwarding*::

    X11Forwarding no

Display a warning message before login
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

A pre login SSH banner shows before the password prompt, during an interactive session.
It is usually used for legal warnings or to show the terms by which someone is allowed to use the system.
This message is commonly located in */etc/issue* but you can also use your custom file, for example */etc/ssh/banner*.
It is suggested to use a warning banner, edit *sshd_config* file and set *Banner* option::

    Banner /etc/ssh/banner

Do not use SSH Agent Forwarding
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

SSH Agent Forwarding is as an easy way to connect to a host with your SSH key and from there connect to another host with the same key.
For example this is used when you cannot connect directly to the second host from your workstation.
To enable SSH Agent Forwarding from command line you have to use ssh -A from command line or edit the AgentForward option in
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

Whitelisting / blacklisting groups
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

By default all systems user can login via SSH using their password or public key.
Sometime you create UNIX / Linux user account for ftp or email purpose. However, those user can login
to system using SSH.
To only allow users in a group (fo example in the foo group), add the following to *sshd_config*::

    AllowGroup foo

Alternatively, you can allow all users to login via SSH but deny only the users in the foo group, with the following line::

    DenyGroups foo

You can also configure Linux PAM allows or deny login via the sshd server.

References
^^^^^^^^^^

* https://heipei.github.io/2015/02/26/SSH-Agent-Forwarding-considered-harmful/
