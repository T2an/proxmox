Ceph and CephFS on Proxmox VE
=============================

Ceph is a distributed storage system that provides excellent performance, reliability, and scalability. CephFS is a POSIX-compliant file system that uses Ceph to store data, allowing for distributed and redundant storage.

This section covers the installation and configuration of Ceph and CephFS on Proxmox VE.

Understanding Ceph and CephFS
-----------------------------

**Ceph**:
- A scalable, distributed storage system that provides object, block, and file storage.
- Designed to be highly available and resilient to failures.
- Uses a CRUSH algorithm to distribute data across the cluster, ensuring no single point of failure.

**CephFS**:
- A file system layer built on top of Ceph's object storage.
- Allows multiple clients to mount and use the same file system simultaneously.

Installing Ceph on Proxmox VE
-----------------------------

Use the Proxmox VE web interface to create and configure the Ceph cluster.

Install Ceph

1. Navigate to `Datacenter > NodeName > Ceph` and click **Configure Ceph** on each node.

   .. image:: ./images/create_ceph.png
       :alt: Create Ceph Cluster
       :align: center

2. In the bottom right corner, choose **No-Subscription** and click **Start Installation**.
3. Wait for the installation to complete.
4. Set the Public Network IP and Cluster Network IP.

   By ticking **Advanced**, you can choose the number of replicas and minimum number of replicas. Replicas determine the number of copies of the data. The minimum number of replicas defines the least number of replicas needed for the system to function correctly.

Add Ceph OSDs

Ceph OSDs (Object Storage Daemons) are responsible for storing data and handling data replication. You need to completely clear the disk you're going to use as an OSD.

1. List your disks using the `lsblk` command, and clear them using `sudo wipefs --all /dev/sdb`.
2. Add OSDs by navigating to `Datacenter > Ceph > OSD` and clicking **Create OSD**.

   .. image:: ./images/ceph_add_osd.png
       :alt: Add Ceph OSD
       :align: center

   Perform this operation on every node that has a storage device to share. The total available space of your Ceph storage will be the sum of all your storage divided by the number of replicas.

3. You should then be able to see all your OSDs, with the state "up/in".

   .. image:: ./images/os_created.png
      :alt: Add Ceph OSD Check
      :align: center

Create Ceph Pools

A Ceph pool allows you to aggregate all your storage and use it for storing your VMs.

1. Navigate to `Datacenter > Ceph > Pools` and click **Create Pool**.

   .. image:: ./images/ceph_pool.png
       :alt: Add Ceph Pool
       :align: center

Verify the Ceph Cluster

- Verify the health of the Ceph cluster by navigating to `Datacenter > Ceph > Status`.
- Ensure that the cluster is in a healthy state (**HEALTH_OK**).
- The default minimum number of storage and replicas is 3. If you have fewer than 3, your cluster state will be **HEALTH_WARN** but will still function.
- You should now be able to see your storage in the left panel of the Proxmox UI and select it when creating a VM.

   .. image:: ./images/ceph_state.png
       :alt: Ceph State
       :align: center

Installing CephFS on Proxmox VE
-------------------------------

1. Navigate to `Datacenter > Ceph > CephFS` and click **Create** in the Metadata Servers section.
   - You'll be asked to define the server that will host the metadata.

   .. image:: ./images/create_cephfs.png
       :alt: Create CephFS
       :align: center

2. Navigate to `Datacenter > Ceph > CephFS` and click **Create CephFS**.
   - Provide a name for the file system and specify the number of metadata servers (MDS).

   .. image:: ./images/create_cephfs2.png
       :alt: Create CephFS
       :align: center

3. You should now see the CephFS storage in the left panel.

   .. image:: ./images/cephfs_success.png
       :alt: CephFS Storage Created
       :align: center

Conclusion
----------

By following these steps, you have successfully installed and configured Ceph and CephFS on Proxmox VE. This setup provides a robust, scalable storage solution for your virtual environment, allowing you to take full advantage of Ceph's distributed architecture and high availability.