---
title: Concepts - Storage
description: Learn about storage capacity, storage policies, fault tolerance, and storage integration in Azure VMware Solution private clouds.
ms.topic: conceptual
ms.custom: contperf-fy21q4
ms.date: 08/31/2021
---

# Azure VMware Solution storage concepts

Azure VMware Solution private clouds provide native, cluster-wide storage with VMware vSAN. Local storage from each host in a cluster is used in a vSAN datastore, and data-at-rest encryption is available and enabled by default. You can use Azure Storage resources to extend storage capabilities of your private clouds.

## vSAN clusters

Local storage in each cluster host is claimed as part of a vSAN datastore. All diskgroups use an NVMe cache tier of 1.6 TB with the raw, per host, SSD-based capacity of 15.4 TB. The size of the raw capacity tier of a cluster is the per host capacity times the number of hosts. For example, a four host cluster provides 61.6-TB raw capacity in the vSAN capacity tier.

Local storage in cluster hosts is used in the cluster-wide vSAN datastore. All datastores are created as part of private cloud deployment and are available for use immediately. The **cloudadmin** user and all users assigned to the CloudAdmin role can manage datastores with these vSAN privileges:

- Datastore.AllocateSpace
- Datastore.Browse
- Datastore.Config
- Datastore.DeleteFile
- Datastore.FileManagement
- Datastore.UpdateVirtualMachineMetadata

>[!IMPORTANT]
>You can't change the name of datastores or clusters. You can select a cluster name other than "Cluster-n" where n > 1 when provisioning from somewhere other than the portal (AzureCLI or PowerShell).

## Storage policies and fault tolerance

The default storage policy is set to RAID-1 (Mirroring), FTT-1, and thick provisioning. Unless you adjust the storage policy or apply a new policy, the cluster grows with this configuration. To set the storage policy, see [Configure storage policy](configure-storage-policy.md).

In a three-host cluster, FTT-1 accommodates a single host's failure. Microsoft governs failures regularly and replaces the hardware when events are detected from an operations perspective.

:::image type="content" source="media/concepts/vsphere-vm-storage-policies.png" alt-text="Screenshot that shows the vSphere Client VM Storage Policies.":::


|Provisioning type  |Description  |
|---------|---------|
|**Thick**      | Reserved or pre-allocated storage space. It protects systems by allowing them to function even if the vSAN datastore is full because the space is already reserved. For example, if you create a 10-GB virtual disk with thick provisioning. In that case, the full amount of virtual disk storage capacity is pre-allocated on the physical storage of the virtual disk and consumes all the space allocated to it in the datastore. It won't allow other virtual machines (VMs) to share the space from the datastore.         |
|**Thin**      | Consumes the space that it needs initially and grows to the data space demand used in the datastore. Outside the default (thick provision), you can create VMs with FTT-1 thin provisioning. For deduplication setup, use thin provisioning for your VM template.         |

>[!TIP]
>If you're unsure if the cluster will grow to four or more, then deploy using the default policy.  If you're sure your cluster will grow, then instead of expanding the cluster after your initial deployment, we recommend to deploy the extra hosts during deployment. As the VMs are deployed to the cluster, change the disk's storage policy in the VM settings to either RAID-5 FTT-1 or RAID-6 FTT-2. 
>
>:::image type="content" source="media/concepts/vsphere-vm-storage-policies-2.png" alt-text="Screenshot showing the RAID-5 FTT-1 and RAID-6 FTT-2 options highlighed.":::


## Data-at-rest encryption

vSAN datastores use data-at-rest encryption by default using keys stored in Azure Key Vault. The encryption solution is KMS-based and supports vCenter Server operations for key management.  When a host is removed from a cluster, all data on SSDs is invalidated immediately.

## Azure storage integration

You can use Azure storage services in workloads running in your private cloud. The Azure storage services include Storage Accounts, Table Storage, and Blob Storage. The connection of workloads to Azure storage services doesn't traverse the internet. This connectivity provides more security and enables you to use SLA-based Azure storage services in your private cloud workloads.

## Alerts and monitoring

Microsoft provides alerts when capacity consumption exceeds 75%. In addition, you can monitor capacity consumption metrics that are integrated into Azure Monitor. For more information, see [Configure Azure Alerts in Azure VMware Solution](configure-alerts-for-azure-vmware-solution.md).

## Next steps

Now that you've covered Azure VMware Solution storage concepts, you may want to learn about:

- [Attach disk pools to Azure VMware Solution hosts (Preview)](attach-disk-pools-to-azure-vmware-solution-hosts.md) - You can use disks as the persistent storage for Azure VMware Solution for optimal cost and performance.

- [Configure storage policy](configure-storage-policy.md) - Each VM deployed to a vSAN datastore is assigned at least one VM storage policy. You can assign a VM storage policy in an initial deployment of a VM or when you perform other VM operations, such as cloning or migrating.

- [Scale clusters in the private cloud][tutorial-scale-private-cloud] - You can scale the clusters and hosts in a private cloud as required for your application workload. Performance and availability limitations for specific services should be addressed on a case by case basis.

- [Azure NetApp Files with Azure VMware Solution](netapp-files-with-azure-vmware-solution.md) - You can use Azure NetApp to migrate and run the most demanding enterprise file-workloads in the cloud: databases, SAP, and high-performance computing applications, with no code changes. 

- [vSphere role-based access control for Azure VMware Solution](concepts-identity.md) - You use vCenter Server to manage VM workloads and NSX-T Manager to manage and extend the private cloud. Access and identity management use the CloudAdmin role for vCenter Server and restricted administrator rights for NSX-T Manager.


<!-- LINKS - external-->

<!-- LINKS - internal -->
[tutorial-scale-private-cloud]: ./tutorial-scale-private-cloud.md
[concepts-identity]: ./concepts-identity.md
