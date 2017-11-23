---
title: Matrice di supporto per la replica in un sito secondario con Azure Site Recovery | Documentazione Microsoft
description: Riepiloga i sistemi operativi e componenti supportati per Azure Site Recovery
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 10/30/2017
ms.author: raynew
ms.openlocfilehash: da120d8e325867eaf9eb8b9be1ae8d9152db54c4
ms.sourcegitcommit: e38120a5575ed35ebe7dccd4daf8d5673534626c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2017
---
# <a name="support-matrix-for-replication-to-a-secondary-site-with-azure-site-recovery"></a>Matrice di supporto per la replica in un sito secondario con Azure Site Recovery

Questo articolo contiene un riepilogo di elementi supportati quando si usa il servizio [Azure Site Recovery](site-recovery-overview.md) per eseguire la replica in un sito locale secondario.

## <a name="supported-scenarios"></a>Scenari supportati

**Distribuzione** | **Dettagli** 
--- | ---
**Da VMware a VMware** | Ripristino di emergenza di macchine virtuali VMware locali nel sito VMware secondario.<br/><br/> Scaricare il [Manuale dell'utente di InMage Scout](https://aka.ms/asr-scout-user-guide)
**Da Hyper-V a Hyper-V** | Ripristino di emergenza di VM Hyper-V locali in cloud VMM in un cloud VMM secondario.<br></br> Non supportato senza VMM.



  

## <a name="host-servers"></a>Server host

**Distribuzione** | **Supporto**
--- | ---
**Server fisico/VM VMware** | vCenter 5.5, 6.0 e 6.5 (supporto per le sole funzionalità 5.5)
**Hyper-V con VMM** | Windows Server 2016 e Windows Server 2012 R2 con gli aggiornamenti più recenti.<br/><br/> Gli host Windows Server 2016 dovranno essere gestiti da VMM 2016.<br/><br/> I cloud VMM 2016 con una combinazione di host Windows Server 2016 e 2012 R2 non sono attualmente supportati.<br/><br/> Attualmente non è supportata la distribuzione che include un aggiornamento di un componente VMM 2012 R2 esistente a System Center 2016.


## <a name="support-for-replicated-machine-os-versions"></a>Supporto per le versioni dei sistemi operativi dei computer replicati

Nella tabella seguente viene descritto il supporto del sistema operativo per i computer replicati con Site Recovery. Qualsiasi carico di lavoro può essere in esecuzione sul sistema operativo supportato.

**Server fisico/VMware** | **Hyper-V (con VMM)**
--- | ---
Windows Server 2016 a 64 bit, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 con almeno il SP1<br/><br/> Red Hat Enterprise Linux 6.7, 6.8, 6.9, 7.1, 7.2 <br/><br/> CentOs 6.5, 6.6, 6.7, 6.8, 6.9, 7.0, 7.1, 7.2 <br/><br/> Oracle Enterprise Linux 6.4, 6.5, 6.8 che esegue il kernel compatibile Red Hat o Unbreakable Enterprise Kernel versione 3 (UEK3) <br/><br/> SUSE Linux Enterprise Server 11 SP3, 11 SP4  | Qualsiasi sistema operativo guest [supportato da Hyper-V](https://technet.microsoft.com/library/mt126277.aspx)

## <a name="linux-machine-storage"></a>Archiviazione di computer Linux

È possibile eseguire la replica solo di computer Linux con le caratteristiche di archiviazione seguenti:

- File system (EXT3, ETX4, ReiserFS, XFS).
- Software con percorsi multipli: mapper dispositivi.
- Gestore volumi: LVM2.
- I server fisici con archiviazione del controller HP CCISS non sono supportati.
- Il file system ReiserFS è supportato solo su SUSE Linux Enterprise Server 11 SP3.

## <a name="network-configuration"></a>Network configuration

### <a name="hosts"></a>Hosts

**Configurazione** | **Server fisico/VMware** | **Hyper-V (con VMM)**
--- | --- | ---
Gruppo NIC | Sì | Sì
VLAN | Sì | Sì
IPv4 | Sì | Sì
IPv6 | No | No

### <a name="guest-vms"></a>VM guest

**Configurazione** | **Server fisico/VMware** | **Hyper-V (con VMM)**
--- | --- | ---
Gruppo NIC | No | No
IPv4 | Sì | Sì
IPv6 | No | No
IP statico (Windows) | Sì | Sì
IP statico (Linux) | Sì | Sì
Più NIC | Sì | Sì


## <a name="storage"></a>Archiviazione

### <a name="host-storage"></a>Archiviazione su host

**Archiviazione (host)** | **Server fisico/VMware** | **Hyper-V (con VMM)**
--- | --- | ---
NFS | Sì | N/D
SMB 3.0 | N/D | Sì
SAN (iSCSI) | Sì | Sì
Percorsi multipli (MPIO) | Sì | Sì

### <a name="guest-or-physical-server-storage"></a>Archiviazione su server fisico o guest

**Configurazione** | **Server fisico/VMware** | **Hyper-V (con VMM)**
--- | --- | ---
VMDK | Sì | N/D
VHD/VHDX | N/D | Sì (fino a 16 dischi)
VM di seconda generazione | N/D | Sì
Disco cluster condiviso | Sì  | No
Disco crittografato | No | No
UEFI| Sì | N/D
NFS | No | No
SMB 3.0 | No | No
RDM | Sì | N/D
Disco superiore a 1 TB | Sì | Sì
Volume con disco con striping superiore a 1 TB<br/><br/> LVM | Sì | Sì
Spazi di archiviazione | No | Sì
Aggiunta/rimozione a caldo disco | Sì | No
Esclusione disco | Sì | Sì
Percorsi multipli (MPIO) | N/D | Sì

## <a name="vaults"></a>Insiemi di credenziali

**Azione** | **Server fisico/VMware** | **Hyper-V (con VMM)**
--- | --- | ---
Spostare gli insiemi di credenziali tra i gruppi di risorse: all'interno o tra le sottoscrizioni | No | No
Spostare le risorse di archiviazione, la rete e le VM di Azure tra i gruppi di risorse: all'interno o tra le sottoscrizioni | No | No

## <a name="provider-and-agent"></a>Provider e agente

**Nome** | **Descrizione** | **Versione più recente** | **Dettagli**
--- | --- | --- | --- | ---
**Provider di Azure Site Recovery** | Coordina le comunicazioni tra server locali e Azure <br/><br/> Installato su server VMM locali o server Hyper-V se non esiste alcun server VMM | 5.1.19 ([disponibile dal portale](http://aka.ms/downloaddra)) | [Funzionalità e correzioni più recenti](https://support.microsoft.com/kb/3155002)
**Servizio Mobility** | Coordina la replica fra server VMware locali/server fisici e sito secondario<br/><br/> Installato in server fisici o in VM VMware da replicare  | N/D (disponibile dal portale) | N/D


## <a name="next-steps"></a>Passaggi successivi

- [Eseguire la replica di VM Hyper-V nel cloud VMM in un sito secondario](tutorial-vmm-to-vmm.md)
- [Replicare VM VMware e server fisici in un sito secondario](tutorial-vmware-to-vmware.md)
