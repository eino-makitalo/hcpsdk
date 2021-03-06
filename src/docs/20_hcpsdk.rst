:mod:`hcpsdk` --- object access
===============================

.. automodule:: hcpsdk
   :synopsis: Framework for HCP access.

**hcpsdk** provides access to HCP through http[s]/\ :term:`reST` dialects.

Setup is easy (:ref:`see example below <hcpsdk_example>`):

    1.  Instantiate an *Authorization* object with the required credentials.

        This class will be queried by *Target* objects for authorization
        tokens.

    2.  **Optional:** create an *SSL context* if you want to have certificates
        presented by HCP validated.

    3.  Instantiate a *Target* class with HCPs *Full Qualified Domain Name*,
        the port to be used, the *Authorization* object, optionally the
        *SSL context* created in 2. and -eventually- the :term:`FQDN` of a replica HCP.

    4.  Instantiate one or more *Connection* objects.

        These objects are the workhorses of the *hcpsdk* - they are providing
        the access methods needed. You'll need to consult the respective HCP
        manuals to find out how to frame the required requests.

        *Connection* objects will open a session to HCP as soon as
        needed, but not before. After use, they keep the session open for an
        adjustable amount of time, helping to speed up things for an subsequent request.

        Don't forget to close *Connection* objects when finished with them!


Functions
---------

version
^^^^^^^

.. py:method:: version()

   Return the full version of the HCPsdk (|release|).

checkport
^^^^^^^^^

.. autofunction:: checkport

Constants
---------

**Ports**

   .. attribute:: P_HTTP

      Port 80 - insecure Namespace access

   .. attribute:: P_HTTPS

      Port 443 - secure Namespace access

   .. attribute:: P_SEARCH

      Port 8888 - search facility

   .. attribute:: P_MAPI

      Port 9090 - Management API access

**Interfaces**

   .. attribute:: I_NATIVE

      HCP's http/REST dialect for access to HCPs authenticated :term:`namespaces <Namespace>`.

   .. attribute:: I_HSWIFT

      HCP's http dialect for access to HCPs :term:`HSwift <HSwift>` gateway.

   .. attribute:: I_DUMMY

      HCP's http dialect for access to HCPs :term:`Default Namespace <Default Namespace>`.


Classes
-------

NativeAuthorization
^^^^^^^^^^^^^^^^^^^

.. autoclass:: NativeAuthorization
   :members:

NativeADAuthorization
^^^^^^^^^^^^^^^^^^^^^

.. autoclass:: NativeADAuthorization
   :members:

   .. versionadded:: 0.9.4.0

LocalSwiftAuthorization
^^^^^^^^^^^^^^^^^^^^^^^

.. autoclass:: LocalSwiftAuthorization
   :members:

   .. versionadded:: 0.9.3.10

DummyAuthorization
^^^^^^^^^^^^^^^^^^

.. autoclass:: DummyAuthorization
   :members:

.. _hcpsdk_target:

Target
^^^^^^

.. autoclass:: Target
   :members:

.. _hcpsdk_connection:

Connection
^^^^^^^^^^

.. autoclass:: Connection
   :members:


Exceptions
----------

.. autoexception:: HcpsdkError

.. autoexception:: HcpsdkCantConnectError

.. autoexception:: HcpsdkTimeoutError

.. autoexception:: HcpsdkCertificateError

.. autoexception:: HcpsdkPortError

.. autoexception:: HcpsdkReplicaInitError


.. _hcpsdk_example:

Example
-------

::

    >>> import hcpsdk
    >>> hcpsdk.version()
    '0.9.0-2'
    >>> auth = hcpsdk.NativeAuthorization('n', 'n01')
    >>> t = hcpsdk.Target('n1.m.hcp1.snomis.local', auth, port=443)
    >>> c = hcpsdk.Connection(t)
    >>> c.connect_time
    '0.000000000010'
    >>>
    >>> r = c.PUT('/rest/hcpsdk/test1.txt', body='This is an example',
                  params={'index': 'true'})
    >>> c.response_status, c.response_reason
    (201, 'Created')
    >>>
    >>> r = c.HEAD('/rest/hcpsdk/test1.txt')
    >>> c.response_status, c.response_reason
    (200, 'OK')
    >>> c.getheaders()
    [('Date', 'Sat, 31 Jan 2015 20:34:53 GMT'),
     ('Server', 'HCP V7.1.0.10'),
     ('X-RequestId', '38AD86EF250DEB35'),
     ('X-HCP-ServicedBySystem', 'hcp1.snomis.local'),
     ('X-HCP-Time', '1422736493'),
     ('X-HCP-SoftwareVersion', '7.1.0.10'),
     ('ETag', '"68791e1b03badd5e4eb9287660f67745"'),
     ('Cache-Control', 'no-cache,no-store'),
     ('Pragma', 'no-cache'),
     ('Expires', 'Thu, 01 Jan 1970 00:00:00 GMT'),
     ('Content-Type', 'text/plain'),
     ('Content-Length', '18'),
     ('X-HCP-Type', 'object'),
     ('X-HCP-Size', '18'),
     ('X-HCP-Hash', 'SHA-256 47FB563CC8F86DC37C86D08BC542968F7986ACD81C97B'
                    'F76DB7AD744407FE117'),
     ('X-HCP-VersionId', '91055133849537'),
     ('X-HCP-IngestTime', '1422736466'),
     ('X-HCP-RetentionClass', ''),
     ('X-HCP-RetentionString', 'Deletion Allowed'),
     ('X-HCP-Retention', '0'),
     ('X-HCP-IngestProtocol', 'HTTP'),
     ('X-HCP-RetentionHold', 'false'),
     ('X-HCP-Shred', 'false'),
     ('X-HCP-DPL', '2'),
     ('X-HCP-Index', 'true'),
     ('X-HCP-Custom-Metadata', 'false'),
     ('X-HCP-ACL', 'false'),
     ('X-HCP-Owner', 'n'),
     ('X-HCP-Domain', ''),
     ('X-HCP-UID', ''),
     ('X-HCP-GID', ''),
     ('X-HCP-CustomMetadataAnnotations', ''),
     ('X-HCP-Replicated', 'false'),
     ('X-HCP-ReplicationCollision', 'false'),
     ('X-HCP-ChangeTimeMilliseconds', '1422736466446.00'),
     ('X-HCP-ChangeTimeString', '2015-01-31T21:34:26+0100'),
     ('Last-Modified', 'Sat, 31 Jan 2015 20:34:26 GMT')
    ]
    >>>
    >>> r = c.GET('/rest/hcpsdk/test1.txt')
    >>> c.response_status, c.response_reason
    (200, 'OK')
    >>> c.read()
    b'This is an example'
    >>> c.service_time2
    0.0005471706390380859
    >>>
    >>> r = c.DELETE('/rest/hcpsdk/test1.txt')
    >>> c.response_status, c.response_reason
    (200, 'OK')
    >>> c.service_time2
    0.0002570152282714844
    >>>
    >>> c.close()
    >>>

