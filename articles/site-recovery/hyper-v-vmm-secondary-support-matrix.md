---
title: Matrice di supporto per la replica delle macchine virtuali Hyper-V di cloud VMM in un sito secondario con Azure Site Recovery | Microsoft Docs
description: Offre un riepilogo del supporto per la replica delle macchine virtuali Hyper-V di cloud VMM in un sito secondario con Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: raynew
ms.openlocfilehash: 767b0e76b73c082ddb75374f51700b85272f713e
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="support-matrix-for-replication-of-hyper-v-vms-to-a-secondary-site"></a>Matrice di supporto per la replica delle macchine virtuali Hyper-V in un sito secondario

Questo articolo elenca i componenti supportati quando si usa il servizio [Azure Site Recovery](site-recovery-overview.md) per replicare in un sito secondario macchine virtuali Hyper-V gestite nei cloud di System Center Virtual Machine Manager (VMM). Per replicare macchine virtuali Hyper-V in Azure, vedere [questa matrice di supporto](hyper-v-azure-support-matrix.md).

> [!NOTE]
> È possibile eseguire la replica in un sito secondario solo quando gli host Hyper-V sono gestiti nei cloud VMM.

  

## <a name="host-servers"></a>Server host

**Sistema operativo** | **Dettagli**
--- | ---
Windows Server 2012 R2 | I server devono eseguire gli aggiornamenti più recenti.
Windows Server 2016 |  I cloud VMM 2016 con una combinazione di host Windows Server 2016 e 2012 R2 non sono attualmente supportati.<br/><br/> Le distribuzioni con aggiornamenti da System Center 2012 R2 VMM 2012 R2 a System Center 2016 non sono attualmente supportate.


## <a name="replicated-vm-support"></a>Supporto di macchine virtuali replicate

Nella tabella seguente viene descritto il supporto del sistema operativo per i computer replicati con Site Recovery. Qualsiasi carico di lavoro può essere in esecuzione sul sistema operativo supportato.

**Versione di Windows** | **Hyper-V (con VMM)**
--- | ---
Windows Server 2016 | Qualsiasi sistema operativo guest [supportato da Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/Supported-Windows-guest-operating-systems-for-Hyper-V-on-Windows) in Windows Server 2016 
Windows Server 2012 R2 | Qualsiasi sistema operativo guest [supportato da Hyper-V](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn792027%28v%3dws.11%29) in Windows Server 2012 R2

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
NFS | N/D
SMB 3.0 |  Sì
SAN (iSCSI) | Sì
Percorsi multipli (MPIO) | Sì

### <a name="guest-or-physical-server-storage"></a>Archiviazione su server fisico o guest

**Configurazione** | **Supportato**
--- | --- | 
VMDK |  N/D
VHD/VHDX | Sì (fino a 16 dischi)
VM di seconda generazione | Sì
Disco cluster condiviso | No 
Disco crittografato | No 
UEFI| N/D
NFS | No 
SMB 3.0 | No 
RDM | N/D
Disco superiore a 1 TB | Sì
Volume con disco con striping superiore a 1 TB<br/><br/> LVM | Sì
Spazi di archiviazione | Sì
Aggiunta/rimozione a caldo disco | No 
Esclusione disco | Sì
Percorsi multipli (MPIO) | Sì

## <a name="vaults"></a>Insiemi di credenziali

**Azione** | **Supportato**
--- | --- 
Spostare gli insiemi di credenziali tra i gruppi di risorse: all'interno o tra le sottoscrizioni |  No 
Spostare le risorse di archiviazione, la rete e le VM di Azure tra i gruppi di risorse: all'interno o tra le sottoscrizioni | No 

## <a name="azure-site-recovery-provider"></a>Provider di Azure Site Recovery

Il provider coordina le comunicazioni tra server VMM. 

**Più recente** | **Aggiornamenti**
--- | --- | --- | --- | ---
5.1.19 ([disponibile dal portale](http://aka.ms/downloaddra)) | [Funzionalità e correzioni più recenti](https://support.microsoft.com/kb/3155002)



## <a name="next-steps"></a>Passaggi successivi

[Eseguire la replica di VM Hyper-V nel cloud VMM in un sito secondario](tutorial-vmm-to-vmm.md)

