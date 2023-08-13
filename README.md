Version: 1.1
14 August 2023
hardev.sanghera@nutanix.com

TO GET STARTED
Assuming a linux Workstation
1. unzip the compressed to a directory or git clone
2. cd to where the files are
2. Edit vars/vars.yaml
   - edit items 1 - 8 to reflect the two clusters you have deployed
3. Upload the qcow2 images to the Primary cluster:
   - ansible-playbook playbooks/get_image_param.yaml
   - wait for the uploads to complete - this can take 10 or so minutes
4. run the main playbook
   - ansible-playbook main.yaml
   - this does a lot and can take 25 or so minutes
5. The completion message will look something like:
   ok: [localhost] => {
    "msg": [
        "All done, now you should:",
        "  - Make sure you Enable Disister Recovery in the PC in cluster_A",
        "  - Create a Protecion Policy for CCLM",
        "  - Create a Recovery Plan for CCLM",
        "  - Demo live migration of SQLSERVER-CCLM-vm from cluster_A to cluster_B over the stretched vlan"
    ]
   } 
   - See WHAT YOU GET below for details of what was deployed and configured
6. In PC of Primary:
    - enable Disaster Recovery (via settings)
    - configure a Protecion Policy
    - configure an Execution Plan
7. Do your demo:
    - Although VMs have been provided with software (SQL Server and/or SQL Studio Manager) no client application has been provided.
    - You could obtain/write a client application that utilizes the SQL Server database while you live migrate it, or use ping!

WHAT YOU GET
- A single PC (of the primary cluster) with cluster_A and cluster_B connected
- A Windows Server VM with SQL Server and SQL Studio Manager installed - connected to vlan: CCLMvlan (the stretch vlan)
  - Only this VM can be live migrated
- A Windows Workstation with SQL Studio Manager installed - connected to vlan: CCLMvlan
  - Although called a Workstation it's a server guest OS!
- Both VMs are enabled for RDP and ping
- cluster_A and cluster_B have:
  - storage container:     CCLMcontainer
  - managed vlan/network:  CCLMvlan
  - category:              CCLMcat

PRE-REQS
- Two HPOC clusters
  - Deploy with runbook: NCS (this gets you PC and well known names for networks, storage pool and others
  - A stretch vlan between the two clusters - this will be the  "Secondary" network information as reported in the RX HPOC booking email
    - You have to raise a Jira ticket requesting that the stretch vlan be setup between the two clusters you have booked

VERSIONS
Tested and working with
- AOS:                      6.5.2.5 with Bundled AHV 20220304.342
- Prism Central:            pc.2022.6.0.3
- Ansible:
                            ansible [core 2.13.7]
                            python version = 3.8.10 (default, May 26 2023, 14:05:08) [GCC 9.4.0]
                            jinja version = 3.1.2
- nutanix.ncp collection:   1.9.0

ANSIBLE WORKSTATION 
You will need access to one - build your own or:
- Upload OVA to the cluster_A PC
  - available at "http://10.42.194.11/users/hardev.sanghera/images/ansible-workstation-ubuntu-20.04.5.ova.zip"
  - this is the HPOC file server
  - you must unzip the above zip file first - do it to the Download folder from eg. a prallels desktop which is close to your clusters
    that way the uploads are much faster.
  - Once the ova has been uploaded and confirmed you can deploy a VM from the ova
    - it's an Ubunto 20.04 workstation
    - user:pw  ubuadmin:nutanix/4u
    - it is enabled for DP (yes, RDP)
    - upgrade ansible collection nutanix.ncp (ansible-galaxy collection install nutanix.ncp --force)

NOTES
The vswitches in the HPOC clusters report their name as "br0", however the API calls accept "vs0".
