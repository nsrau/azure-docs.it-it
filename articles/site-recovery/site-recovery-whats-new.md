---
title: Novità di Azure Site Recovery | Microsoft Docs
description: Riepilogo delle nuove funzionalità introdotte in Azure Site Recovery
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: raynew
ms.openlocfilehash: 50e1cb95249f0108430e978ae3ffe23b6edc778d
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66418395"
---
# <a name="whats-new-in-site-recovery"></a>Novità di Site Recovery

Il servizio [Azure Site Recovery](site-recovery-overview.md) viene aggiornato e migliorato in modo continuativo. Per consentire ai clienti di restare sempre aggiornati, in questo articolo vengono fornite informazioni sulle versioni più recenti, le nuove funzionalità e i nuovi contenuti. Questa pagina viene aggiornata regolarmente.

Se si hanno suggerimenti per le funzionalità di Site Recovery, Microsoft è lieta di ricevere [commenti e suggerimenti dai clienti](https://feedback.azure.com/forums/256299-site-recovery).


## <a name="updates-march-2019"></a>Aggiornamenti (marzo 2019)

### <a name="update-rollup-35"></a>Aggiornamento cumulativo 35

[Aggiornamento cumulativo 35](https://support.microsoft.com/en-us/help/4494485/update-rollup-35-for-azure-site-recovery) fornisce gli aggiornamenti seguenti.

**Aggiornamento** | **Dettagli**
--- | ---
**Provider e agenti** | Un aggiornamento di agenti di Site Recovery e i provider (come descritto in dettaglio nell'aggiornamento cumulativo)
**Correzioni/miglioramenti di problema** | Numerose correzioni e miglioramenti (come descritto in dettaglio nell'aggiornamento cumulativo)

#### <a name="vmwarephysical-server-disaster-recovery"></a>Ripristino di emergenza di VMware/server fisici
Nuove funzionalità aggiunte nell'aggiornamento.

**Funzionalità** | **Dettagli**
--- | ---
**Dischi gestiti** | Replica di macchine virtuali VMware locali e server fisici è ora direttamente al servizio managed disks in Azure. On-premises dati vengono inviati a un account di archiviazione della cache di Azure, e i punti di ripristino vengono creati nei dischi gestiti nel percorso di destinazione. In questo modo che non occorre gestire più account di archiviazione di destinazione.
**Server di configurazione** | Site Recovery supporta attualmente un server di configurazione con più schede di rete. È necessario aggiungere altre schede per il server di configurazione della macchina virtuale prima di registrare il server di configurazione nell'insieme di credenziali. Se si aggiunge in un secondo momento, è necessario registrare nuovamente il server nell'insieme di credenziali.


## <a name="updates-february-2019"></a>Aggiornamenti (febbraio 2019)

### <a name="update-rollup-34"></a>Aggiornamento cumulativo 34 

[Aggiornamento cumulativo 34](https://support.microsoft.com/help/4490016/update-rollup-34-for-azure-site-recovery) fornisce gli aggiornamenti seguenti.

**Aggiornamento** | **Dettagli**
--- | ---
**Provider e agenti** | Un aggiornamento di agenti di Site Recovery e i provider (come descritto in dettaglio nell'aggiornamento cumulativo).
**Correzioni/miglioramenti di problema** | Numerose correzioni e miglioramenti (come descritto in dettaglio nell'aggiornamento cumulativo).


### <a name="update-rollup-33"></a>Aggiornamento cumulativo 33 

[Aggiornamento cumulativo 33](https://support.microsoft.com/help/4489582/update-rollup-33-for-azure-site-recovery) fornisce gli aggiornamenti seguenti.

**Aggiornamento** | **Dettagli**
--- | ---
**Provider e agenti** | Un aggiornamento di agenti di Site Recovery e i provider (come descritto in dettaglio nell'aggiornamento cumulativo).
**Correzioni/miglioramenti di problema** | Numerose correzioni e miglioramenti (come descritto in dettaglio nell'aggiornamento cumulativo).


#### <a name="azure-vm-disaster-recovery"></a>Ripristino di emergenza di macchine Virtuali Azure 
Nuove funzionalità aggiunte nell'aggiornamento.

**Funzionalità** | **Dettagli**
--- | ---
**Mapping di rete** | Per il ripristino di emergenza di macchine Virtuali di Azure, è ora possibile usare qualsiasi rete di destinazione disponibili quando si abilita la replica. 
**Standard SSD** | È ora possibile configurare il ripristino di emergenza per macchine virtuali di Azure usando [dischi SSD Standard](https://docs.microsoft.com/azure/virtual-machines/windows/disks-standard-ssd).
**Spazi di archiviazione diretta** | È possibile configurare il ripristino di emergenza per le App in esecuzione su macchine Virtuali di Azure le app usando [spazi di archiviazione diretta](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview) per la disponibilità elevata.  Usa spazi di archiviazione è diretta (S2D) insieme a Site Recovery offre protezione completa dei carichi di lavoro di macchina virtuale di Azure. S2D consente di ospitare un cluster guest in Azure. Ciò è particolarmente utile quando una macchina virtuale ospita un'applicazione critica, ad esempio livello di SAP ASCS, SQL Server o file server di scalabilità orizzontale.


#### <a name="vmwarephysical-server-disaster-recovery"></a>Ripristino di emergenza di VMware/server fisici
Nuove funzionalità aggiunte nell'aggiornamento.

**Funzionalità** | **Dettagli**
--- | ---
**Sistema di file Linux BRTFS** | Site Recovery supporta ora la replica di macchine virtuali VMware con il file system BRTFS. Replica non è supportata se:<br/><br/>-Il volume del sistema file BTRFS secondari viene modificato dopo l'abilitazione della replica.<br/><br/>-Il file system è distribuito tra più dischi.<br/><br/>-Il file system BTRFS supporta RAID.
**Windows Server 2019** | Supporto aggiunto per le macchine che eseguono Windows Server 2019.


## <a name="updates-january-2019"></a>Aggiornamenti (gennaio 2019)

### <a name="accelerated-networking-azure-vms"></a>Funzionalità rete accelerata (macchine virtuali di Azure)

La rete accelerata abilita SR-IOV (Single Root I/O Virtualization) per le macchine virtuali, migliorandone le prestazioni di rete. Quando si abilita la replica per una macchina virtuale di Azure, Site Recovery rileva se è abilitata la rete accelerata. In caso affermativo, dopo il failover Site Recovery configura automaticamente la rete accelerata nella macchina virtuale di Azure di replica di destinazione, sia per [Windows](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell#enable-accelerated-networking-on-existing-vms) che per [Linux](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli#enable-accelerated-networking-on-existing-vms).

[Altre informazioni](azure-vm-disaster-recovery-with-accelerated-networking.md)

### <a name="update-rollup-32"></a>Aggiornamento cumulativo 32 

[Aggiornamento cumulativo 32](https://support.microsoft.com/help/4485985/update-rollup-32-for-azure-site-recovery) fornisce gli aggiornamenti seguenti.

**Aggiornamento** | **Dettagli**
--- | ---
**Provider e agenti** | Un aggiornamento di agenti di Site Recovery e i provider (come descritto in dettaglio nell'aggiornamento cumulativo).
**Correzioni/miglioramenti di problema** | Numerose correzioni e miglioramenti (come descritto in dettaglio nell'aggiornamento cumulativo).

#### <a name="azure-vm-disaster-recovery"></a>Ripristino di emergenza di macchine Virtuali Azure

Nuove funzionalità aggiunte nell'aggiornamento.

**Funzionalità** | **Dettagli**
--- | ---
**Supporto Linux** | È stato aggiunto il supporto per RedHat Workstation 6, 7 e le nuove versioni del kernel per Ubuntu, Debian e SUSE.
**Spazi di archiviazione diretta** | Site Recovery supporta macchine virtuali di Azure tramite spazi di archiviazione è diretta (S2D).

#### <a name="vmware-vmsphysical-servers-replication"></a>Replica di server fisici/macchine virtuali VMware 
**Funzionalità** | **Dettagli**
--- | ---
**Supporto Linux** | È stato aggiunto il supporto per Red Hat Enterprise Linux 7.6, RedHat Workstation 6, 7, Oracle Linux 6.10/7.6 e nuove versioni del kernel per Ubuntu, Debian e SUSE.


### <a name="update-rollup-31"></a>Aggiornamento cumulativo 31 

[Aggiornamento cumulativo 31](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery) fornisce gli aggiornamenti seguenti.

**Aggiornamento** | **Dettagli**
--- | ---
**Provider e agenti** | Un aggiornamento di agenti di Site Recovery e i provider (come descritto in dettaglio nell'aggiornamento cumulativo).
**Correzioni/miglioramenti di problema** | Numerose correzioni e miglioramenti (come descritto in dettaglio nell'aggiornamento cumulativo).

#### <a name="vmware-vmsphysical-servers-replication"></a>Replica di server fisici/macchine virtuali VMware 
Nuove funzionalità aggiunte nell'aggiornamento.

**Funzionalità** | **Dettagli**
--- | ---
**Supporto Linux** | È stato aggiunto il supporto per Oracle Linux 6.8 e 6.9/7.0 e per il kernel UEK5.
**LVM** | Aggiunto il supporto per volumi LVM e LVM2.<br/><br/> La directory /boot in una partizione del disco e nei volumi LVM è ora supportata.
**Directories** | È stato aggiunto il supporto per queste directory impostate come partizioni separate o file System che non sono nello stesso disco del sistema:<br/><br/> /(root), /boot, /usr, /usr/local, /var, /etc.
**Windows Server 2008** | Aggiunto il supporto per dischi dinamici.
**Failover** | Migliorato il tempo di failover per le macchine virtuali VMware in cui storvsc e vsbus non sono driver di avvio.
**Supporto per UEFI** | Macchine virtuali di Azure non supportano il tipo di avvio UEFI. È ora possibile eseguire la migrazione di server fisici locali con UEFI in Azure con Site Recovery. Site Recovery esegue la migrazione di server per la conversione del tipo di avvio in BIOS prima della migrazione. Solo il ripristino del sito supportato in precedenza questa conversione per le macchine virtuali. Il supporto è disponibile per i server fisici che eseguono Windows Server 2012 o versione successiva.

#### <a name="azure-vm-disaster-recovery"></a>Ripristino di emergenza di macchine Virtuali Azure
Nuove funzionalità aggiunte nell'aggiornamento.

**Funzionalità** | **Dettagli**
--- | ---
**Supporto Linux** | È supportato per Oracle Linux 6.8 e 6.9/7.0; è stato aggiunto e per il kernel UEK5.
**Sistema di file Linux BRTFS** | È supportato per macchine virtuali di Azure.
**Macchine virtuali di Azure nelle zone di disponibilità** | È possibile abilitare la replica in un'altra area per le VM di Azure distribuite nelle zone di disponibilità. È ora possibile abilitare la replica per una macchina virtuale di Azure e impostare la destinazione per il failover su una singola istanza di macchina virtuale, una macchina virtuale in un set di disponibilità o una macchina virtuale in una zona di disponibilità. L'impostazione non influisce sulla replica. [Leggere](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region/) l'annuncio.
**Archiviazione abilitati firewall (portale/PowerShell)** | Aggiunto il supporto per [gli account di archiviazione abilitati firewall](https://docs.microsoft.com/azure/storage/common/storage-network-security).<br/><br/> È possibile replicare macchine virtuali di Azure con dischi non gestiti negli account di archiviazione firewall attivato in un'altra area di Azure per il ripristino di emergenza.<br/><br/> È possibile usare gli account di archiviazione abilitati firewall degli account di archiviazione di destinazione per i dischi non gestiti.<br/><br/> Supportato nel portale e tramite PowerShell.

## <a name="updates-december-2018"></a>Aggiornamenti (dicembre 2018)

### <a name="automatic-updates-for-the-mobility-service-azure-vms"></a>Aggiornamenti automatici per il servizio Mobility (macchine virtuali di Azure)

In Site Recovery è stata aggiunta un'opzione per gli aggiornamenti automatici per l'estensione del servizio Mobility. L'estensione del servizio Mobility viene installata in ogni macchina virtuale di Azure replicata da Site Recovery. Quando si abilita la replica, occorre selezionare se si vuole consentire a Site Recovery di gestire gli aggiornamenti per l'estensione.

Gli aggiornamenti non richiedono un riavvio delle macchine virtuali e non influiscono sulla replica. [Altre informazioni](azure-to-azure-autoupdate.md)

### <a name="pricing-calculator-for-azure-vm-disaster-recovery"></a>Calcolatore dei prezzi per il ripristino di emergenza delle macchine virtuali di Azure

Ripristino di emergenza di VM di Azure comporta costi di licenza della macchina virtuale e i costi di archiviazione e rete. Azure offre un [calcolatore dei prezzi](https://aka.ms/a2a-cost-estimator) per stimare questi costi. Site Recovery offre ora una [stima dei prezzi di esempio](https://aka.ms/a2a-cost-estimator) per una distribuzione di esempio basata su un'app a tre livelli con sei macchine virtuali con 12 dischi HDD Standard e 6 dischi SSD Premium.

- L'esempio presuppone che una modifica dei dati di 10 GB in un giorno per il livello standard e 20 GB per premium.
- È possibile modificare le variabili per stimare i costi per una distribuzione specifica.
- È possibile specificare il numero di macchine virtuali, il numero e il tipo di dischi gestiti e la frequenza di modifica dei dati prevista per tutte le macchine virtuali.
- È anche possibile applicare un fattore di compressione per stimare i costi della larghezza di banda.

[Leggere](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/) l'annuncio.


## <a name="updates-october-2018"></a>Aggiornamenti (ottobre 2018)

### <a name="update-rollup-30"></a>Aggiornamento cumulativo di 30 

[Aggiornamento cumulativo 30](https://support.microsoft.com/help/4468181/azure-site-recovery-update-rollup-30) fornisce gli aggiornamenti seguenti.

**Aggiornamento** | **Dettagli**
--- | ---
**Provider e agenti** | Un aggiornamento di agenti di Site Recovery e i provider (come descritto in dettaglio nell'aggiornamento cumulativo).
**Correzioni/miglioramenti di problema** | Numerose correzioni e miglioramenti (come descritto in dettaglio nell'aggiornamento cumulativo).

#### <a name="azure-vm-disaster-recovery"></a>Ripristino di emergenza di macchine Virtuali Azure
Nuove funzionalità aggiunte nell'aggiornamento.

**Funzionalità** | **Dettagli**
--- | ---
**Supporto per aree** | Supporto aggiunto per Australia centrale 1 e Australia centrale 2 per il ripristino del sito.
**Supporto per la crittografia del disco** | Supporto aggiunto per il ripristino di emergenza di macchine virtuali di Azure crittografate con crittografia dischi di Azure (ADE) con l'app di Azure AD. [Altre informazioni](azure-to-azure-how-to-enable-replication-ade-vms.md)
**Esclusione disco** | Dischi non inizializzati vengono esclusi automaticamente durante la replica di macchine Virtuali di Azure.
**Archiviazione abilitati firewall (PowerShell)** | Aggiunto il supporto per [gli account di archiviazione abilitati firewall](https://docs.microsoft.com/azure/storage/common/storage-network-security).<br/><br/> È possibile replicare macchine virtuali di Azure con dischi non gestiti negli account di archiviazione firewall attivato in un'altra area di Azure per il ripristino di emergenza.<br/><br/> È possibile usare gli account di archiviazione abilitati firewall degli account di archiviazione di destinazione per i dischi non gestiti.<br/><br/> Supportato solo con PowerShell.


### <a name="update-rollup-29"></a>Aggiornamento cumulativo 29 

[Aggiornamento cumulativo 29](https://support.microsoft.com/help/4466466/update-rollup-29-for-azure-site-recovery) fornisce gli aggiornamenti seguenti.

**Aggiornamento** | **Dettagli**
--- | ---
**Provider e agenti** | Un aggiornamento di agenti di Site Recovery e i provider (come descritto in dettaglio nell'aggiornamento cumulativo).
**Correzioni/miglioramenti di problema** | Numerose correzioni e miglioramenti (come descritto in dettaglio nell'aggiornamento cumulativo).


## <a name="updates-august-2018"></a>Aggiornamenti (agosto 2018)

### <a name="update-rollup-28"></a>Aggiornamento cumulativo 28 

[Aggiornamento cumulativo 28](https://support.microsoft.com/help/4460079/update-rollup-28-for-azure-site-recovery) fornisce gli aggiornamenti seguenti.

**Aggiornamento** | **Dettagli**
--- | ---
**Provider e agenti** | Un aggiornamento di agenti di Site Recovery e i provider (come descritto in dettaglio nell'aggiornamento cumulativo).
**Correzioni/miglioramenti di problema** | Numerose correzioni e miglioramenti (come descritto in dettaglio nell'aggiornamento cumulativo).

#### <a name="azure-vms-disaster-recovery"></a>Ripristino di emergenza di macchine virtuali di Azure 
Nuove funzionalità aggiunte nell'aggiornamento.

**Funzionalità** | **Dettagli**
--- | ---
**Supporto Linux** | Aggiunta del supporto per Red Hat Enterprise Linux 6.10; CentOS 6.10.<br/><br/>
**Supporto di cloud** | È supportato il ripristino di emergenza per macchine virtuali di Azure nel cloud Germania.
**Ripristino di emergenza tra sottoscrizioni** | Supporto per la replica di macchine virtuali di Azure in un'unica area a un'altra area in una sottoscrizione diversa, nello stesso tenant di Azure Active Directory. [Altre informazioni](https://aka.ms/cross-sub-blog)

#### <a name="vmware-vmphysical-server-disaster-recovery"></a>Ripristino di emergenza di VM VMware/server fisici 
Nuove funzionalità aggiunte nell'aggiornamento.

**Funzionalità** | **Dettagli**
--- | ---
**Supporto Linux** | Supporto aggiunto per 6.10 di Red Hat Enterprise Linux, CentOS 6.10.<br/><br/> Basato su Linux con macchine virtuali che usa lo stile di partizione GUID partizione GPT (tabella) in modalità di compatibilità BIOS legacy sono ora supportati. Rivedere le [domande frequenti sulla macchina virtuale di Azure](https://docs.microsoft.com/azure/virtual-machines/linux/faq-for-disks) per altre informazioni. 
**Ripristino di emergenza per macchine virtuali dopo la migrazione** | Supporto per abilitare il ripristino di emergenza in un'area secondaria per una macchina virtuale VMware locale eseguita la migrazione ad Azure, senza dover disinstallare Mobility service nella macchina virtuale prima di abilitare la replica.
**Windows Server 2008** | Supporto per la migrazione di macchine in esecuzione Windows Server 2008 R2 o 2008 a 64 bit e a 32 bit.<br/><br/> Solo per la migrazione (replica e failover). Non è supportato il failback.

## <a name="updates-july-2018"></a>Aggiornamenti (luglio 2018)

### <a name="update-rollup-27-july-2018"></a>Aggiornamento cumulativo 27 (luglio 2018)

[Aggiornamento cumulativo 27](https://support.microsoft.com/help/4055712/update-rollup-27-for-azure-site-recovery) fornisce gli aggiornamenti seguenti.

**Aggiornamento** | **Dettagli**
--- | ---
**Provider e agenti** | Un aggiornamento di agenti di Site Recovery e i provider (come descritto in dettaglio nell'aggiornamento cumulativo).
**Correzioni/miglioramenti di problema** | Numerose correzioni e miglioramenti (come descritto in dettaglio nell'aggiornamento cumulativo).

#### <a name="azure-vms-disaster-recovery"></a>Ripristino di emergenza di macchine virtuali di Azure 

Nuove funzionalità aggiunte nell'aggiornamento.

**Funzionalità** | **Dettagli**
--- | ---
**Supporto Linux** | Supporto aggiunto per Red Hat Enterprise Linux 7.5.

#### <a name="vmware-vmphysical-server-disaster-recovery"></a>Ripristino di emergenza di VM VMware/server fisici 

Nuove funzionalità aggiunte nell'aggiornamento.

**Funzionalità** | **Dettagli**
--- | ---
**Supporto Linux** | Supporto aggiunto per Red Hat Enterprise Linux 7.5, SUSE Linux Enterprise Server 12.



## <a name="next-steps"></a>Passaggi successivi

Mantenersi aggiornati nella pagina [Aggiornamenti di Azure](https://azure.microsoft.com/updates/?product=site-recovery).
