---
title: Matrice di supporto di Azure Site Recovery | Documentazione Microsoft
description: Riepiloga i sistemi operativi e componenti supportati per Azure Site Recovery
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: 1bbcc13c-ea21-4349-9ddf-0d7dfdcdcbfb
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 12/04/2016
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: 1e88eba53b8ec3388ede1ae69cb290423919fda6
ms.openlocfilehash: d683e25ef96bbd87a6d5b5ea143754b8f67f288e


---
# <a name="azure-site-recovery-support-matrix"></a>Matrice di supporto di Azure Site Recovery

Questo articolo offre un riepilogo dei sistemi operativi e dei componenti supportati per Azure Site Recovery. Un elenco dei prerequisiti e dei componenti supportati è disponibile per ogni scenario di distribuzione in ogni articolo sulla distribuzione corrispondente. Questo argomento contiene un riepilogo.

## <a name="support-for-azure-replication-scenarios"></a>Supporto per gli scenari di replica di Azure

**Distribuzione** | **Server fisico/VMware** | **Hyper-V (senza VMM)** | **Hyper-V (con VMM)**
--- | --- | --- | ---
**Portale di Azure** | Macchine virtuali VMware locali in Archiviazione di Azure, con Resource Manager o archiviazione e reti classiche.<br/><br/> Failover in VM classiche o basate su Resource Manager. | Macchine virtuali locali Hyper-V (non in cloud VMM) in Archiviazione di Azure, con Resource Manager o archiviazione e reti classiche.<br/><br/> Failover in VM classiche o basate su Resource Manager. | Macchine virtuali locali Hyper-V (in cloud MM) in Archiviazione di Azure, con Resource Manager o archiviazione e reti classiche.<br/><br/> Failover in VM classiche o basate su Resource Manager.
**Portale classico** | Solo modalità manutenzione. Non è possibile creare nuovi insiemi di credenziali. | Solo modalità manutenzione. | Solo modalità manutenzione.
**PowerShell** | Attualmente non è supportata. | Supportato | Supportato


## <a name="support-for-secondary-site-replication-scenarios"></a>Supporto per gli scenari di replica del sito secondario

**Distribuzione** | **Server fisico/VMware** | **Hyper-V (senza VMM)** | **Hyper-V (con VMM)**
--- | --- | --- | ---
**Portale di Azure** | Macchine virtuali VMware locali nel sito VMware secondario.<br/><br/> Scaricare il Manuale dell'utente di InMage Scout (http://download.microsoft.com/download/E/0/8/E08B3BCE-3631-4CED-8E65-E3E7D252D06D/InMage_Scout_Standard_User_Guide_8.0.1.pdf). Non disponibile nel portale di Azure | Non supportate | Macchine virtuali Hyper-V locali in cloud VMM in un cloud VMM secondario<br/><br/> Solo replica Hyper-V standard, SAN non supportato
**Portale classico** | Solo modalità manutenzione. Non è possibile creare nuovi insiemi di credenziali. | Solo modalità manutenzione. | Solo modalità manutenzione.
**PowerShell** | Non supportati. | ND | Supportato



## <a name="support-for-virtualization-server-operating-systems"></a>Supporto per sistemi operativi per server di virtualizzazione

### <a name="host-servers-replicate-to-azure"></a>Server host (replica in Azure)

**Server fisico/VM VMware** | **Hyper-V (senza VMM)** | **Hyper-V (con VMM)**
--- | --- | ---
vCenter 5.5 o 6.0 (supporto per le sole funzionalità 5.5)  <br/><br/> vSphere 6.0, 5.5, o 5.1 con gli ultimi aggiornamenti | Windows Server 2016, Windows Server 2012 R2 con gli aggiornamenti più recenti<br/><br/> Un sito di Hyper-V con una combinazione di host che eseguono Windows Server 2016 e 2012 R2 non è attualmente supportato. | Windows Server 2016, Windows Server 2012 R2 con gli aggiornamenti più recenti<br/><br/> Gli host Windows Server 2016 devono essere gestiti da VMM che eseguono System Center 2016.<br/><br/> Un cloud VMM 2016 con una combinazione di host Windows Server 2016 e 2012 R2 non è attualmente supportato.

