---

copyright:

  years:  2016, 2020

lastupdated: "2020-04-14"

keywords: Veeam, Veeam configuration, order Veeam

subcollection: vmware-solutions


---

{:external: target="_blank" .external}
{:tip: .tip}
{:note: .note}
{:important: .important}

# Ordering Veeam
{: #veeam_ordering}

You can include the Veeam service with a new vCenter Server instance or add the service to your existing vCenter Server instance.

## Ordering Veeam for a new instance
{: #veeam_ordering-new}

When you [order the instance](/docs/vmwaresolutions?topic=vmware-solutions-vc_orderinginstance#vc_orderinginstance-procedure), scroll down to the **Optional Services** section and click **Veeam** in the **Business Continuity and Migration** category. Follow the steps to add the service to your instance.

## Ordering Veeam for an existing instance
{: #veeam_ordering-existing}

On the [instance details page](/docs/vmwaresolutions?topic=vmware-solutions-vc_viewinginstances), click **Services** on the left navigation pane, click **Veeam** in the **Business Continuity and Migration** category, and then click **Add**. Follow the steps to add the service to your instance.

## Veeam service configuration
{: #veeam_ordering-config}

When you order the service, provide the following settings.

### Number of VMs to License
{: #veeam_ordering-config-vms}

At least 10 VMs are required for license management.

### Storage Size
{: #veeam_ordering-config-storage-size}

The capacity that meets your storage needs. For considerations to estimate storage size, see [Estimating Repository Capacity](https://bp.veeam.expert/repository_server/repository_planning/repository_planning_sizing){:external}.

### Storage Performance
{: #veeam_ordering-config-storage-performance}

The IOPS (input/output operations per second) per GB based on your workload requirements.

## Related links
{: #veeam_ordering-related}

* [Managing Veeam](/docs/vmwaresolutions?topic=vmware-solutions-managingveeam)
* [Ordering, viewing, and removing services for vCenter Server instances](/docs/vmwaresolutions?topic=vmware-solutions-vc_addingremovingservices)
* [Managed Backup Services](/docs/vmwaresolutions?topic=vmware-solutions-managing_veeam_services)
* [Veeam website](https://www.veeam.com/){:external}
* [Veeam Help Center](https://www.veeam.com/documentation-guides-datasheets.html){:external}
