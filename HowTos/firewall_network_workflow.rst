.. meta::
  :description: Firewall Network Workflow
  :keywords: AWS Transit Gateway, AWS TGW, TGW orchestrator, Aviatrix Transit network, Transit DMZ, Egress, Firewall, Firewall Network, FireNet


=========================================================
Firewall Network (FireNet)  Workflow
=========================================================

For questions about FireNet, check out `FireNet FAQ. <https://docs.aviatrix.com/HowTos/firewall_network_faq.html>`_

1. Create a Security VPC
------------------------------------------------

We recommend you to use the Aviatrix Useful Tool to create a VPC for FireNet deployment. 

Select "Aviatrix FireNet VPC" option when creating a security VPC. 

2. Subscribe to AWS Marketplace
--------------------------------------

If you have not already done so, follow the Go link to subscribe the VM-Series in AWS Marketplace.

Do not launch the firewall instance from AWS Console as you launch it in the following steps. . 

3. Create a Firewall Domain
-----------------------------

This step creates a Security Domain with Firewall Domain option. 

Go to TGW Orchestrator -> Plan -> Create a Security Domain to create one as shown below.

|firewall_domain|

4. Launch Aviatrix FireNet Gateway
------------------------------------------

This step leverage the Transit Network workflow to launch one Aviatrix gateway for FireNet deployment. 

C5x.large is the minimum Aviatrix gateway instance size for FireNet deployment as it requires `4 interfaces. <https://docs.aviatrix.com/HowTos/firewall_network_faq.html#what-is-the-minimum-gateway-instance-size-for-firenet-deployment>`_

If your deployment requires 2-AZ HA, go through Transit Network -> Setup to launch one Aviatrix gateway and enable HA which effectively launches HA gateway (the second gateway) in a different AZ.


5. Enable Aviatrix FireNet Gateway
---------------------------------------------

This step configures the gateway launched in Step 4 for FireNet function. If you have HA enabled, it
automatically sets up the HA gateway for FireNet deployment.

.. tip ::

  If you do not see any gateways in the drop down menu, refresh the browser to load.

6. Attach Aviatrix FireNet gateway to TGW Firewall Domain
-------------------------------------------------------------

This step requires you have already created a Security Domain with Firewall attribute enabled.

When this step is completed, you have built the network infrastructure for FireNet deployment. This step may take a few minutes. 


|gw_launch|


7a. Launch and Associate Firewall Instance
--------------------------------------------

This approach is recommended if this is the first Firewall instance to be attached to the gateway. 

This step launches a VM-Series and associate it with one of the FireNet gateway. Note the VM-Series and the 
associated FireNet gateway must be in the same AZ.

7a.1 Launch and Attach
##########################

==========================================      ==========
**Setting**                                     **Value**
==========================================      ==========
VPC ID                                          The Security VPC created in Step 1.
Gateway Name                                    The primary FireNet gateway.
Firewall Instance Name                          The name that will be displayed on AWS Console.
Firewall Image                                  The AWS AMI that you have subscribed in Step 2.
Management Interface Subnet.                    Select the subnet whose name contains "gateway and firewall management"
Egress Interface Subnet                         Select the subnet whose name contains "FW-ingress-egress".
Key Pair Name (Optional)                        The .pem file name for SSH access to the firewall instance.
Attach (Optional)                               By selecting this option, the firewall instance is inserted in the data path to receive packet. If this is the second firewall instance for the same gateway and you have an operational FireNet deployment, you should not select this option as the firewall is not configured yet. You can attach the firewall instance later at Firewall Network -> Advanced page. 
==========================================      ==========

7a.2 Launch and Associate More
#################################

Repeat Step 7a.1 to launch the second firewall instance to associate with the HA FireNet gateway. 
Or repeat this step to launch more firewall instances to associate with the same FireNet gateway.

7a.3 Example Setup for "Allow All" Policy
###########################################

After a firewall instance is launched, wait for 15 minutes for it to come up. 

You can follow `this example configuration guide <https://docs.aviatrix.com/HowTos/config_paloaltoVM.html>`_ to build
a simple "Allow All" policy on the firewall instance for a test validation that traffic is indeed being routed
to firewall instance. 


7b. Associate an Existing Firewall Instance
--------------------------------------------

This step is the alternative step to Step 7a. If you already launched VM-Series from AWS Console, you can still
associate it with the FireNet gateway. 

If firewall instance is by a vendor other than Palo Alto Network, for example, Checkpoint or Fortinet, you should launch the firewall 
instances from AWS Console and associate them to the Aviatrix FireNet gateway. The `Management Interface Subnet` may be the same as the `Egress Interface Subnet`


8. Specify Security Domain for Firewall Inspection
-----------------------------------------------------

The method to specify a Spoke VPC that needs inspection is to define a connection policy of the Security Domain where the  Spoke VPC is a member to the Firewall Domain.

For example, if you wish to inspect traffic between on-prem to VPC, connect Aviatrix Edge Domain to the 
Firewall Domain. This means on-prem traffic to any Spoke VPC is routed to firewall first and then it is forwarded
to the destination Spoke VPC. Conversely, any Spoke VPC traffic destined to on-prem is routed to firewall first and then forwarded to on-prem. 



.. |firewall_domain| image:: firewall_network_workflow_media/firewall_domain.png
   :scale: 30%

.. |gw_launch| image:: firewall_network_workflow_media/gw_launch.png
   :scale: 30%

.. disqus::
