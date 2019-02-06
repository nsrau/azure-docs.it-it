---
title: Novità di Azure Site Recovery | Microsoft Docs
description: Riepilogo delle nuove funzionalità introdotte in Azure Site Recovery
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 01/28/2019
ms.author: raynew
ms.openlocfilehash: 61e66a19b625141c69a9473373d3d5d808e18fde
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55211118"
---
# <a name="whats-new-in-site-recovery"></a>Novità di Site Recovery

Il servizio [Azure Site Recovery](site-recovery-overview.md) viene aggiornato e migliorato in modo continuativo. Per consentire ai clienti di restare sempre aggiornati, in questo articolo vengono fornite informazioni sulle versioni più recenti, le nuove funzionalità e i nuovi contenuti. Questa pagina viene aggiornata regolarmente.

Se si hanno suggerimenti per le funzionalità di Site Recovery, Microsoft è lieta di ricevere [commenti e suggerimenti dai clienti](https://feedback.azure.com/forums/256299-site-recovery).

## <a name="q1-2019"></a>T1 2019

### <a name="linux-support"></a>Supporto Linux

L'[aggiornamento cumulativo 32](https://support.microsoft.com/help/4485985/update-rollup-32-for-azure-site-recovery) include un aggiornamento per gli agenti e i provider di Site Recovery. Gli aggiornamenti aggiungono il supporto per Linux come segue:

- **Ripristino di emergenza di macchine virtuali di Azure**: RedHat Workstation 6/7; nuove versioni del kernel per Ubuntu, Debian e SUSE.
- **Ripristino di emergenza di macchine virtuali VMware/server fisici in Azure**: RedHat Enterprise Linux 7.6; RedHat Workstation 6/7; Oracle Linux 6.10/7.6; nuove versioni del kernel per Ubuntu, Debian e SUSE.



## <a name="q4-2018"></a>T4 2018

## <a name="pricing-calculator-for-azure-vm-disaster-recovery"></a>Calcolatore dei prezzi per il ripristino di emergenza delle macchine virtuali di Azure

Il ripristino di emergenza per macchine virtuali di Azure comporta costi di licenza per le macchine virtuali e costi per rete e archiviazione. Azure offre un [calcolatore dei prezzi](https://aka.ms/a2a-cost-estimator) per stimare questi costi. Site Recovery offre ora una [stima dei prezzi di esempio](https://aka.ms/a2a-cost-estimator) per una distribuzione di esempio basata su un'app a tre livelli con sei macchine virtuali con 12 dischi HDD Standard e 6 dischi SSD Premium. L'esempio presuppone modifiche dei dati per 10 GB al giorno per il livello Standard e 20 GB per il livello Premium. È possibile modificare le variabili per stimare i costi per una distribuzione specifica. È possibile specificare il numero di macchine virtuali, il numero e il tipo di dischi gestiti e la frequenza di modifica dei dati prevista per tutte le macchine virtuali. È anche possibile applicare un fattore di compressione per stimare i costi della larghezza di banda. [Leggere](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/) l'annuncio.

### <a name="support-for-azure-vms-in-zones"></a>Supporto per macchine virtuali di Azure nelle zone

Le zone di disponibilità di Azure sono località fisiche esclusive all'interno di un'area di Azure. Ogni zona è costituita da uno o più data center dotati di impianti indipendenti per l'alimentazione, il raffreddamento e la connettività di rete. È ora possibile abilitare la replica per una macchina virtuale di Azure e impostare la destinazione per il failover su una singola istanza di macchina virtuale, una macchina virtuale in un set di disponibilità o una macchina virtuale in una zona di disponibilità. L'impostazione non influisce sulla replica. [Leggere](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region/) l'annuncio.
 
### <a name="disaster-recovery-for-encrypted-vms"></a>Ripristino di emergenza per macchine virtuali crittografate

Site Recovery supporta macchine virtuali di Azure crittografate con Crittografia dischi di Azure con l'app Azure AD. [Altre informazioni](azure-to-azure-how-to-enable-replication-ade-vms.md)

### <a name="disaster-recovery-for-vms-using-accelerated-networking"></a>Ripristino di emergenza per macchine virtuali che usano la rete accelerata

La rete accelerata abilita SR-IOV (Single Root I/O Virtualization) per le macchine virtuali, migliorandone le prestazioni di rete. Quando si abilita la replica per una macchina virtuale di Azure, Site Recovery rileva se è abilitata la rete accelerata. In caso affermativo, dopo il failover Site Recovery configura automaticamente la rete accelerata nella macchina virtuale di Azure di replica di destinazione, sia per [Windows](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell#enable-accelerated-networking-on-existing-vms) che per [Linux](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli#enable-accelerated-networking-on-existing-vms). [Altre informazioni](azure-vm-disaster-recovery-with-accelerated-networking.md)

### <a name="automatic-updates-for-the-mobility-service-extension"></a>Aggiornamenti automatici per l'estensione del servizio Mobility

In Site Recovery è stata aggiunta un'opzione per gli aggiornamenti automatici per l'estensione del servizio Mobility. L'estensione del servizio Mobility viene installata in ogni macchina virtuale di Azure replicata da Site Recovery. Quando si abilita la replica, occorre selezionare se si vuole consentire a Site Recovery di gestire gli aggiornamenti per l'estensione. Gli aggiornamenti non richiedono un riavvio delle macchine virtuali e non influiscono sulla replica. [Altre informazioni](azure-to-azure-autoupdate.md)

### <a name="support-for-standard-ssd-disks"></a>Supporto per dischi SSD Standard

In Azure sono stati introdotti i [dischi SSD (Solid State Drives) Standard](https://docs.microsoft.com/azure/virtual-machines/windows/disks-standard-ssd) per fornire una soluzione di archiviazione conveniente per le app, ad esempio server Web che richiedono prestazioni coerenti, ma non hanno livelli elevati di operazioni di I/O al secondo su disco. Questi dischi combinano elementi dei dischi SSD Premium e HDD Standard. Site Recovery fornisce il ripristino di emergenza per le macchine virtuali di Azure tramite disco SSD Standard. Per impostazione predefinita, il tipo di disco viene mantenuto dopo il failover all'area di destinazione.

### <a name="support-for-azure-storage-firewall"></a>Supporto per il firewall di archiviazione di Azure

È possibile proteggere gli account di archiviazione di Azure in un set specifico di reti attivando le regole del firewall per l'account. Configurare gli account di archiviazione per rifiutare il traffico da reti interne e Internet per impostazione predefinita, quindi concedere l'accesso al traffico da specifiche reti virtuali. Site Recovery supporta la replica per le macchine virtuali con dischi non gestiti negli account di archiviazione abilitati per il firewall in un'area secondaria. Nell'area di destinazione, per i dischi non gestiti, è possibile selezionare gli account di archiviazione con i firewall abilitati. È anche possibile limitare l'accesso all'account di archiviazione della cache, limitando l'accesso alla rete alla sola rete in cui si trovano le macchine virtuali di origine. Si noti che è necessario [consentire l'accesso](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions) per i servizi Microsoft attendibili.

## <a name="q3-2018"></a>T3 2018 

### <a name="linux-support"></a>Supporto Linux

#### <a name="update-rollup-28"></a>Aggiornamento cumulativo 28

L'[aggiornamento cumulativo 28](https://support.microsoft.com/help/4460079/update-rollup-28-for-azure-site-recovery) include un aggiornamento per gli agenti e i provider di Site Recovery. Gli aggiornamenti aggiungono il supporto per Linux come segue:

- **Ripristino di emergenza di macchine virtuali di Azure**: RedHat Enterprise Linux 6.10; CentOS 6.10; sono ora supportate le macchine virtuali basate su Linux che usano lo stile di partizione GPT (GUID Partition Table) in modalità di compatibilità BIOS legacy.
- **Ripristino di emergenza di macchine virtuali VMware/server fisici in Azure**: RedHat Enterprise Linux 6.10; CentOS 6.10; sono ora supportate le macchine virtuali basate su Linux che usano lo stile di partizione GPT (GUID Partition Table) in modalità di compatibilità BIOS legacy.

#### <a name="update-rollup-27"></a>Aggiornamento cumulativo 27

L'[aggiornamento cumulativo 28](https://support.microsoft.com/help/4460079/update-rollup-28-for-azure-site-recovery) include un aggiornamento per gli agenti e i provider di Site Recovery. Gli aggiornamenti aggiungono il supporto per Linux come segue:

- **Ripristino di emergenza di macchine virtuali di Azure**: Red Hat Enterprise Linux 7.5
- **Ripristino di emergenza di macchine virtuali VMware/server fisici in Azure**: SUSE Linux Enterprise Server 12, Red Hat Enterprise Linux 7.5

### <a name="support-for-azure-vms-running-on-windows-server-2016"></a>Supporto per macchine virtuali di Azure in esecuzione in Windows Server 2016

È possibile replicare macchine virtuali di Azure in esecuzione in Windows Server 2016 in aree di Azure con Azure Site Recovery.

### <a name="support-for-azure-vms-running-storage-spaces-direct"></a>Supporto per macchine virtuali di Azure che eseguono Spazi di archiviazione diretta

[Spazi di archiviazione diretta](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview) (disponibile da Windows Server 2016 in poi) raggruppa le unità in un pool di archiviazione e quindi usa la capacità del pool per creare spazi di archiviazione. Gli spazi di archiviazione possono essere usati in una macchina virtuale autonoma, in un [cluster guest di macchine virtuali di Azure](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-in-vm) con archiviazione locale in ogni nodo del cluster o per risorse di archiviazione condivise all'interno del cluster.

## <a name="next-steps"></a>Passaggi successivi

Mantenersi aggiornati nella pagina [Aggiornamenti di Azure](https://azure.microsoft.com/updates/?product=site-recovery).


