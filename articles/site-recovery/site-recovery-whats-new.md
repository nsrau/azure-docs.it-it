---
title: Novità di Azure Site Recovery | Microsoft Docs
description: Riepilogo delle nuove funzionalità introdotte in Azure Site Recovery
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/12/2019
ms.author: raynew
ms.openlocfilehash: fece1adc9ad2f5844a7d6cc1e0e9cc92d44de6d8
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57892932"
---
# <a name="whats-new-in-site-recovery"></a>Novità di Site Recovery

Il servizio [Azure Site Recovery](site-recovery-overview.md) viene aggiornato e migliorato in modo continuativo. Per consentire ai clienti di restare sempre aggiornati, in questo articolo vengono fornite informazioni sulle versioni più recenti, le nuove funzionalità e i nuovi contenuti. Questa pagina viene aggiornata regolarmente.

Se si hanno suggerimenti per le funzionalità di Site Recovery, Microsoft è lieta di ricevere [commenti e suggerimenti dai clienti](https://feedback.azure.com/forums/256299-site-recovery).

## <a name="q1-2019"></a>T1 2019 

### <a name="update-rollup-34-february-2019"></a>Aggiornamento cumulativo di 34 (febbraio 2019)

[Aggiornamento cumulativo 34](https://support.microsoft.com/help/4490016/update-rollup-34-for-azure-site-recovery) fornisce gli aggiornamenti seguenti.

**Aggiornamento** | **Dettagli**
--- | ---
**Provider e agenti** | Un aggiornamento di agenti di Site Recovery e i provider (come descritto in dettaglio nell'aggiornamento cumulativo)
**Correzioni di problema** | Numerose correzioni e miglioramenti (come descritto in dettaglio nell'aggiornamento cumulativo)



### <a name="update-rollup-33-february-2019"></a>Aggiornamento cumulativo 33 (febbraio 2019)

[Aggiornamento cumulativo 33](https://support.microsoft.com/help/4489582/update-rollup-33-for-azure-site-recovery) fornisce gli aggiornamenti seguenti.

**Aggiornamento** | **Dettagli**
--- | ---
**Provider e agenti** | Un aggiornamento di agenti di Site Recovery e i provider (come descritto in dettaglio nell'aggiornamento cumulativo)
**Correzioni di problema** | Numerose correzioni e miglioramenti (come descritto in dettaglio nell'aggiornamento cumulativo)
**Mapping di rete** | Per il ripristino di emergenza di macchine Virtuali di Azure, è ora possibile usare qualsiasi rete di destinazione disponibili quando si abilita la replica. 
**Standard SSD** | È ora possibile configurare il ripristino di emergenza per macchine virtuali di Azure usando [dischi SSD Standard](https://docs.microsoft.com/azure/virtual-machines/windows/disks-standard-ssd).
**Spazi di archiviazione diretta** | È possibile configurare il ripristino di emergenza per le App in esecuzione su macchine Virtuali di Azure le app usando [spazi di archiviazione diretta](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview) per la disponibilità elevata.
**System file BRTFS** | È supportato per le macchine virtuali VMware, oltre alle macchine virtuali di Azure.<br/><br/> Non è supportato se: Il volume del sistema file BTRFS secondario è stato modificato dopo l'abilitazione della replica, il file system è distribuito tra più dischi o se il BTRFS file system supporta RAID.



### <a name="update-rollup-32-january-2019"></a>Aggiornamento cumulativo 32 (gennaio 2019)

[Aggiornamento cumulativo 31](https://support.microsoft.com/help/4485985/update-rollup-32-for-azure-site-recovery) fornisce gli aggiornamenti seguenti.

**Aggiornamento** | **Dettagli**
--- | ---
**Provider e agenti** | Un aggiornamento di agenti di Site Recovery e i provider (come descritto in dettaglio nell'aggiornamento cumulativo)
**Correzioni di problema** | Numerose correzioni e miglioramenti (come descritto in dettaglio nell'aggiornamento cumulativo)
**Ripristino di emergenza per Linux** | **Macchine virtuali di Azure**: Workstation di RedHat 6 o 7. supporto per nuove versioni del kernel per Ubuntu, Debian e SUSE.<br/><br/> **VM VMware/server fisici**: Red Hat Enterprise Linux 7.6; Workstation di RedHat 6 o 7. Oracle Linux 6.10/7.6; supportati per le nuove versioni del kernel per Ubuntu, Debian e SUSE.


### <a name="update-rollup-31-january-2019"></a>Aggiornamento cumulativo di 31 (gennaio 2019)

[Aggiornamento cumulativo 31](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery) fornisce gli aggiornamenti seguenti.

**Aggiornamento** | **Dettagli**
--- | ---
**Provider e agenti** | Un aggiornamento di agenti di Site Recovery e i provider (come descritto in dettaglio nell'aggiornamento cumulativo)
**Correzioni di problema** | Numerose correzioni e miglioramenti (come descritto in dettaglio nell'aggiornamento cumulativo)
**Ripristino di emergenza per Linux** | **Macchine virtuali di Azure**: Oracle Linux 6.8 e 6.9/7.0; supporto per i kernel UEK5.<br/><br/> **VM VMware/server fisici**: Oracle Linux 6.8 e 6.9/7.0; supporto per UEK5 kernel.
**System file BRTFS** | È supportato per macchine virtuali di Azure.
**LVM** | Aggiunto il supporto per volumi LVM e LVM2.<br/><br/> La directory /boot in una partizione del disco e nei volumi LVM è supportata.
**Directories** | Aggiunto il supporto per questi seet directory come partizioni separate o file System che non sono nello stesso disco del sistema: / (root), /boot, /usr, /usr/local, /var, /etc.
**Windows Server 2008** | Aggiunto il supporto per dischi dinamici.
**Failover di VM VMware** | Migliorato il tempo di failover per le macchine virtuali VMware in cui storvsc e vsbus non sono driver di avvio.
**Supporto per UEFI** | Macchine virtuali di Azure non supportano il tipo di avvio UEFI. È ora possibile eseguire la migrazione di server fisici locali con UEFI in Azure con Site Recovery. Site Recovery esegue la migrazione di server per la conversione del tipo di avvio in BIOS prima della migrazione. Solo il ripristino del sito supportato in precedenza questa conversione per le macchine virtuali. Il supporto è disponibile per i server fisici che eseguono Windows Server 2012 o versione successiva.
**Macchine virtuali di Azure nelle zone di disponibilità** | È possibile abilitare la replica in un'altra area per le VM di Azure distribuite nelle zone di disponibilità. unità organizzativa possa ora abilitare la replica per una macchina virtuale di Azure e impostare la destinazione per il failover su una singola istanza di macchina virtuale, una macchina virtuale in un set di disponibilità o una macchina virtuale in una zona di disponibilità. L'impostazione non influisce sulla replica. [Leggere](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region/) l'annuncio.


## <a name="q4-2018"></a>T4 2018

### <a name="update-rollup-30-october-2018"></a>Aggiornamento cumulativo di 30 (ottobre 2018)

[Aggiornamento cumulativo 30](https://support.microsoft.com/help/4468181/azure-site-recovery-update-rollup-30) fornisce gli aggiornamenti seguenti.

**Aggiornamento** | **Dettagli**
--- | ---
**Provider e agenti** | Un aggiornamento di agenti di Site Recovery e i provider (come descritto in dettaglio nell'aggiornamento cumulativo)
**Correzioni di problema** | Numerose correzioni e miglioramenti (come descritto in dettaglio nell'aggiornamento cumulativo)
**Supporto per aree** | Supporto aggiunto per Australia centrale 1 e Australia centrale 2 per il ripristino del sito.
**Supporto per la crittografia del disco** | Supporto aggiunto per il ripristino di emergenza di macchine virtuali di Azure crittografate con crittografia dischi di Azure (ADE) con l'app di Azure AD. [Altre informazioni](azure-to-azure-how-to-enable-replication-ade-vms.md)
**Esclusione disco** | Dischi non inizializzati vengono esclusi automaticamente durante la replica di macchine Virtuali di Azure.
**Archiviazione abilitati firewall** | Aggiunto il supporto per [gli account di archiviazione abilitati firewall](https://docs.microsoft.com/azure/storage/common/storage-network-security).<br/><br/> È possibile replicare macchine virtuali di Azure con dischi non gestiti negli account di archiviazione firewall attivato in un'altra area di Azure per il ripristino di emergenza.<br/><br/> È possibile usare gli account di archiviazione abilitati firewall degli account di archiviazione di destinazione per i dischi non gestiti.<br/><br/> Supportato solo con PowerShell.


### <a name="update-rollup-29-october-2018"></a>Aggiornamento cumulativo 29 (ottobre 2018)

[Aggiornamento cumulativo 29](https://support.microsoft.com/help/4466466/update-rollup-29-for-azure-site-recovery) fornisce gli aggiornamenti seguenti.

**Aggiornamento** | **Dettagli**
--- | ---
**Provider e agenti** | Un aggiornamento di agenti di Site Recovery e i provider (come descritto in dettaglio nell'aggiornamento cumulativo)
**Correzioni di problema** | Numerose correzioni e miglioramenti (come descritto in dettaglio nell'aggiornamento cumulativo)

### <a name="automatic-updates-for-the-mobility-service-extension"></a>Aggiornamenti automatici per l'estensione del servizio Mobility

In Site Recovery è stata aggiunta un'opzione per gli aggiornamenti automatici per l'estensione del servizio Mobility. L'estensione del servizio Mobility viene installata in ogni macchina virtuale di Azure replicata da Site Recovery. Quando si abilita la replica, occorre selezionare se si vuole consentire a Site Recovery di gestire gli aggiornamenti per l'estensione. Gli aggiornamenti non richiedono un riavvio delle macchine virtuali e non influiscono sulla replica. [Altre informazioni](azure-to-azure-autoupdate.md)

### <a name="disaster-recovery-for-vms-using-accelerated-networking"></a>Ripristino di emergenza per macchine virtuali che usano la rete accelerata

La rete accelerata abilita SR-IOV (Single Root I/O Virtualization) per le macchine virtuali, migliorandone le prestazioni di rete. Quando si abilita la replica per una macchina virtuale di Azure, Site Recovery rileva se è abilitata la rete accelerata. In caso affermativo, dopo il failover Site Recovery configura automaticamente la rete accelerata nella macchina virtuale di Azure di replica di destinazione, sia per [Windows](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell#enable-accelerated-networking-on-existing-vms) che per [Linux](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli#enable-accelerated-networking-on-existing-vms). [Altre informazioni](azure-vm-disaster-recovery-with-accelerated-networking.md)

### <a name="pricing-calculator-for-azure-vm-disaster-recovery"></a>Calcolatore dei prezzi per il ripristino di emergenza delle macchine virtuali di Azure

Il ripristino di emergenza per macchine virtuali di Azure comporta costi di licenza per le macchine virtuali e costi per rete e archiviazione. Azure offre un [calcolatore dei prezzi](https://aka.ms/a2a-cost-estimator) per stimare questi costi. Site Recovery offre ora una [stima dei prezzi di esempio](https://aka.ms/a2a-cost-estimator) per una distribuzione di esempio basata su un'app a tre livelli con sei macchine virtuali con 12 dischi HDD Standard e 6 dischi SSD Premium. L'esempio presuppone modifiche dei dati per 10 GB al giorno per il livello Standard e 20 GB per il livello Premium. È possibile modificare le variabili per stimare i costi per una distribuzione specifica. È possibile specificare il numero di macchine virtuali, il numero e il tipo di dischi gestiti e la frequenza di modifica dei dati prevista per tutte le macchine virtuali. È anche possibile applicare un fattore di compressione per stimare i costi della larghezza di banda. [Leggere](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/) l'annuncio.



## <a name="q3-2018"></a>T3 2018 


### <a name="update-rollup-28-august-2018"></a>Aggiornamento cumulativo 28 (agosto 2018)

[Aggiornamento cumulativo 28](https://support.microsoft.com/help/4460079/update-rollup-28-for-azure-site-recovery) fornisce gli aggiornamenti seguenti.

**Aggiornamento** | **Dettagli**
--- | ---
**Provider e agenti** | Un aggiornamento di agenti di Site Recovery e i provider (come descritto in dettaglio nell'aggiornamento cumulativo)
**Ripristino di emergenza per Linux** | **Macchine virtuali di Azure**: Aggiunta del supporto per Red Hat Enterprise Linux 6.10; CentOS 6.10.<br/><br/> **Le macchine virtuali VMware**: RedHat Enterprise Linux 6.10; CentOS 6.10.<br/><br/> Basato su Linux con macchine virtuali che usa lo stile di partizione GUID partizione GPT (tabella) in modalità di compatibilità BIOS legacy sono ora supportati.
**Supporto di cloud** | È supportato il ripristino di emergenza per macchine virtuali di Azure nel cloud Germania.
**Ripristino di emergenza tra sottoscrizioni** | Supporto per la replica di macchine virtuali di Azure in un'unica area a un'altra area in una sottoscrizione diversa, nello stesso tenant di Azure Active Directory. [Altre informazioni](https://aka.ms/cross-sub-blog)
**Windows Server 2008** | Supporto per la migrazione di macchine in esecuzione Windows Server 2008 R2 o 2008 a 64 bit e a 32 bit.<br/><br/> Solo per la migrazione (replica e failover). Non è supportato il failback.

### <a name="update-rollup-27-july-2018"></a>Aggiornamento cumulativo 27 (luglio 2018)

[Aggiornamento cumulativo 27](https://support.microsoft.com/help/4055712/update-rollup-27-for-azure-site-recovery) fornisce gli aggiornamenti seguenti.

**Aggiornamento** | **Dettagli**
--- | ---
**Provider e agenti** | Un aggiornamento di agenti di Site Recovery e i provider (come descritto in dettaglio nell'aggiornamento cumulativo)
**Ripristino di emergenza per Linux** | **Macchine virtuali di Azure**: Red Hat Enterprise Linux 7.5<br/><br/> **VM VMware/server fisici**: Red Hat Enterprise Linux 7.5, SUSE Linux Enterprise Server 12




## <a name="next-steps"></a>Passaggi successivi

Mantenersi aggiornati nella pagina [Aggiornamenti di Azure](https://azure.microsoft.com/updates/?product=site-recovery).




 









