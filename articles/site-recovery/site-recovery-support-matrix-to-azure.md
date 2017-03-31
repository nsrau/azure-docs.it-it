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
ms.date: 01/25/2017
ms.author: rajanaki
translationtype: Human Translation
ms.sourcegitcommit: cfe4957191ad5716f1086a1a332faf6a52406770
ms.openlocfilehash: da63e54b3f4e27ed3c4a1fd909c6c28295c6730d
ms.lasthandoff: 03/09/2017


---
# <a name="azure-site-recovery-support-matrix-for-replicating-to-azure"></a>Matrice di supporto di Azure Site Recovery per la replica in Azure

> [!div class="op_single_selector"]
> * [Replica in Azure](site-recovery-support-matrix-to-azure.md)
> * [Replica in sito secondario di proprietà del cliente](site-recovery-support-matrix-to-sec-site.md)


In questo articolo vengono riepilogati le configurazioni e i componenti supportati per Azure Site Recovery durante la replica e il ripristino in Azure. Per altre informazioni sui requisiti di Azure Site Recovery, vedere i [prerequisiti](site-recovery-prereq.md).


## <a name="support-for-deployment-options"></a>Supporto per opzioni di distribuzione

**Distribuzione** | **Server fisico/VMware** | **Hyper-V (senza Virtual Machine Manager)** | **Hyper-V (con Virtual Machine Manager)**
--- | --- | --- | ---
**Portale di Azure** | Macchine virtuali VMware locali in Archiviazione di Azure, con Azure Resource Manager o archiviazione e reti classiche.<br/><br/> Failover in VM classiche o basate su Resource Manager. | Macchine virtuali locali Hyper-V (non in cloud Virtual Machine Manager) in Archiviazione di Azure, con Resource Manager o archiviazione e reti classiche.<br/><br/> Failover in VM classiche o basate su Resource Manager. | Macchine virtuali locali Hyper-V (in cloud Virtual Machine Manager) in Archiviazione di Azure, con Resource Manager o archiviazione e reti classiche.<br/><br/> Failover in VM classiche o basate su Resource Manager.
**Portale classico** | Solo modalità manutenzione. Non è possibile creare nuovi insiemi di credenziali. | Solo modalità manutenzione. | Solo modalità manutenzione.
**PowerShell** | Attualmente non è supportata. | Supportato | Supportato


## <a name="support-for-datacenter-management-servers"></a>Supporto per server di gestione dei data center

### <a name="virtualization-management-entities"></a>Entità di gestione della virtualizzazione

**Distribuzione** | **Supporto**
--- | ---
**Server fisico/VM VMware** | vSphere 6.0, 5.5 o 5.1 con aggiornamento più recente
**Hyper-V (con Virtual Machine Manager)** | System Center Virtual Machine Manager 2016 e System Center Virtual Machine Manager 2012 R2

  >[!Note]
  > Un cloud System Center Virtual Machine Manager 2016 con una combinazione di host Windows Server 2016 e 2012 R2 non è attualmente supportato. 

### <a name="host-servers"></a>Server host

**Distribuzione** | **Supporto**
--- | ---
**Server fisico/VM VMware** | vCenter 5.5 o 6.0 (supporto per le sole funzionalità 5.5) 
**Hyper-V (senza Virtual Machine Manager)** | Windows Server 2016, Windows Server 2012 R2 con gli aggiornamenti più recenti
**Hyper-V (con Virtual Machine Manager)** | Windows Server 2016, Windows Server 2012 R2 con gli aggiornamenti più recenti.<br/><br/> Gli host Windows Server 2016 devono essere gestiti da System Center Virtual Machine Manager 2016.

  >[!Note]
  >Un sito di Hyper-V con una combinazione di host che eseguono Windows Server 2016 e 2012 R2 non è attualmente supportato. Il ripristino in un percorso alternativo per le VM in un host Windows Server 2016 non è attualmente supportato.

## <a name="support-for-replicated-machine-os-versions"></a>Supporto per le versioni dei sistemi operativi dei computer replicati

