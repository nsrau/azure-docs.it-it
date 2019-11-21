---
title: Azure Migrate appliance architecture
description: Provides an overview of the Azure Migrate appliance used in server assessment and migration.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: raynew
ms.openlocfilehash: 49545ca6c43c272c3fd84f8bee59b8617aae136d
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74232561"
---
# <a name="azure-migrate-appliance"></a>Appliance Azure Migrate

This article describes the Azure Migrate appliance. You deploy the appliance when you use Azure Migrate Assessment and Migration tools to discover, assess and migrate apps, infrastructure, and workloads to Microsoft Azure. 

[Azure Migrate](migrate-services-overview.md) provides a central hub to track discovery, assessment and migration of your on-premises apps and workloads, and private/public cloud VMs, to Azure. L'hub fornisce gli strumenti di Azure Migrate per la valutazione e la migrazione, nonché offerte di ISV terzi.



## <a name="appliance-overview"></a>Appliance overview

The Azure Migrate appliance types and usage are as follows.

**Deployed as** | **Usata per** | **Dettagli**
--- | --- |  ---
Macchina virtuale VMware | VMware VM assessment with the Azure Migrate Assessment tool.<br/><br/> VMware VM agentless migration with the Azure Migrate Server Migration tool | Download OVA template and import to vCenter Server to create the appliance VM.
Macchina virtuale Hyper-V | Hyper-V VM assessment with the Azure Migrate Assessment tool. | Download zipped VHD and import to Hyper-V to create the appliance VM.

## <a name="appliance-access"></a>Appliance access

After you have configured the appliance, you can remotely access the appliance VM through TCP port 3389. You can also remotely access the web management app for the appliance, on port 44368 with URL: `https://<appliance-ip-or-name>:44368`.

## <a name="appliance-license"></a>Appliance license
The appliance comes with a Windows Server 2016 evaluation license, which is valid for 180 days. If the evaluation period is close to expiry, we recommend that you download and deploy a new appliance, or that you activate the operating system license of the appliance VM.

## <a name="appliance-agents"></a>Appliance agents
The appliance has these agents installed.

**Agent** | **Dettagli**
--- | ---
Discovery agent | Gathers configuration data of on-premises virtual machines
Agente di valutazione | Profiles the on-premises environment to collect VM performance data.
Migration adapter | Orchestrates VM replication, and coordinates communication between VMs and Azure.
Migration gateway | Sends replicated VM data to Azure.


## <a name="appliance-deployment-requirements"></a>Appliance deployment requirements