### <a name="host-servers-replicate-to-secondary-site"></a>Server host (replica in sito secondario)

**Server fisico/VM VMware** | **Hyper-V (con VMM)**
--- | --- | ---
vCenter 5.5 o 6.0 (supporto per le sole funzionalità 5.5)  <br/><br/> vSphere 6.0, 5.5, o 5.1 con gli ultimi aggiornamenti | Windows Server 2016, Windows Server 2012 R2 o Windows Server 2012 con gli aggiornamenti più recenti.<br/><br/> Gli host Windows Server 2016 devono essere gestiti da VMM che eseguono System Center 2016.<br/><br/> Un cloud VMM 2016 con una combinazione di host Windows Server 2016 e versioni precedenti non è attualmente supportato.


## <a name="support-for-replicated-machines"></a>Supporto per computer replicati

### <a name="machines-replicate-to-azure"></a>Computer (replica in Azure)

Le macchine virtuali devono essere conformi ai [requisiti di Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements).

**Requisito** | **Server fisico/VMware** | **Hyper-V (senza VMM)** | **Hyper-V (con VMM)**
--- | --- | --- | ---
Ciò che viene replicato | Qualsiasi carico di lavoro in VM Windows o Linux | Qualsiasi carico di lavoro | Qualsiasi carico di lavoro
Sistema operativo host | Le VM Windows Server 2012 R2 a 64 bit, Windows Server 2012, Windows Server 2008 R2 con SP1 o successivo<br/><br/> Red Hat Enterprise Linux 6.7, 7.1, 7.2 <br/><br/> Centos 6.5, 6.6, 6.7, 7.0, 7.1, 7.2 <br/><br/> Oracle Enterprise Linux 6.4 o 6.5 che esegue il kernel compatibile Red Hat o Unbreakable Enterprise Kernel versione 3 (UEK3) <br/><br/> SUSE Linux Enterprise Server 11 SP3<bbr/><br/> Archiviazione richiesta: file system (EXT3, ETX4, ReiserFS, XFS); Multipath software-Device Mapper (multipath)); Archiviazione volumi: (LVM2). I server fisici con archiviazione del controller HP CCISS non sono supportati. Il file system ReiserFS è supportato solo su SUSE Linux Enterprise Server 11 SP3. | Qualsiasi sistema operativo guest [supportato da Azure](https://technet.microsoft.com/library/cc794868.aspx) | Qualsiasi sistema operativo guest [supportato da Azure](https://technet.microsoft.com/library/cc794868.aspx)


### <a name="machines-replicate-to-secondary-site"></a>Computer (replica in sito secondario)

**Requisito** | **Server fisico/VMware** | **Hyper-V (con VMM)**
--- | --- | ---
Ciò che viene replicato | Qualsiasi carico di lavoro in VM Windows o Linux | Qualsiasi carico di lavoro | Qualsiasi carico di lavoro
Sistema operativo host | Le VM Windows Server 2012 R2 a 64 bit, Windows Server 2012, Windows Server 2008 R2 con SP1 o successivo<br/><br/> Red Hat Enterprise Linux 6.7, 7.1, 7.2 <br/><br/> Centos 6.5, 6.6, 6.7, 7.0, 7.1, 7.2 <br/><br/> Oracle Enterprise Linux 6.4 o 6.5 che esegue il kernel compatibile Red Hat o Unbreakable Enterprise Kernel versione 3 (UEK3) <br/><br/> SUSE Linux Enterprise Server 11 SP3<bbr/><br/> Archiviazione richiesta: file system (EXT3, ETX4, ReiserFS, XFS); Multipath software-Device Mapper (multipath)); Archiviazione volumi: (LVM2).<br/><br/> I server fisici con archiviazione del controller HP CCISS non sono supportati. Il file system ReiserFS è supportato solo su SUSE Linux Enterprise Server 11 SP3. | Qualsiasi sistema operativo guest supportato da Hyper-V (https://technet.microsoft.com/library/mt126277.aspx)


## <a name="support-for-provider-and-agent"></a>Supporto per provider e agente

**Nome** | **Descrizione** | **Versione più recente** | **Dettagli**
--- | --- | --- | --- | ---
**Provider di Azure Site Recovery** | Coordina le comunicazioni tra i server locali e il sito di Azure/secondario  <br/><br/> Installato su server VMM locali o server Hyper-V se non esiste alcun server VMM | 5.1.1700 (disponibile dal portale) | [Funzionalità e correzioni più recenti](https://support.microsoft.com/kb/3155002)
**Installazione unificata di Azure Site Recovery (da VMware ad Azure)** | Coordina le comunicazioni tra server VMware locali e Azure  <br/><br/> Installato su server VMware locali | 9.3.4246.1 (disponibile dal portale) | [Funzionalità e correzioni più recenti](https://support.microsoft.com/kb/3155002)
**Servizio Mobility** | Coordina la replica fra server VMware locali/server fisici e sito Azure/secondario<br/><br/> Installato in server fisici o in macchine virtuali VMware da replicare  | ND (disponibile dal portale) | .
**Agente di Servizi di ripristino di Microsoft Azure (MARS)** | Coordina la replica tra le macchine virtuali Hyper-V e Azure<br/><br/> Installato nei server Hyper-V locali (con o senza un server VMM) | |

## <a name="support-for-networking"></a>Supporto per rete

### <a name="networking-replicate-to-azure"></a>Rete (replica in Azure)

**Rete host** | **Server fisico/VMware** | **Hyper-V (senza VMM)** | **Hyper-V (con VMM)**
--- | --- | --- | ---
Gruppo NIC | Sì<br/><br/>Non supportato in computer fisici| Sì | Sì
VLAN | Sì | Sì | Sì
IPv4 | Sì | Sì | Sì
IPv6 | No | No | No

**Rete VM guest** | **Server fisico/VMware** | **Hyper-V (senza VMM)** | **Hyper-V (con VMM)**
--- | --- | --- | ---
Gruppo NIC | No | No | No
IPv4 | Sì | Sì | Sì
IPv6 | No | No | No
IP statico (Windows) | Sì | Sì | Sì
IP statico (Linux) | No | No | No
Più NIC | Sì | Sì | Sì

**Rete di Azure** | **Server fisico/VMware** | **Hyper-V (senza VMM)** | **Hyper-V (con VMM)**
--- | --- | --- | ---
Express Route | Sì | Sì | Sì
ILB | Sì | Sì | Sì
ELB | Sì |  |
Gestione traffico | Sì | Sì | Sì
Più NIC | Sì | Sì | Sì
IP riservato | Sì | Sì | Sì
IPv4 | Sì | Sì | Sì
Conservazione IP origine | Sì | Sì | Sì

### <a name="networking-replicate-to-secondary-site"></a>Rete (replica in sito secondario)

**Rete host** | **Server fisico/VMware** | **Hyper-V (con VMM)**
--- | --- | ---
Gruppo NIC | Sì | Sì
VLAN | Sì | Sì
IPv4 | Sì | Sì
IPv6 | No | No

**Rete VM guest** | **Server fisico/VMware** | **Hyper-V (con VMM)**
--- | --- | ---
Gruppo NIC | No | No
IPv4 | Sì | Sì
IPv6 | No | No
IP statico (Windows) | Sì | Sì
IP statico (Linux) | Sì | Sì
Più NIC | Sì | Sì


## <a name="support-for-storage"></a>Supporto per archiviazione

### <a name="storage-replicate-to-azure"></a>Archiviazione (replica in Azure)

**Archiviazione (host)** | **Server fisico/VMware** | **Hyper-V (senza VMM)** | **Hyper-V (con VMM)**
--- | --- | --- | ---
NFS | Sì per VMware<br/><br/> No per server fisici | ND | ND
SMB 3.0 | ND | Sì | Sì
SAN (iSCSI) | Sì | Sì | Sì
Percorsi multipli (MPIO) | Sì per VMware<br/><br/> N/D per server fisici | Sì | Sì

**Archiviazione (server fisico/VM guest)** | **Server fisico/VMware** | **Hyper-V (senza VMM)** | **Hyper-V (con VMM)**
--- | --- | --- | ---
VMDK | Sì | ND | ND
VHD/VHDX | ND | Sì | Sì
VM di seconda generazione | ND | Sì | Sì
Disco cluster condiviso | Sì per VMware<br/><br/> N/D per server fisici | No | No
Disco crittografato | No | No | No
EFI/UEFI| No | Sì | Sì
NFS | No | No | No
SMB 3.0 | No | No | No
RDM | Sì<br/><br/> N/D per server fisici | ND | ND
Disco superiore a 1 TB | No | No | No
Volume con disco con striping superiore a 1 TB<br/><br/> LVM | Sì | Sì | Sì
Spazi di archiviazione | No | Sì | Sì
Aggiunta/rimozione a caldo disco | No | No | No
Esclusione disco | Sì | No | No
Percorsi multipli (MPIO) | ND | Sì | Sì

**Archiviazione di Azure** | **Server fisico/VMware** | **Hyper-V (senza VMM)** | **Hyper-V (con VMM)**
--- | --- | --- | ---
Archiviazione con ridondanza locale | Sì | Sì | Sì
Archiviazione con ridondanza geografica | Sì | Sì | Sì
Archiviazione ad accesso sporadico | No | No | No
Archiviazione ad accesso frequente| No | No | No
Crittografia di dati inattivi | Sì | Sì | Sì
Archiviazione Premium | Sì | No | No
Servizio di importazione/esportazione | No | No | No


### <a name="storage-replicate-to-secondary-site"></a>Archiviazione (replica in sito secondario)

**Archiviazione (host)** | **Server fisico/VMware** | **Hyper-V (con VMM)**
--- | --- | ---
NFS | Sì | ND
SMB 3.0 | ND | Sì
SAN (iSCSI) | Sì | Sì
Percorsi multipli (MPIO) | Sì | Sì

**Archiviazione (server fisico/VM guest)** | **Server fisico/VMware** | **Hyper-V (con VMM)**
--- | --- | ---
VMDK | Sì | ND
VHD/VHDX | ND | Sì (fino a 16 dischi)
VM di seconda generazione | ND | Sì
Disco cluster condiviso | Sì  | No
Disco crittografato | No | No
UEFI| No | ND
NFS | No | No
SMB 3.0 | No | No
RDM | Sì | ND
Disco superiore a 1 TB | No | Sì
Volume con disco con striping superiore a 1 TB<br/><br/> LVM | Sì | Sì
Spazi di archiviazione | No | Sì
Aggiunta/rimozione a caldo disco | No | No
Esclusione disco | No | No
Percorsi multipli (MPIO) | ND | Sì

## <a name="support-for-recovery-services-vault-actions"></a>Supporto per azioni su insiemi di credenziali di Servizi di ripristino

### <a name="vaults-replicate-to-azure"></a>Insiemi di credenziali (replicare in Azure)

**Azione** | **Server fisico/VMware** | **Hyper-V (senza VMM)** | **Hyper-V (con VMM)**
--- | --- | --- | ---
Spostamento insieme di credenziali tra gruppi di risorse<br/><br/> All'interno e tra sottoscrizioni | No | No | No
Spostamento di risorse di archiviazione, rete e VM di Azure tra gruppi di risorse<br/><br/> All'interno e tra sottoscrizioni | No | No | No

### <a name="vaults-replicate-to-secondary-site"></a>Insiemi di credenziali (replica in sito secondario)

**Azione** | **Server fisico/VMware** | **Hyper-V (con VMM)**
--- | --- | ---
Spostamento insieme di credenziali tra gruppi di risorse<br/><br/> All'interno e tra sottoscrizioni | No | No
Spostamento di risorse di archiviazione, rete e VM di Azure tra gruppi di risorse<br/><br/> All'interno e tra sottoscrizioni | No | No


## <a name="support-for-azure-compute-replicate-to-azure"></a>Supporto per calcolo di Azure (replica in Azure)

**Funzionalità di calcolo** | **Server fisico/VMware** | **Hyper-V (senza VMM)** | **Hyper-V (con VMM)**
--- | --- | --- | ---
Cluster guest in dischi condivisi | No | No | No
Set di disponibilità | No | No | No
HUB | Sì | Sì | Sì

## <a name="support-for-azure-vms"></a>Supporto per macchine virtuali di Azure

È possibile distribuire Site Recovery per replicare macchine virtuali e server fisici che eseguono qualsiasi sistema operativo supportato da Azure, vale a dire la maggior parte delle versioni di Windows e Linux. Le macchine virtuali locali da replicare devono essere conformi ai requisiti di Azure.

**Funzionalità** | **Requisiti** | **Dettagli**
--- | --- | ---
**Host Hyper-V** | Deve eseguire Windows Server 2012 R2 o versione successiva | Il controllo dei prerequisiti avrà esito negativo se il sistema operativo non è supportato.
**Hypervisor VMware** | Sistema operativo supportato. | [Controllare i requisiti](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment)
**Sistema operativo guest** | Replica da Hyper-V ad Azure: Site Recovery supporta tutti i sistemi operativi [supportati da Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx). <br/><br/> Per la replica di server fisici e VMware, controllare i [prerequisiti](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment) | Il controllo dei prerequisiti avrà esito negativo se non supportato.
**Architettura sistema operativo guest** | 64 bit | Il controllo dei prerequisiti avrà esito negativo se non supportato
**Dimensioni disco del sistema operativo** | Fino a 1023 GB | Il controllo dei prerequisiti avrà esito negativo se non supportato
**Numero di dischi del sistema operativo** | 1 | Il controllo dei prerequisiti avrà esito negativo se non supportato.
**Numero di dischi dati** | 64 o meno se si replicano **VM VMware in Azure**; 16 o meno se si replicano **VM Hyper-V in Azure** | Il controllo dei prerequisiti avrà esito negativo se non supportato
**Dimensioni dei dischi rigidi virtuali dei dischi dati** | Fino a 1023 GB | Il controllo dei prerequisiti avrà esito negativo se non supportato
**Schede di rete** | Sono supportate più schede |
**Indirizzo IP statico** | Supportato | Se la macchina virtuale principale usa un indirizzo IP statico, è possibile specificare l'indirizzo IP statico per la macchina virtuale che verrà creata in Azure<br/><br/> L'indirizzo IP statico non è supportato per una **VM Linux in esecuzione in Hyper-V**.
**Disco iSCSI** | Non supportate | Il controllo dei prerequisiti avrà esito negativo se non supportato
**Disco rigido virtuale condiviso** | Non supportate | Il controllo dei prerequisiti avrà esito negativo se non supportato
**Disco FC** | Non supportate | Il controllo dei prerequisiti avrà esito negativo se non supportato
**Formato disco rigido** | VHD  <br/><br/> VHDX | Anche se VHDX al momento non è supportato in Azure, in Site Recovery VHDX viene convertito automaticamente in VHD quando si esegue il failover in Azure. Quando si esegue il failback in locale, le macchine virtuali continuano a utilizzare il formato VHDX.
**BitLocker** | Non supportate | Prima di proteggere una macchina virtuale è necessario disabilitare BitLocker.
**Nome VM** | Tra 1 e 63 caratteri. Limitato a lettere, numeri e trattini. Il nome deve iniziare e terminare con una lettera o un numero | Aggiornare il valore nelle proprietà della macchina virtuale in Site Recovery
**Tipo di VM** | Prima generazione<br/><br/> Seconda generazione - Windows | Sono supportate le VM di seconda generazione con disco del sistema operativo di base che include uno o più volumi di dati in formato VHDX e inferiori a 300 GB.<br/><br/>. Le VM Linux di seconda generazione non sono supportate. [Altre informazioni](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/) |








## <a name="next-steps"></a>Passaggi successivi
[Preparare la distribuzione](site-recovery-best-practices.md)



<!--HONumber=Feb17_HO2-->


