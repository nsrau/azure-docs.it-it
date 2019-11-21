---
title: Support matrix for VMware/physical disaster recovery in Azure Site Recovery
description: Summarizes support for disaster recovery of VMware VMs and physical server to Azure using Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/15/2019
ms.author: raynew
ms.openlocfilehash: b75d9e9fc88cabacab912efa17938708eb061b99
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74232424"
---
# <a name="support-matrix-for-disaster-recovery--of-vmware-vms-and-physical-servers-to-azure"></a>Matrice di supporto per il ripristino di emergenza di macchine virtuali VMware e server fisici in Azure

This article summarizes supported components and settings for disaster recovery of VMware VMs and physical servers to Azure using [Azure Site Recovery](site-recovery-overview.md).

- [Learn more](vmware-azure-architecture.md) about VMware VM/physical server disaster recovery architecture.
- Follow our [tutorials](tutorial-prepare-azure.md) to try out disaster recovery.

## <a name="deployment-scenarios"></a>Scenari di distribuzione

**Scenario** | **Dettagli**
--- | ---
Disaster recovery of VMware VMs | Replica di macchine virtuali VMware locali in Azure. Si può distribuire questo scenario con il portale di Azure o tramite [PowerShell](vmware-azure-disaster-recovery-powershell.md).
Disaster recovery of physical servers | Replica di server fisici Windows/Linux locali in Azure. Si può distribuire questo scenario nel portale di Azure.

## <a name="on-premises-virtualization-servers"></a>Server di virtualizzazione locali

**Server** | **Requisiti** | **Dettagli**
--- | --- | ---
Server vCenter | Version 6.7, 6.5, 6.0, or 5.5 | We recommend that you use a vCenter server in your disaster recovery deployment.
vSphere hosts | Version 6.7, 6.5, 6.0, or 5.5 | È consigliabile che gli host di vSphere e i server vCenter si trovino nella stessa rete del server di elaborazione. By default the process server runs on the configuration server. [Altre informazioni](vmware-physical-azure-config-process-server-overview.md).


## <a name="site-recovery-configuration-server"></a>Server di configurazione di Site Recovery

Il server di configurazione è un computer locale in cui vengono eseguiti tutti i componenti locali di Site Recovery, inclusi il server di configurazione, il server di elaborazione e il server di destinazione master.

- For VMware VMs you set the configuration server by downloading an OVF template to create a VMware VM.
- For physical servers, you set up the configuration server machine manually.

