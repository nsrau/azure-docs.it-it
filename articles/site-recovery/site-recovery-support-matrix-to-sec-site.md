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
ms.date: 05/24/2017
ms.author: raynew
ms.openlocfilehash: 69c5d09b6608484210870e1a69c51b112b497810
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="support-matrix-for-replication-to-a-secondary-site-with-azure-site-recovery"></a>Matrice di supporto per la replica in un sito secondario con Azure Site Recovery

Questo articolo contiene un riepilogo di elementi supportati quando si usa Azure Site Recovery per eseguire la replica in un sito locale secondario.

## <a name="deployment-options"></a>Opzioni di distribuzione

**Distribuzione** | **Server fisico/VMware** | **Hyper-V (con/senza SCVMM)**
--- | --- | --- | ---
**Portale di Azure** | Macchine virtuali VMware locali nel sito VMware secondario.<br/><br/> Scaricare il [manuale dell'utente per InMage Scout](http://download.microsoft.com/download/E/0/8/E08B3BCE-3631-4CED-8E65-E3E7D252D06D/InMage_Scout_Standard_User_Guide_8.0.1.pdf) (non disponibile nel portale di Azure). | VM Hyper-V locali in cloud VMM in un cloud VMM secondario.<br></br> Non supportato senza VMM  <br/><br/> Viene usata solo la replica Hyper-V standard. SAN non supportato.
**Portale classico** | Solo modalità manutenzione. Non è possibile creare nuovi insiemi di credenziali. | Solo modalità di manutenzione.<br></br> Non supportato senza SCVMM
**PowerShell** | Non supportate | Supportato<br></br> Non supportato senza SCVMM

## <a name="on-premises-servers"></a>Server locali

### <a name="virtualization-servers"></a>Server di virtualizzazione

**Distribuzione** | **Supporto**
--- | ---
**Server fisico/VM VMware** | vSphere 6.0, 5.5 o 5.1 con aggiornamento più recente
**Hyper-V (con VMM)** | VMM 2016 e VMM 2012 R2

  >[!Note]
  > I cloud VMM 2016 con una combinazione di host Windows Server 2016 e 2012 R2 non sono attualmente supportati.
  > Le configurazioni che includono l'aggiornamento di un SCVMM 2012 R2 esistente alla versione 2016 non sono attualmente supportate.
### <a name="host-servers"></a>Server host

**Distribuzione** | **Supporto**
--- | ---
**Server fisico/VM VMware** | vCenter 5.5 o 6.0 (supporto per le sole funzionalità 5.5) 
**Hyper-V (senza VMM)** | Configurazione non supportata per la replica in un sito secondario
**Hyper-V con VMM** | Windows Server 2016 e Windows Server 2012 R2 con gli aggiornamenti più recenti.<br/><br/> Gli host Windows Server 2016 dovranno essere gestiti da VMM 2016.

## <a name="support-for-replicated-machine-os-versions"></a>Supporto per le versioni dei sistemi operativi dei computer replicati
La tabella seguente offre un riepilogo dei sistemi operativi supportati nei vari scenari di distribuzione quando si usa Azure Site Recovery. Il supporto è applicabile per qualsiasi carico di lavoro in esecuzione nel sistema operativo indicato.

**Server fisico/VMware** | **Hyper-V (con VMM)**
--- | --- | ---
Le VM Windows Server 2012 R2 a 64 bit, Windows Server 2012, Windows Server 2008 R2 con SP1 o successivo<br/><br/> Red Hat Enterprise Linux 6.7, 7.1, 7.2 <br/><br/> Centos 6.5, 6.6, 6.7, 7.0, 7.1, 7.2 <br/><br/> Oracle Enterprise Linux 6.4 o 6.5 che esegue il kernel compatibile Red Hat o Unbreakable Enterprise Kernel versione 3 (UEK3) <br/><br/> SUSE Linux Enterprise Server 11 SP3 | Qualsiasi sistema operativo guest [supportato da Hyper-V](https://technet.microsoft.com/library/mt126277.aspx)

>[!Note]
>Solo per i computer Linux con le risorse di archiviazione seguenti: file system (EXT3, ETX4, ReiserFS e XFS); software per percorsi multipli - Device Mapper; Archiviazione volumi: LVM2.
>I server fisici con archiviazione del controller HP CCISS non sono supportati.
>Il file system ReiserFS è supportato solo su SUSE Linux Enterprise Server 11 SP3.

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
UEFI| No | N/D
NFS | No | No
SMB 3.0 | No | No
RDM | Sì | N/D
Disco superiore a 1 TB | No | Sì
Volume con disco con striping superiore a 1 TB<br/><br/> LVM | Sì | Sì
Spazi di archiviazione | No | Sì
Aggiunta/rimozione a caldo disco | No | No
Esclusione disco | No | Sì
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

- [Eseguire la replica di VM Hyper-V nel cloud VMM in un sito secondario](site-recovery-vmm-to-vmm.md)
- [Replicare VM VMware e server fisici in un sito secondario](site-recovery-vmware-to-vmware.md)
