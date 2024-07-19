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

1. **Install Ceph**:

   - Navigate to `Datacenter > Nodename > Ceph` and click **Install Ceph** on each node.

   .. image:: ./images/create_ceph.png
       :alt: Create Ceph Cluster
       :align: center

    In this bottom right corner, choose "No-Subscription" then click "start installation"

4. **Create the Ceph Cluster**:
   - Use the Proxmox VE web interface to create and configure the Ceph cluster.
   - Navigate to `Datacenter > Ceph` and click **Create Cluster**.
   - Follow the prompts to initialize the Ceph Monitor (MON) and Manager (MGR) services.

   .. image:: ./images/ceph_create_cluster.png
       :alt: Create Ceph Cluster
       :align: center

5. **Add Ceph OSDs**:
   - Ceph OSDs (Object Storage Daemons) are responsible for storing data and handling data replication.
   - Add OSDs by navigating to `Datacenter > Ceph > OSD` and clicking **Create OSD**.
   - Select the storage devices to be used for the OSDs and configure their settings.

   .. image:: ./images/ceph_add_osd.png
       :alt: Add Ceph OSD
       :align: center

6. **Verify the Ceph Cluster**:
   - After adding the OSDs, verify the health of the Ceph cluster by navigating to `Datacenter > Ceph > Status`.
   - Ensure that the cluster is in a healthy state (HEALTH_OK).

   .. image:: ./images/ceph_status.png
       :alt: Ceph Status
       :align: center

Installing CephFS on Proxmox VE
-------------------------------

1. **Create a CephFS File System**:
   - Navigate to `Datacenter > Ceph > CephFS` and click **Create CephFS**.
   - Provide a name for the file system and specify the number of metadata servers (MDS).

   .. image:: ./images/create_cephfs.png
       :alt: Create CephFS
       :align: center

2. **Add CephFS to Proxmox VE Storage**:
   - Navigate to `Datacenter > Storage` and click **Add**.
   - Select **CephFS** as the storage type.
   - Provide the necessary information, such as the ID, Ceph cluster name, and the CephFS path.

   .. image:: ./images/add_cephfs_storage.png
       :alt: Add CephFS Storage
       :align: center

3. **Mount CephFS**:
   - CephFS can be mounted on any Proxmox VE node using the following command:

     .. code-block:: shell

        mkdir /mnt/cephfs
        mount -t ceph <mon-ip>:6789:/ /mnt/cephfs -o name=<ceph-username>,secretfile=/etc/ceph/<ceph-username>.secret

   - Ensure that `<mon-ip>`, `<ceph-username>`, and other parameters are replaced with appropriate values.

4. **Verify CephFS Mount**:
   - Verify that CephFS is mounted correctly by running:

     .. code-block:: shell

        df -h /mnt/cephfs

   - The output should show the CephFS mount point with the correct storage capacity.

Conclusion
----------

By following these steps, you have successfully installed and configured Ceph and CephFS on Proxmox VE. This setup provides a robust, scalable storage solution for your virtual environment, allowing you to take full advantage of Ceph's distributed architecture and high availability.

In the next section, we will cover advanced networking configurations in Proxmox VE.
