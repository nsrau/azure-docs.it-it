---
title: Matrice di supporto per la replica di VM VMware e server fisici in Azure con Azure Site Recovery | Microsoft Docs
description: Riepiloga i sistemi operativi e i componenti supportati per la replica di macchine virtuali VMware e server fisici in Azure tramite Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 03/08/2018
ms.author: raynew
ms.openlocfilehash: 413234204175b9361cd2a837e0b318bf5220f58f
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="support-matrix-for-vmware-and-physical-server-replication-to-azure"></a>Matrice di supporto per la replica di VM VMware e server fisici in Azure

Questo articolo riepiloga i componenti supportati e le impostazioni per il ripristino di emergenza di macchine virtuali VMware in Azure tramite [Azure Site Recovery](site-recovery-overview.md).

## <a name="supported-scenarios"></a>Scenari supportati

**Scenario** | **Dettagli**
--- | ---
**VM VMware** | È possibile eseguire il ripristino di emergenza in Azure per le macchine virtuali VMware locali. Si può distribuire questo scenario con il portale di Azure o tramite PowerShell.
**Server fisici** | È possibile eseguire il ripristino di emergenza in Azure per server fisici Windows/Linux locali. Si può distribuire questo scenario nel portale di Azure.

## <a name="on-premises-virtualizationhost-servers"></a>Server di virtualizzazione/host locali

**Server** | **Requisiti** | **Dettagli**
--- | --- | ---
**VMware** | Server vCenter 6.5, 6.0 o 5.5 oppure vSphere 6.5, 6.0 o 5.5 | È consigliabile usare un server vCenter.
**Server fisici** | N/D


## <a name="replicated-machines"></a>Computer replicati

La tabella seguente riepiloga il supporto della replica per le macchine virtuali VMware e i server fisici. Site Recovery supporta la replica di qualsiasi carico di lavoro in esecuzione in un computer con un sistema operativo supportato.

