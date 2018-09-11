NethServer SOGo
===============

`SOGo <http://www.sogo.nu/english/about/overview.html>`__ configuration
for NethServer.

    SOGo offers multiple ways to access the calendaring and messaging
    data. Your users can either use a web browser, Microsoft Outlook,
    Mozilla Thunderbird, Apple iCal, or a mobile device to access the
    same information.

.. contents:: :local:

Features
--------

-  ``mysql``, ``slapd``, ``sogod``, ``memcached`` configuration
-  apache2 configuration to access SOGo web interface at
   ``https://<hostname>/SOGo/``
-  daily cronjob to check *auto-reply* expiration 
-  daily cronjob to backup user-data
-  extension for Thunderbird intergration


Database Reference
------------------

Special properties:

* AdminUsers: Parameter used to set which usernames require
  administrative privileges over all the users tables.

* DraftsFolder: name of draft folder, default is ‘Drafts’

* SentFolder: name of the sent folder, default is ‘Sent’

* TrashFolder: name of the trash folder, default is ‘Trash’

* WOWorkersCount: The amount of instances of SOGo that will be spawned
  to handle multiple requests simultaneously

* MailAuxiliaryUserAccountsEnabled: Parameter used to activate the
  auxiliary IMAP accounts in SOGo. When set to YES, users can add
  other IMAP accounts that will be visible from the SOGo Webmail
  interface.
  
* Notifications: enabled notifications. The value is a comma separated
  list. Default value is “Appointment, EMail”

Configuration DB
~~~~~~~~~~~~~~~~

::

    sogod=service
        ...
        AdminUsers=admin
        DraftsFolder=Drafts
        Notifications=Appointment,ACLs
        SentFolder=Sent
        TrashFolder=Trash
        VirtualHost=

Inspect SOGo configuration
--------------------------

To dump the complete current configuration type:

::

      # sogo-tool dump-defaults all

Increase ``sogod`` log verbosity
--------------------------------

For instance, to see LDAP queries premanently add the following custom fragment:

::

    mkdir -p /etc/e-smith/templates-custom/etc/sogo/sogo.conf
    printf "\n\n  LDAPDebugEnabled = YES;"  > /etc/e-smith/templates-custom/etc/sogo/sogo.conf/80verbosedebug
    signal-event nethserver-sogo-update

Download and publish new rpms
-----------------------------

* Configure SOGo repo `/etc/yum.repos.d/sogo.repo`: ::

  [sogo3-rhel7]
  name=Inverse SOGo Repository
  baseurl=http://inverse.ca/rhel-v3/7/$basearch
  gpgcheck=0

* Download latest release: ::

  yum --enablerepo=nethserver-testing --downloadonly --downloaddir=sogo install sogo --disablerepo=epel,nethserver*

* Remove all packages downloaded from non-sogo repos. This should be enough: ::

  rm -f libevent* libmemcached* libobjc* memcached* zip*

* Upload the rpms to NethForge: ::

  upload-rpms packages.nethserver.org:nscom/7.2.1511/nethforge *rpm