- [Review](migrate-support-matrix-vmware.md#assessment-appliance-requirements) the deployment requirements for a VMware appliance, and the URLs that the appliance needs to access.
- [Review](migrate-support-matrix-hyper-v.md#assessment-appliance-requirements) the deployment requirements for a Hyper-V appliance, and the URLs that the appliance  needs to access.


## <a name="collected-performance-data-vmware"></a>Collected performance data-VMware

Here's the VMware VM performance data that the appliance collects and sends to Azure.

**Dati** | **Contatore** | **Assessment impact**
--- | --- | ---
Uso della CPU | cpu.usage.average | Recommended VM size/cost
Utilizzo della memoria | mem.usage.average | Recommended VM size/cost
Disk read throughput (MB per second) | virtualDisk.read.average | Calculation for disk size, storage cost, VM size
Disk write throughput (MB per second) | virtualDisk.write.average | Calculation for disk size, storage cost, VM size
Disk read operations per second | virtualDisk.numberReadAveraged.average | Calculation for disk size, storage cost, VM size
Disk write operations per second | virtualDisk.numberWriteAveraged.average  | Calculation for disk size, storage cost, VM size
NIC read throughput (MB per second) | net.received.average | Calculation for VM size
NIC write throughput (MB per second) | net.transmitted.average  |Calculation for VM size


## <a name="collected-metadata-vmware"></a>Collected metadata-VMware

> [!NOTE]
> Metadata discovered by the Azure Migrate appliance is used to help you right-size your applications as you migrate them to Azure, perform Azure suitability analysis, application dependency analysis, and cost planning. Microsoft does not use this data in relation to any license compliance audit.

Here's the full list of VMware VM metadata that the appliance collects and sends to Azure.

**Dati** | **Contatore**
--- | --- 
**Machine details** | 
ID macchina virtuale | vm.Config.InstanceUuid 
Nome della VM. | vm.Config.Name
ID server vCenter | VMwareClient.Instance.Uuid
Descrizione macchina virtuale | vm.Summary.Config.Annotation
Nome prodotto con licenza | vm.Client.ServiceContent.About.LicenseProductName
Tipo di sistema operativo | vm.SummaryConfig.GuestFullName
Tipo di avvio | vm.Config.Firmware
Numero di core | vm.Config.Hardware.NumCPU
Memory (MB) | vm.Config.Hardware.MemoryMB
Numero di dischi | vm.Config.Hardware.Device.ToList().FindAll(x => is VirtualDisk).count
Elenco dimensioni disco | vm.Config.Hardware.Device.ToList().FindAll(x => is VirtualDisk)
Elenco schede di rete | vm.Config.Hardware.Device.ToList().FindAll(x => is VirtualEthernet).count
Uso della CPU | cpu.usage.average
Utilizzo della memoria |mem.usage.average
**Per disk details** | 
Valore chiave disco | disk.Key
Dikunit number | disk.UnitNumber
Valore chiave controller del disco | disk.ControllerKey.Value
Gigabyte con provisioning | virtualDisk.DeviceInfo.Summary
Disk name | Value generated using disk.UnitNumber, disk.Key, disk.ControllerKey.VAlue
Operazioni di lettura per secondo | virtualDisk.numberReadAveraged.average
Operazioni di scrittura per secondo | virtualDisk.numberWriteAveraged.average
Read throughput (MB per second) | virtualDisk.read.average
Write throughput (MB per second) | virtualDisk.write.average
**Per NIC details** | 
Nome scheda di rete | nic.Key
Indirizzo MAC | ((VirtualEthernetCard)nic).MacAddress
IPv4 addresses | vm.Guest.Net
IPv6 addresses | vm.Guest.Net
Read throughput (MB per second) | net.received.average
Write throughput (MB per second) | net.transmitted.average
**Inventory path details** | 
name | container.GetType().Name
Tipo di oggetto figlio | container.ChildType
Informazioni di riferimento | container.MoRef
Dettagli elemento padre | Container.Parent
Folder details per VM | ((Folder)container).ChildEntity.Type
Datacenter details per VM | ((Datacenter)container).VmFolder
Datacenter details per host folder | ((Datacenter)container).HostFolder
Cluster details per host | ((ClusterComputeResource)container).Host
Host details per VM | ((HostSystem)container).VM



## <a name="collected-performance-data-hyper-v"></a>Collected performance data-Hyper-V

> [!NOTE]
> Metadata discovered by the Azure Migrate appliance is used to help you right-size your applications as you migrate them to Azure, perform Azure suitability analysis, application dependency analysis, and cost planning. Microsoft does not use this data in relation to any license compliance audit.

Here's the Hyper VM performance data that the appliance collects and sends to Azure.

**Performance counter class** | **Contatore** | **Assessment impact**
--- | --- | ---
Hyper-V Hypervisor Virtual Processor | % Guest Run Time | Recommended VM size/cost
Hyper-V Dynamic Memory VM | Current Pressure (%)<br/> Guest Visible Physical Memory (MB) | Recommended VM size/cost
Hyper-V Virtual Storage Device | Read Bytes/Second | Calculation for disk size, storage cost, VM size
Hyper-V Virtual Storage Device | Write Bytes/Second | Calculation for disk size, storage cost, VM size
Hyper-V Virtual Network Adapter | Bytes Received/Second | Calculation for VM size
Hyper-V Virtual Network Adapter | Bytes Sent/Second | Calculation for VM size

- CPU utilization is the sum of all usage, for all virtual processors attached to a VM.
- Memory utilization is (Current Pressure * Guest Visible Physical Memory) / 100.
- Disk and network utilization values are collected from the listed Hyper-V performance counters.

## <a name="collected-metadata-hyper-v"></a>Collected metadata-Hyper-V

Here's the full list of Hyper-V VM metadata that the appliance collects and sends to Azure.

**Dati** | **WMI class** | **WMI class property**
--- | --- | ---
**Machine details** | 
Serial number of BIOS _ Msvm_BIOSElement | BIOSSerialNumber
VM type (Gen 1 or 2) | Msvm_VirtualSystemSettingData | VirtualSystemSubType
VM display name | Msvm_VirtualSystemSettingData | ElementName
VM version | Msvm_ProcessorSettingData | VirtualQuantity
Memory (bytes) | Msvm_MemorySettingData | VirtualQuantity
Maximum memory that can be consumed by VM | Msvm_MemorySettingData | Limite
Dynamic memory enabled | Msvm_MemorySettingData | DynamicMemoryEnabled
Operating system name/version/FQDN | Msvm_KvpExchangeComponent | GuestIntrinsicExchangeItems Name Data
VM power status | Msvm_ComputerSystem | EnabledState
**Per disk details** | 
Disk identifier | Msvm_VirtualHardDiskSettingData | VirtualDiskId
Virtual hard disk type | Msvm_VirtualHardDiskSettingData | Type
Virtual hard disk size | Msvm_VirtualHardDiskSettingData | MaxInternalSize
Virtual hard disk parent | Msvm_VirtualHardDiskSettingData | ParentPath
**Per NIC details** | 
IP addresses (synthetic NICs) | Msvm_GuestNetworkAdapterConfiguration | IPAddresses
DHCP enabled (synthetic NICs) | Msvm_GuestNetworkAdapterConfiguration | DHCPEnabled
NIC ID (synthetic NICs) | Msvm_SyntheticEthernetPortSettingData | InstanceID
NIC MAC address (synthetic NICs) | Msvm_SyntheticEthernetPortSettingData | Indirizzo
NIC ID (legacy NICs) | MsvmEmulatedEthernetPortSetting Data | InstanceID
NIC MAC ID (legacy NICs) | MsvmEmulatedEthernetPortSetting Data | Indirizzo




## <a name="discovery-and-collection-process"></a>Discovery and collection process

The appliance communicates with vCenter Servers and Hyper-V hosts/cluster using the following process.


1. **Start discovery**:
    - When you start the discovery on the Hyper-V appliance, it communicates with the Hyper-V hosts on WinRM ports 5985 (HTTP) and 5986 (HTTPS).
    - When you start discovery on the VMware appliance, it communicates with the vCenter server on TCP port 443 by default. IF the vCenter server listens on a different port, you can configure it in the appliance web app.
2. **Gather metadata and performance data**:
    - The appliance uses a Common Information Model (CIM) session to gather Hyper-V VM data from the Hyper-V host on ports 5985 and 5986.
    - The appliance communicates with port 443 by default, to gather VMware VM data from the vCenter Server.
3. **Send data**: The appliance sends the collected data to Azure Migrate Server Assessment and Azure Migrate Server Migration over SSL port 443.
    - For performance data, the appliance collects real-time utilization data.
        - Performance data is collected every 20 seconds for VMware, and every 30 seconds for Hyper-V, for each performance metric.
        - The collected data is rolled up to create a single data point for ten minutes.
        - The peak utilization value is selected from all of the 20/30 second data points, and sent to Azure for assessment calculation.
        - Based on the percentile value specified in the assessment properties (50th/90th/95th/99th), the ten-minute points are sorted in ascending order, and the appropriate percentile value is used to compute the assessment
    - For Server Migration, the appliance starts collecting VM data, and replicates it to Azure.
4. **Assess and migrate**: You can now create assessments from the metadata collected by the appliance using Azure Migrate Server Assessment. In addition, you can also start migrating VMware VMs using Azure Migrate Server Migration to orchestrate agentless VM replication.


![Architecture](./media/migrate-appliance/architecture.png)


## <a name="appliance-upgrades"></a>Appliance upgrades

The appliance is upgraded as the Azure Migrate agents running on the appliance are updated.

- This happens automatically because the auto-update is enabled on the appliance by default.
- You can change this default setting to update the agents manually.
- To disable the auto-update, go to the Registry Editor>HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance and set the registry key- "AutoUpdate" to 0 (DWORD).
 
### <a name="set-agent-updates-to-manual"></a>Set agent updates to manual

For manual updates, make sure that you update all the agents on the appliance at the same time, using the **Update** button for each outdated agent on the appliance. You can switch the update setting back to automatic updates at any time.

## <a name="next-steps"></a>Passaggi successivi

[Learn how](tutorial-assess-vmware.md#set-up-the-appliance-vm) to set up the appliance for VMware.
[Learn how](tutorial-assess-hyper-v.md#set-up-the-appliance-vm) to set up the appliance for Hyper-V.

