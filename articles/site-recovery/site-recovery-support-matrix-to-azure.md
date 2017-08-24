---
title: Matrice di supporto di Azure Site Recovery per la replica in Azure | Documentazione Microsoft
description: Riepiloga i sistemi operativi e componenti supportati per Azure Site Recovery
services: site-recovery
documentationcenter: 
author: Rajani-Janaki-Ram
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/04/2017
ms.author: rajanaki
ms.translationtype: HT
ms.sourcegitcommit: 22aa82e5cbce5b00f733f72209318c901079b665
ms.openlocfilehash: a2ccc3d43a56a569897e1efe24f576eb92610ec3
ms.contentlocale: it-it
ms.lasthandoff: 07/24/2017

---
# <a name="azure-site-recovery-support-matrix-for-replicating-from-on-premises-to-azure"></a>Matrice di supporto di Azure Site Recovery per la replica da locale ad Azure


In questo articolo vengono riepilogati le configurazioni e i componenti supportati per Azure Site Recovery durante la replica e il ripristino in Azure. Per altre informazioni sui requisiti di Azure Site Recovery, vedere i [prerequisiti](site-recovery-prereq.md).


## <a name="support-for-deployment-options"></a>Supporto per opzioni di distribuzione

**Distribuzione** | **Server fisico/VMware** | **Hyper-V (con/senza Virtual Machine Manager)** |
--- | --- | ---
**Portale di Azure** | Macchine virtuali VMware locali in Archiviazione di Azure, con Azure Resource Manager o archiviazione e reti classiche.<br/><br/> Failover in VM classiche o basate su Resource Manager. | VM Hyper-V locali in Archiviazione di Azure, con Resource Manager o archiviazione e reti classiche.<br/><br/> Failover in VM classiche o basate su Resource Manager.
**Portale classico** | Solo modalità manutenzione. Non è possibile creare nuovi insiemi di credenziali. | Solo modalità manutenzione.
**PowerShell** | Attualmente non è supportata. | Supportato


## <a name="support-for-datacenter-management-servers"></a>Supporto per server di gestione dei data center

### <a name="virtualization-management-entities"></a>Entità di gestione della virtualizzazione

**Distribuzione** | **Supporto**
--- | ---
**Server fisico/VM VMware** | vCenter 6.5, 6.0 o 5.5
**Hyper-V (con Virtual Machine Manager)** | System Center Virtual Machine Manager 2016 e System Center Virtual Machine Manager 2012 R2

  >[!Note]
  > Un cloud System Center Virtual Machine Manager 2016 con una combinazione di host Windows Server 2016 e 2012 R2 non è attualmente supportato.

### <a name="host-servers"></a>Server host

**Distribuzione** | **Supporto**
--- | ---
**Server fisico/VM VMware** | vSphere 6.5, 6.0 e 5.5
**Hyper-V (con/senza Virtual Machine Manager)** | Windows Server 2016, Windows Server 2012 R2 con gli aggiornamenti più recenti.<br></br>Se si usa SCVMM, gli host Windows Server 2016 dovranno essere gestiti da SCVMM 2016.


  >[!Note]
  >Un sito di Hyper-V con una combinazione di host che eseguono Windows Server 2016 e 2012 R2 non è attualmente supportato. Il ripristino in un percorso alternativo per le VM in un host Windows Server 2016 non è attualmente supportato.

## <a name="support-for-replicated-machine-os-versions"></a>Supporto per le versioni dei sistemi operativi dei computer replicati