**Componente** | **Requisiti**
--- |---
Core CPU | 8
RAM | 16 GB
Numero di dischi | 3 dischi<br/><br/> I dischi includono il disco del sistema operativo, il disco della cache del server di elaborazione e l'unità di conservazione per il failback.
Spazio disponibile su disco | 600 GB of space for the process server cache.
Spazio disponibile su disco | 600 GB  of space for the retention drive.
Sistema operativo  | Windows Server 2012 R2, or Windows Server 2016 with Desktop experience <br/><br> If you plan to use the in-built Master Target of this appliance for failback, ensure that the OS version is same or higher than the replicated items.|
Impostazioni locali del sistema operativo | Inglese (en-us)
[PowerCLI](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) | Not needed for configuration server version [9.14](https://support.microsoft.com/help/4091311/update-rollup-23-for-azure-site-recovery) or later. 
Ruoli di Windows Server | Don't enable Active Directory Domain Services; Internet Information Services (IIS) or Hyper-V. 
Criteri di gruppo| - Impedisci accesso al prompt dei comandi <br/> - Impedisci accesso agli strumenti di modifica del Registro di sistema <br/> - Logica di attendibilità per file allegati <br/> - Attiva l'esecuzione di script <br/> - [Learn more](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)|
IIS | Assicurarsi di:<br/><br/> - Non avere un sito Web predefinito preesistente <br/> - Abilitare l'[autenticazione anonima](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br/> - Abilitare l'impostazione di [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx)  <br/> - Non avere un sito Web o un'app preesistente in ascolto sulla porta 443<br/>
Tipo di scheda di interfaccia di rete | VMXNET3 (quando distribuito come macchina virtuale VMware)
Tipo di indirizzo IP | Statica
Porte | 443 used for control channel orchestration<br/>9443 for data transport

## <a name="replicated-machines"></a>Computer replicati

Site Recovery supporta la replica di qualsiasi carico di lavoro in esecuzione in un computer supportato.

> [!Note]
> The following table lists the support for machines with BIOS boot. Please refer to [Storage](#storage) section for support on UEFI based machines.

**Componente** | **Dettagli**
--- | ---
Impostazioni del computer | I computer che eseguono la replica in Azure devono soddisfare i [requisiti di Azure](#azure-vm-requirements).
Carico di lavoro del computer | Site Recovery supporta la replica di qualsiasi carico di lavoro in esecuzione in un computer supportato. [Altre informazioni](https://aka.ms/asr_workload).
Windows Server 2019 | Supported from [Update rollup 34](https://support.microsoft.com/help/4490016) (version 9.22 of the Mobility service) onwards.
Windows Server 2016 64-bit | Supported for Server Core, Server with Desktop Experience.
Windows Server 2012 R2 / Windows Server 2012 | Supportato.
Windows Server 2008 R2 with SP1 onwards. | Supportato.<br/><br/> From version 9.30.x.x (expected release beginning of November 2019) of the Mobility service agent, you need [servicing stack update (SSU)](https://support.microsoft.com/help/4490628) and [SHA-2 update](https://support.microsoft.com/help/4474419) installed on machines running Windows 2008 R2 with SP1 or later. SHA-1 isn't supported from September 2019, and if SHA-2 code signing isn't enabled the agent extension won't install/upgrade as expected. Learn more about [SHA-2 upgrade and requirements](https://aka.ms/SHA-2KB).
Windows Server 2008 with SP2 or later (64-bit/32-bit) |  Supported for migration only. [Altre informazioni](migrate-tutorial-windows-server-2008.md).<br/><br/> From version 9.30.x.x (expected release beginning of November 2019) of the Mobility service agent, you need [servicing stack update (SSU)](https://support.microsoft.com/help/4493730) and [SHA-2 update](h https://support.microsoft.com/help/4474419) installed on Windows 2008 SP2 machines. ISHA-1 isn't supported from September 2019, and if SHA-2 code signing isn't enabled the agent extension won't install/upgrade as expected. Learn more about [SHA-2 upgrade and requirements](https://aka.ms/SHA-2KB).
Windows 10, Windows 8.1, Windows 8 | Supportato.
Windows 7 with SP1 64-bit | Supported from [Update rollup 36](https://support.microsoft.com/help/4503156) (version 9.22 of the Mobility service) onwards. </br></br> From version 9.30.x.x (expected release beginning of November 2019) of the Mobility service agent, you need [servicing stack update (SSU)](https://support.microsoft.com/help/4490628) and [SHA-2 update](https://support.microsoft.com/help/4474419) installed on Windows 7 SP1 machines.  SHA-1 isn't supported from September 2019, and if SHA-2 code signing isn't enabled the agent extension won't install/upgrade as expected. Learn more about [SHA-2 upgrade and requirements](https://aka.ms/SHA-2KB).
Linux | Only 64-bit system is supported. 32-bit system isn't supported.<br/><br/>Every Linux server should have [Linux Integration Services (LIS) components](https://www.microsoft.com/download/details.aspx?id=55106) installed. It is required to boot the server in Azure after test failover/failover. If LIS components are missing, ensure to install the [components](https://www.microsoft.com/download/details.aspx?id=55106) before enabling replication for the machines to boot in Azure. <br/><br/> Site Recovery orchestra il failover per l'esecuzione di server Linux in Azure. Tuttavia i fornitori di Linux potrebbero limitare il supporto solo alle versioni di distribuzione che non hanno raggiunto la fine del ciclo di vita.<br/><br/> Nelle distribuzioni di Linux sono supportati solo i kernel di scorta che fanno parte di una versione/aggiornamento secondaria della distribuzione.<br/><br/> L'aggiornamento di computer protetti tra versioni di distribuzione principali di Linux non è supportato. Per eseguire l'aggiornamento, disabilitare la replica, aggiornare il sistema operativo e quindi abilitare nuovamente la replica.<br/><br/> [Learn more](https://support.microsoft.com/help/2941892/support-for-linux-and-open-source-technology-in-azure) about support for Linux and open-source technology in Azure.
Linux Red Hat Enterprise | 5.2 to 5.11</b><br/> 6.1 to 6.10</b> </br> 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6, [7.7](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery), [8.0](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery) <br/> Servers running Red Hat Enterprise Linux 5.2-5.11 & 6.1-6.10 do not have [Linux Integration Services (LIS) components](https://www.microsoft.com/download/details.aspx?id=55106) pre-installed. Ensure to install the [components](https://www.microsoft.com/download/details.aspx?id=55106) before enabling replication for the machines to boot in Azure.
Linux: CentOS | 5.2 to 5.11</b><br/> 6.1 to 6.10</b><br/> 7.0 to 7.6<br/> <br/> Servers running  CentOS 5.2-5.11 & 6.1-6.10 do not have  [Linux Integration Services (LIS) components](https://www.microsoft.com/download/details.aspx?id=55106) pre-installed. Ensure to install the [components](https://www.microsoft.com/download/details.aspx?id=55106) before enabling replication for the machines to boot in Azure.
Ubuntu | Ubuntu 14.04 LTS server [(review supported kernel versions)](#ubuntu-kernel-versions)<br/><br/>Ubuntu 16.04 LTS server [(review supported kernel versions)](#ubuntu-kernel-versions) </br> Ubuntu 18.04 LTS server [(review supported kernel versions)](#ubuntu-kernel-versions)
Debian | Debian 7/Debian 8 [(review supported kernel versions)](#debian-kernel-versions)
SUSE Linux | SUSE Linux Enterprise Server 12 SP1, SP2, SP3, SP4 [(review supported kernel versions)](#suse-linux-enterprise-server-12-supported-kernel-versions)<br/> SUSE Linux Enterprise Server 11 SP3, SUSE Linux Enterprise Server 11 SP4<br/> Upgrading replicated machines from SUSE Linux Enterprise Server 11 SP3 to SP4 isn't supported. To upgrade, disable replication and re-enable after the upgrade.
Oracle Linux | 6.4, 6.5, 6.6, 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6, [7.7](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery)<br/><br/> Running the Red Hat compatible kernel or Unbreakable Enterprise Kernel Release 3, 4 & 5 (UEK3, UEK4, UEK5) 


### <a name="ubuntu-kernel-versions"></a>Versioni del kernel Ubuntu

**Versione supportata** | **Versione del servizio Mobility** | **Versione del kernel** |
--- | --- | --- |
14.04 LTS | [9.28][9.28 UR]| 3.13.0-24-generic to 3.13.0-170-generic,<br/>Da 3.16.0-25 generica a 3.16.0-77 generica<br/>Da 3.19.0-18 generica a 3.19.0-80 generica<br/>Da 4.2.0-18 generica a 4.2.0-42 generica<br/>4.4.0-21-generic to 4.4.0-148-generic,<br/>4.15.0-1023-azure to 4.15.0-1045-azure |
14.04 LTS | [9.27][9.27 UR]| 3.13.0-24-generic to 3.13.0-170-generic,<br/>Da 3.16.0-25 generica a 3.16.0-77 generica<br/>Da 3.19.0-18 generica a 3.19.0-80 generica<br/>Da 4.2.0-18 generica a 4.2.0-42 generica<br/>4.4.0-21-generic to 4.4.0-148-generic,<br/>4.15.0-1023-azure to 4.15.0-1045-azure |
14.04 LTS | [9.26][9.26 UR]| 3.13.0-24-generic to 3.13.0-170-generic,<br/>Da 3.16.0-25 generica a 3.16.0-77 generica<br/>Da 3.19.0-18 generica a 3.19.0-80 generica<br/>Da 4.2.0-18 generica a 4.2.0-42 generica<br/>4.4.0-21-generic to 4.4.0-148-generic,<br/>4.15.0-1023-azure to 4.15.0-1045-azure |
14.04 LTS | [9.25][9.25 UR]  | 3.13.0-24-generic to 3.13.0-169-generic,<br/>Da 3.16.0-25 generica a 3.16.0-77 generica<br/>Da 3.19.0-18 generica a 3.19.0-80 generica<br/>Da 4.2.0-18 generica a 4.2.0-42 generica<br/>4.4.0-21-generic to 4.4.0-146-generic,<br/>4.15.0-1023-azure to 4.15.0-1042-azure |
|||
16.04 LTS | [9.30](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery) | 4.4.0-21-generic to 4.4.0-166-generic,<br/>Da 4.8.0-34 generica a 4.8.0-58 generica<br/>Da 4.10.0-14 generica a 4.10.0-42 generica<br/>Da 4.11.0-13 generica a 4.11.0-14 generica<br/>Da 4.13.0-16 generica a 4.13.0-45 generica<br/>4.15.0-13-generic to 4.15.0-66-generic<br/>Da 4.11.0-1009 Azure a 4.11.0-1016 Azure<br/>Da 4.13.0-1005 Azure a 4.13.0-1018 Azure <br/>4.15.0-1012-azure to 4.15.0-1061-azure|
16.04 LTS | [9.28][9.28 UR] | 4.4.0-21-generic to 4.4.0-159-generic,<br/>Da 4.8.0-34 generica a 4.8.0-58 generica<br/>Da 4.10.0-14 generica a 4.10.0-42 generica<br/>Da 4.11.0-13 generica a 4.11.0-14 generica<br/>Da 4.13.0-16 generica a 4.13.0-45 generica<br/>4.15.0-13-generic to 4.15.0-58-generic<br/>Da 4.11.0-1009 Azure a 4.11.0-1016 Azure<br/>Da 4.13.0-1005 Azure a 4.13.0-1018 Azure <br/>4.15.0-1012-azure to 4.15.0-1055-azure|
16.04 LTS | [9.27][9.27 UR] | 4.4.0-21-generic to 4.4.0-154-generic,<br/>Da 4.8.0-34 generica a 4.8.0-58 generica<br/>Da 4.10.0-14 generica a 4.10.0-42 generica<br/>Da 4.11.0-13 generica a 4.11.0-14 generica<br/>Da 4.13.0-16 generica a 4.13.0-45 generica<br/>4.15.0-13-generic to 4.15.0-54-generic<br/>Da 4.11.0-1009 Azure a 4.11.0-1016 Azure<br/>Da 4.13.0-1005 Azure a 4.13.0-1018 Azure <br/>4.15.0-1012-azure to 4.15.0-1050-azure|
16.04 LTS | [9.26][9.26 UR] | 4.4.0-21-generic to 4.4.0-148-generic,<br/>Da 4.8.0-34 generica a 4.8.0-58 generica<br/>Da 4.10.0-14 generica a 4.10.0-42 generica<br/>Da 4.11.0-13 generica a 4.11.0-14 generica<br/>Da 4.13.0-16 generica a 4.13.0-45 generica<br/>4.15.0-13-generic to 4.15.0-50-generic<br/>Da 4.11.0-1009 Azure a 4.11.0-1016 Azure<br/>Da 4.13.0-1005 Azure a 4.13.0-1018 Azure <br/>4.15.0-1012-azure to 4.15.0-1045-azure|
16.04 LTS | [9.25][9.25 UR] | 4.4.0-21-generic to 4.4.0-146-generic,<br/>Da 4.8.0-34 generica a 4.8.0-58 generica<br/>Da 4.10.0-14 generica a 4.10.0-42 generica<br/>Da 4.11.0-13 generica a 4.11.0-14 generica<br/>Da 4.13.0-16 generica a 4.13.0-45 generica<br/>4.15.0-13-generic to 4.15.0-48-generic<br/>Da 4.11.0-1009 Azure a 4.11.0-1016 Azure<br/>Da 4.13.0-1005 Azure a 4.13.0-1018 Azure <br/>4.15.0-1012-azure to 4.15.0-1042-azure|
16.04 LTS | [9.24][9.24 UR] | 4.4.0-21-generic to 4.4.0-143-generic,<br/>Da 4.8.0-34 generica a 4.8.0-58 generica<br/>Da 4.10.0-14 generica a 4.10.0-42 generica<br/>Da 4.11.0-13 generica a 4.11.0-14 generica<br/>Da 4.13.0-16 generica a 4.13.0-45 generica<br/>4.15.0-13-generic to 4.15.0-46-generic<br/>Da 4.11.0-1009 Azure a 4.11.0-1016 Azure<br/>Da 4.13.0-1005 Azure a 4.13.0-1018 Azure <br/>4.15.0-1012-azure to 4.15.0-1040-azure|
|||
18.04 LTS | [9.29](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery) | 4.15.0-20-generic to 4.15.0-62-generic </br> 4.18.0-13-generic to 4.18.0-25-generic </br> 5.0.0-15-generic to 5.0.0-27-generic </br> 4.15.0-1009-azure to 4.15.0-1037-azure </br> 4.18.0-1006-azure to 4.18.0-1025-azure </br> 5.0.0-1012-azure to 5.0.0-1018-azure
18.04 LTS | [9.30](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery) | 4.15.0-20-generic to 4.15.0-66-generic </br> 4.18.0-13-generic to 4.18.0-25-generic </br> 5.0.0-15-generic to 5.0.0-32-generic </br> 4.15.0-1009-azure to 4.15.0-1037-azure </br> 4.18.0-1006-azure to 4.18.0-1025-azure </br> 5.0.0-1012-azure to 5.0.0-1023-azure


### <a name="debian-kernel-versions"></a>Versioni del kernel Debian


**Versione supportata** | **Versione del servizio Mobility** | **Versione del kernel** |
--- | --- | --- |
Debian 7 | [9.25][9.25 UR],[9.26][9.26 UR], [9.27][9.27 UR], [9.28][9.28 UR]| Da 3.2.0-4-amd64 a 3.2.0-6-amd64, 3.16.0-0.bpo.4-amd64 |
|||
Debian 8 | [9.28][9.28 UR] | 3.16.0-4-amd64 to 3.16.0-10-amd64, 4.9.0-0.bpo.4-amd64 to 4.9.0-0.bpo.9-amd64 |
Debian 8 | [9.27][9.27 UR] | 3.16.0-4-amd64 to 3.16.0-9-amd64, 4.9.0-0.bpo.4-amd64 to 4.9.0-0.bpo.9-amd64 |
Debian 8 | [9.25][9.25 UR], [9.26][9.26 UR] | 3.16.0-4-amd64 to 3.16.0-8-amd64, 4.9.0-0.bpo.4-amd64 to 4.9.0-0.bpo.8-amd64 |

### <a name="suse-linux-enterprise-server-12-supported-kernel-versions"></a>SUSE Linux Enterprise Server 12 (versioni del kernel supportate)

**Versione** | **Versione del servizio Mobility** | **Versione del kernel** |
--- | --- | --- |
SUSE Linux Enterprise Server 12 (SP1,SP2,SP3,SP4) | [9.28][9.28 UR] | Da 3.12.49-11 SP1 predefinita a 3.12.74-60.64.40 predefinita</br></br> SP1(LTSS) 3.12.74-60.64.45-default to 3.12.74-60.64.118-default</br></br> Da 4.4.21-69 SP2 predefinita a 4.4.120-92.70 predefinita</br></br>SP2(LTSS) 4.4.121-92.73-default to 4.4.121-92.117-default</br></br>SP3 4.4.73-5-default to 4.4.180-94.100-default</br></br>SP3 4.4.138-4.7-azure to 4.4.180-4.31-azure</br></br>SP4 4.12.14-94.41-default to 4.12.14-95.29-default</br>SP4 4.12.14-6.3-azure to 4.12.14-6.23-azure |
SUSE Linux Enterprise Server 12 (SP1,SP2,SP3,SP4) | [9.27][9.27 UR] | Da 3.12.49-11 SP1 predefinita a 3.12.74-60.64.40 predefinita</br></br> SP1(LTSS) 3.12.74-60.64.45-default to 3.12.74-60.64.115-default</br></br> Da 4.4.21-69 SP2 predefinita a 4.4.120-92.70 predefinita</br></br>SP2(LTSS) 4.4.121-92.73-default to 4.4.121-92.114-default</br></br>SP3 4.4.73-5-default to 4.4.180-94.97-default</br></br>SP3 4.4.138-4.7-azure to 4.4.180-4.31-azure</br></br>SP4 4.12.14-94.41-default to 4.12.14-95.19-default</br>SP4 4.12.14-6.3-azure to 4.12.14-6.15-azure |
SUSE Linux Enterprise Server 12 (SP1,SP2,SP3,SP4) | [9.26][9.26 UR] | Da 3.12.49-11 SP1 predefinita a 3.12.74-60.64.40 predefinita</br></br> SP1(LTSS) 3.12.74-60.64.45-default to 3.12.74-60.64.110-default</br></br> Da 4.4.21-69 SP2 predefinita a 4.4.120-92.70 predefinita</br></br>SP2(LTSS) 4.4.121-92.73-default to 4.4.121-92.109-default</br></br>SP3 4.4.73-5-default to 4.4.178-94.91-default</br></br>SP3 4.4.138-4.7-azure to 4.4.178-4.28-azure</br></br>SP4 4.12.14-94.41-default to 4.12.14-95.16-default</br>SP4 4.12.14-6.3-azure to 4.12.14-6.9-azure |
SUSE Linux Enterprise Server 12 (SP1,SP2,SP3,SP4) | [9.25][9.25 UR] | Da 3.12.49-11 SP1 predefinita a 3.12.74-60.64.40 predefinita</br></br> Da 3.12.74-60.64.45 SP1 (LTSS) predefinita a 3.12.74-60.64.107 predefinita</br></br> Da 4.4.21-69 SP2 predefinita a 4.4.120-92.70 predefinita</br></br>SP2(LTSS) 4.4.121-92.73-default to 4.4.121-92.104-default</br></br>SP3 4.4.73-5-default to 4.4.176-94.88-default</br></br>SP3 4.4.138-4.7-azure to 4.4.176-4.25-azure</br></br>SP4 4.12.14-94.41-default to 4.12.14-95.13-default</br>SP4 4.12.14-6.3-azure to 4.12.14-6.9-azure |

## <a name="linux-file-systemsguest-storage"></a>File system/archiviazione guest Linux

**Componente** | **Supportato**
--- | ---
File system | ext3, ext4, XFS
Gestore volumi | - LVM is supported.<br/> - /boot on LVM is supported from [Update Rollup 31](https://support.microsoft.com/help/4478871/) (version 9.20 of the Mobility service) onwards. It isn't supported in earlier Mobility service versions.<br/> - Multiple OS disks aren't supported.
Dispositivi di archiviazione paravirtualizzati | I dispositivi esportati da driver paravirtualizzati non sono supportati.
Dispositivi di I/O a blocchi a code multiple | Non supportati.
Server fisici con controller di archiviazione HP CCISS | Non supportati.
Convenzione di denominazione del dispositivo e del punto di montaggio | Il nome del dispositivo o del punto di montaggio deve essere univoco.<br/> Ensure that no two devices/mount points have case-sensitive names. For example naming devices for the same VM as *device1* and *Device1* isn't supported.
Directory | If you're running a version of the Mobility service earlier than version 9.20 (released in [Update Rollup 31](https://support.microsoft.com/help/4478871/)), then these restrictions apply:<br/><br/> - These directories (if set up as separate partitions/file-systems) must be on the same OS disk on the source server: /(root), /boot, /usr, /usr/local, /var, /etc.</br> - The /boot directory should be on a disk partition and not be an LVM volume.<br/><br/> From version 9.20 onwards, these restrictions don't apply. 
Directory di avvio | - Boot disks mustn't be in GPT partition format. This is an Azure architecture limitation. GPT disks are supported as data disks.<br/><br/> Multiple boot disks on a VM aren't supported<br/><br/> - /boot on an LVM volume across more than one disk isn't supported.<br/> - A machine without a boot disk can't be replicated.
Requisiti di spazio disponibile nel disco| 2 GB nella partizione /root <br/><br/> 250 MB nella cartella di installazione
XFSv5 | XFSv5 features on XFS file systems, such as metadata checksum, are supported (Mobility service version 9.10 onwards).<br/> Usare l'utilità xfs_info per controllare il superblocco XFS per la partizione. If `ftype` is set to 1, then XFSv5 features are in use.
BTRFS | BTRFS is supported from [Update Rollup 34](https://support.microsoft.com/help/4490016) (version 9.22 of the Mobility service) onwards. BTRFS isn't supported if:<br/><br/> - The BTRFS file system subvolume is changed after enabling protection.</br> - The BTRFS file system is spread over multiple disks.</br> - The BTRFS file system supports RAID.

## <a name="vmdisk-management"></a>Gestione della macchina virtuale/del disco

**Azione** | **Dettagli**
--- | ---
Ridimensionamento del disco nella macchina virtuale replicata | Supportato.
Aggiunta del disco alla macchina virtuale replicata | Non supportati.<br/> Disable replication for the VM, add the disk, and then re-enable replication.

## <a name="network"></a>Rete

**Componente** | **Supportato**
--- | ---
Gruppo NIC della rete host | Supportato per le macchine virtuali VMware. <br/><br/>Non supportato per la replica di computer fisici.
VLAN rete host | Sì.
IPv4 rete host | Sì.
IPv6 rete host | No.
Gruppo NIC della rete guest/server | No.
IPv4 rete guest/server | Sì.
IPv6 rete guest/server | No.
IP statico (Windows) rete guest/server | Sì.
IP statico (Linux) rete guest/server | Sì. <br/><br/>Le VM sono configurate per l'uso di DHCP in caso di failback.
Più schede di interfaccia rete guest/server | Sì.


## <a name="azure-vm-network-after-failover"></a>Rete VM di Azure (dopo il failover)

**Componente** | **Supportato**
--- | ---
ExpressRoute di Azure | SÌ
ILB | SÌ
ELB | SÌ
Gestione traffico di Azure | SÌ
Più NIC | SÌ
Indirizzo IP riservato | SÌ
IPv4 | SÌ
Conservazione indirizzo IP di origine | SÌ
Endpoint servizio di rete virtuale di Azure<br/> | SÌ
Rete accelerata | No

## <a name="storage"></a>Archiviazione
**Componente** | **Supportato**
--- | ---
Disco dinamico | OS disk must be a basic disk. <br/><br/>I dischi dati possono essere dinamici
Configurazione dei dischi Docker | No
NFS host | Sì per VMware<br/><br/> No per server fisici
Host SAN (iSCSI/FC) | SÌ
Host vSAN | Sì per VMware<br/><br/> N/D per server fisici
Percorsi multipli (MPIO) | Sì, testato con DSM Microsoft, EMC PowerPath 5.7 SP4, DSM EMC PowerPath per CLARiiON
Volumi virtuali host | Sì per VMware<br/><br/> N/D per server fisici
VMDK guest/server | SÌ
Disco cluster condiviso guest/server | No
Disco crittografato guest/server | No
NFS guest/server | No
Guest/server iSCSI | For Migration - Yes<br/>For Disaster Recovery - No, iSCSI will failback as an attached disk to the VM
SMB 3.0 guest/server | No
RDM guest/server | SÌ<br/><br/> N/D per server fisici
Disco guest/server > 1 TB | Yes, disk must be larger than 1024 MB<br/><br/>Up to 8,192 GB when replicating to managed disks (9.26 version onwards)<br></br> Up to 4,095 GB when replicating to storage accounts
Disco guest/server con dimensioni logiche di settore a 4 KB e dimensioni fisiche di settore a 4 KB | No
Guest/server disk with 4K logical and 512-bytes physical sector size | No
Volume con disco con striping > 4 TB guest/server <br/><br/>Gestione volumi logici (LVM)| SÌ
Guest/server - Spazi di archiviazione | No
Disco di aggiunta/rimozione a caldo guest/server | No
Guest/server - esclusione disco | SÌ
Percorsi multipli (MPIO) guest/server | No
Guest/server GPT partitions | Five partitions are supported from [Update Rollup 37](https://support.microsoft.com/help/4508614/) (version 9.25 of the Mobility service) onwards. In precedenza ne erano supportate quattro.
ReFS | Resilient File System is supported with Mobility service version 9.23 or higher
Guest/server EFI/UEFI boot | - Supported for Windows Server 2012 or later, SLES 12 SP4 and RHEL 8.0 with mobility agent version 9.30 onwards<br/> - Secure UEFI boot type is not supported. 

## <a name="replication-channels"></a>Replication channels

|**Type of replication**   |**Supportato**  |
|---------|---------|
|Offloaded Data Transfers  (ODX)    |       No  |
|Seeding offline        |   No      |
| Azure Data Box | No

## <a name="azure-storage"></a>Archiviazione di Azure

**Componente** | **Supportato**
--- | ---
Archiviazione con ridondanza locale | SÌ
Archiviazione con ridondanza geografica | SÌ
Archiviazione con ridondanza geografica e accesso in lettura | SÌ
Archiviazione ad accesso sporadico | No
Archiviazione ad accesso frequente| No
BLOB in blocchi | No
Encryption-at-rest (SSE)| SÌ
Encryption-at-rest (CMK)| No
Archiviazione Premium | SÌ
Servizio di importazione/esportazione | No
Azure Storage firewalls for VNets | Sì.<br/> Configured on target storage/cache storage account (used to store replication data).
General-purpose v2 storage accounts (hot and cool tiers) | Yes (Transaction costs are substantially higher for V2 compared to V1)

## <a name="azure-compute"></a>Calcolo di Azure

**Funzionalità** | **Supportato**
--- | ---
Set di disponibilità | SÌ
Zone di disponibilità | No
HUB | SÌ
Managed Disks | SÌ

## <a name="azure-vm-requirements"></a>Requisiti per le VM di Azure

On-premises VMs replicated to Azure must meet the Azure VM requirements summarized in this table. When Site Recovery runs a prerequisites check for replication, the check will fail if some of the requirements aren't met.

**Componente** | **Requisiti** | **Dettagli**
--- | --- | ---
Sistema operativo guest | Verificare i [sistemi operativi supportati](#replicated-machines) per le macchine replicate. | Il controllo ha esito negativo se non supportato.
Architettura del sistema operativo guest | 64 bit. | Il controllo ha esito negativo se non supportato.
Dimensioni disco del sistema operativo | Fino a 2.048 GB. | Il controllo ha esito negativo se non supportato.
Conteggio dischi del sistema operativo | 1 | Il controllo ha esito negativo se non supportato.
Conteggio dischi dati | 64 o un numero inferiore. | Il controllo ha esito negativo se non supportato.
Dimensioni del disco dati | Up to 8,192 GB when replicating to managed disk (9.26 version onwards)<br></br>Up to 4,095 GB when replicating to storage account| Il controllo ha esito negativo se non supportato.
Schede di rete | Sono supportate più schede. |
VHD condiviso | Non supportati. | Il controllo ha esito negativo se non supportato.
Disco FC | Non supportati. | Il controllo ha esito negativo se non supportato.
BitLocker | Non supportati. | Prima di abilitare la replica per un computer, occorre disabilitare BitLocker. |
Nome della VM. | Da 1 a 63 caratteri.<br/><br/> Limitato a lettere, numeri e trattini.<br/><br/> Il nome del computer deve iniziare e terminare con una lettera o un numero. |  Aggiornare il valore nelle proprietà del computer in Site Recovery.

## <a name="resource-group-limits"></a>Resource group limits

To understand the number of virtual machines that can be protected under a single resource group, refer to the article on [subscription limits and quotas](https://docs.microsoft.com/azure/azure-subscription-service-limits#resource-group-limits)

## <a name="churn-limits"></a>Churn limits

La tabella seguente contiene i limiti di Azure Site Recovery. 
- These limits are based on our tests, but don't cover all possible app I/O combinations.
- I risultati effettivi possono variare in base alla combinazione di I/O delle applicazioni.
- For best results, we strongly recommend that you run the [Deployment Planner tool](site-recovery-deployment-planner.md), and perform extensive application testing using test failovers to get the true performance picture for your app.

**Replication target** | **Dimensioni medie I/O disco di origine** |**Varianza dati media disco di origine** | **Varianza dati totale giornaliera disco di origine**
---|---|---|---
Archiviazione standard | 8 KB | 2 MB/s | 168 GB per disco
Disco P10 o P15 Premium | 8 KB  | 2 MB/s | 168 GB per disco
Disco P10 o P15 Premium | 16 KB | 4 MB/s |  336 GB per disco
Disco P10 o P15 Premium | 32 KB o superiori | 8 MB/s | 672 GB per disco
Disco P20, P30, P40 o P50 Premium | 8 KB    | 5 MB/s | 421 GB per disco
Disco P20, P30, P40 o P50 Premium | 16 KB o superiori |20 MB/s | 1684 GB per disk


**Varianza dati di origine** | **Limite Massimo**
---|---
Picco di varianza dei dati su tutti i dischi in una VM | 54 MB/s
Varianza massima dei dati al giorno supportata da un server di elaborazione | 2 TB

- Si tratta di numeri medi presupponendo una sovrapposizione I/O del 30%.
- Site Recovery può gestire una velocità effettiva maggiore in base alla percentuale di sovrapposizione, alle dimensioni di scrittura maggiori e all'effettivo I/O del carico di lavoro.
- These numbers assume a typical backlog of approximately five minutes. ovvero i dati, dopo essere stati caricati, verranno elaborati e verrà creato un punto di ripristino entro cinque minuti.

## <a name="vault-tasks"></a>Attività di insieme di credenziali

**Azione** | **Supportato**
--- | ---
Spostamento insieme di credenziali tra gruppi di risorse | No
Move vault within and across subscriptions | No
Spostamento di risorse di archiviazione, rete e VM di Azure tra gruppi di risorse | No
Move storage, network, Azure VMs within and across subscriptions. | No


## <a name="obtain-latest-components"></a>Obtain latest components

**Nome** | **Descrizione** | **Dettagli**
--- | --- | ---
Server di configurazione | Installed on-premises.<br/> Coordinates communications between on-premises VMware servers or physical machines, and Azure. | - [Learn about](vmware-physical-azure-config-process-server-overview.md) the configuration server.<br/> - [Learn about](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server) upgrading to the latest version.<br/> - [Learn about](vmware-azure-deploy-configuration-server.md) setting up the configuration server. 
Server di elaborazione | Installato per impostazione predefinita nel server di configurazione.<br/> Receives replication data, optimizes it with caching, compression, and encryption, and sends it to Azure.<br/> As your deployment grows, you can add additional process servers to handle larger volumes of replication traffic. | - [Learn about](vmware-physical-azure-config-process-server-overview.md) the process server.<br/> - [Learn about](vmware-azure-manage-process-server.md#upgrade-a-process-server) upgrading to the latest version.<br/> - [Learn about](vmware-physical-large-deployment.md#set-up-a-process-server) setting up scale-out process servers.
Servizio Mobility | Installed on VMware VM or physical servers you want to replicate.<br/> Coordinates replication between on-premises VMware servers/physical servers and Azure.| - [Learn about](vmware-physical-mobility-service-overview.md) the Mobility service.<br/> - [Learn about](vmware-physical-manage-mobility-service.md#update-mobility-service-from-azure-portal) upgrading to the latest version.<br/> 



## <a name="next-steps"></a>Passaggi successivi
[Informazioni su come](tutorial-prepare-azure.md) preparare Azure per il ripristino di emergenza delle macchine virtuali VMware.

[9.28 UR]: https://support.microsoft.com/en-in/help/4521530/update-rollup-40-for-azure-site-recovery
[9.27 UR]: https://support.microsoft.com/en-in/help/4517283/update-rollup-39-for-azure-site-recovery
[9.26 UR]: https://support.microsoft.com/en-in/help/4513507/update-rollup-38-for-azure-site-recovery
[9.25 UR]: https://support.microsoft.com/en-in/help/4508614/update-rollup-37-for-azure-site-recovery
[9.24 UR]: https://support.microsoft.com/en-in/help/4503156
[9.23 UR]: https://support.microsoft.com/en-in/help/4494485/update-rollup-35-for-azure-site-recovery
[9.22 UR]: https://support.microsoft.com/help/4489582/update-rollup-33-for-azure-site-recovery
[9.21 UR]: https://support.microsoft.com/help/4485985/update-rollup-32-for-azure-site-recovery
[9.20 UR]: https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery
[9.19 UR]: https://support.microsoft.com/help/4468181/azure-site-recovery-update-rollup-30
[9.18 UR]: https://support.microsoft.com/help/4466466/update-rollup-29-for-azure-site-recovery
