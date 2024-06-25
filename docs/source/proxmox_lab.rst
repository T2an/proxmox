Proxmox Lab
===========

.. _Proxmox Lab:

Proxmox Lab
-----------

This tutorial's Proxmox lab consists of the following components:

- 2 Proxmox VE worker nodes
- 1 Proxmox Backup Server
- 1 NFS server

Below is a schematic of the infrastructure:

.. image:: ./images/proxmoxlab.png
   :alt: Proxmox Lab Infrastructure
   :align: center

You can download the latest versions of Proxmox VE and Proxmox Backup Server from the official Proxmox website: https://www.proxmox.com/en/downloads

Please note that this lab is experimental and has the following constraints:

- Only one network interface (NIC) for each server
- Each server must establish a connection to a RADIUS server with 802.1X authentication

----------------
