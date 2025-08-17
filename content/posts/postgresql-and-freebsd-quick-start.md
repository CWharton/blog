+++
date = '2016-10-28T00:00:00-05:00'
draft = false
title = 'Postgresql and Freebsd Quick Start'
+++

[![Postgres and FreeBSD](https://www.cwharton.com/blog/media_src/postgresql-and-freebsd-quick-start-header.png)](/blog/post/postgresql-and-freebsd-quick-start/)

PostgreSQL is a powerful, open-source object-relational database with over two decades of continuous development. It is one of my favorite databases because of its community, functionality, and extendability. FreeBSD with built-in ZFS support, jails, and several other features is a great platform to host your PostgreSQL instance.

Installing PostgreSQL
---------------------

At the time of this write up the latest version of PostgreSQL is 9.6 but you are not required to install the latest version. FreeBSD supports several versions of PostgreSQL. Simply search for all the latest versions and choose the one that your project supports.

    # pkg search postgresql

Install your desired version of PostgreSQL.

    # pkg install postgresql96-server

To have PostgreSQL startup when the server does you need to add \*postgresql\_enable="YES"\* to the \*/etc/rc.conf\* file:

    # sysrc postgresql_enable="YES"

Before you start using Postgres we need to initialize the cluster. By default, the cluster will be placed in a data folder in \*/var/db/postgres\* directory.

    # service postgresql initdb

If you wish to place your data in a different location, such as a ZFS Datasets or an NFS (Network File Share), specify the \*-D\* flag then the path.

Now you can start PostgreSQL

    # service postgresql start

Create users and databases
--------------------------

Log into the pgsql account created for you when you installed PostgreSQL.

    # su postgres

Create a PostgreSQL user account. \*Supply the -s flag if you want the user to be a superuser. The -P flag will prompt you for a password.\*.

    createuser -P user_test

Create a database supplying the owner of the database.

    createdb db_test -O user_test

Allowing Remote Connections
---------------------------

By default, you will not be able to log into the database server unless you are on the local server. If your application is hosted on the same server as the database, you can skip this section.

Configure PostgreSQL to listen for database connections on all system IP addresses by adding the following line to \*postgresql.conf\* file. This file is located in the directory where the database is initialized. For FreeBSD 11.0-RELEASE and PostgreSQL 9.6 this location will be \*/var/db/postgres/data96/postgresql.conf\* unless you specified another location.

    listen_addresses = '*'

Next, you need to edit \*pg\_hba.conf\* file in the same directory. Append the following configuration lines to give access to 10.1.1.0/32 network:

    host all all 10.1.1.0/32 trust

Restart the service for the changes to take effect.

    # /usr/local/etc/rc.d/postgresql restart

Be sure to install the Postgres client on the host computer. Now test your connection from your other host computer.

    psql -h  -U user_test db_test

Utilize ZFS
-----------

If you are capable you should be running your PostgreSQL instance on OpenZFS. If you are unfamiliar with ZFS please refer to the FreeBSD Handbook in the section [The Z File System (ZFS)](https://www.freebsd.org/doc/en_US.ISO8859-1/books/handbook/zfs.html). In short, ZFS is one of the best if not the best File systems out. I have several features to protect and backup your files, such as copy-on-write, snapshots, and block-level checksum validation. You also have the ability to use an SSD as a secondary cache (L2ARC) to improve random reads and improve the performance of your database.

PostgreSQL In Jail
------------------

You also have the option to run your instance of Postgres in a jail. A jail is an operating-system-level virtualization the allows you to separate your services from each other to protect your server. However, this is outside the scope of this post.