In caso di replica in Azure, le macchine virtuali protette devono soddisfare i [requisiti di Azure](#failed-over-azure-vm-requirements).
La tabella seguente offre un riepilogo dei sistemi operativi replicati supportati nei vari scenari di distribuzione quando si usa Azure Site Recovery. Il supporto è applicabile per qualsiasi carico di lavoro in esecuzione nel sistema operativo indicato.

 **Server fisico/VMware** | **Hyper-V (con/senza VMM)** |
--- | --- |
Le VM Windows Server 2012 R2 a 64 bit, Windows Server 2012, Windows Server 2008 R2 con SP1 o successivo<br/>*Windows Server 2016*: non supportato attualmente in macchine virtuali VMware e server fisici. <br/><br/> Red Hat Enterprise Linux: da 5.2 a 5.11, da 6.1 a 6.8, da 7.0 a 7.3 <br/><br/>CentOS: da 5.2 a 5.11, da 6.1 a 6.8, da 7.0 a 7.3 <br/><br/>Server Ubuntu 14.04 LTS[ (versioni del kernel supportate)](#supported-ubuntu-kernel-versions-for-vmwarephysical-servers)<br/><br/>Server Ubuntu 16.04 LTS[ (versioni del kernel supportate)](#supported-ubuntu-kernel-versions-for-vmwarephysical-servers)<br/><br/>Oracle Enterprise Linux 6.4 o 6.5 che esegue il kernel compatibile Red Hat o Unbreakable Enterprise Kernel versione 3 (UEK3) <br/><br/> SUSE Linux Enterprise Server 11 SP3 <br/><br/> SUSE Linux Enterprise Server 11 SP4 <br/>(L'aggiornamento dei computer di replica da SLES 11 SP3 a SLES 11 SP4 non è supportato. Se un computer replicato è stato aggiornato da 11SP3 SLES a SLES 11 SP4, è necessario disabilitare la replica e proteggere di nuovo il computer dopo l'aggiornamento.) | Qualsiasi sistema operativo guest [supportato da Azure](https://technet.microsoft.com/library/cc794868.aspx)


>[!IMPORTANT]
>(Applicabile ai server VMware/fisici replicati in Azure)
>
> Sui server Red Hat Enterprise Linux Server 7+ e CentOS 7+ la versione del kernel 3.10.0-514 è supportata a partire dalla versione 9.8 del servizio Mobility di Azure Site Recovery.<br/><br/>
> I clienti sul kernel di 3.10.0-514 con una versione del servizio Mobility inferiore alla 9.8 devono disabilitare la replica, aggiornare il servizio Mobility alla versione 9.8 e quindi abilitare nuovamente la replica.


### <a name="supported-ubuntu-kernel-versions-for-vmwarephysical-servers"></a>Versioni del kernel Ubuntu supportate per server VMware/fisici

**Versione** | **Versione del servizio Mobility** | **Versione del kernel** |
--- | --- | --- |
14.04 LTS | 9.9 | Da 3.13.0-24 generica a 3.13.0-117 generica<br/>Da 3.16.0-25 generica a 3.16.0-77 generica<br/>Da 3.19.0-18 generica a 3.19.0-80 generica<br/>Da 4.2.0-18 generica a 4.2.0-42 generica<br/>Da 4.4.0-21 generica a 4.4.0-75 generica |
14.04 LTS | 9.10 | Da 3.13.0-24 generica a 3.13.0-121 generica<br/>Da 3.16.0-25 generica a 3.16.0-77 generica<br/>Da 3.19.0-18 generica a 3.19.0-80 generica<br/>Da 4.2.0-18 generica a 4.2.0-42 generica<br/>Da 4.4.0-21 generica a 4.4.0-81 generica |
16.04 LTS | 9.10 | Da 4.4.0-21 generica a 4.4.0-81 generica<br/>Da 4.8.0-34 generica a 4.8.0-56 generica<br/>Da 4.10.0-14 generica a 4.10.0-24 generica |


## <a name="supported-file-systems-and-guest-storage-configurations-on-linux-vmwarephysical-servers"></a>File system e configurazioni di archiviazione guest supportate in Linux (server VMware/fisici)

I file system e i software di configurazione dell'archiviazione seguenti sono supportati nei server Linux eseguiti in server fisici o VMware:
* File system: ext3, ext4, ReiserFS (solo Suse Linux Enterprise Server), XFS
* Gestore volumi: LVM2
* Software con percorsi multipli: mapper dispositivi

I dispositivi di archiviazione paravirtualizzati, ovvero dispositivi esportati da driver paravirtualizzati, non sono supportati.<br/>
I dispositivi di I/O a blocchi a code multiple non sono supportati.<br/>
I server fisici con il controller di archiviazione HP CCISS non sono supportati.<br/>

>[!Note]
> Sui server Linux le seguenti directory (se impostate come partizioni o file system separati) devono essere tutte nello stesso disco (il disco del sistema operativo) nel server di origine: / (root), /boot, /usr, /usr/local, /var, ecc.<br/><br/>
> Le funzionalità di XFSv5 nei file system XFS, ad esempio il checksum dei metadati, sono supportate a partire dalla versione 9.10 del servizio Mobility. Se si usano le funzionalità di XFSv5, verificare che sia in esecuzione il servizio Mobility 9.10 o versione successiva. È possibile usare l'utilità xfs_info per controllare il superblocco XFS per la partizione. Se ftype è impostato su 1, le funzionalità XFSv5 sono in uso.
>


## <a name="support-for-network-configuration"></a>Supporto per la configurazione di rete
Le tabelle seguenti offrono un riepilogo delle configurazioni di rete supportate nei vari scenari di distribuzione che usano Azure Site Recovery per la replica in Azure.

### <a name="host-network-configuration"></a>Configurazione di rete per host

**Configurazione** | **Server fisico/VMware** | **Hyper-V (con/senza Virtual Machine Manager)**
--- | --- | ---
Gruppo NIC | Sì<br/><br/>Non supportato quando i computer fisici vengono replicati| Sì
VLAN | Sì | Sì
IPv4 | Sì | Sì
IPv6 | No | No

### <a name="guest-vm-network-configuration"></a>Configurazione di rete per VM guest

**Configurazione** | **Server fisico/VMware** | **Hyper-V (con/senza Virtual Machine Manager)**
--- | --- | ---
Gruppo NIC | No | No
IPv4 | Sì | Sì
IPv6 | No | No
IP statico (Windows) | Sì | Sì
IP statico (Linux) | Sì <br/><br/>Macchine virtuali è configurato per l'utilizzo di DHCP in failback  | No
Più NIC | Sì | Sì

### <a name="failed-over-azure-vm-network-configuration"></a>Configurazione di rete per VM di Azure sottoposte a failover

**Rete di Azure** | **Server fisico/VMware** | **Hyper-V (con/senza Virtual Machine Manager)**
--- | --- | ---
Express Route | Sì | Sì
ILB | Sì | Sì
ELB | Sì | Sì
Gestione traffico | Sì | Sì
Più NIC | Sì | Sì
IP riservato | Sì | Sì
IPv4 | Sì | Sì
Conservazione IP origine | Sì | Sì


## <a name="support-for-storage"></a>Supporto per archiviazione
Le tabelle seguenti offrono un riepilogo delle configurazioni di archiviazione supportate nei vari scenari di distribuzione che usano Azure Site Recovery per la replica in Azure.

### <a name="host-storage-configuration"></a>Configurazione di archiviazione per host

**Configurazione** | **Server fisico/VMware** | **Hyper-V (con/senza Virtual Machine Manager)**
--- | --- | --- | ---
NFS | Sì per VMware<br/><br/> No per server fisici | N/D
SMB 3.0 | N/D | Sì
SAN (iSCSI) | Sì | Sì
Percorsi multipli (MPIO)<br></br>Testata con: DSM Microsoft, EMC PowerPath 5.7 SP4, DSM EMC PowerPath per CLARiiON | Sì | Sì

### <a name="guest-or-physical-server-storage-configuration"></a>Configurazione di archiviazione per server fisici o guest

**Configurazione** | **Server fisico/VMware** | **Hyper-V (con/senza Virtual Machine Manager)**
--- | --- | ---
VMDK | Sì | N/D
VHD/VHDX | N/D | Sì
VM di seconda generazione | N/D | Sì
EFI/UEFI| No | Sì
Disco cluster condiviso | No | No
Disco crittografato | No | No
NFS | No | N/D
SMB 3.0 | No | No
RDM | Sì<br/><br/> N/D per server fisici | N/D
Disco superiore a 1 TB | Sì<br/><br/>Fino a 4095 GB | Sì<br/><br/>Fino a 4095 GB
Disco con dimensioni del settore di 4K | Sì | Sì, supportato per VM di generazione 1<br/><br/>Non supportato per VM di generazione 2
Volume con disco con striping superiore a 1 TB<br/><br/> Gestione volumi logici (LVM) | Sì | Sì
Spazi di archiviazione | No | Sì
Aggiunta/rimozione a caldo disco | No | No
Esclusione disco | Sì | Sì
Percorsi multipli (MPIO) | N/D | Sì

**Archiviazione di Azure** | **Server fisico/VMware** | **Hyper-V (con/senza Virtual Machine Manager)**
--- | --- | ---
Archiviazione con ridondanza locale | Sì | Sì
Archiviazione con ridondanza geografica | Sì | Sì
RA-GRS | Sì | Sì
Archiviazione ad accesso sporadico | No | No
Archiviazione ad accesso frequente| No | No
Crittografia dei dati inattivi (SSE)| Sì | Sì
Archiviazione Premium | Sì | Sì
Servizio di importazione/esportazione | No | No


## <a name="support-for-azure-compute-configuration"></a>Supporto per configurazione di calcolo di Azure

**Funzionalità di calcolo** | **Server fisico/VMware** | **Hyper-V (con/senza Virtual Machine Manager)**
--- | --- | --- 
Set di disponibilità | Sì | Sì
HUB | Sì | Sì  
Dischi gestiti | Sì | Sì<br/><br/>Il failback in locale da macchina virtuale Azure con i dischi gestiti non è attualmente supportato.

## <a name="failed-over-azure-vm-requirements"></a>Requisiti per VM di Azure sottoposte a failover

È possibile distribuire Site Recovery per replicare macchine virtuali e server fisici in esecuzione su qualsiasi sistema operativo supportato da Azure, vale a dire la maggior parte delle versioni di Windows e Linux. In caso di replica in Azure, le macchine virtuali locali da replicare devono essere conformi ai requisiti di Azure riportati di seguito.

**Entità** | **Requisiti** | **Dettagli**
--- | --- | ---
**Sistema operativo guest** | Replica da Hyper-V ad Azure: Site Recovery supporta tutti i sistemi operativi [supportati da Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx). <br/><br/> Per la replica di server fisici e VMware, controllare i [prerequisiti](site-recovery-vmware-to-azure-classic.md) | Il controllo dei prerequisiti avrà esito negativo se non supportato.
**Architettura sistema operativo guest** | 64 bit | Il controllo dei prerequisiti avrà esito negativo se non supportato
**Dimensioni disco del sistema operativo** | Fino a 2048 GB se si replicano **macchine virtuali VMware o server fisici in Azure**.<br/><br/>Fino a 2048 GB per le macchine virtuali **Hyper-V di prima generazione**.<br/><br/>Fino a 300 GB per le **macchine virtuali Hyper-V di seconda generazione**.  | Il controllo dei prerequisiti avrà esito negativo se non supportato
**Numero di dischi del sistema operativo** | 1 | Il controllo dei prerequisiti avrà esito negativo se non supportato.
**Numero di dischi dati** | 64 o meno se si replicano **VM VMware in Azure**; 16 o meno se si replicano **VM Hyper-V in Azure** | Il controllo dei prerequisiti avrà esito negativo se non supportato
**Dimensioni dei dischi rigidi virtuali dei dischi dati** | Fino a 4095 GB | Il controllo dei prerequisiti avrà esito negativo se non supportato
**Schede di rete** | Sono supportate più schede |
**Disco rigido virtuale condiviso** | Non supportate | Il controllo dei prerequisiti avrà esito negativo se non supportato
**Disco FC** | Non supportate | Il controllo dei prerequisiti avrà esito negativo se non supportato
**Formato disco rigido** | VHD  <br/><br/> VHDX | Anche se VHDX al momento non è supportato in Azure, in Site Recovery VHDX viene convertito automaticamente in VHD quando si esegue il failover in Azure. Quando si esegue il failback in locale, le macchine virtuali continuano a utilizzare il formato VHDX.
**BitLocker** | Non supportate | Prima di proteggere una macchina virtuale è necessario disabilitare BitLocker.
**Nome VM** | Tra 1 e 63 caratteri. Limitato a lettere, numeri e trattini. Il nome della macchina virtuale deve iniziare e terminare con una lettera o un numero. | Aggiornare il valore nelle proprietà della macchina virtuale in Site Recovery.
**Tipo di VM** | Prima generazione<br/><br/> Seconda generazione - Windows | Sono supportate le macchine virtuali di seconda generazione con disco del sistema operativo di base che include uno o più volumi di dati in formato VHDX e inferiori a 300 GB di spazio su disco.<br></br>Le macchine virtuali Linux di seconda generazione non sono supportate. [Altre informazioni](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/)|

## <a name="support-for-recovery-services-vault-actions"></a>Supporto per azioni su insiemi di credenziali di Servizi di ripristino

**Azione** | **Server fisico/VMware** | **Hyper-V (senza Virtual Machine Manager)** | **Hyper-V (con Virtual Machine Manager)**
--- | --- | --- | ---
Spostamento insieme di credenziali tra gruppi di risorse<br/><br/> All'interno e tra sottoscrizioni | No | No | No
Spostamento di risorse di archiviazione, rete e VM di Azure tra gruppi di risorse<br/><br/> All'interno e tra sottoscrizioni | No | No | No


## <a name="support-for-provider-and-agent"></a>Supporto per provider e agente

**Nome** | **Descrizione** | **Versione più recente** | **Dettagli**
--- | --- | --- | --- | ---
**Provider di Azure Site Recovery** | Coordina le comunicazioni tra server locali e Azure <br/><br/> Installato nei server Virtual Machine Manager locali o nei server Hyper-V se non è presente un server Virtual Machine Manager | 5.1.19 ([disponibile dal portale](http://aka.ms/downloaddra)) | [Funzionalità e correzioni più recenti](https://support.microsoft.com/kb/3155002)
**Installazione unificata di Azure Site Recovery (da VMware ad Azure)** | Coordina le comunicazioni tra server VMware locali e Azure  <br/><br/> Installato su server VMware locali | 9.3.4246.1 (disponibile dal portale) | [Funzionalità e correzioni più recenti](https://support.microsoft.com/kb/3155002)
**Servizio Mobility** | Coordina la replica fra server VMware locali/server fisici e sito Azure/secondario<br/><br/> Installato in server fisici o in macchine virtuali VMware da replicare  | N/D (disponibile dal portale) | N/D
**Agente di Servizi di ripristino di Microsoft Azure (MARS)** | Coordina la replica tra le macchine virtuali Hyper-V e Azure<br/><br/> Installato nei server Hyper-V locali (con o senza un server Virtual Machine Manager) | Agente più recente ([disponibile dal portale](http://aka.ms/latestmarsagent)) |






## <a name="next-steps"></a>Passaggi successivi
[Verifica dei prerequisiti](site-recovery-prereq.md)

