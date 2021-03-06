.. Nuts documentation master file, created by
   sphinx-quickstart on Mon Aug 28 19:32:21 2017.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

================================
Welcome to Nuts' documentation!
================================

Nuts - Network Unit Testing System
==================================
.. image:: https://travis-ci.org/HSRNetwork/Nuts.svg?branch=master
    :alt: Build status
    :target: https://travis-ci.org/HSRNetwork/Nuts

.. image:: https://img.shields.io/pypi/v/nuts.svg
    :alt: PyPi Version
    :target: https://pypi.python.org/pypi/nuts

.. image:: https://img.shields.io/pypi/pyversions/nuts.svg
    :alt: PyPi Python Versions
    :target: https://pypi.python.org/pypi/nuts

.. image:: https://img.shields.io/pypi/wheel/nuts.svg
    :alt: PyPi wheel
    :target: https://pypi.python.org/pypi/nuts

.. image:: https://img.shields.io/pypi/l/nuts.svg
    :alt: PyPi Licence
    :target: https://pypi.python.org/pypi/nuts

Nuts is a network unit testing system that automates tests in the network similar to unit tests you might know from programming.
The project uses SaltStack and napalm to communicate with network devices. Regular Salt minions are used for Linux devices.
This project is currently under heavy construction and we can't guarantee you that this code works. But we do our best.
If you have any questions, feature request or use cases please reach out to us at https://github.com/HSRNetwork/Nuts or https://networktocode.slack.com/


How do you decide a network change was successful? Hopefully you have a test protocol and can do all the necessary test
on every touched box. When no test protocol is in place, you end up jumping from device to device and
doing some random ``ping`` and ``show`` commands. For example, verifying your BGP sessions are up and running. After that,
you will maybe do some pings from the client side. But are you confident you did not do any mistakes? Can you rule out side effects?
It’s 4 o’clock in the morning and you must decide if you can close the maintenance window or more tests are needed.

In a perfect world, the network engineer is sleeping well at 4 o’clock in the morning,
network automation is doing the approved change and if the change fails, a rollback is triggered.
On both fail and success scenarios, a report is generated and the change is documented.
The network engineer is happy, network is happy and the customers are happy.
Engineers can do more engineering stuff and less operations.

For the perfect world, you must be able to decide whether a change was successful or not.
So, what is the definition of success? Of course, if the network is working as expected and the change has no side effects.
If we break DHCP in the VoIP subnet after a firewall change, the phones will loose connectivity after the lease timed out.
If we do not test DHCP explicitly from this segment, we will not notice the issue before the first phone is disconnected.

To avoid side effects, software developer have been unit and integration test for decades.
Every feature gets its own tests and when someone adds a feature in the future,
test ensure that no existing features broke.

So let’s do the same in the network environment. Write some tests you can run (automatically) after a change to rule out side effects!
Like an automated test protocol and you will not forget to test anything.

Nuts supports different test involving network devices and/or Linux clients.

Network Engineers write tests in a easy readable YAML format. For example to test if you receive a virtual HSRP MAC address:

.. code:: yaml

 - name: example_arp
   command: arp
   devices: cisco.csr.1000v
   parameter: ['192.168.16.128']
   operator: '='
   expected: '00:0C:29:EA:D1:68'

Or you can use a raspberry pi with sub interfaces to test https traffic from a specific segment:

.. code:: yaml

 - name: http_access_vlan_10
   command: webresponse
   devices: raspberry01
   parameter: ['https://github.com']
   operator: '='
   expected: True
   setup:
     - command: cmd.run
       parameter: ['ip route add 10.10.0.0/16 via 192.168.50.1 dev eth0.10']
       devices: raspberry01
   teardown:
     - command: cmd.run
       parameter: ['ip route del 10.10.0.0/16 via 192.168.50.1 dev eth0.10']
       devices: raspberry01

You can validate a test file first or just start the tests and wait for the result.

.. code:: bash

 nuts /path/to/my/testfile.yml



Documentation
=============

.. toctree::
   :maxdepth: 2

   install/index
   tests/index
   usecases/index


Indices and tables
==================

* :ref:`genindex`
* :ref:`modindex`
* :ref:`search`
