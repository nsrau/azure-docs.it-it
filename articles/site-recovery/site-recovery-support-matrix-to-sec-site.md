---
title: Matrice di supporto di Azure Site Recovery per la replica in un sito secondario | Documentazione Microsoft
description: Riepiloga i sistemi operativi e componenti supportati per Azure Site Recovery
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 01/24/2017
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: e858a70f2ce173eb6b99cbe7a130abb5fdc265e1
ms.openlocfilehash: 0a2c3bec5fc6fb44b4baddc393d6f9387bbb5b94


---
# <a name="azure-site-recovery-support-matrix-for-replicating-to-customer-owned-secondary-site"></a>Matrice di supporto di Azure Site Recovery per la replica in un sito secondario di proprietà del cliente

> [!div class="op_single_selector"]
> * [Replica in Azure](site-recovery-support-matrix-to-azure.md)
> * [Replica in sito secondario di proprietà del cliente](site-recovery-support-matrix-to-sec-site.md)

Un elenco dei prerequisiti per Azure Site Recovery è riportato [qui](site-recovery-best-practices.md). L'articolo di seguito offre un riepilogo delle configurazioni e dei componenti supportati per Azure Site Recovery in caso di replica e ripristino in un sito secondario di proprietà del cliente.

## <a name="support-for-deployment-options"></a>Supporto per opzioni di distribuzione

**Distribuzione** | **Server fisico/VMware** | **Hyper-V (senza VMM)** | **Hyper-V (con VMM)**
--- | --- | --- | ---
**Portale di Azure** | Macchine virtuali VMware locali nel sito VMware secondario.<br/><br/> Scaricare il Manuale dell'utente di InMage Scout (http://download.microsoft.com/download/E/0/8/E08B3BCE-3631-4CED-8E65-E3E7D252D06D/InMage_Scout_Standard_User_Guide_8.0.1.pdf). Non disponibile nel portale di Azure | Non supportate | Macchine virtuali Hyper-V locali in cloud VMM in un cloud VMM secondario<br/><br/> Solo replica Hyper-V standard, SAN non supportato
**Portale classico** | Solo modalità manutenzione. Non è possibile creare nuovi insiemi di credenziali. | Non supportate | Solo modalità manutenzione.
**PowerShell** | Non supportati. | Non supportate | Supportato



## <a name="support-for-datacenter-management-servers"></a>Supporto per server di gestione dei data center

### <a name="virtualization-management-entities"></a>Entità di gestione della virtualizzazione

**Distribuzione** | **Supporto**
--- | ---
**Server fisico/VM VMware** | vSphere 6.0, 5.5 o 5.1 con aggiornamento più recente
**Hyper-V (con VMM)** | SCVMM 2016 e SCVMM 2012 R2

  >[!Note]
  > Non sono attualmente supportati cloud SCVMM 2016 con una combinazione di host Windows Server 2016 e 2012 R2.

### <a name="host-servers"></a>Server host

**Distribuzione** | **Supporto**
--- | ---
**Server fisico/VM VMware** | vCenter 5.5 o 6.0 (supporto per le sole funzionalità 5.5) 
**Hyper-V (senza VMM)** | Configurazione non supportata per la replica in un sito secondario
**Hyper-V con VMM** | Windows Server 2016, Windows Server 2012 R2 con gli aggiornamenti più recenti<br/><br/> Gli host Windows Server 2016 dovranno essere gestiti da SCVMM 2016

## <a name="support-for-replicated-machine-machine-os-versions"></a>Supporto per le versioni dei sistemi operativi dei computer replicati
La tabella seguente offre un riepilogo dei sistemi operativi supportati nei vari scenari di distribuzione quando si usa Azure Site Recovery. Il supporto è **applicabile per qualsiasi carico di lavoro** in esecuzione nel sistema operativo indicato.

