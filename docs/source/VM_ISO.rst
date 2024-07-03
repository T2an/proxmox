Adding an ISO Image and Deploying a VM
======================================

In this section, we will explain how to add an ISO image to your Proxmox VE storage and use it to deploy a virtual machine (VM).

Adding an ISO Image
-------------------

1. **Download the ISO Image**:

   - Obtain the desired ISO image for the operating system you want to install. Ensure it is stored on your local machine or accessible from a network location.

2. **Upload the ISO to Proxmox VE**:

   - Log in to the Proxmox VE web interface.
   - Navigate to `Datacenter > Storage`.
   - Select the storage location where you want to upload the ISO, As configured in the previous section, we want to store the ISO in the NFS server.
   - Click on the `ISO Images` tab.
   - Click the **Upload** button and select the ISO file from your local machine.

   .. image:: ./images/ISO.png
       :alt: Upload ISO Image
       :align: center

   .. note::
      Ensure that the storage location has enough free space to accommodate the ISO file.

Deploying a VM Using the ISO Image
----------------------------------

1. **Create a New VM**:
   - Navigate to the `Datacenter` view.
   - Click **Create VM** in the upper right corner of the interface.

2. **VM Configuration Steps**:

    **General**:

    - Enter a **Name** for your VM.
    - Select the **Node** where the VM will be hosted.
    - You can select a Ressource Pool 

    .. image:: ./images/create_vm_general.png
        :alt: VM General Configuration
        :align: center

    **OS**:

    - Select the ISO image you uploaded from the **ISO Image** dropdown.
    - Choose the appropriate **Guest OS** type.

    .. image:: ./images/create_vm_os.png
        :alt: VM OS Configuration
        :align: center

    **System**:

    - Configure the system settings as per your requirements (e.g., BIOS, SCSI Controller).


    **Disk**:

    - Configure the hard disk size and type. Select the storage where the VM disk will be stored. In last section, we defined local-lvm as our VM/LXC storage. 

    .. image:: ./images/create_vm_harddisk.png
        :alt: VM Hard Disk Configuration
        :align: center

    **CPU**:

    - Allocate the number of cores and type for the VM. **If starting your VM generate an error, this could be due to a bad CPU type.**

    .. image:: ./images/create_vm_cpu.png
        :alt: VM CPU Configuration
        :align: center

    **Memory**:

    - Allocate the amount of RAM for the VM.

    **Network**:

    - Configure the network settings, such as the bridge and model. Here, we use the SDN we created in the previous section. 

   .. image:: ./images/create_vm_network.png
       :alt: VM Network Configuration
       :align: center

3. **Review and Confirm**:

   - Review all the configurations.
   - Click **Finish** to create the VM.

4. **Start the VM and Begin Installation**:

   - Once the VM is created, it will appear in the VM list.
   - Select the VM and click **Start**.
   - Open the VM console to begin the installation of the operating system from the ISO image.


Conclusion
----------

By following these steps, you have successfully added an ISO image to your Proxmox VE environment and deployed a virtual machine using this image. This process enables you to set up new VMs with various operating systems efficiently.

