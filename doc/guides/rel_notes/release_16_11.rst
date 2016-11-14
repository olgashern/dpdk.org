DPDK Release 16.11
==================

.. **Read this first.**

   The text below explains how to update the release notes.

   Use proper spelling, capitalization and punctuation in all sections.

   Variable and config names should be quoted as fixed width text: ``LIKE_THIS``.

   Build the docs and view the output file to ensure the changes are correct::

      make doc-guides-html

      firefox build/doc/html/guides/rel_notes/release_16_11.html


New Features
------------

.. This section should contain new features added in this release. Sample format:

   * **Add a title in the past tense with a full stop.**

     Add a short 1-2 sentence description in the past tense. The description
     should be enough to allow someone scanning the release notes to understand
     the new feature.

     If the feature adds a lot of sub-features you can use a bullet list like this.

     * Added feature foo to do something.
     * Enhanced feature bar to do something else.

     Refer to the previous release notes for examples.

     This section is a comment. Make sure to start the actual text at the margin.


* **Added software parser for packet type.**

  * Added a new function ``rte_pktmbuf_read()`` to read the packet data from an
    mbuf chain, linearizing if required.
  * Added a new function ``rte_net_get_ptype()`` to parse an Ethernet packet
    in an mbuf chain and retrieve its packet type by software.
  * Added new functions ``rte_get_ptype_*()`` to dump a packet type as a string.

* **Improved offloads support in mbuf.**

  * Added a new function ``rte_raw_cksum_mbuf()`` to process the checksum of
    data embedded in an mbuf chain.
  * Added new Rx checksum flags in mbufs to describe more states: unknown,
    good, bad, or not present (useful for virtual drivers). This modification
    was done for IP and L4.
  * Added a new RX LRO mbuf flag, used when packets are coalesced. This
    flag indicates that the segment size of original packets is known.

* **Added vhost-user dequeue zero copy support**

  The copy in dequeue path is saved, which is meant to improve the performance.
  In the VM2VM case, the boost is quite impressive. The bigger the packet size,
  the bigger performance boost you may get. However, for VM2NIC case, there
  are some limitations, yet the boost is not that impressive as VM2VM case.
  It may even drop quite a bit for small packets.

  For such reason, this feature is disabled by default. It can be enabled when
  ``RTE_VHOST_USER_DEQUEUE_ZERO_COPY`` flag is given. Check the vhost section
  at programming guide for more information.

* **Added vhost-user indirect descriptors support.**

  If indirect descriptor feature is negotiated, each packet sent by the guest
  will take exactly one slot in the enqueue virtqueue. Without the feature, in
  current version, even 64 bytes packets take two slots with Virtio PMD on guest
  side.

  The main impact is better performance for 0% packet loss use-cases, as it
  behaves as if the virtqueue size was enlarged, so more packets can be buffered
  in case of system perturbations. On the downside, small performance degradation
  is measured when running micro-benchmarks.

* **Added vhost PMD xstats.**

  Added extended statistics to vhost PMD from per port perspective.

* **Supported offloads with virtio.**

  * Rx/Tx checksums
  * LRO
  * TSO

* **Added virtio NEON support for ARM.**

* **Updated the ixgbe base driver.**

  Updated the ixgbe base driver, including the following changes:

  * add X550em_a 10G PHY support
  * support flow control auto negotiation for X550em_a 1G PHY
  * add X550em_a FW ALEF support
  * increase mailbox version to ixgbe_mbox_api_13
  * add two MAC ops for Hyper-V support

* **Added API's for VF management to the ixgbe PMD.**

  Eight new API's have been added to the ixgbe PMD for VF management from the PF.
  The declarations for the API's can be found in ``rte_pmd_ixgbe.h``.

* **Updated the enic driver.**

  * Use interrupt for link status checking instead of polling
  * More flow director modes on UCS Blade with firmware version >= 2.0(13e)
  * Full support for MTU update
  * Support for rte_eth_rx_queue_count function

