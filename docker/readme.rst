===========
TimescaleDB
===========

About
=====
**TimescaleDB** [1]_ is an open-source relational database for time-series and analytics.

Version
-------
TimescaleDB version **2.6.1-pg14** deployed based on the Docker Hub image: [2]_. 

License
-------
**Apache License Vesrion 2** [3]_

Pre-requisites
==============
* *docker* installed
* access to DIGITbrain private docker repo (username, password) to pull the image:
  
  - ``docker login dbs-container-repo.emgora.eu``
  - ``docker pull dbs-container-repo.emgora.eu/timescaledb:2.6.1``

Usage
=====
.. code-block:: bash

  docker run -d --rm \
      --name timescaledb \
      -e POSTGRES_PASSWORD=mydatabasepassword \
      -p 5432:5432 \
      timescaledb:2.6.1 \
      -c ssl=on \
      -c ssl_cert_file=/etc/certs/server-cert.pem \
      -c ssl_key_file=/etc/certs/server-key.pem \
      -c ssl_ca_file=/etc/certs/ca.pem

where POSTGRES_PASSWORD parameter sets password for user (role) ``postgres``,
standard PostgreSQL port 5432 is opened on the host, and SSL turned on.
(Use: ``psql -U postgres`` in CLI.)

.. warning::
  Always update ``POSTGRES_PASSWORD`` parameter with the values of your choice
  prior to running this container.

Security
========
The image uses **SSL/TLS traffic encryption** and **username-password authentication**, by
default using a DIGITbrain server certificate signed by DIGITbrain CA. You can override these certificates with your own,
see *volumes* parameters below.

In clients, choose ``sslmode=require``, ``sslmode=verify-ca``, or preferably ``sslmode=verify-full`` option 
(the latter includes hostname verification that will not work with the pre-built certificates) [4]_.

Note that without ``sslmode`` option the connection will succeed without any traffic encryption.
Also note that connection from localhost (PostgreSQL host) is allowed without authentication.
For forcing SSL connection or setting up TLS client authentication refer to [5]_ (requires altering file: ``/var/lib/postgresql/data/pg_hba.conf``). 

Configuration
=============

Environment variables
---------------------
.. list-table:: 
   :header-rows: 1

   * - Name
     - Example
     - Comment
   * - *password*
     - ``-e POSTGRES_PASSWORD=mydatabasepassword``
     - Password for a newly created user  

Ports
-----
.. list-table:: 
  :header-rows: 1

  * - Container port
    - Host port bind example
    - Comment
  * - *5432*
    - ``-p 15432:5432``
    - Default TimescaleDB (PostgreSQL) container port 5432 is opened as port 15432 on the host  

Volumes
-------
.. list-table:: 
  :header-rows: 1

  * - Name
    - Volume mount example
    - Comment
  * - *PostgreSQL data*    
    - ``-v $PWD/data:/var/lib/postgresql/data/``
    - PostgreSQL data will be persisted in host directory: ``./data``.
  * - *CA certificate*    
    - ``-v $PWD/certificates/ca.pem:/etc/certs/ca.pem``  
    - Overrides Certificate Authority (CA) certificate
  * - *Server key*    
    - ``-v $PWD/certificates/server-key.pem:/etc/certs/server-key.pem``  
    - Overrides server key (path defined by $PGSSLKEY)
  * - *Server certificate*    
    - ``-v $PWD/certificates/server-cert.pem:/etc/certs/server-cert.pem``  
    - Overrides server certificate (path defined by $PGSSLCERT)

References
==========
.. [1] https://www.timescale.com/

.. [2] https://hub.docker.com/r/timescale/timescaledb

.. [3] https://github.com/timescale/timescaledb/blob/main/LICENSE

.. [4] https://www.postgresql.org/docs/14/libpq-ssl.html#LIBPQ-SSL-PROTECTION

.. [5] https://smallstep.com/hello-mtls/doc/server/postgresql
