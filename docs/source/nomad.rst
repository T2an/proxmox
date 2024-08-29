Setting Up a Nomad Cluster on Proxmox VE
========================================

In this tutorial, we will guide you through setting up a Nomad cluster using Proxmox VE. The steps include creating containers, installing Nomad, and configuring it to work with Docker.

Prerequisites
-------------

Before starting, ensure you have:

- Proxmox VE installed and configured.
- Access to the Proxmox web interface.
- Basic knowledge of Linux commands.

Step 1: Create Nomad Containers
-------------------------------

Download LXC Template
^^^^^^^^^^^^^^^^^^^^^

1. Go to your image storage in Proxmox and download the **Rocky Linux 9** LXC template.

   .. image:: ./images/nomad_rocky.png
       :alt: Download Rocky Linux 9 Template
       :align: center

Create a Nomad Container
^^^^^^^^^^^^^^^^^^^^^^^^

2. Once the template is downloaded, create a new container on any Proxmox node.

   .. image:: ./images/nomad_tp.png
       :alt: Create Nomad Container
       :align: center

   **Note:** If you plan to mount volumes in the container, you might want to set it as a privileged container. Privileged containers offer more capabilities but are less secure (e.g., container escape risks). For more information, refer to the official Proxmox documentation: `Linux Container <https://pve.proxmox.com/wiki/Linux_Container>`_.

Configure Container Settings
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

3. Choose the Rocky Linux template from your image storage.

   .. image:: ./images/nomad_tp2.png
       :alt: Select Rocky Linux Template
       :align: center

4. Choose the data storage and configure CPU, memory, and network settings as needed. You can modify these settings later if required.

Step 2: Connect to the Container and Install Prerequisites
----------------------------------------------------------

Access the Container
^^^^^^^^^^^^^^^^^^^^

1. Connect to your newly created container using the Proxmox Console.

2. Update the system packages:

   .. code-block:: bash

      sudo dnf update

3. Install necessary tools:

   .. code-block:: bash

      sudo dnf install -y nano openssh-server

4. Enable and start the SSH service:

   .. code-block:: bash

      sudo systemctl enable --now sshd
      sudo systemctl status sshd

   **Optional:** If you want to access the server using the root account (not recommended), you can either:

   - Add your public SSH key to the container.
   - Modify the `/etc/ssh/sshd_config` file to set `PermitRootLogin yes`.

   After making changes, restart the SSH service:

   .. code-block:: bash

      sudo systemctl restart sshd

Step 3: Install Nomad and Docker
--------------------------------

Install Nomad
^^^^^^^^^^^^^

1. Add the HashiCorp repository and install Nomad:

   .. code-block:: bash

      sudo yum install -y yum-utils
      sudo yum-config-manager --add-repo https://rpm.releases.hashicorp.com/RHEL/hashicorp.repo
      sudo yum -y install nomad consul-cni

2. Configure system networking for Nomad:

   .. code-block:: bash

      echo "net.bridge.bridge-nf-call-arptables = 1
      net.bridge.bridge-nf-call-ip6tables = 1
      net.bridge.bridge-nf-call-iptables = 1" | sudo tee /etc/sysctl.d/bridge.conf

   .. code-block:: bash

      sudo sysctl --system

3. Enable and start the Nomad service:

   .. code-block:: bash

      sudo systemctl enable --now nomad.service

Install Docker
^^^^^^^^^^^^^^

1. Add the Docker repository and install Docker:

   .. code-block:: bash

      sudo dnf config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
      sudo dnf install -y docker-ce docker-ce-cli containerd.io

2. Enable and start the Docker service:

   .. code-block:: bash

      sudo systemctl enable --now docker

Step 4: Configure Nomad
-----------------------

Edit the Nomad Configuration File
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

1. The main configuration file for Nomad is located at `/etc/nomad.d/nomad.hcl`. Open this file with your preferred text editor (e.g., `nano` or `vim`).

2. Add the following configuration:

   .. code-block:: hcl

      # Nomad Configuration
      datacenter = "dc1"  # Define the datacenter name
      name       = "nomad-server-1"  # Name of this Nomad server
      data_dir   = "/opt/nomad/data"  # Directory where Nomad stores its state
      bind_addr  = "0.0.0.0"  # Bind address for Nomad server (0.0.0.0 binds to all available IPs)

      # Enable Access Control Lists (ACLs)
      acl {
         enabled = true  # Enable ACLs for security
      }

      # Server Configuration
      server {
         enabled          = true  # Enable this node as a server
         bootstrap_expect = 1  # Number of servers to wait for before bootstrapping
      }

      # Client Configuration
      client {
        enabled = true  # Enable Nomad client functionality
      }

      # Raw Exec Plugin Configuration
      plugin "raw_exec" {
         config {
            enabled = true  # Enable the raw_exec plugin for executing jobs without containers
         }
      }

      # Docker Plugin Configuration
      plugin "docker" {
         config {
            volumes {
               enabled = true  # Enable Docker volume support
            }
            allow_privileged = true
         }
      }

   **Note:** This configuration enables Nomad's Access Control Lists (ACLs). To disable ACLs, simply remove the `acl` section from the configuration.

