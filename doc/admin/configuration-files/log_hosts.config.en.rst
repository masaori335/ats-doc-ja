log_hosts.config
****************

.. Licensed to the Apache Software Foundation (ASF) under one
   or more contributor license agreements.  See the NOTICE file
  distributed with this work for additional information
  regarding copyright ownership.  The ASF licenses this file
  to you under the Apache License, Version 2.0 (the
  "License"); you may not use this file except in compliance
  with the License.  You may obtain a copy of the License at
 
   http://www.apache.org/licenses/LICENSE-2.0
 
  Unless required by applicable law or agreed to in writing,
  software distributed under the License is distributed on an
  "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY
  KIND, either express or implied.  See the License for the
  specific language governing permissions and limitations
  under the License.

To record HTTP transactions for different origin servers in separate log
files, you must list each origin server hostname in the
``log_hosts.config`` file. In addition, you must enable the `HTTP Host
Log Splitting <../working-log-files#HTTPHostLogSplitting>`_ option. You
should use the same ``log_hosts.config`` file on every Traffic Server
node in your cluster. After you modify the ``log_hosts.config`` file,
navigate to the Traffic Server\ ``bin`` directory and run the
``traffic_line -x`` command to apply the changes. When you apply the
changes to a node in a cluster, Traffic Server automatically applies the
changes to all other nodes in the cluster.

Format
======

Each line in the ``log_hosts.config`` file has the following format:

::

    hostname

where *``hostname``* is the hostname of the origin server.

**Tip:** You can specify keywords in the ``log_hosts.config`` file to
record all transactions from origin servers with the specified keyword
in their names in a separate log file. See the example below.

Examples
========

The following example configures Traffic Server to create separate log
files containing all HTTP transactions for the origin servers
``webserver1``, ``webserver2``, and ``webserver3``.

::

    webserver1
    webserver2
    webserver3

The following example records all HTTP transactions from origin servers
that contain ``sports`` in their names. For example:
``sports.yahoo.com`` and ``www.foxsports.com`` in a log file called
``squid-sport.log`` (the Squid format is enabled).

::

    sports

