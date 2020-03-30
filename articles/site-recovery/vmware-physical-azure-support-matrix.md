---
title: Matrice di supporto per il ripristino di emergenza vMware/fisico in Azure Site RecoverySupport matrix for VMware/physical disaster recovery in Azure Site Recovery
description: Riepiloga il supporto per il ripristino di emergenza delle macchine virtuali VMware e del server fisico in Azure usando Azure Site Recovery.
ms.service: site-recovery
ms.topic: conceptual
ms.date: 2/24/2020
ms.author: raynew
ms.openlocfilehash: 05e60c5b008746bbfd72dbe7a2e14b18aa563671
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371393"
---
# <a name="support-matrix-for-disaster-recovery--of-vmware-vms-and-physical-servers-to-azure"></a>Matrice di supporto per il ripristino di emergenza di macchine virtuali VMware e server fisici in Azure

Questo articolo riepiloga i componenti e le impostazioni supportati per il ripristino di emergenza delle macchine virtuali VMware e dei server fisici in Azure tramite [Azure Site Recovery.](site-recovery-overview.md)

- Altre informazioni sull'architettura di ripristino di [emergenza](vmware-azure-architecture.md) di Vm/server fisico VMware.Learn more about VMware VM/physical server disaster recovery architecture.
- Segui [i](tutorial-prepare-azure.md) nostri tutorial per provare il disaster recovery.

## <a name="deployment-scenarios"></a>Scenari di distribuzione

**Scenario** | **Dettagli**
--- | ---
Ripristino di emergenza delle macchine virtuali VMwareDisaster recovery of VMware VMs | Replica di macchine virtuali VMware locali in Azure. È possibile distribuire questo scenario nel portale di Azure o tramite [PowerShell](vmware-azure-disaster-recovery-powershell.md).
Ripristino di emergenza dei server fisici | Replica di server fisici Windows/Linux locali in Azure. Si può distribuire questo scenario nel portale di Azure.

## <a name="on-premises-virtualization-servers"></a>Server di virtualizzazione locali

**Server** | **Requisiti** | **Dettagli**
--- | --- | ---
Server vCenter | Versione 6.7, 6.5, 6.0 o 5.5 | È consigliabile usare un server vCenter nella distribuzione del ripristino di emergenza.
Host vSphere | Versione 6.7, 6.5, 6.0 o 5.5 | È consigliabile che gli host di vSphere e i server vCenter si trovino nella stessa rete del server di elaborazione. Per impostazione predefinita, il server di elaborazione viene eseguito nel server di configurazione. [Scopri di più](vmware-physical-azure-config-process-server-overview.md).


## <a name="site-recovery-configuration-server"></a>Server di configurazione di Site Recovery

Il server di configurazione è un computer locale in cui vengono eseguiti tutti i componenti locali di Site Recovery, inclusi il server di configurazione, il server di elaborazione e il server di destinazione master.

- Per le macchine virtuali VMware si imposta il server di configurazione scaricando un modello OVF per creare una macchina virtuale VMware.
- Per i server fisici, configurare manualmente il computer del server di configurazione.

