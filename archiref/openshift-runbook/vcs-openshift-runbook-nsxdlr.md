---

copyright:

  years:  2019

lastupdated: "2019-09-03"

subcollection: vmware-solutions


---

{:external: target="_blank" .external}
{:tip: .tip}
{:note: .note}
{:important: .important}

# OpenShift NSX DLR configuration
{: #openshift-runbook-runbook-nsxdlr-intro}

This section details the NSX distributed logical router used to support the OpenShift 4.1 environment. To use this information, you must understand how to create these components and add the configuration.

Review [Distributed Logical Router](https://docs.vmware.com/en/VMware-NSX-Data-Center-for-vSphere/6.4/com.vmware.nsx.admin.doc/GUID-A20103B0-ABA1-4884-8EC3-287874E23181.html){:external}. PowerNSX commands are provided if you would want to use this method.

## NSX DLR
{: #openshift-runbook-runbook-nsxdlr-config}

The NSX distributed logical router runs as a kernel module in the hypervisor of each host and is optimized for East-West routing. Optionally, a DLR Control VM can be installed. The DLR Control VM is needed to peer with NSX Edges and NSX Controllers for dynamic routing (OSPF or BGP) updates. In this deployment static routing is used, however, we deploy DLR Control VMs in case you require to use a routing protocol.

The NSX DLR Control VMs are configured as an active/passive pair of appliances. The size of a DLR Control VM is always "compact" of the configuration process and the NSX DLR is connected to the OpenShift Edge.

| Component | Configuration |
|-----------|---------------|
| CPU       | 1 vCPU        |
| RAM       | 1 GB          |
| Disk      | 4.5 GB VMDK resident on shared storage with 4 GB swap |
{: caption="Table 1. NSX DLR deployment" caption-side="top"}

Since the NSX DLR Control VMs are configured as active/passive, you must create vSphere Distributed Resource Scheduler (DRS) anti-affinity rules to ensure that NSX Edges do not run on the same host as their respective peer appliance.

| Field     | Value         |
|-----------|---------------|
| Name      | OpenShift-DLR |
| Type      | Separate virtual machines |
| Members   | OpenShift-DLR-0 <br> OpenShift-DLR-1 |
{: caption="Table 2. NSX DLR anti-affinity rules" caption-side="top"}

## NSX DLR interfaces
{: #openshift-runbook-runbook-nsxdlr-interfaces}

The NSX DLR is deployed with an interface uplink that is connected to the Transit network that connects to the OpenShift NSX Edge and an internal interface to the OpenShift Logical Switch.

| Interface name| Interface type | IP addresses | Port group / Logical switch |
| --- | ---| --- | --- |
| OpenShift-LS | Internal | 192.168.133.1 | OpenShift-LS |
| OpenShift-Transit | Uplink | 192.168.100.2/24 | OpenShift-Transit  |
{: caption="Table 3. Configuration for NSX DLR - interfaces" caption-side="top"}

## NSX DLR firewall
{: #openshift-runbook-runbook-nsxdlr-firewall}

The DLR firewall is configured open.

| Firewall rule | Source | Destination | Service | Action |
| --- | --- | --- | --- | --- |
| Default | any | any | any | Accept |
{: caption="Table 4. Configuration for NSX DLR - NSX firewalls" caption-side="top"}

## NSX DLR routes
{: #openshift-runbook-runbook-nsxdlr-routes}

On the Edge, the default route is configured to be to the transit network connection to the OpenShift Edge.

### Global configuration
{: #openshift-runbook-runbook-nsxdlr-global-config}

| Global configuration | vNIC | Gateway IP |
| --- | --- | --- |
| Default Gateway | Transit-Uplink | 192.168.100.1 |
{: caption="Table 5. Configuration for NSX Edge - global configuration" caption-side="top"}

## NSX DHCP relay
{: #openshift-runbook-runbook-nsxdlr-dhcprelay}

For the OpenShift 4.1 environment, the connection to the DHCP Service runs on the OpenShift Edge.

| DCHP relay | Value |
| :--- | --- |
| IP address  | 192.168.101.1 |
{: caption="Table 6. Configuration for NSX Edge - DHCP relay" caption-side="top"}

| DCHP relay | Value |
| :--- | --- |
| Name  | OpenShift-LS |
| Gateway IP | 192.168.133.1 |
{: caption="Table 7. Configuration for NSX Edge - DHCP relay agent" caption-side="top"}

## PowerNSX commands
{: #openshift-runbook-runbook-nsxdlr-powernsx}

This section provides example PowerNSX commands to script the provisioning and configuration of the NSX DLR. Use the following table to document the parameters you will need for your deployment, examples are shown that match the deployment described previously.

| Parameters | Example | Your Deployment |
| --- | --- | --- |
| vCenter Server IP address | | |
| vCenter User | | |
| vCenter Password | | |
| Transit Logical Switch| OpenShift-Transit | |
| OpenShift Logical Switch | OpenShift-LS| |
| Uplink IP address | 192.168.100.2 | |
| Internal IP address | 192.168.133.1 | |
| DLR Name | OpenShift-DLR | |
| vCenter Server instance Cluster | cluster1 | |
| vCenter Server instance Datastore | vsanDatastore | |

{: caption="Table 8. PowerNSX DLR Parameters" caption-side="top"}

```powernsx
# Connect to NSX Manager
Connect-NsxServer -vCenterServer <IP_Address> -User <UserName> -Password '<Password>'

# Create DLR interface specifications and then create DLR
$uplinkLif = New-NsxLogicalRouterInterfaceSpec -Name ToESG -Type uplink -ConnectedTo (Get-NsxLogicalSwitch OpenShift-Transit) -PrimaryAddress 192.168.100.2 -SubnetPrefixLength 24
$internalLif = New-NsxLogicalRouterInterfaceSpec -Name ToLS -Type internal -ConnectedTo (Get-NsxLogicalSwitch OpenShift-LS) -PrimaryAddress 192.168.133.1 -SubnetPrefixLength 24
$haLif = New-NsxLogicalRouterInterfaceSpec -Name ToHA -Type internal -ConnectedTo (Get-NsxLogicalSwitch OpenShift-HA)
New-NsxLogicalRouter -Name OpenShift-DLR -ManagementPortGroup (Get-NsxLogicalSwitch OpenShift-HA) -Interface $uplinkLif,$internalLif,$haLif -Cluster (Get-Cluster cluster1) -EnableHA -Datastore (Get-Datastore vsanDatastore)

# Configure the default gateway on the DLR to be the ESG
$uplinkVnic = Get-NsxLogicalRouter OpenShift-DLR | Get-NsxLogicalRouterInterface "ToESG"
$uplinkVnicId = $uplinkVnic.index
Get-NsxLogicalRouter OpenShift-DLR | Get-NsxLogicalRouterRouting | Set-NsxLogicalRouterRouting -DefaultGatewayVnic $uplinkVnicId -DefaultGatewayAddress 192.168.133.1 -Confirm:$false

# Configure the default firewall rule on the DLR to "allow"
$dlr = Get-NsxLogicalRouter OpenShift-DLR
$dlr.features.firewall.defaultpolicy.action = "Accept"
$dlr | Set-NsxLogicalRouter -Confirm:$false

# Disconnect
Disconnect-NsxServer
```

## Related links
{: #openshift-runbook-runbook-nsxdlr-related}

* [OpenShift Bastion host setup](/docs/services/vmwaresolutions?topic=vmware-solutions-openshift-runbook-runbook-bastion-intro)
* [Red Hat OpenShift 4.1 user provider infrastructure installation](/docs/services/vmwaresolutions?topic=vmware-solutions-openshift-runbook-runbook-install-intro)