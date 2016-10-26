===========================
Primary Storage
===========================

It's possible to use an object store as primary storage, this replaces the default
way of storing files in `nextcloud/data` (note that the data directory might still be used
for other reasons)

---------------------------
Implications
---------------------------

When using an object store as primary storage, Nextcloud assumes exclusive access
over the bucket being used.

Contrary to using an object store as external storage, when an object store is used
as primary storage, no metadata (names, directory structures, etc) is stored in the
object store. The metadata is only stored in the database and the object store only
holds the file content by unique identifier.

Because of this primary object stores usually perform better than when using the same
object store as external storage but it restricts being able to access the files from
outside of Nextcloud.

---------------------------
Configuring
---------------------------

Primary object stores need to be configured in :code:`config.php` by specifying the objectstore
backend and any backend specific configuration.

.. note:: Configuring a primary object store on an existing Nextcloud instance will
	make all existing files on the instance inaccessible.

The configuration has the following structure.

::

	'objectstore' => array(
		'class' => 'Object\\Storage\\Backend\\Class',
		'arguments' => array(
			...
		),
	),

--------
Openstack Swift
--------

The Swift backend mounts a container on an OpenStack Object Storage server into the virtual filesystem. The class to be used is :code:`\\OC\\Files\\ObjectStore\\Swift`

::

	'objectstore' => array(
		'class' => 'OC\\Files\\ObjectStore\\Swift',
		'arguments' => array(
			'username' => 'username',
			'password' => 'Secr3tPaSSWoRdt7',
			'container' => 'nextcloud',
			'autocreate' => true,
			'region' => 'RegionOne',
			// The Identity / Keystone endpoint
			'url' => 'http://example.com/v2.0',
			// optional on some swift implementations
			'tenantName' => 'username',
			'serviceName' => 'swift',
			// The Interface / url Type, optional
			'urlType' => 'internal'
		),
	),

--------
Amazon S3
--------

The S3 backend mounts a bucket on an Amazon S3 Storage or compatible server into the virtual filesystem. The class to be used is :code:`\\OC\\Files\\ObjectStore\\S3`

::

	'objectstore' => array(
		'class' => 'OC\\Files\\ObjectStore\\S3',
		'arguments' => array(
			'bucket' => 'nextcloud',
			'autocreate' => true,
			'key'    => 'EJ39ITYZEUH5BGWDRUFY',
			'secret' => 'M5MrXTRjkyMaxXPe2FRXMTfTfbKEnZCu+7uRTVSj',
			'hostname' => 'example.com',
			'port' => 1234,
			'use_ssl' => true,
			'region' => 'optional',
			// required for some non amazon s3 implementations
			'use_path_style'=>true
		),
	),

Not all configuration options are required for all S3 servers.
Overriding the hostname, port and region of your S3 server is only
required for non-Amazon servers such as Ceph Object Gateway, which in turn
usually don't require the region to be set.

:code:`use_path_style` is usually not required (and is, in fact, incompatible with newer Amazon datacenters),
but can be used with non-Amazon servers where the DNS infrastructure cannot be controlled. Ordinarily,
requests will be made with http://bucket.hostname.domain/, but with path style enabled,
requests are made with http://hostname.domain/bucket instead.