**Componente** | **Requisiti**
--- |---
Core CPU | 8
RAM | 16 GB
Numero di dischi | 3 dischi<br/><br/> I dischi includono il disco del sistema operativo, il disco della cache del server di elaborazione e l'unità di conservazione per il failback.
Spazio disponibile su disco | 600 GB di spazio per la cache del server di elaborazione.
Spazio disponibile su disco | 600 GB di spazio per l'unità di conservazione.
Sistema operativo  | Windows Server 2012 R2 o Windows Server 2016 con esperienza desktop <br/><br> Se si prevede di utilizzare la destinazione master incorporata dell'appliance per il failback, assicurarsi che la versione del sistema operativo sia uguale o superiore agli elementi replicati.|
Impostazioni locali del sistema operativo | Inglese (en-us)
[PowerCLI](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) | Non necessario per il server di configurazione versione [9.14](https://support.microsoft.com/help/4091311/update-rollup-23-for-azure-site-recovery) o successiva. 
Ruoli di Windows Server | Non abilitare Servizi di dominio Active Directory; Internet Information Services (IIS) o Hyper-V. 
Criteri di gruppo| - Impedisci accesso al prompt dei comandi <br/> - Impedisci accesso agli strumenti di modifica del Registro di sistema <br/> - Logica di attendibilità per file allegati <br/> - Attiva l'esecuzione di script <br/> - [Learn more](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)|
IIS | Assicurarsi di:<br/><br/> - Non avere un sito Web predefinito preesistente <br/> - Abilitare l'[autenticazione anonima](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br/> - Abilitare l'impostazione di [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx)  <br/> - Non avere un sito Web o un'app preesistente in ascolto sulla porta 443<br/>
Tipo di scheda di interfaccia di rete | VMXNET3 (quando distribuito come macchina virtuale VMware)
Tipo di indirizzo IP | Statico
Porte | 443 utilizzato per l'orchestrazione del canale di controllo<br/>9443 per il trasporto dati

## <a name="replicated-machines"></a>Computer replicati

Site Recovery supporta la replica di qualsiasi carico di lavoro in esecuzione in un computer supportato.

> [!Note]
> Nella tabella seguente è elencato il supporto per i computer con avvio del BIOS. Fare riferimento alla sezione [Archiviazione](#storage) per il supporto su macchine basate su UEFI.

**Componente** | **Dettagli**
--- | ---
Impostazioni del computer | I computer che eseguono la replica in Azure devono soddisfare i [requisiti di Azure](#azure-vm-requirements).
Carico di lavoro del computer | Site Recovery supporta la replica di qualsiasi carico di lavoro in esecuzione in un computer supportato. [Scopri di più](https://aka.ms/asr_workload).
Windows Server 2019 | Supportato [dall'aggiornamento cumulativo 34](https://support.microsoft.com/help/4490016) (versione 9.22 del servizio Mobility) e versioni successive.
Windows Server 2016 a 64 bit | Supportato per Server Core, Server con Esperienza desktop.
Windows Server 2012 R2 / Windows Server 2012 | Supportato.
Windows Server 2008 R2 con SP1 e versioni successive. | Supportato.<br/><br/> Dalla versione [9.30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) dell'agente del servizio Mobility, è necessario [installare l'aggiornamento dello stack di manutenzione (SSU)](https://support.microsoft.com/help/4490628) e [SHA-2](https://support.microsoft.com/help/4474419) nei computer che eseguono Windows 2008 R2 con SP1 o versione successiva. SHA-1 non è supportato da settembre 2019 e se la firma del codice SHA-2 non è abilitata l'estensione dell'agente non installerà/aggiorna come previsto. Ulteriori informazioni [sull'aggiornamento e sui requisiti SHA-2](https://aka.ms/SHA-2KB).
Windows Server 2008 con SP2 o versione successiva (64 bit/32 bit) |  Supportato solo per la migrazione. [Scopri di più](migrate-tutorial-windows-server-2008.md).<br/><br/> Dalla versione [9.30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) dell'agente del servizio Mobility, è necessario [installare l'aggiornamento dello stack di manutenzione (SSU)](https://support.microsoft.com/help/4493730) e [l'aggiornamento SHA-2](hhttps://support.microsoft.com/help/4474419) nei computer Windows 2008 SP2. ISHA-1 non è supportato da settembre 2019 e se la firma del codice SHA-2 non è abilitata l'estensione dell'agente non installerà/aggiorna come previsto. Ulteriori informazioni [sull'aggiornamento e sui requisiti SHA-2](https://aka.ms/SHA-2KB).
Windows 10, Windows 8.1, Windows 8 | Supportato.
Windows 7 con SP1 a 64 bit | Supportato [dall'aggiornamento cumulativo 36](https://support.microsoft.com/help/4503156) (versione 9.22 del servizio Mobility) e versioni successive. </br></br> Dalle [9.30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) dell'agente del servizio Mobility, è necessario installare l'aggiornamento dello stack di [manutenzione (SSU)](https://support.microsoft.com/help/4490628) e [l'aggiornamento SHA-2](https://support.microsoft.com/help/4474419) nei computer Windows 7 SP1.  SHA-1 non è supportato da settembre 2019 e se la firma del codice SHA-2 non è abilitata l'estensione dell'agente non installerà/aggiorna come previsto. Ulteriori informazioni [sull'aggiornamento e sui requisiti SHA-2](https://aka.ms/SHA-2KB).
Linux | È supportato solo il sistema a 64 bit. Il sistema a 32 bit non è supportato.<br/><br/>In ogni server Linux devono essere installati [componenti Linux Integration Services (LIS).](https://www.microsoft.com/download/details.aspx?id=55106) È necessario per avviare il server in Azure dopo il failover di test o il failover. Se mancano i componenti LIS, assicurarsi di installare i componenti prima di abilitare la replica per l'avvio dei computer in Azure.If LIS components are missing, ensure to install the [components](https://www.microsoft.com/download/details.aspx?id=55106) before enabling replication for the machines to boot in Azure. <br/><br/> Site Recovery orchestra il failover per l'esecuzione di server Linux in Azure. Tuttavia i fornitori di Linux potrebbero limitare il supporto solo alle versioni di distribuzione che non hanno raggiunto la fine del ciclo di vita.<br/><br/> Nelle distribuzioni di Linux sono supportati solo i kernel di scorta che fanno parte di una versione/aggiornamento secondaria della distribuzione.<br/><br/> L'aggiornamento di computer protetti tra versioni di distribuzione principali di Linux non è supportato. Per eseguire l'aggiornamento, disabilitare la replica, aggiornare il sistema operativo e quindi abilitare nuovamente la replica.<br/><br/> [Altre informazioni](https://support.microsoft.com/help/2941892/support-for-linux-and-open-source-technology-in-azure) sul supporto per Linux e la tecnologia open source in Azure.Learn more about support for Linux and open-source technology in Azure.
Linux Red Hat Enterprise | da 5,2 a 5,11</b><br/> da 6.1 a 6,10</b> </br> 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6, [7.7](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery), [8.0](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery), 8.1 <br/> Nei server che eseguono Red Hat Enterprise Linux 5.2-5.11 & 6.1-6.10 i [componenti Linux Integration Services (LIS)](https://www.microsoft.com/download/details.aspx?id=55106) non sono stati preinstallati. Assicurarsi di installare i componenti prima di abilitare la replica per l'avvio dei computer in Azure.Ensure to install the [components](https://www.microsoft.com/download/details.aspx?id=55106) before enabling replication for the machines to boot in Azure.
Linux: CentOS | da 5,2 a 5,11</b><br/> da 6.1 a 6,10</b><br/> 7.0 a 7.6<br/> <br/> da 8.0 a 8.1<br/><br/> Nei server che eseguono CentOS 5.2-5.11 & 6.1-6.10 i [componenti Linux Integration Services (LIS)](https://www.microsoft.com/download/details.aspx?id=55106) non sono stati preinstallati. Assicurarsi di installare i componenti prima di abilitare la replica per l'avvio dei computer in Azure.Ensure to install the [components](https://www.microsoft.com/download/details.aspx?id=55106) before enabling replication for the machines to boot in Azure.
Ubuntu | Server UBuntu 14.04 LTS [(esaminare le versioni del kernel supportate)](#ubuntu-kernel-versions)<br/><br/>Server UBuntu 16.04 LTS [(esaminare le versioni del kernel supportate)](#ubuntu-kernel-versions) </br> Server LTS Ubuntu 18.04 [(esaminare le versioni del kernel supportate)](#ubuntu-kernel-versions)
Debian | Debian 7/Debian 8 [(rivedere le versioni del kernel supportate)](#debian-kernel-versions)
SUSE Linux | SUSE Linux Enterprise Server 12 SP1, SP2, SP3, SP4 [(esaminare le versioni del kernel supportate)](#suse-linux-enterprise-server-12-supported-kernel-versions) <br/> SUSE Linux Enterprise Server 15, 15 SP1 [(esaminare le versioni del kernel supportate)](#suse-linux-enterprise-server-15-supported-kernel-versions)<br/> SUSE Linux Enterprise Server 11 SP3, SUSE Linux Enterprise Server 11 SP4<br/> L'aggiornamento dei computer replicati da SUSE Linux Enterprise Server 11 da SP3 a SP4 non è supportato. Per eseguire l'aggiornamento, disabilitare la replica e riabilitarla dopo l'aggiornamento.
Oracle Linux | 6,4, 6,5, 6,6, 6,7, 6,9, 6,10, 7,0, 7,1, 7,2, 7,3, 7.4, 7.5, 7.5, 7.6, [7.77](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery)<br/><br/> Esecuzione del kernel compatibile con Red Hat o di Unbreakable Enterprise Kernel Release 3, 4 & 5 (UEK3, UEK4, UEK5) 

> [!Note]
> Per ognuna delle versioni di Windows, Azure Site Recovery supporta solo compilazioni di canale di manutenzione a [lungo termine (LTSC).](https://docs.microsoft.com/windows-server/get-started-19/servicing-channels-19#long-term-servicing-channel-ltsc)  I rilasci [del Canale semestrale](https://docs.microsoft.com/windows-server/get-started-19/servicing-channels-19#semi-annual-channel) non sono attualmente supportati al momento.

### <a name="ubuntu-kernel-versions"></a>Versioni del kernel Ubuntu

**Versione supportata** | **Versione del servizio Mobility** | **Versione kernel** |
--- | --- | --- |
14.04 LTS | [9.32][9.32 UR] | 3.13.0-24-generico a 3.13.0-170-generic,<br/>Da 3.16.0-25 generica a 3.16.0-77 generica<br/>Da 3.19.0-18 generica a 3.19.0-80 generica<br/>Da 4.2.0-18 generica a 4.2.0-42 generica<br/>4.4.0-21-generico a 4.4.0-148-generico,<br/>4.15.0-1023-azure a 4.15.0-1045-azure |
14.04 LTS | [9.31][9.31 UR] | 3.13.0-24-generico a 3.13.0-170-generic,<br/>Da 3.16.0-25 generica a 3.16.0-77 generica<br/>Da 3.19.0-18 generica a 3.19.0-80 generica<br/>Da 4.2.0-18 generica a 4.2.0-42 generica<br/>4.4.0-21-generico a 4.4.0-148-generico,<br/>4.15.0-1023-azure a 4.15.0-1045-azure |
14.04 LTS | [9.30][9.30 UR] | 3.13.0-24-generico a 3.13.0-170-generic,<br/>Da 3.16.0-25 generica a 3.16.0-77 generica<br/>Da 3.19.0-18 generica a 3.19.0-80 generica<br/>Da 4.2.0-18 generica a 4.2.0-42 generica<br/>4.4.0-21-generico a 4.4.0-148-generico,<br/>4.15.0-1023-azure a 4.15.0-1045-azure |
14.04 LTS | [9.29][9.29 UR]| 3.13.0-24-generico a 3.13.0-170-generic,<br/>Da 3.16.0-25 generica a 3.16.0-77 generica<br/>Da 3.19.0-18 generica a 3.19.0-80 generica<br/>Da 4.2.0-18 generica a 4.2.0-42 generica<br/>4.4.0-21-generico a 4.4.0-148-generico,<br/>4.15.0-1023-azure a 4.15.0-1045-azure |
|||
16.04 LTS | [9.32][9.32 UR] | 4.4.0-21-generico a 4.4.0-171-generico,<br/>Da 4.8.0-34 generica a 4.8.0-58 generica<br/>Da 4.10.0-14 generica a 4.10.0-42 generica<br/>Da 4.11.0-13 generica a 4.11.0-14 generica<br/>Da 4.13.0-16 generica a 4.13.0-45 generica<br/>4.15.0-13-generico a 4.15.0-74-generic<br/>Da 4.11.0-1009 Azure a 4.11.0-1016 Azure<br/>Da 4.13.0-1005 Azure a 4.13.0-1018 Azure <br/>4.15.0-1012-azure a 4.15.0-1066-azure|
16.04 LTS | [9.31][9.31 UR] | 4.4.0-21-generico a 4.4.0-170-generico,<br/>Da 4.8.0-34 generica a 4.8.0-58 generica<br/>Da 4.10.0-14 generica a 4.10.0-42 generica<br/>Da 4.11.0-13 generica a 4.11.0-14 generica<br/>Da 4.13.0-16 generica a 4.13.0-45 generica<br/>4.15.0-13-generico a 4.15.0-72-generico<br/>Da 4.11.0-1009 Azure a 4.11.0-1016 Azure<br/>Da 4.13.0-1005 Azure a 4.13.0-1018 Azure <br/>4.15.0-1012-azure a 4.15.0-1063-azure|
16.04 LTS | [9.30][9.30 UR] | 4.4.0-21-generico a 4.4.0-166-generico,<br/>Da 4.8.0-34 generica a 4.8.0-58 generica<br/>Da 4.10.0-14 generica a 4.10.0-42 generica<br/>Da 4.11.0-13 generica a 4.11.0-14 generica<br/>Da 4.13.0-16 generica a 4.13.0-45 generica<br/>4.15.0-13-generico a 4.15.0-66-generico<br/>Da 4.11.0-1009 Azure a 4.11.0-1016 Azure<br/>Da 4.13.0-1005 Azure a 4.13.0-1018 Azure <br/>4.15.0-1012-azure a 4.15.0-1061-azure|
16.04 LTS | [9.29][9.29 UR] | 4.4.0-21-generico a 4.4.0-164-generico,<br/>Da 4.8.0-34 generica a 4.8.0-58 generica<br/>Da 4.10.0-14 generica a 4.10.0-42 generica<br/>Da 4.11.0-13 generica a 4.11.0-14 generica<br/>Da 4.13.0-16 generica a 4.13.0-45 generica<br/>4.15.0-13-generico a 4.15.0-64-generic<br/>Da 4.11.0-1009 Azure a 4.11.0-1016 Azure<br/>Da 4.13.0-1005 Azure a 4.13.0-1018 Azure <br/>4.15.0-1012-azure a 4.15.0-1059-azure|
|||
18.04 LTS | [9.32][9.32 UR]| 4.15.0-20-generico a 4.15.0-74-generic </br> 4.18.0-13-generico a 4.18.0-25-generico </br> 5.0.0-15-generico a 5.0.0-37-generico </br> 5.3.0-19-generico a 5.3.0-24-generic </br> 4.15.0-1009-azure a 4.15.0-1037-azure </br> 4.18.0-1006-azure a 4.18.0-1025-azure </br> 5.0.0-1012-azure a 5.0.0-1028-azure </br> 5.3.0-1007-azure a 5.3.0-1009-azure|
18.04 LTS | [9.31][9.31 UR]| 4.15.0-20-generico a 4.15.0-72-generico </br> 4.18.0-13-generico a 4.18.0-25-generico </br> 5.0.0-15-generico a 5.0.0-37-generico </br> 5.3.0-19-generico a 5.3.0-24-generic </br> 4.15.0-1009-azure a 4.15.0-1037-azure </br> 4.18.0-1006-azure a 4.18.0-1025-azure </br> 5.0.0-1012-azure a 5.0.0-1025-azure </br> 5.3.0-1007-azure|
18.04 LTS | [9.30][9.30 UR] | 4.15.0-20-generico a 4.15.0-66-generico </br> 4.18.0-13-generico a 4.18.0-25-generico </br> 5.0.0-15-generico a 5.0.0-32-generic </br> 4.15.0-1009-azure a 4.15.0-1037-azure </br> 4.18.0-1006-azure a 4.18.0-1025-azure </br> 5.0.0-1012-azure a 5.0.0-1023-azure|
18.04 LTS | [9.29][9.29 UR] | 4.15.0-20-generico a 4.15.0-62-generic </br> 4.18.0-13-generico a 4.18.0-25-generico </br> 5.0.0-15-generico a 5.0.0-27-generico </br> 4.15.0-1009-azure a 4.15.0-1037-azure </br> 4.18.0-1006-azure a 4.18.0-1025-azure </br> 5.0.0-1012-azure a 5.0.0-1018-azure|

### <a name="debian-kernel-versions"></a>Versioni del kernel Debian


**Versione supportata** | **Versione del servizio Mobility** | **Versione kernel** |
--- | --- | --- |
Debian 7 | [9.29][9.29 UR], [9.30][9.30 UR], [9.31][9.31 UR], [9.32][9.32 UR]| Da 3.2.0-4-amd64 a 3.2.0-6-amd64, 3.16.0-0.bpo.4-amd64 |
|||
Debian 8 | [9.30][9.30 UR], [9.31][9.31 UR], [9.32][9.32 UR] | da 4.16.0-4-amd64 a 3.16.0-10-amd64, 4.9.0.0.bpo.4-amd64 a 4.9.0-0.bpo.11-amd64 |
Debian 8 | [9.29][9.29 UR] | da 3.16.0-4-amd64 a 3.16.0-10-amd64, 4.9.0.0.bpo.4-amd64 a 4.9.0.0.bpo.9-amd64 |

### <a name="suse-linux-enterprise-server-12-supported-kernel-versions"></a>SUSE Linux Enterprise Server 12 (versioni del kernel supportate)

**Rilascio** | **Versione del servizio Mobility** | **Versione kernel** |
--- | --- | --- |
SUSE Linux Enterprise Server 12 (SP1,SP2,SP3,SP4) | [9.32][9.32 UR] | Sono supportati tutti i [kernel SUSE 12 SP1,SP2,SP3,SP4,](https://wiki.microfocus.com/index.php/SUSE/SLES/Kernel_versions#SUSE_Linux_Enterprise_Server_12) di stock.</br></br> 4.4.138-4.7-azure a 4.4.180-4.31-azure,</br>4.12.14-6.3-azure a 4.12.14-6.34-azure  |
SUSE Linux Enterprise Server 12 (SP1,SP2,SP3,SP4) | [9.31][9.31 UR] | Sono supportati tutti i [kernel SUSE 12 SP1,SP2,SP3,SP4,](https://wiki.microfocus.com/index.php/SUSE/SLES/Kernel_versions#SUSE_Linux_Enterprise_Server_12) di stock.</br></br> 4.4.138-4.7-azure a 4.4.180-4.31-azure,</br>4.12.14-6.3-azure a 4.12.14-6.29-azure  |
SUSE Linux Enterprise Server 12 (SP1,SP2,SP3,SP4) | [9.30][9.30 UR] | Sono supportati tutti i [kernel SUSE 12 SP1,SP2,SP3,SP4,](https://wiki.microfocus.com/index.php/SUSE/SLES/Kernel_versions#SUSE_Linux_Enterprise_Server_12) di stock.</br></br> 4.4.138-4.7-azure a 4.4.180-4.31-azure,</br>4.12.14-6.3-azure a 4.12.14-6.26-azure  |
SUSE Linux Enterprise Server 12 (SP1,SP2,SP3,SP4) | [9.29][9.29 UR] | Sono supportati tutti i [kernel SUSE 12 SP1,SP2,SP3,SP4,](https://wiki.microfocus.com/index.php/SUSE/SLES/Kernel_versions#SUSE_Linux_Enterprise_Server_12) di stock.</br></br> 4.4.138-4.7-azure a 4.4.180-4.31-azure,</br>4.12.14-6.3-azure a 4.12.14-6.23-azure  |

### <a name="suse-linux-enterprise-server-15-supported-kernel-versions"></a>Versioni del kernel supportate da SUSE Linux Enterprise Server 15

**Rilascio** | **Versione del servizio Mobility** | **Versione kernel** |
--- | --- | --- |
SUSE Linux Enterprise Server 15 e 15 SP1 | 9.32 | Sono supportati tutti i [kernel SUSE 15 e 15.](https://wiki.microfocus.com/index.php/SUSE/SLES/Kernel_versions#SUSE_Linux_Enterprise_Server_15)</br></br> 4.12.14-5.5-azure a 4.12.14-8.22-azure |

## <a name="linux-file-systemsguest-storage"></a>File system/archiviazione guest Linux

**Componente** | **Supportato**
--- | ---
File system | ext3, ext4, XFS, BTRFS (condizioni applicabili in base alla presente tabella)
Gestore volumi | - LVM è supportato.<br/> - /boot su LVM è supportato [dall'aggiornamento cumulativo 31](https://support.microsoft.com/help/4478871/) (versione 9.20 del servizio Mobility) in poi. Non è supportato nelle versioni precedenti del servizio Mobility.<br/> - Più dischi del sistema operativo non sono supportati.
Dispositivi di archiviazione paravirtualizzati | I dispositivi esportati da driver paravirtualizzati non sono supportati.
Dispositivi di I/O a blocchi a code multiple | Non supportato.
Server fisici con controller di archiviazione HP CCISS | Non supportato.
Convenzione di denominazione del dispositivo e del punto di montaggio | Il nome del dispositivo o del punto di montaggio deve essere univoco.<br/> Assicurarsi che due dispositivi/punti di montaggio non abbiano nomi con distinzione tra maiuscole e minuscole. Ad esempio, la denominazione dei dispositivi per la stessa macchina virtuale di *device1* e *Device1* non è supportata.
Directory | Se si esegue una versione del servizio Mobility precedente alla versione 9.20 (rilasciata [nell'aggiornamento cumulativo 31](https://support.microsoft.com/help/4478871/)), si applicano le restrizioni seguenti:<br/><br/> - Queste directory (se impostate come partizioni/file system separati) devono trovarsi sullo stesso disco del sistema operativo nel server di origine: /(root), /boot, /usr, /usr/local, /var, /etc.</br> - La directory /boot deve trovarsi in una partizione del disco e non essere un volume LVM.<br/><br/> Dalla versione 9.20 in poi, queste restrizioni non si applicano. 
Directory di avvio | - I dischi di avvio non devono essere in formato partizione GPT. Si tratta di una limitazione dell'architettura di Azure.This is an Azure architecture limitation. I dischi GPT sono supportati come dischi dati.<br/><br/> Più dischi di avvio in una macchina virtuale non sono supportatiMultiple boot disks on a VM aren't supported<br/><br/> - /boot su un volume LVM su più di schivare non è supportato.<br/> - Un computer senza disco di avvio non può essere replicato.
Requisiti di spazio disponibile nel disco| 2 GB nella partizione /root <br/><br/> 250 MB nella cartella di installazione
XFSv5 | Sono supportate le funzionalità XFSv5 nei file system XFS, ad esempio il checksum dei metadati (Mobility service version 9.10 onwards).<br/> Usare l'utilità xfs_info per controllare il superblocco XFS per la partizione. Se `ftype` è impostato su 1, le funzionalità XFSv5 sono in uso.
Btrfs | BTRFS è supportato [dall'aggiornamento cumulativo 34](https://support.microsoft.com/help/4490016) (versione 9.22 del servizio Mobility) in poi. BTRFS non è supportato se:<br/><br/> - Il sottovolume del file system BTRFS viene modificato dopo l'abilitazione della protezione.</br> - Il file system BTRFS è distribuito su più dischi.</br> - Il file system BTRFS supporta RAID.

## <a name="vmdisk-management"></a>Gestione della macchina virtuale e del disco

**Azione** | **Dettagli**
--- | ---
Ridimensionamento del disco nella macchina virtuale replicata | Supportato nella macchina virtuale di origine prima del failover, direttamente nelle proprietà della macchina virtuale. Non è necessario disabilitare/riattivare la replica.<br/><br/> Se si modifica la macchina virtuale di origine dopo il failover, le modifiche non vengono acquisite.<br/><br/> Se si modificano le dimensioni del disco nella macchina virtuale di Azure dopo il failover, quando si esegue il failback, Site Recovery crea una nuova macchina virtuale con gli aggiornamenti.
Aggiunta del disco alla macchina virtuale replicata | Non supportato.<br/> Disabilitare la replica per la macchina virtuale, aggiungere il disco e quindi riabilitare la replica.

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
Azure ExpressRoute | Sì
ILB | Sì
ELB | Sì
Gestione traffico di Azure | Sì
Più NIC | Sì
Indirizzo IP riservato | Sì
IPv4 | Sì
Conservazione indirizzo IP di origine | Sì
Endpoint servizio di rete virtuale di Azure<br/> | Sì
Rete accelerata | No

## <a name="storage"></a>Archiviazione
**Componente** | **Supportato**
--- | ---
Disco dinamico | Il disco del sistema operativo deve essere un disco di base. <br/><br/>I dischi dati possono essere dinamici
Configurazione dei dischi Docker | No
NFS host | Sì per VMware<br/><br/> No per server fisici
Host SAN (iSCSI/FC) | Sì
Host vSAN | Sì per VMware<br/><br/> N/D per server fisici
Percorsi multipli (MPIO) | Sì, testato con DSM Microsoft, EMC PowerPath 5.7 SP4, DSM EMC PowerPath per CLARiiON
Volumi virtuali host | Sì per VMware<br/><br/> N/D per server fisici
VMDK guest/server | Sì
Disco cluster condiviso guest/server | No
Disco crittografato guest/server | No
NFS guest/server | No
iSCSI guest/server | Per la migrazione - Sì<br/>Per il ripristino di emergenza - No, iSCSI eseguirà il failback come disco collegato alla macchina virtualeFor Disaster Recovery - No, iSCSI will failback as an attached disk to the VM
SMB 3.0 guest/server | No
RDM guest/server | Sì<br/><br/> N/D per server fisici
Disco guest/server > 1 TB | Sì, le dimensioni del disco devono essere superiori a 1024 MB<br/><br/>Fino a 8.192 GB durante la replica su dischi gestiti (versione 9.26 e versioni successive)<br></br> Fino a 4.095 GB durante la replica in account di archiviazione
Disco guest/server con dimensioni logiche di settore a 4 KB e dimensioni fisiche di settore a 4 KB | No
Disco guest/server con dimensioni del settore fisico 4K logiche e 512 byte | No
Volume con disco con striping > 4 TB guest/server <br/><br/>Gestione volumi logici (LVM)| Sì
Guest/server - Spazi di archiviazione | No
Disco di aggiunta/rimozione a caldo guest/server | No
Guest/server - esclusione disco | Sì
Percorsi multipli (MPIO) guest/server | No
Partizioni GPT guest/server | Cinque partizioni sono supportate [dall'aggiornamento cumulativo 37](https://support.microsoft.com/help/4508614/) (versione 9.25 del servizio Mobility) in poi. In precedenza ne erano sostenuti quattro.
ReFS | Resilient File System è supportato con il servizio Mobility versione 9.23 o successiva
Avvio EFI/UEFI guest/server | - Supportato per Windows Server 2012 o versione successiva, SLES 12 SP4 e RHEL 8.0 con agente per dispositivi mobili versione 9.30 e versioni successive<br/> - Il tipo di avvio UEFI sicuro non è supportato. 

## <a name="replication-channels"></a>Canali di replica

|**Tipo di replica**   |**Supportato**  |
|---------|---------|
|Trasferimenti di dati scaricati (ODX)    |       No  |
|Offline Seeding        |   No      |
| Azure Data Box | No

## <a name="azure-storage"></a>Archiviazione di Azure

**Componente** | **Supportato**
--- | ---
Archiviazione con ridondanza locale | Sì
Archiviazione con ridondanza geografica | Sì
Archiviazione con ridondanza geografica e accesso in lettura | Sì
Archiviazione ad accesso sporadico | No
Archiviazione ad accesso frequente| No
BLOB in blocchi | No
Crittografia a riposo (SSE)| Sì
Crittografia a riposo (CMK)| Sì (tramite il modulo Powershell Az 3.3.0 in poi)
Archiviazione Premium | Sì
Servizio di importazione/esportazione | No
Azure Storage firewalls for VNets | Sì.<br/> Configurato nell'account di archiviazione cache/archiviazione di destinazione (utilizzato per archiviare i dati di replica).
Account di archiviazione v2 generici (livelli hot e cool)General-purpose v2 storage accounts (hot and cool tiers) | Sì (i costi di transazione sono notevolmente più elevati per V2 rispetto al V1)

## <a name="azure-compute"></a>Calcolo di Azure

**Funzionalità** | **Supportato**
--- | ---
Set di disponibilità | Sì
Zone di disponibilità | No
HUB | Sì
Dischi gestiti | Sì

## <a name="azure-vm-requirements"></a>Requisiti per le VM di Azure

Le macchine virtuali locali replicate in Azure devono soddisfare i requisiti delle macchine virtuali di Azure riepilogati in questa tabella. Quando Site Recovery esegue un controllo dei prerequisiti per la replica, il controllo avrà esito negativo se alcuni dei requisiti non vengono soddisfatti.

**Componente** | **Requisiti** | **Dettagli**
--- | --- | ---
Sistema operativo guest | Verificare i [sistemi operativi supportati](#replicated-machines) per le macchine replicate. | Il controllo ha esito negativo se non supportato.
Architettura del sistema operativo guest | 64 bit. | Il controllo ha esito negativo se non supportato.
Dimensioni disco del sistema operativo | Fino a 2.048 GB. | Il controllo ha esito negativo se non supportato.
Conteggio dischi del sistema operativo | 1 | Il controllo ha esito negativo se non supportato.
Conteggio dischi dati | 64 o un numero inferiore. | Il controllo ha esito negativo se non supportato.
Dimensioni del disco dati | Fino a 8.192 GB durante la replica su disco gestito (versione 9.26 in poi)<br></br>Fino a 4.095 GB durante la replica in un account di archiviazione| Il controllo ha esito negativo se non supportato.
Schede di rete | Sono supportate più schede. |
VHD condiviso | Non supportato. | Il controllo ha esito negativo se non supportato.
Disco FC | Non supportato. | Il controllo ha esito negativo se non supportato.
BitLocker | Non supportato. | Prima di abilitare la replica per un computer, occorre disabilitare BitLocker. |
Nome della VM. | Da 1 a 63 caratteri.<br/><br/> Limitato a lettere, numeri e trattini.<br/><br/> Il nome del computer deve iniziare e terminare con una lettera o un numero. |  Aggiornare il valore nelle proprietà del computer in Site Recovery.

## <a name="resource-group-limits"></a>Limiti dei gruppi di risorse

Per comprendere il numero di macchine virtuali che possono essere protette in un singolo gruppo di risorse, vedere l'articolo sui [limiti di sottoscrizione e le quoteTo](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#resource-group-limits) understand the number of virtual machines that can be protected under a single resource group, refer to the article on subscription limits and quotas

## <a name="churn-limits"></a>Limiti di varianza

La tabella seguente indica i limiti di Azure Site Recovery. 
- Questi limiti si basano sui nostri test, ma non coprono tutte le possibili combinazioni di I/O delle app.
- I risultati effettivi possono variare in base alla combinazione di I/O delle applicazioni.
- Per ottenere risultati ottimali, è consigliabile eseguire [lo strumento Pianificazione distribuzione](site-recovery-deployment-planner.md)ed eseguire test completi delle applicazioni usando failover di test per ottenere l'immagine delle prestazioni reale per l'app.

**Destinazione replica** | **Dimensione media dei / o del disco di origine** |**Media scissioni dei dati del disco di origine** | **Variazione totale dei dati del disco di origine al giorno**
---|---|---|---
Archiviazione standard | 8 KB | 2 MB/s | 168 GB per disco
Disco P10 o P15 Premium | 8 KB  | 2 MB/s | 168 GB per disco
Disco P10 o P15 Premium | 16 KB | 4 MB/s |  336 GB per disco
Disco P10 o P15 Premium | 32 KB o superiori | 8 MB/s | 672 GB per disco
Disco P20, P30, P40 o P50 Premium | 8 KB    | 5 MB/s | 421 GB per disco
Disco P20, P30, P40 o P50 Premium | 16 KB o superiori |20 MB/s | 1684 GB per disco


**Varianza dati di origine** | **Limite massimo**
---|---
Picco di varianza dei dati su tutti i dischi in una VM | 54 MB/s
Varianza massima dei dati al giorno supportata da un server di elaborazione | 2 TB

- Si tratta di numeri medi presupponendo una sovrapposizione I/O del 30%.
- Site Recovery può gestire una velocità effettiva maggiore in base alla percentuale di sovrapposizione, alle dimensioni di scrittura maggiori e all'effettivo I/O del carico di lavoro.
- Questi numeri presuppongono un tipico backlog di circa cinque minuti. ovvero i dati, dopo essere stati caricati, verranno elaborati e verrà creato un punto di ripristino entro cinque minuti.

## <a name="vault-tasks"></a>Attività di insieme di credenziali

**Azione** | **Supportato**
--- | ---
Spostamento insieme di credenziali tra gruppi di risorse | No
Spostare l'insieme di credenziali all'interno e tra le sottoscrizioni | No
Spostamento di risorse di archiviazione, rete e VM di Azure tra gruppi di risorse | No
Spostare archiviazione, rete, macchine virtuali di Azure all'interno e tra le sottoscrizioni. | No


## <a name="obtain-latest-components"></a>Ottenere i componenti più recenti

**Nome** | **Descrizione** | **Dettagli**
--- | --- | ---
Server di configurazione | Installato in locale.<br/> Coordina le comunicazioni tra server VMware locali o computer fisici e Azure. | - [Informazioni sul](vmware-physical-azure-config-process-server-overview.md) server di configurazione.<br/> - [Scopri come](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server) eseguire l'aggiornamento alla versione più recente.<br/> - [Informazioni sulla](vmware-azure-deploy-configuration-server.md) configurazione del server di configurazione. 
Server di elaborazione | Installato per impostazione predefinita nel server di configurazione.<br/> Riceve i dati di replica, li ottimizza con la memorizzazione nella cache, la compressione e la crittografia e li invia ad Azure.Receives replication data, optimizes it with caching, compression, and encryption, and sends it to Azure.<br/> Man mano che la distribuzione aumenta, è possibile aggiungere altri server di elaborazione per gestire volumi maggiori di traffico di replica. | - [Informazioni sul](vmware-physical-azure-config-process-server-overview.md) server di elaborazione.<br/> - [Scopri come](vmware-azure-manage-process-server.md#upgrade-a-process-server) eseguire l'aggiornamento alla versione più recente.<br/> - [Informazioni sulla](vmware-physical-large-deployment.md#set-up-a-process-server) configurazione dei server di elaborazione con scalabilità verticale.
Servizio Mobility | Installato in VM VMware o server fisici che si desidera replicare.<br/> Coordina la replica tra i server VMware/server fisici locali e Azure.| - [Informazioni sul](vmware-physical-mobility-service-overview.md) servizio Mobility.<br/> - [Scopri come](vmware-physical-manage-mobility-service.md#update-mobility-service-from-azure-portal) eseguire l'aggiornamento alla versione più recente.<br/> 



## <a name="next-steps"></a>Passaggi successivi
[Informazioni su come](tutorial-prepare-azure.md) preparare Azure per il ripristino di emergenza delle macchine virtuali VMware.

[9.32 UR]: https://support.microsoft.com/en-in/help/4538187/update-rollup-44-for-azure-site-recovery
[9.31 UR]: https://support.microsoft.com/en-in/help/4537047/update-rollup-43-for-azure-site-recovery
[9.30 UR]: https://support.microsoft.com/en-in/help/4531426/update-rollup-42-for-azure-site-recovery
[9.29 UR]: https://support.microsoft.com/en-in/help/4528026/update-rollup-41-for-azure-site-recovery
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
