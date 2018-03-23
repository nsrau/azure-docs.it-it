---
title: Matrice di supporto per la replica di macchine virtuali VMware o server fisici in un sito VMware secondario tramite Azure Site Recovery | Microsoft Docs
description: Viene riepilogato il supporto per la replica di server VMware/fisici in un sito secondario tramite Azure Site Recovery
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: raynew
ms.openlocfilehash: b51a4573ad7a8461b7261f08d94639d2030492d9
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="support-matrix-for-replication-of-vmware-vms-and-physical-servers-to-a-secondary-site"></a>Matrice di supporto per la replica di macchine virtuali VMware e server fisici in un sito secondario

Questo articolo contiene un riepilogo di elementi supportati quando si usa il servizio [Azure Site Recovery](site-recovery-overview.md) per eseguire la replica in un sito VMware secondario di macchine virtuali VMware o server fisici Windows/Linux.

- Se si vuole replicare macchine virtuali VMware o server fisici in Azure, fare riferimento a [questa matrice di supporto](vmware-physical-azure-support-matrix.md).
- Se si vuole replicare macchine virtuali Hyper-V in un sito secondario, fare riferimento a [questa matrice di supporto](hyper-v-azure-support-matrix.md).

> [!NOTE]
> La replica di macchine virtuali VMware locali e server fisici viene eseguita usando InMage Scout. InMage Scout è incluso nella sottoscrizione del servizio Azure Site Recovery.


## <a name="host-servers"></a>Server host

**Sistema operativo** | **Dettagli**
--- | ---
Server vCenter | vCenter 5.5, 6.0 e 6.5<br/><br/> Se si esegue la versione 6.0 o 6.5, sono supportate solo le funzionalità della versione 5.5.


## <a name="replicated-vm-support"></a>Supporto di macchine virtuali replicate

Nella tabella seguente viene descritto il supporto del sistema operativo per i computer replicati con Site Recovery. Qualsiasi carico di lavoro può essere in esecuzione sul sistema operativo supportato.

**Sistema operativo** | **Dettagli**
--- | ---
Windows Server | Windows Server 2016 a 64 bit, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 con almeno il SP1.
Linux | Red Hat Enterprise Linux 6.7, 6.8, 6.9, 7.1, 7.2 <br/><br/> CentOs 6.5, 6.6, 6.7, 6.8, 6.9, 7.0, 7.1, 7.2 <br/><br/> Oracle Enterprise Linux 6.4, 6.5, 6.8 che esegue il kernel compatibile Red Hat o Unbreakable Enterprise Kernel versione 3 (UEK3) <br/><br/> SUSE Linux Enterprise Server 11 SP3, 11 SP4 


## <a name="linux-machine-storage"></a>Archiviazione di computer Linux

È possibile eseguire la replica solo di computer Linux con le caratteristiche di archiviazione seguenti:

- File system (EXT3, ETX4, ReiserFS, XFS).
- Software con percorsi multipli: mapper dispositivi.
- Gestore volumi: LVM2.
- I server fisici con archiviazione del controller HP CCISS non sono supportati.
- Il file system ReiserFS è supportato solo su SUSE Linux Enterprise Server 11 SP3.

## <a name="network-configuration---hostguest-vm"></a>Configurazione di rete - Macchine virtuali host/guest

**Configurazione** | **Supportato**  
--- | --- 
Host - Gruppo NIC | Sì 
Host - VLAN | Sì 
Host - IPv4 | Sì 
Host - IPv6 | No  
VM guest - Gruppo NIC | No 
VM guest - IPv4 | Sì
VM guest - IPv6 | No 
VM guest - Windows/Linux - Indirizzo IP statico | Sì
VM guest - Più NIC | Sì


## <a name="storage"></a>Archiviazione

### <a name="host-storage"></a>Archiviazione su host

**Archiviazione (host)** | **Supportato** 
--- | --- 
NFS | Sì 
SMB 3.0 | N/D 
SAN (iSCSI) | Sì 
Percorsi multipli (MPIO) | Sì 

### <a name="guest-or-physical-server-storage"></a>Archiviazione su server fisico o guest

**Configurazione** | **Supportato** 
--- | --- 
VMDK | Sì 
VHD/VHDX | N/D 
VM di seconda generazione | N/D 
Disco cluster condiviso | Sì 
Disco crittografato | No  
UEFI| Sì 
NFS | No  
SMB 3.0 | No  
RDM | Sì 
Disco superiore a 1 TB | Sì 
Volume con disco con striping superiore a 1 TB<br/><br/> LVM | Sì 
Spazi di archiviazione | No  
Aggiunta/rimozione a caldo disco | Sì 
Esclusione disco | Sì 
Percorsi multipli (MPIO) | N/D 

## <a name="vaults"></a>Insiemi di credenziali

**Azione** | **Supportato** 
--- | --- 
Spostare gli insiemi di credenziali tra i gruppi di risorse: all'interno o tra le sottoscrizioni | No  
Spostare le risorse di archiviazione, la rete e le VM di Azure tra i gruppi di risorse: all'interno o tra le sottoscrizioni | No  

## <a name="mobility-service-and-updates"></a>Servizio Mobility e aggiornamenti

Il servizio Mobility coordina la replica fra server VMware locali/server fisici e sito secondario. Quando si configura la replica, accertarsi di disporre dell'ultima versione del servizio Mobility e di altri componenti.

**Aggiornamento** | **Dettagli** 
--- | --- 
Aggiornamenti di Scout | [Informazioni introduttive e su come scaricare](/vmware-physical-secondary-disaster-recovery.md#updates) gli aggiornamenti più recenti di Scout | Gli aggiornamenti di Scout sono cumulativi.
Aggiornamenti dei componenti | Gli aggiornamenti di Scout includono gli aggiornamenti di tutti i componenti, tra cui server RX, server di configurazione, server di destinazione di elaborazione e master, server vContinuum e server di origine che si vuole proteggere.<br/><br/> [Altre informazioni](vmware-physical-secondary-disaster-recovery.md#download-and-install-component-updates).


## <a name="next-steps"></a>Passaggi successivi

Scaricare il [Manuale dell'utente di InMage Scout](https://aka.ms/asr-scout-user-guide)

- [Eseguire la replica di VM Hyper-V nel cloud VMM in un sito secondario](tutorial-vmm-to-vmm.md)
- [Replicare VM VMware e server fisici in un sito secondario](tutorial-vmware-to-vmware.md)