**Componente** | **Dettagli**
--- | ---
Impostazioni del computer | I computer che eseguono la replica in Azure devono soddisfare i [requisiti di Azure](#failed-over-azure-vm-requirements).
Sistema operativo Windows | Windows Server 2016 a 64 bit (Server Core, server con Esperienza desktop), Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 con almeno SP1. Windows 2016 Nano Server non è supportato.
Sistema operativo Linux | Red Hat Enterprise Linux: da 5.2 a 5.11, da 6.1 a 6.9, da 7.0 a 7.4 <br/><br/>CentOS: da 5.2 a 5.11, da 6.1 a 6.9, da 7.0 a 7.4 <br/><br/>Server Ubuntu 14.04 LTS[ (versioni del kernel supportate)](#supported-ubuntu-kernel-versions-for-vmwarephysical-servers)<br/><br/>Server Ubuntu 16.04 LTS[ (versioni del kernel supportate)](#supported-ubuntu-kernel-versions-for-vmwarephysical-servers)<br/><br/>Debian 7/Debian 8<br/><br/>Oracle Enterprise Linux 6.4, 6.5 che esegue il kernel compatibile Red Hat o Unbreakable Enterprise Kernel versione 3 (UEK3) <br/><br/>SUSE Linux Enterprise Server 11 SP3, SUSE Linux Enterprise Server 11 SP4 <br/><br/>L'aggiornamento dei computer replicati da SP3 a SP4 non è supportato. Per eseguire l'aggiornamento, disabilitare la replica e abilitarla nuovamente dopo l'aggiornamento.

>[!NOTE]
>
> - Nelle distribuzioni di Linux sono supportati solo i kernel di scorta che fanno parte di una versione/aggiornamento secondaria della distribuzione.
>
> - L'aggiornamento di computer protetti tra versioni di distribuzione principali di Linux non è supportato. Per eseguire l'aggiornamento, disabilitare la replica, aggiornare il sistema operativo e quindi abilitare nuovamente la replica.
>

### <a name="ubuntu-kernel-versions"></a>Versioni del kernel Ubuntu


**Versione supportata** | **Versione del servizio Mobility di Azure Site Recovery** | **Versione del kernel** |
--- | --- | --- |
14.04 LTS | 9.10 | Da 3.13.0-24 generica a 3.13.0-121 generica<br/>Da 3.16.0-25 generica a 3.16.0-77 generica<br/>Da 3.19.0-18 generica a 3.19.0-80 generica<br/>Da 4.2.0-18 generica a 4.2.0-42 generica<br/>Da 4.4.0-21 generica a 4.4.0-81 generica |
14.04 LTS | 9.11 | Da 3.13.0-24 generica a 3.13.0-128 generica<br/>Da 3.16.0-25 generica a 3.16.0-77 generica<br/>Da 3.19.0-18 generica a 3.19.0-80 generica<br/>Da 4.2.0-18 generica a 4.2.0-42 generica<br/>Da 4.4.0-21 generica a 4.4.0-91 generica |
14.04 LTS | 9.12 | Da 3.13.0-24 generica a 3.13.0-132 generica<br/>Da 3.16.0-25 generica a 3.16.0-77 generica<br/>Da 3.19.0-18 generica a 3.19.0-80 generica<br/>Da 4.2.0-18 generica a 4.2.0-42 generica<br/>Da 4.4.0-21 generica a 4.4.0-96 generica |
14.04 LTS | 9.13 | Da 3.13.0-24 generica a 3.13.0-137 generica<br/>Da 3.16.0-25 generica a 3.16.0-77 generica<br/>Da 3.19.0-18 generica a 3.19.0-80 generica<br/>Da 4.2.0-18 generica a 4.2.0-42 generica<br/>Da 4.4.0-21 generica a 4.4.0-104 generica |
16.04 LTS | 9.10 | Da 4.4.0-21 generica a 4.4.0-81 generica<br/>Da 4.8.0-34 generica a 4.8.0-56 generica<br/>Da 4.10.0-14 generica a 4.10.0-24 generica |
16.04 LTS | 9.11 | Da 4.4.0-21 generica a 4.4.0-91 generica<br/>Da 4.8.0-34 generica a 4.8.0-58 generica<br/>Da 4.10.0-14 generica a 4.10.0-32 generica |
16.04 LTS | 9.12 | Da 4.4.0-21 generica a 4.4.0-96 generica<br/>Da 4.8.0-34 generica a 4.8.0-58 generica<br/>Da 4.10.0-14 generica a 4.10.0-35 generica |
16.04 LTS | 9.13 | Da 4.4.0-21 generica a 4.4.0-104 generica<br/>Da 4.8.0-34 generica a 4.8.0-58 generica<br/>Da 4.10.0-14 generica a 4.10.0-42 generica |

## <a name="linux-file-systemsguest-storage"></a>File system/archiviazione guest Linux

**Componente** | **Supportato**
--- | ---
File system | ext3, ext4, ReiserFS (solo Suse Linux Enterprise Server), XFS
Gestore volumi | LVM2
Software con percorsi multipli | Mapper dispositivi
Dispositivi di archiviazione paravirtualizzati | I dispositivi esportati da driver paravirtualizzati non sono supportati.
Dispositivi di I/O a blocchi a code multiple | Non supportati.
Server fisici con controller di archiviazione HP CCISS | Non supportati.
Directory | Queste directory (se impostate come partizioni, file-system separati) devono essere tutte nello stesso disco del sistema operativo nel server di origine: /(root), /boot, /usr, /usr/local, /var, /etc.</br></br> /boot deve trovarsi in una partizione del disco e non essere un volume LVM<br/><br/>
Requisiti di spazio disponibile nel disco| 2 GB nella partizione /root <br/><br/> 250 MB nella cartella di installazione
XFSv5 | Le funzionalità di XFSv5 nei file system XFS, ad esempio il checksum dei metadati, sono supportate a partire dalla versione 9.10 del servizio Mobility. Usare l'utilità xfs_info per controllare il superblocco XFS per la partizione. Se ftype è impostato su 1, le funzionalità XFSv5 sono in uso.



## <a name="network"></a>Rete

**Componente** | **Supportato**
--- | ---
Gruppo NIC rete host | Supportato per le macchine virtuali VMware. <br/><br/>Non supportato per la replica di computer fisici.
VLAN rete host | Sì
IPv4 rete host | Sì
IPv6 rete host | No 
Gruppo NIC rete guest/server | No 
IPv4 rete guest/server | Sì
IPv6 rete guest/server | No 
IP statico (Windows) rete guest/server | Sì
IP statico (Linux) rete guest/server | Sì <br/><br/>Le VM sono configurate per l'uso di DHCP in caso di failback.  
Più schede di interfaccia rete guest/server | Sì


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
Endpoint del servizio Rete virtuale di Azure<br/><br/> (firewall e reti virtuali di Archiviazione di Azure) | No 

## <a name="storage"></a>Archiviazione
**Componente** | **Supportato**
--- | ---
NFS host | Sì per VMware<br/><br/> No per server fisici.
Host SAN (ISCSI) | Sì
Percorsi multipli (MPIO) NFS | Sì, testato con DSM Microsoft, EMC PowerPath 5.7 SP4, DSM EMC PowerPath per CLARiiON
VMDK guest/server | Sì
EFI/UEFI guest/server| Parziale (Migrazione ad Azure per macchine virtuali VMware con Windows Server 2012 e versioni successive) </br></br> Vedere la nota alla fine della tabella.
Disco cluster condiviso guest/server | No 
Disco crittografato guest/server | No 
NFS guest/server | No 
SMB 3.0 guest/server | No 
RDM guest/server | Sì<br/><br/> N/D per server fisici
Disco guest/server > 1 TB | Sì<br/><br/>Fino a 4.095 GB
Disco guest/server con dimensioni logiche di settore a 4 KB e dimensioni fisiche di settore a 4 KB | Sì<
Disco guest/server con dimensioni logiche di settore a 4 KB e dimensioni fisiche di settore a 512 KB | Sì
Volume con disco con striping > 4 TB guest/server <br><br/>Gestione volumi logici (LVM) | Sì
Guest/server - Spazi di archiviazione | No 
Disco di aggiunta/rimozione a caldo guest/server | No 
Guest/server - esclusione disco | Sì
Percorsi multipli (MPIO) guest/server | N/D

> [!NOTE]
> È possibile eseguire la migrazione in Azure di macchine virtuali VMware con avvio UEFI che eseguono Windows Server 2012 o versioni successive. Si applicano le restrizioni seguenti:

> - È supportata solo la migrazione in Azure. Il failback nel sito VMware locale non è supportato.
> - Il server non deve avere più di quattro partizioni nel disco del sistema operativo.
> - Richiede il servizio Mobility versione 9.13 o successiva.
> - Non supportato per i server fisici.

## <a name="azure-storage"></a>Archiviazione di Azure

**Componente** | **Supportato**
--- | ---
Archiviazione con ridondanza locale | Sì
Archiviazione con ridondanza geografica | Sì
RA-GRS | Sì
Archiviazione ad accesso sporadico | No 
Archiviazione ad accesso frequente| No 
BLOB in blocchi | No 
Crittografia per dati inattivi (SSE)| Sì
Archiviazione Premium | Sì
Servizio di importazione/esportazione | No 
Endpoint del servizio Rete virtuale<br/><br/> Firewall e reti virtuali di archiviazione configurati in un account di archiviazione di destinazione/archiviazione della cache (usato per l'archiviazione dei dati di replica) | No 
Account di archiviazione v2 generico (livelli di accesso frequente e sporadico) | No 

## <a name="azure-compute"></a>Calcolo di Azure

**Funzionalità** | **Supportato**
--- | ---
Set di disponibilità | Sì
HUB | Sì   
Dischi gestiti | Sì

## <a name="azure-vm-requirements"></a>Requisiti per le VM di Azure

Le macchine virtuali locali replicate in Azure devono soddisfare i requisiti per le VM di Azure riepilogati in questa tabella. Un controllo dei prerequisiti eseguito da Site Recovery avrà esito negativo se alcuni dei requisiti non vengono soddisfatti.

**Componente** | **Requisiti** | **Dettagli**
--- | --- | ---
**Sistema operativo guest** | Verificare i [sistemi operativi supportati](#replicated machines). | Il controllo ha esito negativo se non supportato. 
**Architettura sistema operativo guest** | 64 bit | Il controllo ha esito negativo se non supportato. 
**Dimensioni disco del sistema operativo** | Fino a 2.048 GB | Il controllo ha esito negativo se non supportato. 
**Numero di dischi del sistema operativo** | 1 | Il controllo ha esito negativo se non supportato.  
**Numero di dischi dati** | 64 o un numero inferiore | Il controllo ha esito negativo se non supportato.  
**Dimensioni dei dischi rigidi virtuali dei dischi dati** | Fino a 4.095 GB | Il controllo ha esito negativo se non supportato. 
**Schede di rete** | Sono supportate più schede. | 
**Disco rigido virtuale condiviso** | Non supportati. | Il controllo ha esito negativo se non supportato. 
**Disco FC** | Non supportati. | Il controllo ha esito negativo se non supportato. 
**Formato disco rigido** | VHD  <br/><br/> VHDX | VHDX non è al momento supportato in Azure, ma Site Recovery converte automaticamente VHDX in VHD dopo il failover. Quando si esegue il failback in locale, le macchine virtuali continuano a usare il formato VHDX.
**BitLocker** | Non supportate | Prima di abilitare la replica per un computer, occorre disabilitare BitLocker. | 
**Nome VM** | Da 1 a 63 caratteri<br/><br/> Limitato a lettere, numeri e trattini.<br/><br/> Il nome del computer deve iniziare e terminare con una lettera o un numero. |  Aggiornare il valore nelle proprietà del computer in Site Recovery.
**Tipo di VM** | Generazione 1, generazione 2 (solo Windows) |  Le VM di generazione 2 devono avere un disco del sistema operativo di base (inclusi uno o due volumi di dati in formato VHDX) e meno di 300 GB di spazio su disco 
Le macchine virtuali Linux di seconda generazione non sono supportate. 

## <a name="vault-tasks"></a>Attività di insieme di credenziali

**Azione** | **Supportato**
--- | ---
Spostamento insieme di credenziali tra gruppi di risorse<br/><br/> All'interno e tra sottoscrizioni | No 
Spostamento di risorse di archiviazione, rete e VM di Azure tra gruppi di risorse<br/><br/> All'interno e tra sottoscrizioni | No 


## <a name="mobility-service"></a>Servizio Mobility

**Nome** | **Descrizione** | **Versione più recente** | **Dettagli**
--- | --- | --- | --- | ---
**Installazione unificata di Azure Site Recovery** | Coordina le comunicazioni tra server VMware locali e Azure  <br/><br/> Installato su server VMware locali | 9.12.4653.1 (disponibile dal portale) | [Funzionalità e correzioni più recenti](https://aka.ms/latest_asr_updates)
**Servizio Mobility** | Coordina la replica fra server VMware locali/server fisici e sito Azure/secondario<br/><br/> Installato in server fisici o in macchine virtuali VMware da replicare | 9.12.4653.1 (disponibile dal portale) | [Funzionalità e correzioni più recenti](https://aka.ms/latest_asr_updates)


## <a name="next-steps"></a>Passaggi successivi
[Informazioni su come](tutorial-prepare-azure.md) preparare Azure per il ripristino di emergenza delle macchine virtuali VMware.