3. Restart the Nomad service to apply the configuration:

   .. code-block:: bash

      sudo systemctl restart nomad.service

(Optional) Initialize ACLs
^^^^^^^^^^^^^^^^^^^^^^^^^^

If you have enabled Nomad ACLs, initialize them by running:

   .. code-block:: bash

      nomad acl bootstrap

   This will generate an initial management token:

   .. code-block:: bash

      Accessor ID  = e02bd587-1f96-f60f-c79b-4c5beadae911
      Secret ID    = 3fffa295-5f90-acb2-3d47-8ac7add477a6
      Name         = Bootstrap Token
      Type         = management
      Global       = true
      Policies     = n/a
      Create Time  = 2024-08-08 10:41:08.988749307 +0000 UTC
      Create Index = 5651
      Modify Index = 5651

4. Export the `Secret ID` as an environment variable:

   .. code-block:: bash

      export NOMAD_TOKEN="3fffa295-5f90-acb2-3d47-8ac7add477a6"

5. To make the token persistent, add the export command to your shell configuration file (e.g., `.bashrc`, `.bash_profile`, `.zshrc`).

Step 5: Adding a New Client
---------------------------

If you want to add a new client that will be able to execute tasks, you don't have to go through all the same steps. Since we're using Nomad in a virtual machine, we can clone it.

1. Stop the Nomad server.
2. Transform your VM into a template.
3. Clone the template as many times as needed. In this case, we create one Nomad client per host. Choose the "Full Clone" option.

   .. image:: ./images/nomad_clone.png
       :alt: Clone the Nomad Template
       :align: center

On each Nomad node, you need to set the following before boot:

- IP address
- Resources according to the host.

Once you finish cloning, you can start your Nomad servers.

The only file you need to edit on each client is `/etc/nomad.d/nomad.hcl`.

You need to change:

- `name`
- Remove the server section.
- Change the server's IP in the client section to target your Nomad server.

Here is the configuration for the client:

.. code-block:: hcl

   # Nomad Configuration
   datacenter = "dc1"  # Define the datacenter name
   name       = "nomad-client-1"  # Name of this Nomad client
   data_dir   = "/opt/nomad/data"  # Directory where Nomad stores its state

   # Enable Access Control Lists (ACLs)
   acl {
     enabled = true  # Enable ACLs for security
   }

   # Client Configuration
   client {
     enabled = true  # Enable Nomad client functionality
     servers = ["192.168.13.200:4647"]  # List of Nomad servers to connect to
     server_join {
       retry_join = ["192.168.13.200:4647"]
       retry_interval = "5s"
     }
   }

   # Raw Exec Plugin Configuration
   plugin "raw_exec" {
     config {
       enabled = true  # Enable the raw_exec plugin for executing jobs without containers
     }
   }

   # Docker Plugin Configuration
   plugin "docker" {
     config {
       volumes {
         enabled = true  # Enable Docker volume support
       }
       allow_privileged = true
     }
   }

You can then restart your Nomad service:

.. code-block:: bash

   sudo systemctl restart nomad.service

Step 6: Submit a Job with the Web UI
------------------------------------

You should be able to access your Nomad web UI at:

   ``http://<nomad_address>:4646``

If you have enabled ACLs, you will need to provide your token.

Next, navigate to **Jobs > Run Job**.

   .. image:: ./images/nomad_job.png
      :alt: Run Job
      :align: center

From here, you can write your own job or choose a template.

You have two main ways to run a job:

- **Raw Exec**
- **Docker Exec**

You may prefer using **Docker Exec**, which allows you to create a layer between your server and your job execution, protecting it in case of misconfiguration or dependency issues.

Here is an example of a Docker job:

.. code-block:: hcl

   job "Simulation" {
     # Specifies the datacenter where this job should be run
     # This can be omitted and it will default to ["*"]
     datacenters = ["*"]
  
     type = "batch"

     # A group defines a series of tasks that should be co-located
     # on the same client (host). All tasks within a group will be
     # placed on the same host.
     group "servers" {    
       # Tasks are individual units of work that are run by Nomad.
       task "simulation" {
         driver = "docker"

         config {
           image = "nicolasfarabegoli/alchemist-experiments-bootstrap-simulation:1.2.1"
         }

         # Specify the maximum resources required to run the task
         resources {
           cpu    = 50000
           memory = 64000
         }
       }
     }
   }

Step 7: Connect to Nomad Using the CLI on Your Working Machine
--------------------------------------------------------------

Install Nomad on your working machine and set the following environment variables:

.. code-block:: bash

   export NOMAD_ADDR=http://192.168.13.201:4646

   # If you have enabled ACLs, also set:
   export NOMAD_TOKEN=8485ed276-457a-66d5-5c24-6324d545538fb

You should now be able to submit jobs to your Nomad cluster. Verify your connection by typing:

.. code-block:: bash

   nomad node status


Conclusion
----------

By following these steps, you have successfully set up a Nomad cluster on Proxmox VE. Your cluster is now ready to manage containerized workloads with Nomad and Docker.
