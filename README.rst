Elasticsearch
=============

|galaxy|

.. |galaxy| image:: https://img.shields.io/ansible/role/24785.svg
    :target: https://galaxy.ansible.com/tjanez/elasticsearch/
    :alt: Ansible Role

Ansible role to install and configure Elasticsearch 6.x on RHEL/CentOS 7.

**NOTE: This version of the role only supports Elasticsearch 6.x. To use the
role with Elasticsearch 2.4 or 5.6, use version 1.0.0 or 2.0.0,
respectively.**

Main features:

- Installs Elasticsearch from upstream's RPM repositories.
- Upgrades Elastcisearch from version 5.x (if necessary).
- Configures Elasticsearch according to the `official Elasticsearch Reference
  for version 6.0`_
- Configures system's performance settings with `Tuned`_ if it is enabled
  (default for RHEL/CentOS 7).
- Validates `important Elasticsearch system and JVM configuration`_.

.. _official Elasticsearch Reference for version 6.0:
  https://www.elastic.co/guide/en/elasticsearch/reference/6.0/index.html
.. _Tuned:
  https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/
  Performance_Tuning_Guide/chap-Red_Hat_Enterprise_Linux-Performance_Tuning_Guide-Tuned.html
.. _important Elasticsearch system and JVM configuration:
  https://www.elastic.co/guide/en/elasticsearch/reference/6.0/important-settings.html


Requirements
------------

This role requires Ansible 2.4 or higher.


Role Variables
--------------

Variables in ``defaults/main.yml``:
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

+--------------------------------------------+---------+-------------------------------------------+-----------------------------------------+
|                Name                        |  Type   |                Description                |           Default                       |
+============================================+=========+===========================================+=========================================+
| ``elasticsearch_data_dir``                 | string  | Path of Elasticsearch data directory.     | ``/var/lib/elasticsearch``              |
|                                            |         |                                           |                                         |
|                                            |         | For more information, see:                |                                         |
|                                            |         | `Configuring Elasticsearch`_.             |                                         |
+--------------------------------------------+---------+-------------------------------------------+-----------------------------------------+
| ``elasticsearch_log_dir``                  | string  | Path of Elasticsearch log directory.      | ``/var/log/elasticsearch``              |
|                                            |         |                                           |                                         |
|                                            |         | For more information, see:                |                                         |
|                                            |         | `Configuring Elasticsearch`_.             |                                         |
+--------------------------------------------+---------+-------------------------------------------+-----------------------------------------+
| ``elasticsearch_version``                  | string  | Major version to install.                 | ``"6"``                                 |
|                                            |         | The actual version that is installed is   |                                         |
|                                            |         | the latest minor (and patch) version of   |                                         |
|                                            |         | the chosen major version.                 |                                         |
|                                            |         |                                           |                                         |
|                                            |         | It can be one of the following:           |                                         |
|                                            |         |                                           |                                         |
|                                            |         | * ``"6"``                                 |                                         |
+--------------------------------------------+---------+-------------------------------------------+-----------------------------------------+
| ``elasticsearch_sysctl_file``              | string  | Path of Elasticsearch's sysctl            | ``/etc/sysctl.d/00-elasticsearch.conf`` |
|                                            |         | configuration file.                       |                                         |
|                                            |         |                                           |                                         |
|                                            |         | *NOTE*: It should be in one of the        |                                         |
|                                            |         | locations that are searched for by the    |                                         |
|                                            |         | `systemd-sysctl.service`_. For more       |                                         |
|                                            |         | information, see `sysctl.d's man page`_.  |                                         |
+--------------------------------------------+---------+-------------------------------------------+-----------------------------------------+
| ``elasticsearch_heap_size_mem_frac``       | float   | Fraction of total system memory to use    | ``0.5``                                 |
|                                            |         | for JVM's heap.                           |                                         |
|                                            |         |                                           |                                         |
|                                            |         |                                           |                                         |
|                                            |         | *NOTE*: Before changing this value, read  |                                         |
|                                            |         | the documentation on                      |                                         |
|                                            |         | `setting heap size`_. Regardless of the   |                                         |
|                                            |         | chosen fraction value, the minimal and    |                                         |
|                                            |         | maximal heap sizes are 256 MB and 31 GB   |                                         |
|                                            |         | (respectively).                           |                                         |
+--------------------------------------------+---------+-------------------------------------------+-----------------------------------------+
| ``elasticsearch_cluster_name``             | string  | Name of Elasticsearch cluster.            | ``elasticsearch``                       |
|                                            |         |                                           |                                         |
|                                            |         | For more information, see:                |                                         |
|                                            |         | `cluster.name documentation`_.            |                                         |
|                                            |         |                                           |                                         |
|                                            |         | *NOTE*: A node can only join a cluster    |                                         |
|                                            |         | when it shares the same cluster name with |                                         |
|                                            |         | all other nodes in the cluster.           |                                         |
+--------------------------------------------+---------+-------------------------------------------+-----------------------------------------+
| ``elasticsearch_node_name``                | string  | Name of Elasticsearch node.               | ``"{{ ansible_nodename }}"``            |
|                                            |         |                                           |                                         |
|                                            |         |                                           |                                         |
|                                            |         | For more information, see:                |                                         |
|                                            |         | `node.name documentation`_.               |                                         |
+--------------------------------------------+---------+-------------------------------------------+-----------------------------------------+
| ``elasticsearch_network_host``             | string  | Name of host or IP address to bind to.    | ``localhost``                           |
|                                            |         |                                           |                                         |
|                                            |         | For more information, see:                |                                         |
|                                            |         | `network settings documentation`_.        |                                         |
|                                            |         |                                           |                                         |
|                                            |         | *NOTE*: One can't use special values,     |                                         |
|                                            |         | e.g. ``_local_``, ``_site_``, since the   |                                         |
|                                            |         | variable is also used by Ansible which    |                                         |
|                                            |         | doesn't understand Elasticsearch's        |                                         |
|                                            |         | special syntax.                           |                                         |
+--------------------------------------------+---------+-------------------------------------------+-----------------------------------------+
| ``elasticsearch_http_port``                | integer | Port to bind to.                          | ``9200``                                |
|                                            |         |                                           |                                         |
|                                            |         | For more information, see:                |                                         |
|                                            |         | `network settings documentation`_.        |                                         |
+--------------------------------------------+---------+-------------------------------------------+-----------------------------------------+
| ``elasticsearch_discovery_zen_hosts``      | list    | List of nodes to contact initially to     | ``["127.0.0.1", "[::1]"]``              |
|                                            |         | join a cluster. One can specify host      |                                         |
|                                            |         | names or IP addresses.                    |                                         |
|                                            |         |                                           |                                         |
|                                            |         | For more information, see:                |                                         |
|                                            |         | `network settings documentation`_.        |                                         |
+--------------------------------------------+---------+-------------------------------------------+-----------------------------------------+
| ``elasticsearch_only_one_node_per_system`` | boolean | Limit Elasticsearch to one node per       | ``true``                                |
|                                            |         | system.                                   |                                         |
|                                            |         |                                           |                                         |
|                                            |         | For more information, see:                |                                         |
|                                            |         | `node.max_local_storage_nodes             |                                         |
|                                            |         | documentation`_.                          |                                         |
+--------------------------------------------+---------+-------------------------------------------+-----------------------------------------+
| ``elasticsearch_logging_to_file``          | boolean | Save main logging events to a file.       | ``false``                               |
|                                            |         | system.                                   |                                         |
|                                            |         |                                           |                                         |
|                                            |         | *NOTE*: Main logging events are always    |                                         |
|                                            |         | stored via `systemd-journald.service`_.   |                                         |
+--------------------------------------------+---------+-------------------------------------------+-----------------------------------------+

