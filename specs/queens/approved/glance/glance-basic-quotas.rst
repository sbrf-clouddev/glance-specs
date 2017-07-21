..
 This work is licensed under a Creative Commons Attribution 3.0 Unported
 License.

 http://creativecommons.org/licenses/by/3.0/legalcode

===================
Glance Basic Quotas
===================

https://blueprints.launchpad.net/glance/+spec/glance-basic-quotas

Glance project allows us to set image quotas only using configuration and
only the same ones for all users/tenants. In real deployments it is common
situation, that different users/tenants require/need different amount
of resources, and, in this case, we should be able to handle such situation.

Problem description
===================

Not being able to handle quotas per project/domain we are forced to set
highest common quota in config file for all users. It leads to inefficient
resource management without real cause to do so.

Proposed change
===============

Add four following quota resources:

* total_images_number
* total_images_size
* total_snapshots_number
* total_snapshots_size

And following new APIs:

* quota-class-show <resource_name>

  This API allows to read default values for resource quotas that are applied
  when quotas are not set explicitly for project or domain.

* quota-class-update

  --total_images_number <int_value>

  --total_images_size <int_value>

  --total_snapshots_number <int_value>

  --total_snapshots_size <int_value>

  This API allows to set default values for resource quotas that are applied
  for each project and domain.

* quota-show <SCOPE>

* quota-update <SCOPE>

  --total_images_number <int_value>

  --total_images_size <int_value>

  --total_snapshots_number <int_value>

  --total_snapshots_size <int_value>

* quota-delete <SCOPE>

* limits <SCOPE>

  This API shows current usages and limits for requested project or domain.

Where "SCOPE" is either project or domain ID.

Other requirements:

* Access to these new APIs should be handled by common "policy" mechanism.
* Default policies should allow admins have access for all APIs.
* Default policies should allow member user have access only for
  'quota-class-show', 'quota-show' and 'limits' APIs.
* Project's quota should not exceed domain's quota.
* Summary usage of all projects in one domain should not exceed domain quota.
* Each quota is defined with integer value.
* '-1' quota value should mean 'unlimited'.
* Zero (0) and bigger values should mean 'limit' that cannot be exceeded.

Alternatives
------------

We can continue depend on config options that define quota for all.

Data model impact
-----------------

Following new DB models will be added:

* quotas
* quota_classes
* reservations

REST API impact
---------------

Following are new APIs to be implemented:

* quota-class-show

  * URL: /quota_classes
  * Method: GET

* quota-class-update

  * URL: /quota_classes
  * Method: PUT
  * JSON body:

    .. code-block:: json

        {
          'quota_classes': {
            'total_images_number': 100,
            'total_images_size': 10240,
            'total_snapshots_number': 200,
            'total_snapshots_size': 20480,
          }
        }

* quota-show

  * URL: /quotas/{scope}
  * Method: GET

* quota-update

  * URL: /quota_classes/{scope}
  * Method: PUT
  * JSON body:

    .. code-block:: json

        {
          'quotas': {
            'total_images_number': 100,
            'total_images_size': 10240,
            'total_snapshots_number': 200,
            'total_snapshots_size': 20480,
          }
        }

* quota-delete

  * URL: /quotas/{scope}
  * Method: DELETE

* limits

  * URL: /quotas/{scope}/usage
  * Method: GET

Security impact
---------------

None.

Notifications impact
--------------------

None.

Other end user impact
---------------------

New CLI commands will be added. Users will be able to view usages and limits.

Performance Impact
------------------

Quota and usages calculation is very cheap operation and not considered as
a performance impact.

Other deployer impact
---------------------

Deployer will "be able"/"need" to define default quotas.

Developer impact
----------------

None.

Implementation
==============

Assignee(s)
-----------

Primary assignee:

- Dmitri Plakhov

Work Items
----------

* Implement feature

* Update the api-ref

* Write a release note

* Add appropriate client changes

Dependencies
============

None.

Testing
=======

Unit and functional testing.

Documentation Impact
====================

Detailed description of new APIs will be required.

References
==========

None.