In caso di replica in Azure, le macchine virtuali protette devono soddisfare i [requisiti di Azure](#failed-over-azure-vm-requirements).
La tabella seguente offre un riepilogo dei sistemi operativi replicati supportati nei vari scenari di distribuzione quando si usa Azure Site Recovery. Il supporto è applicabile per qualsiasi carico di lavoro in esecuzione nel sistema operativo indicato.

 **Server fisico/VMware** | **Hyper-V (senza Virtual Machine Manager)** | **Hyper-V (con Virtual Machine Manager)**
--- | --- | ---
Le VM Windows Server 2012 R2 a 64 bit, Windows Server 2012, Windows Server 2008 R2 con SP1 o successivo<br/><br/> Red Hat Enterprise Linux 6.7, 6.8, 7.1, 7.2 <br/><br/> CentOS 6.5, 6.6, 6.7, 6.8, 7.0, 7.1, 7.2 <br/><br/> Oracle Enterprise Linux 6.4 o 6.5 che esegue il kernel compatibile Red Hat o Unbreakable Enterprise Kernel versione 3 (UEK3) <br/><br/> SUSE Linux Enterprise Server 11 SP3 | Qualsiasi sistema operativo guest [supportato da Azure](https://technet.microsoft.com/library/cc794868.aspx) | Qualsiasi sistema operativo guest [supportato da Azure](https://technet.microsoft.com/library/cc794868.aspx)


>[!Note]
>Supporto dell'archiviazione per versioni di Linux: file system EXT3, ETX4, ReiserFS e XFS, software per percorsi multipli Device Mapper, gestore dei volumi LVM2 e server fisici con archiviazione del controller HP CCISS *non* sono supportati.
>Il file system ReiserFS è supportato solo su SUSE Linux Enterprise Server 11 SP3.

## <a name="support-for-network-configuration"></a>Supporto per la configurazione di rete
Le tabelle seguenti offrono un riepilogo delle configurazioni di rete supportate nei vari scenari di distribuzione che usano Azure Site Recovery per la replica in Azure.

### <a name="host-network-configuration"></a>Configurazione di rete per host

**Configurazione** | **Server fisico/VMware** | **Hyper-V (senza Virtual Machine Manager)** | **Hyper-V (con Virtual Machine Manager)**
--- | --- | --- | ---
Gruppo NIC | Sì<br/><br/>Non supportato in computer fisici| Sì | Sì
VLAN | Sì | Sì | Sì
IPv4 | Sì | Sì | Sì
IPv6 | No | No | No

### <a name="guest-vm-network-configuration"></a>Configurazione di rete per VM guest

**Configurazione** | **Server fisico/VMware** | **Hyper-V (senza Virtual Machine Manager)** | **Hyper-V (con Virtual Machine Manager)**
--- | --- | --- | ---
Gruppo NIC | No | No | No
IPv4 | Sì | Sì | Sì
IPv6 | No | No | No
IP statico (Windows) | Sì | Sì | Sì
IP statico (Linux) | No | No | No
Più NIC | Sì | Sì | Sì

### <a name="failed-over-azure-vm-network-configuration"></a>Configurazione di rete per VM di Azure sottoposte a failover

**Rete di Azure** | **Server fisico/VMware** | **Hyper-V (senza Virtual Machine Manager)** | **Hyper-V (con Virtual Machine Manager)**
--- | --- | --- | ---
Express Route | Sì | Sì | Sì
ILB | Sì | Sì | Sì
ELB | Sì | Sì | Sì
Gestione traffico | Sì | Sì | Sì
Più NIC | Sì | Sì | Sì
IP riservato | Sì | Sì | Sì
IPv4 | Sì | Sì | Sì
Conservazione IP origine | Sì | Sì | Sì


## <a name="support-for-storage"></a>Supporto per archiviazione
Le tabelle seguenti offrono un riepilogo delle configurazioni di archiviazione supportate nei vari scenari di distribuzione che usano Azure Site Recovery per la replica in Azure.

### <a name="host-storage-configuration"></a>Configurazione di archiviazione per host

**Configurazione** | **Server fisico/VMware** | **Hyper-V (senza Virtual Machine Manager)** | **Hyper-V (con Virtual Machine Manager)**
--- | --- | --- | ---
NFS | Sì per VMware<br/><br/> No per server fisici | N/D | N/D
SMB 3.0 | N/D | Sì | Sì
SAN (iSCSI) | Sì | Sì | Sì
Percorsi multipli (MPIO)<br></br>Testata con: DSM Microsoft, EMC PowerPath 5.7 SP4, DSM EMC PowerPath per CLARiiON | Sì | Sì | Sì

### <a name="guest-or-physical-server-storage-configuration"></a>Configurazione di archiviazione per server fisici o guest

**Configurazione** | **Server fisico/VMware** | **Hyper-V (senza Virtual Machine Manager)** | **Hyper-V (con Virtual Machine Manager)**
--- | --- | --- | ---
VMDK | Sì | N/D | N/D
VHD/VHDX | N/D | Sì | Sì
VM di seconda generazione | N/D | Sì | Sì
EFI/UEFI| No | Sì | Sì
Disco cluster condiviso | Sì per VMware<br/><br/> N/D per server fisici | No | No
Disco crittografato | No | No | No
NFS | No | N/D | N/D
SMB 3.0 | No | No | No
RDM | Sì<br/><br/> N/D per server fisici | N/D | N/D
Disco superiore a 1 TB | No | No | No
Volume con disco con striping superiore a 1 TB<br/><br/> Gestione volumi logici (LVM) | Sì | Sì | Sì
Spazi di archiviazione | No | Sì | Sì
Aggiunta/rimozione a caldo disco | No | No | No
Esclusione disco | Sì | Sì | Sì
Percorsi multipli (MPIO) | N/D | Sì | Sì

**Archiviazione di Azure** | **Server fisico/VMware** | **Hyper-V (senza Virtual Machine Manager)** | **Hyper-V (con Virtual Machine Manager)**
--- | --- | --- | ---
Archiviazione con ridondanza locale | Sì | Sì | Sì
Archiviazione con ridondanza geografica | Sì | Sì | Sì
Archiviazione ad accesso sporadico | No | No | No
Archiviazione ad accesso frequente| No | No | No
Crittografia dei dati inattivi (SSE)| Sì | Sì | Sì
Archiviazione Premium | Sì | No | No
Servizio di importazione/esportazione | No | No | No


## <a name="support-for-azure-compute-configuration"></a>Supporto per configurazione di calcolo di Azure

**Funzionalità di calcolo** | **Server fisico/VMware** | **Hyper-V (senza Virtual Machine Manager)** | **Hyper-V (con Virtual Machine Manager)**
--- | --- | --- | ---
Set di disponibilità | No | No | No
HUB | Sì | Sì | Sì

## <a name="failed-over-azure-vm-requirements"></a>Requisiti per VM di Azure sottoposte a failover

È possibile distribuire Site Recovery per replicare macchine virtuali e server fisici in esecuzione su qualsiasi sistema operativo supportato da Azure, vale a dire la maggior parte delle versioni di Windows e Linux. In caso di replica in Azure, le macchine virtuali locali da replicare devono essere conformi ai requisiti di Azure riportati di seguito.

**Entità** | **Requisiti** | **Dettagli**
--- | --- | ---
**Sistema operativo guest** | Replica da Hyper-V ad Azure: Site Recovery supporta tutti i sistemi operativi [supportati da Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx). <br/><br/> Per la replica di server fisici e VMware, controllare i [prerequisiti](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment) | Il controllo dei prerequisiti avrà esito negativo se non supportato.
**Architettura sistema operativo guest** | 64 bit | Il controllo dei prerequisiti avrà esito negativo se non supportato
**Dimensioni disco del sistema operativo** | Fino a 1023 GB | Il controllo dei prerequisiti avrà esito negativo se non supportato
**Numero di dischi del sistema operativo** | 1 | Il controllo dei prerequisiti avrà esito negativo se non supportato.
**Numero di dischi dati** | 64 o meno se si replicano **VM VMware in Azure**; 16 o meno se si replicano **VM Hyper-V in Azure** | Il controllo dei prerequisiti avrà esito negativo se non supportato
**Dimensioni dei dischi rigidi virtuali dei dischi dati** | Fino a 1023 GB | Il controllo dei prerequisiti avrà esito negativo se non supportato
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
[Controllare i prerequisiti](site-recovery-prereq.md)