* **Updated the mlx5 driver.**

  * Add support for RSS hash result
  * Several performance improvements
  * Several bug fixes

* **Updated the QAT PMD.**

  The QAT PMD was updated with following support:

  * MD5_HMAC algorithm
  * SHA224-HMAC algorithm
  * SHA384-HMAC algorithm
  * GMAC algorithm
  * KASUMI (F8 and F9) algorithm
  * 3DES algorithm
  * NULL algorithm
  * C3XXX device
  * C62XX device

* **Added openssl PMD.**

  A new crypto PMD has been added, which provides several ciphering and hashing.
  All cryptography operations are using Openssl library crypto API.

* **Updated the IPsec example with following support:**

  * configuration file
  * AES CBC IV generation with cipher forward function
  * AES GCM/CTR mode

* **Added support for new gcc -march option.**

  The GCC 4.9 ``-march`` option supports the Intel processor code names.
  The config option ``RTE_MACHINE`` can be used to pass code names to the compiler as ``-march`` flag.


Resolved Issues
---------------

.. This section should contain bug fixes added to the relevant sections. Sample format:

   * **code/section Fixed issue in the past tense with a full stop.**

     Add a short 1-2 sentence description of the resolved issue in the past tense.
     The title should contain the code/lib section like a commit message.
     Add the entries in alphabetic order in the relevant sections below.

   This section is a comment. Make sure to start the actual text at the margin.


EAL
~~~


Drivers
~~~~~~~

* **enic: Fixed several flow director issues.**

* **enic: Fixed inadvertent setting of L4 checksum ptype on ICMP packets.**

* **enic: Fixed high driver overhead when servicing Rx queues beyond the first.**


Libraries
~~~~~~~~~


Examples
~~~~~~~~


Other
~~~~~


Known Issues
------------

.. This section should contain new known issues in this release. Sample format:

   * **Add title in present tense with full stop.**

     Add a short 1-2 sentence description of the known issue in the present
     tense. Add information on any known workarounds.

   This section is a comment. Make sure to start the actual text at the margin.


API Changes
-----------

.. This section should contain API changes. Sample format:

   * Add a short 1-2 sentence description of the API change. Use fixed width
     quotes for ``rte_function_names`` or ``rte_struct_names``. Use the past tense.

   This section is a comment. Make sure to start the actual text at the margin.

* The driver names have been changed. It especially impacts ``--vdev`` arguments.
  Examples: ``eth_pcap`` becomes ``net_pcap``
  and ``cryptodev_aesni_mb_pmd`` becomes ``crypto_aesni_mb``.

* The log history is removed.

* The ``rte_ivshmem`` feature (including library and EAL code) has been removed
  in 16.11 because it had some design issues which were not planned to be fixed.

