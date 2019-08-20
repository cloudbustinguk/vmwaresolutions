---

copyright:

  years:  2016, 2019

lastupdated: "2019-07-22"

keywords: single-node trial, data protection DR, tech specs data protection DR

subcollection: vmware-solutions


---

{:external: target="_blank" .external}
{:tip: .tip}
{:note: .note}
{:important: .important}

# Single-node Trial for Data Protection and Disaster Recovery 概述
{: #dr_backup_bundle_overview}

利用 Single-node Trial for Data Protection and Disaster Recovery，可以试用 {{site.data.keyword.cloud}} 以将 VMware 工作负载迁移和恢复到 {{site.data.keyword.cloud_notm}}。此外，Single-node Trial for Data Protection and Disaster Recovery 包含 Veeam on {{site.data.keyword.cloud_notm}}、VMware HCX on {{site.data.keyword.cloud_notm}} 和 Zerto on {{site.data.keyword.cloud_notm}} 服务。

单节点试用版是 VMware vCenter Server on {{site.data.keyword.cloud_notm}} 的试用版，提供了单租户 VMware 平台，可使用与内部部署环境中相同的工具来管理该平台。您可以利用云的速度和规模，同时保持内部部署提供的相同级别的控制和可视性。

该试用版旨在使用 vCenter Server 来迁移最多 20 个简单的开发或测试工作负载。自动化功能将在 {{site.data.keyword.cloud_notm}} 中安装和配置 VMware HCX，提供内部部署 HCX 激活密钥，并在几小时内安装 Veeam on {{site.data.keyword.cloud_notm}} 和 Zerto on {{site.data.keyword.cloud_notm}}。您可以使用 Veeam on {{site.data.keyword.cloud_notm}} 和 Zerto on {{site.data.keyword.cloud_notm}} 来备份和复制 20 个虚拟机 (VM)。

Single-node Trial for Data Protection and Disaster Recovery 仅用于概念验证 (POC)。不要在此环境中运行生产工作负载。不支持添加和除去主机和集群、订购其他附加组件服务以及应用更新等管理功能。
{:important}

部署单节点试用版实例之后，可以使用 [IBM Analytics Cloud Expert Services](https://www.ibm.com/analytics/us/en/services/cloud-expert-services.html){:external} 提供的 [IBM On Demand Consulting for Hybrid Cloud](https://public.dhe.ibm.com/software/data/sw-library/services/ODC.pdf){:external} 来辅助使用实例。此外，[{{site.data.keyword.cloud_notm}} Garage 服务](https://www.ibm.com/cloud/garage/){:external}可以通过最新的云本机实践帮助您加速应用程序现代化。

此试用版最长可使用 90 天。每月周期性费用根据您的计费安排来计费，而不是在订购实例时计费。如果在计费周期的最后一天或之前未取消实例，那么将向您收取下个月的费用。在试用 90 天后可能会收取四个月的费用，除非在第四个月开始之前完成取消。
{:note}

试用版使用时间到期后，可以删除此环境，然后供应满足您的容量需求的新环境。


## Single-node Trial for Data Protection and Disaster Recovery 实例的技术规范
{: #dr_backup_bundle_overview-tech-specs}

Single-node Trial for Data Protection and Disaster Recovery 实例中包含以下组件。

标准化硬件配置的可用性和定价可能会因选择用于部署的 {{site.data.keyword.CloudDataCent_notm}} 而有所不同。
{:note}

### 裸机服务器
{: #dr_backup_bundle_overview-bare-metal}

Skylake 双 Intel Xeon Gold 5120 处理器 / 共 28 个核心，2.2 GHz（带 384 GB RAM），最多支持约 20 个 VM

#### CPU 超配
{: #dr_backup_bundle_overview-cpu}

16:1 CPU 超配，适用于 vCenter Server 管理、HCX 和 20 个客户工作负载 VM

#### RAM 超配
{: #dr_backup_bundle_overview-ram}

* 1.22:1 RAM 超配，适用于 20 个工作负载 VM（每个 VM 8 GB）的客户部署
* 1:1（无超配），适用于 9 个工作负载 VM（每个 VM 8 GB）的客户部署

### NFS 存储器
{: #dr_backup_bundle_overview-nfs-storage}

* 2 TB，用于管理
* 1 TB，用于客户工作负载（20 个客户 VM）

### Single-node Trial for Data Protection and Disaster Recovery 实例的联网规范
{: #dr_backup_bundle_overview-networking-specs}

订购了以下联网组件：
*  10 Gbps 双公用和专用网络上行链路
*  三个 VLAN（虚拟 LAN）：一个公用 VLAN 和两个专用 VLAN
*  一个 VXLAN（虚拟可扩展 LAN），带 DLR（分布式逻辑路由器），用于处理连接到第 2 层 (L2) 网络的本地工作负载之间的潜在东-西通信。VXLAN 部署为样本路由拓扑，可以基于该拓扑进行构建，或者进行修改或将其除去。还可以通过将更多 VXLAN 连接到 DLR 上的新逻辑接口来添加安全区域。
*  两个 VMware NSX Edge 服务网关：
  * 用于出站 HTTPS 管理流量的安全管理服务 VMware NSX Edge 服务网关 (ESG)，由 IBM 部署为管理联网拓扑的一部分。IBM 管理 VM 使用此 ESG 来与自动化相关的特定外部 IBM 管理组件进行通信。

    您无法访问此 ESG，也无法使用此 ESG。如果对其进行修改，那么可能无法在 {{site.data.keyword.vmwaresolutions_short}} 控制台中管理 Single-node Trial for Data Protection and Disaster Recovery 实例。此外，请注意，使用防火墙或禁用与外部 IBM 管理组件的 ESG 通信将导致 {{site.data.keyword.vmwaresolutions_short}} 无法使用。
{:important}
  * 用于出站和入站 HTTPS 工作负载流量的客户管理的安全 VMware NSX Edge 服务网关，由 IBM 部署为模板，您可修改此模板来提供 VPN 访问或公共访问。

### 虚拟服务器实例
{: #dr_backup_bundle_overview-vsi}

订购了以下虚拟服务器实例 (VSI)：

* 用于 IBM CloudBuilder 的 VSI，在完成实例部署后取消。
* 用于 Microsoft Active Directory (AD) 的 Microsoft Windows Server VSI 已部署并且可进行查找。此 VSI 充当在其中注册主机和 VM 的实例的 DNS。
* Zerto on {{site.data.keyword.cloud_notm}} 的 VSI - Zerto Virtual Manager。
* 一个 VSI，带 Veeam Backup and Replication 9.5 附加组件和 Veeam Availability Suite 9.5。

### IBM 提供的许可证和费用
{: #dr_backup_bundle_overview-license-and-fee}

Single-node Trial for Data Protection and Disaster Recovery 实例订单中包含以下许可证。

* vCenter Server 许可证：
  * VMware vSphere Enterprise Plus 6.7u2
  * VMware vCenter Server 6.5
  * VMware NSX Service Providers Advanced Edition 6.4
* {{site.data.keyword.cloud_notm}} Automation Manager 3.1.2

Single-node Trial for Data Protection and Disaster Recovery 实例不支持自带许可证。
{:note}

## VMware HCX on IBM Cloud 的技术规范
{: #dr_backup_bundle_overview-hcx-tech-specs}

Single-node Trial for Data Protection and Disaster Recovery 包含 HCX on {{site.data.keyword.cloud_notm}}。HCX on {{site.data.keyword.cloud_notm}} 服务中订购并包含了以下组件：

内部部署 HCX 实例仅包括许可和激活。
{:note}

### VMware NSX Edge 服务网关 (ESG) 的主动/被动对，以用于 HCX 管理
{: #dr_backup_bundle_overview-esg}

* CPU：6 个 vCPU
* RAM：8 GB
* 磁盘：3 GB VMDK

### HCX 管理设备 - 虚拟机
{: #dr_backup_bundle_overview-hcx-mgmt-appliance}

* CPU：4 个 vCPU
* RAM：12 GB
* 磁盘：60 GB VMDK

配置期间根据需要部署了更多 HCX 设备，以用于 L2 连接、WAN 优化和网关连接。

### HCX on IBM Cloud 的联网规范
{: #dr_backup_bundle_overview-hcx-networking-specs}

* 一个具有 16 个 IP 地址的公用可移植子网
* 两个具有 64 个 IP 地址的专用可移植子网
* 专用可移植 vMotion 子网中的 8 个 IP 地址

## Veeam on {{site.data.keyword.cloud_notm}} 的技术规范
{: #dr_backup_bundle_overview-veeam-tech-specs}

Single-node Trial for Data Protection and  Disaster Recovery 包含 Veeam on {{site.data.keyword.cloud_notm}}。Veeam on {{site.data.keyword.cloud_notm}} 服务中订购并包含了以下组件。

* Veeam Availability Suite 的 25 包许可证
* 4000 GB 存储器
* 0.25 IOPS/GB 存储器性能
* Windows Server 2016 Standard Edition（64 位）
* 4 个 2.0 GHz 核心
* 8 GB RAM
* 1 Gbps 专用网络上行链路
* 100 GB 磁盘 (SAN)

## Zerto on {{site.data.keyword.cloud_notm}} 的技术规范
{: #dr_backup_bundle_overview-zerto-tech-specs}

Single-node Trial for Data Protection and Disaster Recovery 包含 Zerto on {{site.data.keyword.cloud_notm}}。Zerto on {{site.data.keyword.cloud_notm}} 服务中订购并包含了以下组件。

* Zerto Virtual Replication V6.5u3 许可证
* 一个虚拟服务实例 (VSI) - Zerto Virtual Manager
* 2 个 2.0 GHz 核心
* 4 GB RAM
* Windows Server 2016 Standard Edition（64 位）
* 100 GB (SAN) 磁盘

## IBM Cloud Automation Manager 的技术规范
{: #dr_backup_bundle_overview-cam-tech-specs}

在所有 Single-node Trial for Data Protection and Disaster Recovery 实例上，都会使用开发/测试拓扑安装 {{site.data.keyword.cloud_notm}} Automation Manager 3.1.2。有关 {{site.data.keyword.cloud_notm}} Automation Manager 的更多信息，请参阅 [{{site.data.keyword.cloud_notm}} Automation Manager 文档](https://www.ibm.com/support/knowledgecenter/en/SS2L37_3.1.2.0/kc_welcome.html){: external}。

## 相关链接
{: #dr_backup_bundle_overview-related}

* [VMware HCX 资源](https://hcx.vmware.com/#/docs){:external}
* [VMware HCX 用户指南](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-BFD7E194-CFE5-4259-B74B-991B26A51758.html){:external}
* [管理 Veeam on {{site.data.keyword.cloud_notm}}](/docs/services/vmwaresolutions?topic=vmware-solutions-managingveeam)
* [管理 Zerto on {{site.data.keyword.cloud_notm}}](/docs/services/vmwaresolutions?topic=vmware-solutions-managingzertodr)