**Server fisico/VMware** | **Hyper-V (con VMM)**
--- | --- | ---
Le VM Windows Server 2012 R2 a 64 bit, Windows Server 2012, Windows Server 2008 R2 con SP1 o successivo<br/><br/> Red Hat Enterprise Linux 6.7, 7.1, 7.2 <br/><br/> Centos 6.5, 6.6, 6.7, 7.0, 7.1, 7.2 <br/><br/> Oracle Enterprise Linux 6.4 o 6.5 che esegue il kernel compatibile Red Hat o Unbreakable Enterprise Kernel versione 3 (UEK3) <br/><br/> SUSE Linux Enterprise Server 11 SP3 | Qualsiasi sistema operativo guest [supportato da Hyper-V](https://technet.microsoft.com/library/mt126277.aspx)

>[!Note]
>**Supporto dell'archiviazione per versioni di Linux**: file system EXT3, ETX4, ReiserFS e XFS, software per percorsi multipli Device Mapper e gestore dei volumi LVM2. I server fisici con archiviazione del controller HP CCISS **non** sono supportati.
>Il file system ReiserFS è supportato solo su SUSE Linux Enterprise Server 11 SP3.

## <a name="support-for-network"></a>Supporto per rete
Le tabelle seguenti offrono un riepilogo delle configurazioni di rete supportate nei vari scenari di distribuzione quando si usa Azure Site Recovery per la replica in Azure.

### <a name="host-network-configuration"></a>Configurazione di rete per host

**Configurazione** | **Server fisico/VMware** | **Hyper-V (con VMM)**
--- | --- | ---
Gruppo NIC | Sì | Sì
VLAN | Sì | Sì
IPv4 | Sì | Sì
IPv6 | No | No

### <a name="guest-vm-network-configuration"></a>Configurazione di rete per VM guest

**Configurazione** | **Server fisico/VMware** | **Hyper-V (con VMM)**
--- | --- | ---
Gruppo NIC | No | No
IPv4 | Sì | Sì
IPv6 | No | No
IP statico (Windows) | Sì | Sì
IP statico (Linux) | Sì | Sì
Più NIC | Sì | Sì


## <a name="support-for-storage"></a>Supporto per archiviazione
Le tabelle seguenti offrono un riepilogo delle configurazioni di archiviazione supportate nei vari scenari di distribuzione quando si usa Azure Site Recovery per la replica in Azure.

### <a name="host-storage-configuration"></a>Configurazione di archiviazione per host

**Archiviazione (host)** | **Server fisico/VMware** | **Hyper-V (con VMM)**
--- | --- | ---
NFS | Sì | ND
SMB 3.0 | ND | Sì
SAN (iSCSI) | Sì | Sì
Percorsi multipli (MPIO) | Sì | Sì

### <a name="guest-physical-server-storage-configuration"></a>Configurazione di archiviazione per server fisici/guest

**Configurazione** | **Server fisico/VMware** | **Hyper-V (con VMM)**
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

**Azione** | **Server fisico/VMware** | **Hyper-V (con VMM)**
--- | --- | ---
Spostamento insieme di credenziali tra gruppi di risorse<br/><br/> All'interno e tra sottoscrizioni | No | No
Spostamento di risorse di archiviazione, rete e VM di Azure tra gruppi di risorse<br/><br/> All'interno e tra sottoscrizioni | No | No

## <a name="support-for-provider-and-agent"></a>Supporto per provider e agente

**Nome** | **Descrizione** | **Versione più recente** | **Dettagli**
--- | --- | --- | --- | ---
**Provider di Azure Site Recovery** | Coordina le comunicazioni tra server locali e Azure <br/><br/> Installato su server VMM locali o server Hyper-V se non esiste alcun server VMM | 5.1.19 ([disponibile dal portale](http://aka.ms/downloaddra)) | [Funzionalità e correzioni più recenti](https://support.microsoft.com/kb/3155002)
**Servizio Mobility** | Coordina la replica tra server VMware locali/server fisici e sito secondario<br/><br/> Installato in server fisici o in macchine virtuali VMware da replicare  | ND (disponibile dal portale) | ND


## <a name="next-steps"></a>Passaggi successivi
[Preparare la distribuzione](site-recovery-best-practices.md)



<!--HONumber=Jan17_HO4-->