* The ``file_name`` data type of ``struct rte_port_source_params`` and
  ``struct rte_port_sink_params`` is changed from `char *`` to ``const char *``.

* **Improved device/driver hierarchy and generalized hotplugging**

  Device and driver relationship has been restructured by introducing generic
  classes. This paves way for having PCI, VDEV and other device types as
  just instantiated objects rather than classes in themselves. Hotplugging too
  has been generalized into EAL so that ethernet or crypto devices can use the
  common infrastructure.

  * removed ``pmd_type`` as way of segregation of devices
  * moved ``numa_node`` and ``devargs`` into ``rte_driver`` from
    ``rte_pci_driver``. These can now be used by any instantiated object of
    ``rte_driver``.
  * added ``rte_device`` class and all PCI and VDEV devices inherit from it
  * renamed devinit/devuninit handlers to probe/remove to make it more
    semantically correct with respect to device<=>driver relationship
  * moved hotplugging support to EAL. Hereafter, PCI and vdev can use the
    APIs ``rte_eal_dev_attach`` and ``rte_eal_dev_detach``.
  * helpers and support macros have been renamed to make them more synonymous
    with their device types
    (e.g. ``PMD_REGISTER_DRIVER`` => ``RTE_PMD_REGISTER_PCI``)
  * Device naming functions have been generalized from ethdev and cryptodev
    to EAL. ``rte_eal_pci_device_name`` has been introduced for obtaining
    unique device name from PCI Domain-BDF description.
  * Virtual device registration APIs have been added: ``rte_eal_vdrv_register``
    and ``rte_eal_vdrv_unregister``.


ABI Changes
-----------

.. This section should contain ABI changes. Sample format:

   * Add a short 1-2 sentence description of the ABI change that was announced in
     the previous releases and made in this release. Use fixed width quotes for
     ``rte_function_names`` or ``rte_struct_names``. Use the past tense.

   This section is a comment. Make sure to start the actual text at the margin.



Shared Library Versions
-----------------------

.. Update any library version updated in this release and prepend with a ``+``
   sign, like this:

     libethdev.so.4
     librte_acl.so.2
   + librte_cfgfile.so.2
     librte_cmdline.so.2



The libraries prepended with a plus sign were incremented in this version.

.. code-block:: diff

     librte_acl.so.2
     librte_cfgfile.so.2
     librte_cmdline.so.2
   + librte_cryptodev.so.2
     librte_distributor.so.1
   + librte_eal.so.3
   + librte_ethdev.so.5
     librte_hash.so.2
     librte_ip_frag.so.1
     librte_jobstats.so.1
     librte_kni.so.2
     librte_kvargs.so.1
     librte_lpm.so.2
     librte_mbuf.so.2
     librte_mempool.so.2
     librte_meter.so.1
     librte_net.so.1
     librte_pdump.so.1
     librte_pipeline.so.3
     librte_pmd_bond.so.1
     librte_pmd_ring.so.2
     librte_port.so.3
     librte_power.so.1
     librte_reorder.so.1
     librte_ring.so.1
     librte_sched.so.1
     librte_table.so.2
     librte_timer.so.1
     librte_vhost.so.3


Tested Platforms
----------------

.. This section should contain a list of platforms that were tested with this release.

   The format is:

   #. Platform name.

      * Platform details.
      * Platform details.

   This section is a comment. Make sure to start the actual text at the margin.

#. Intel(R) Server board S2600WTT

   - Processor: Intel(R) Xeon(R) CPU E5-2697 v2 @ 2.70GHz

#. Intel(R) Server

   - Intel(R) Xeon(R) CPU E5-2680 v2 @ 2.80GHz

#. Intel(R) Server

   - Intel(R) Xeon(R) CPU E5-2697 v3 @ 2.60GHz

#. IBM(R) Power8(R)

   - Machine type-model: 8247-22L
   - Firmware FW810.21 (SV810_108)
   - Processor: POWER8E (raw), AltiVec supported


Tested NICs
-----------

.. This section should contain a list of NICs that were tested with this release.

   The format is:

   #. NIC name.

      * NIC details.
      * NIC details.

   This section is a comment. Make sure to start the actual text at the margin.

#. Mellanox(R) ConnectX(R)-4 10G MCX4111A-XCAT (1x10G)

   * Host interface: PCI Express 3.0 x8
   * Device ID: 15b3:1013
   * MLNX_OFED: 3.4-1.0.0.0
   * Firmware version: 12.17.1010

#. Mellanox(R) ConnectX(R)-4 10G MCX4121A-XCAT (2x10G)

   * Host interface: PCI Express 3.0 x8
   * Device ID: 15b3:1013
   * MLNX_OFED: 3.4-1.0.0.0
   * Firmware version: 12.17.1010

#. Mellanox(R) ConnectX(R)-4 25G MCX4111A-ACAT (1x25G)

   * Host interface: PCI Express 3.0 x8
   * Device ID: 15b3:1013
   * MLNX_OFED: 3.4-1.0.0.0
   * Firmware version: 12.17.1010

#. Mellanox(R) ConnectX(R)-4 25G MCX4121A-ACAT (2x25G)

   * Host interface: PCI Express 3.0 x8
   * Device ID: 15b3:1013
   * MLNX_OFED: 3.4-1.0.0.0
   * Firmware version: 12.17.1010

#. Mellanox(R) ConnectX(R)-4 40G MCX4131A-BCAT/MCX413A-BCAT (1x40G)

   * Host interface: PCI Express 3.0 x8
   * Device ID: 15b3:1013
   * MLNX_OFED: 3.4-1.0.0.0
   * Firmware version: 12.17.1010

#. Mellanox(R) ConnectX(R)-4 40G MCX415A-BCAT (1x40G)

   * Host interface: PCI Express 3.0 x16
   * Device ID: 15b3:1013
   * MLNX_OFED: 3.4-1.0.0.0
   * Firmware version: 12.17.1010

#. Mellanox(R) ConnectX(R)-4 50G MCX4131A-GCAT/MCX413A-GCAT (1x50G)

   * Host interface: PCI Express 3.0 x8
   * Device ID: 15b3:1013
   * MLNX_OFED: 3.4-1.0.0.0
   * Firmware version: 12.17.1010

#. Mellanox(R) ConnectX(R)-4 50G MCX414A-BCAT (2x50G)

   * Host interface: PCI Express 3.0 x8
   * Device ID: 15b3:1013
   * MLNX_OFED: 3.4-1.0.0.0
   * Firmware version: 12.17.1010

#. Mellanox(R) ConnectX(R)-4 50G MCX415A-GCAT/MCX416A-BCAT/MCX416A-GCAT (2x50G)

   * Host interface: PCI Express 3.0 x16
   * Device ID: 15b3:1013
   * MLNX_OFED: 3.4-1.0.0.0
   * Firmware version: 12.17.1010

#. Mellanox(R) ConnectX(R)-4 50G MCX415A-CCAT (1x100G)

   * Host interface: PCI Express 3.0 x16
   * Device ID: 15b3:1013
   * MLNX_OFED: 3.4-1.0.0.0
   * Firmware version: 12.17.1010

#. Mellanox(R) ConnectX(R)-4 100G MCX416A-CCAT (2x100G)

   * Host interface: PCI Express 3.0 x16
   * Device ID: 15b3:1013
   * MLNX_OFED: 3.4-1.0.0.0
   * Firmware version: 12.17.1010

#. Mellanox(R) ConnectX(R)-4 Lx 10G MCX4121A-XCAT (2x10G)

   * Host interface: PCI Express 3.0 x8
   * Device ID: 15b3:1015
   * MLNX_OFED: 3.4-1.0.0.0
   * Firmware version: 14.17.1010

#. Mellanox(R) ConnectX(R)-4 Lx 25G MCX4121A-ACAT (2x25G)

   * Host interface: PCI Express 3.0 x8
   * Device ID: 15b3:1015
   * MLNX_OFED: 3.4-1.0.0.0
   * Firmware version: 14.17.1010


Tested OSes
-----------

.. This section should contain a list of OSes that were tested with this release.
   The format is as follows, in alphabetical order:

   * CentOS 7.0
   * Fedora 23
   * Fedora 24
   * FreeBSD 10.3
   * Red Hat Enterprise Linux 7.2
   * SUSE Enterprise Linux 12
   * Ubuntu 15.10
   * Ubuntu 16.04 LTS
   * Wind River Linux 8

   This section is a comment. Make sure to start the actual text at the margin.

* Red Hat Enterprise Linux Server release 6.7 (Santiago)
* Red Hat Enterprise Linux Server release 7.0 (Maipo)
* Red Hat Enterprise Linux Server release 7.2 (Maipo)
* Wind River Linux 6.0.0.26
* Ubuntu 14.04
* Ubuntu 15.04