.. _Configuring Elasticsearch: https://www.elastic.co/guide/en/elasticsearch/reference/6.0/rpm.html#rpm-configuring
.. _systemd-sysctl.service: https://www.freedesktop.org/software/systemd/man/systemd-sysctl.service.html
.. _sysctl.d's man page: http://man7.org/linux/man-pages/man5/sysctl.d.5.html
.. _setting heap size: https://www.elastic.co/guide/en/elasticsearch/reference/6.0/heap-size.html
.. _cluster.name documentation: https://www.elastic.co/guide/en/elasticsearch/reference/6.0/important-settings.html#cluster.name
.. _node.name documentation: https://www.elastic.co/guide/en/elasticsearch/reference/6.0/important-settings.html#node.name
.. _network settings documentation: https://www.elastic.co/guide/en/elasticsearch/reference/6.0/modules-network.html#common-network-settings
.. _node.max_local_storage_nodes documentation: https://www.elastic.co/guide/en/elasticsearch/reference/6.0/important-settings.html#node.max_local_storage_nodes
.. _systemd-journald.service: https://www.freedesktop.org/software/systemd/man/systemd-journald.service.html


Variables in distribution-specific files under ``vars/``:
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

+--------------------------------------------+---------+-------------------------------------------+---------------------------------+
|                Name                        |  Type   |                Description                |           Default               |
+============================================+=========+===========================================+=================================+
| ``elasticsearch_java_package``             | string  | Name of the package that provides a JVM.  | ``java-1.8.0-openjdk-headless`` |
|                                            |         |                                           |                                 |
|                                            |         | *NOTE*: Elasticsearch 6.0 requires Java   |                                 |
|                                            |         | 1.8. For more information, see:           |                                 |
|                                            |         | `Product and JVM matrix`_.                |                                 |
+--------------------------------------------+---------+-------------------------------------------+---------------------------------+

.. _Product and JVM matrix: https://www.elastic.co/support/matrix#show_jvm


Dependencies
------------

None.


Example Playbook
----------------

.. code-block:: yaml

    - hosts: all

      vars:
        elasticsearch_data_dir: /mnt/fast_storage/elasticsearch

      roles:
        - ansible-elasticsearch-role


License
-------

GPLv3

Author Information
------------------

Tadej Janež

Acknowledgement
---------------

This Ansible role was originally developed for `Genialis`_. With
approval from Genialis, the code was generalised and published as Open
Source, for which the author would like to express his gratitude.

.. _Genialis:
  https://www.genialis.com/
