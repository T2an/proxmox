Testing Your Proxmox Cluster and SDN with a Container
=====================================================

Now that you have a functioning cluster and a fully operational Software-Defined Network (SDN), let's create a container to test the setup.

Understanding Storage Options
-----------------------------

In your Proxmox cluster, you will notice that two local storage options are present on each node:

**Local Storage (`local`)**:
   - This storage is typically used for storing configuration files, ISO images, container templates, and backups. It resides on the root file system of the Proxmox node and is not shared across the cluster.

**Local-LVM Storage (`local-lvm`)**:
   - This storage is used for storing virtual machine (VM) and container disk images. It utilizes Logical Volume Management (LVM) to manage storage space, allowing for flexible volume resizing and efficient storage utilization. This storage is also local to each node and not shared across the cluster.

.. image:: ./images/storage.png
    :alt: Storage Options
    :align: center


By understanding these storage options, you can better manage your virtual machines and containers in a clustered environment. We will cover how to manage these volumes and add new ones later.

Downloading Container Template
------------------------------

To add a container template, navigate to `Datacenter > yourserver > local > CT Template` and click **Templates**.

You will find a list of up-to-date container templates available for download. Once downloaded, these templates will be ready for easy deployment.

.. image:: ./images/template.png
    :alt: Template
    :align: center


Choose any template from the list you want to download. Here, we chose Rocky Linux 9.

.. image:: ./images/template_list.png
    :alt: Container template list
    :align: center


Wait for the download to complete; this could take a few minutes. Once downloaded, exit the template window.

.. image:: ./images/download_success.png
    :alt: Download success
    :align: center


Now, you can use this template to create a container.

.. image:: ./images/create_container.png
    :alt: Create Container
    :align: center


We will use a simple configuration and not go through all the different options. Some will be covered in the next sections.

Creating a Container with SDN
-----------------------------

1. **General Settings**:
    - **Node**: Select the node where you want to create your container. It must be the same node where you downloaded the container template, as it is stored in local storage.
    - **Hostname**: Enter the hostname of the container.
    - **Password**: Set the password for the root user.

.. image:: ./images/CT1.png
    :alt: Container General Settings
    :align: center


2. **Template Selection**:
    - Choose your template from the list.

.. image:: ./images/CT2.png
    :alt: Template Selection
    :align: center


3. **Storage**:
    - Select `local-lvm` as your container storage.

.. image:: ./images/CT3.png
    :alt: Container Storage
    :align: center


4. **Network**:
    - **Name**: Enter the interface name.
    - **Bridge**: Choose the name of the SDN you created.
    - **IPv4**: Ensure the DHCP option is checked.

.. image:: ./images/CT4.png
    :alt: Container Network Settings
    :align: center


5. **Confirmation**:
    - Review your configuration details. You can check the "Start after created" option to start the container automatically. Then press **Finish**.

.. image:: ./images/CT5.png
    :alt: Container Confirmation
    :align: center


You should see the message "TASK OK".

.. image:: ./images/CT6.png
    :alt: Task OK Message
    :align: center


Sometimes, the container might not start automatically even with the "Start after created" option. In that case, you can start it manually.

.. image:: ./images/start_CT.png
    :alt: Start Container Button
    :align: center


Accessing and Testing the Container
-----------------------------------

Access your container terminal by clicking on your container and then selecting **Console**. You can also open the terminal by double-clicking on the container.

.. image:: ./images/CT_console.png
    :alt: Container Console
    :align: center


**Verification**:

- Check that the first IP of the pool defined in the SDN section has been assigned to your container.

- Test the NAT configuration by pinging an external IP.

.. image:: ./images/CT_test.png
    :alt: Container Console Test
    :align: center

New Network Scheme
------------------

You're new network scheme should be the following : 

.. image:: ./images/SDN_CT_scheme.png
    :alt: SDN CT scheme
    :align: center

Conclusion
----------

You have successfully created and tested a container within your Proxmox cluster using the SDN configuration. This ensures that your cluster and SDN setup are functioning correctly.

In the next section, we will cover how to create an other type of SDN, EVPN, which allow you to have full connectivity over your network. 