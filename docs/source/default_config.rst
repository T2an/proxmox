
Default Configuration
=====================

Your default configuration in `/etc/network/interfaces` should be the following:

.. code-block:: ini

    auto lo
    iface lo inet loopback

    auto enp1s0f0
    iface eth0 inet manual

    auto vmbr0
    iface vmbr0 inet static
        address 192.168.1.100
        netmask 255.255.255.0
        gateway 192.168.1.1
        bridge_ports eth0
        bridge_stp off
        bridge_fd 0

**`vmbr0` is a bridge interface** used by Proxmox for various purposes and should not be removed. The IP address assigned to `vmbr0` is the one you set during the initial Proxmox installation.

Removing or editing the `vmbr0` bridge in Proxmox can disrupt network connectivity for VMs, containers, and management interfaces, leading to potential instability and loss of access. It is crucial to maintain this default configuration unless you have a deep understanding of the implications and a proper plan for reconfiguration and recovery.

.. _defaultconfig:

