---
title: Matrice di supporto di Azure Site Recovery per la replica da Azure ad Azure | Microsoft Docs
description: Riepiloga le configurazioni e i sistemi operativi supportati per la replica di Azure Site Recovery di macchine virtuali di Azure (VM) da un'area a un'altra qualora si renda necessario un ripristino di emergenza.
services: site-recovery
documentationcenter: 
author: sujayt
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/10/2017
ms.author: sujayt
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 482bcf08b1256e26e15f7093fda621da4fdd5344
ms.contentlocale: it-it
ms.lasthandoff: 08/21/2017

---
# <a name="azure-site-recovery-support-matrix-for-replicating-from-azure-to-azure"></a>Matrice di supporto di Azure Site Recovery per la replica da Azure ad Azure


>[!NOTE]
>
> La replica di Site Recovery per le macchine virtuali di Azure è attualmente in anteprima.

Questo articolo riepiloga le configurazioni e i componenti supportati per Azure Site Recovery per la replica e il ripristino di macchine virtuali di Azure da un'area a un'altra.

## <a name="user-interface-options"></a>Opzioni dell'interfaccia utente

**Interfaccia utente** |  **Supportato / Non supportato**
--- | ---
**Portale di Azure** | Supportato
**Portale classico** | Non supportate
**PowerShell** | Attualmente non supportato
**API REST** | Attualmente non supportato
**CLI** | Attualmente non supportato


## <a name="resource-move-support"></a>Supporto spostamento risorse

**Tipo spostamento risorse** | **Supportato / Non supportato** | **Osservazioni**  
--- | --- | ---
**Spostamento insieme di credenziali tra gruppi di risorse** | Non supportate |È possibile spostare l'insieme di credenziali di servizi di ripristino tra gruppi di risorse.
**Spostamento servizi di calcolo, archiviazione e rete tra gruppi di risorse** | Non supportate |Se si sposta una macchina virtuale (o i componenti associati come archiviazione e rete) dopo aver abilitato la replica, è necessario disabilitarla e riabilitarla per la macchina virtuale.


## <a name="support-for-deployment-models"></a>Supporto per modelli di distribuzione

**Modello di distribuzione** | **Supportato / Non supportato** | **Osservazioni**  
--- | --- | ---
**Classico** | Supportato | È possibile eseguire la replica di una macchina virtuale classica e ripristinarla solo come macchina virtuale classica. Non è possibile ripristinarla come macchina virtuale di Resource Manager. Non è possibile distribuire una macchina virtuale classica senza una rete virtuale e direttamente in un'area di Azure.
**Gestione risorse** | Supportato |

## <a name="support-for-replicated-machine-os-versions"></a>Supporto per le versioni dei sistemi operativi dei computer replicati

Il supporto è applicabile a qualsiasi carico di lavoro in esecuzione nel sistema operativo indicato.

#### <a name="windows"></a>Windows

- Windows Server 2016 (Server Core e Server con esperienza desktop)*
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2 con almeno SP1

>[!NOTE]
>
> \*Windows Server 2016 Nano Server non è supportato.

#### <a name="linux"></a>Linux

- Red Hat Enterprise Linux 6.7, 6.8, 7.0, 7.1, 7.2, 7.3
- CentOS 6.5, 6.6, 6.7, 6.8, 7.0, 7.1, 7.2, 7.3
- Server Ubuntu 14.04 LTS[ (versioni del kernel supportate)](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)
- Server Ubuntu 16.04 LTS[ (versioni del kernel supportate)](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)
- Oracle Enterprise Linux 6.4 o 6.5 che esegue il kernel compatibile Red Hat o Unbreakable Enterprise Kernel versione 3 (UEK3)
- SUSE Linux Enterprise Server 11 SP3

>[!NOTE]
>
> Per i server Ubuntu che usano l'accesso e l'autenticazione basati su password e che usano il pacchetto cloud-init per configurare le macchine virtuali nel cloud, è possibile che l'accesso basato su password venga disabilitato in caso di failover, a seconda della configurazione di cloudinit. L'accesso basato su password può essere riabilitato nella macchina virtuale reimpostando la password dal menu Impostazioni (nella sezione SUPPORTO + RISOLUZIONE DEI PROBLEMI) di tale macchina virtuale con failover nel portale di Azure.

### <a name="supported-ubuntu-kernel-versions-for-azure-virtual-machines"></a>Versioni del kernel Ubuntu supportate per macchine virtuali di Azure

**Versione** | **Versione del servizio Mobility** | **Versione del kernel** |
--- | --- | --- |
14.04 LTS | 9.9 | Da 3.13.0-24 generica a 3.13.0-117 generica<br/>Da 3.16.0-25 generica a 3.16.0-77 generica<br/>Da 3.19.0-18 generica a 3.19.0-80 generica<br/>Da 4.2.0-18 generica a 4.2.0-42 generica<br/>Da 4.4.0-21 generica a 4.4.0-75 generica |
14.04 LTS | 9.10 | Da 3.13.0-24 generica a 3.13.0-121 generica<br/>Da 3.16.0-25 generica a 3.16.0-77 generica<br/>Da 3.19.0-18 generica a 3.19.0-80 generica<br/>Da 4.2.0-18 generica a 4.2.0-42 generica<br/>Da 4.4.0-21 generica a 4.4.0-81 generica |
16.04 LTS | 9.10 | Da 4.4.0-21 generica a 4.4.0-81 generica<br/>Da 4.8.0-34 generica a 4.8.0-56 generica<br/>Da 4.10.0-14 generica a 4.10.0-24 generica |

## <a name="supported-file-systems-and-guest-storage-configurations-on-azure-virtual-machines-running-linux-os"></a>File system e configurazioni di archiviazione guest supportati nelle macchine virtuali di Azure che eseguono il sistema operativo Linux

* File system: ext3, ext4, ReiserFS (solo Suse Linux Enterprise Server), XFS
* Gestore volumi: LVM2
* Software con percorsi multipli: mapper dispositivi

## <a name="region-support"></a>Supporto di area

È possibile eseguire la replica e il ripristino di macchine virtuali tra due aree qualsiasi all'interno dello stesso cluster geografico.

**Cluster geografico** | **Aree di Azure**
-- | --
America | Canada orientale, Canada centrale, Stati Uniti centro-meridionali, Stati Uniti centro-occidentali, Stati Uniti orientali, Stati Uniti orientali 2, Stati Uniti occidentali, Stati Uniti occidentali 2, Stati Uniti centrali, Stati Uniti centro-settentrionali
Europa | Regno Unito occidentale, Regno Unito meridionale, Europa settentrionale, Europa occidentale
Asia | India meridionale, India centrale, Asia sud-orientale, Asia orientale, Giappone orientale, Giappone occidentale, Corea centrale, Corea meridionale
Australia   | Australia orientale, Australia sudorientale

>[!NOTE]
>
> Per il Brasile meridionale, è possibile eseguire la replica e il failover solo in una delle aree di Stati Uniti centro-meridionali, Stati Uniti centro-occidentali, Stati Uniti orientali, Stati Uniti orientali 2, Stati Uniti occidentali, Stati Uniti occidentali 2 e Stati Uniti centro-settentrionali e failback.


## <a name="support-for-compute-configuration"></a>Supporto per la configurazione del servizio di calcolo

**Configurazione** | **Supportato/Non supportato** | **Osservazioni**
--- | --- | ---
Dimensione | Macchine virtuali di Azure di qualsiasi dimensione con almeno 2 core CPU e 1 GB di RAM | Vedere [Dimensioni delle macchine virtuali di Azure](../virtual-machines/windows/sizes.md)
Set di disponibilità | Supportato | Se si usa l'opzione predefinita durante il passaggio 'Abilita replica' sul portale, il set di disponibilità viene creato automaticamente in base alla configurazione dell'area di origine. È possibile modificare la disponibilità di destinazione impostata in 'Elemento replicato > Impostazioni > Calcolo e rete > Set di disponibilità' in qualsiasi momento.
Macchine virtuali con vantaggio Hybrid Use (HUB, Hybrid Use Benefit) | Supportato | Se la macchina virtuale di origine dispone di una licenza HUB abilitata, anche la macchina virtuale di failover o il failover di test userà la licenza HUB.
set di scalabilità di macchine virtuali | Non supportate |
Immagini della raccolta di Azure - Pubblicate da Microsoft | Supportato | Supportato purché la macchina virtuale sia eseguita in un sistema operativo supportato da Site Recovery
Immagini della raccolta di Azure - Pubblicate da terze parti | Supportato | Supportato purché la macchina virtuale venga eseguita in un sistema operativo supportato da Site Recovery.
Immagini personalizzate - Pubblicate da terze parti | Supportato | Supportato purché la macchina virtuale venga eseguita in un sistema operativo supportato da Site Recovery.
Macchine virtuali migrate tramite Site Recovery | Supportato | Se si tratta di una macchina fisica/VMware migrata in Azure tramite Site Recovery, è necessario disinstallare la versione precedente del servizio di mobilità e riavviare il computer prima di eseguirne la replica in un'altra area di Azure.

## <a name="support-for-storage-configuration"></a>Supporto per la configurazione dell'archiviazione

**Configurazione** | **Supportato/Non supportato** | **Osservazioni**
--- | --- | ---
Dimensioni massime del disco del sistema operativo | 1023 GB | Vedere [Dischi usati dalle VM.](../virtual-machines/windows/about-disks-and-vhds.md#disks-used-by-vms)
Dimensioni massime del disco dati | 1023 GB | Vedere [Dischi usati dalle VM.](../virtual-machines/windows/about-disks-and-vhds.md#disks-used-by-vms)
Numero di dischi dati | Fino a 64 come supportato da una dimensione specifica di una macchina virtuale di Azure | Vedere [Dimensioni delle macchine virtuali di Azure](../virtual-machines/windows/sizes.md)
Disco temporaneo | Sempre escluso dalla replica | Il disco temporaneo è sempre escluso dalla replica. Non è opportuno inserire dati persistenti su un disco temporaneo in base alle indicazioni di Azure. Vedere [Disco temporaneo sulle macchine virtuali di Azure](../virtual-machines/windows/about-disks-and-vhds.md#temporary-disk) per ulteriori dettagli.
Frequenza di modifica dei dati sul disco | Massimo 6 MB/s per disco | Se la frequenza di modifica dei dati sul disco supera costantemente 6 MB/s, la replica non verrà aggiornata. Se invece si tratta di un picco di dati occasionale e la frequenza di modifica dei dati supera 6 MB/s solo per un breve intervallo, la replica verrà aggiornata. In questo caso, potrebbero verificarsi punti di ripristino leggermente ritardati.
Dischi su account di archiviazione standard | Supportato |
Dischi su account di archiviazione premium | Supportato | Se una macchina virtuale dispone di dischi distribuiti tra account di archiviazione standard e premium, è possibile selezionare un account di archiviazione di destinazione diverso per ogni disco per assicurarsi di avere la stessa configurazione di archiviazione nell'area di destinazione
Dischi gestiti standard | Non supportate |  
Dischi gestiti premium | Non supportate |
Spazi di archiviazione | Supportato |         
Crittografia per dati inattivi (SSE) | Supportato | Per gli account di archiviazione della cache e di destinazione, è possibile selezionare un account di archiviazione SSE abilitato.     
Crittografia dischi di Azure (ADE) | Non supportate |
Aggiunta/rimozione a caldo disco | Non supportate | Se si aggiungono o rimuovono dischi dati dalla macchina virtuale, è necessario disabilitare la replica e abilitarla nuovamente per la macchina virtuale.
Esclusione disco | Non supportate|   Il disco temporaneo è escluso per impostazione predefinita.
Archiviazione con ridondanza locale | Supportato |
Archiviazione con ridondanza geografica | Supportato |
RA-GRS | Supportato |
ZRS | Non supportate |  
Archiviazione ad accesso frequente e sporadico | Non supportate | I dischi delle macchine virtuali non sono supportati per l'archiviazione ad accesso frequente e sporadico

>[!IMPORTANT]
> Assicurarsi di seguire le [indicazioni per l'archiviazione](../storage/common/storage-scalability-targets.md#scalability-targets-for-virtual-machine-disks) per le macchine virtuali di Azure di origine per evitare problemi di prestazioni. Se si seguono le impostazioni predefinite, Site Recovery crea gli account di archiviazione necessari in base alla configurazione di origine. Se si personalizzano e si selezionano impostazioni specifiche, assicurarsi di seguire (../storage/common/storage-scalability-targets.md#scalability-targets-for-virtual-machine-disks) come le macchine virtuali di origine.

## <a name="support-for-network-configuration"></a>Supporto per la configurazione di rete
**Configurazione** | **Supportato/Non supportato** | **Osservazioni**
--- | --- | ---
Interfaccia di rete (NIC) | Fino al numero massimo di schede di rete supportato da dimensioni specifiche delle macchine virtuali di Azure | Le schede di rete vengono create quando viene creata la macchina virtuale nell'ambito dell'operazione di failover o del failover di test. Il numero di schede di rete sulla macchina virtuale di failover dipende dal numero di schede di rete di cui dispone la macchina virtuale quando viene abilitata la replica. Eventuali aggiunte o rimozioni di schede di rete dopo aver abilitato la replica non influiscono sul numero di schede di rete sulla macchina virtuale di failover.
Servizio di bilanciamento del carico Internet | Supportato | È necessario associare il servizio di bilanciamento del carico preconfigurato tramite uno script di automazione di Azure in un piano di ripristino.
Servizio di bilanciamento del carico interno | Supportato | È necessario associare il servizio di bilanciamento del carico preconfigurato tramite uno script di automazione di Azure in un piano di ripristino.
IP pubblico| Supportato | È necessario associare un IP pubblico esistente alla scheda di rete o crearne uno e associarlo alla scheda di rete tramite uno script di automazione di Azure in un piano di ripristino.
Gruppo di sicurezza di rete (NSG) sulla scheda di rete (Resource Manager)| Supportato | È necessario associare il gruppo di sicurezza di rete alla scheda di rete tramite uno script di automazione di Azure in un piano di ripristino.  
Gruppo di sicurezza di rete (NSG) sulla subnet (Resource Manager e classica)| Supportato | È necessario associare il gruppo di sicurezza di rete alla scheda di rete tramite uno script di automazione di Azure in un piano di ripristino.
Gruppo di sicurezza di rete (NSG) sulla macchina virtuale (classica)| Supportato | È necessario associare il gruppo di sicurezza di rete alla scheda di rete tramite uno script di automazione di Azure in un piano di ripristino.
IP riservato (IP statico) / Mantieni IP di origine | Supportato | Se la scheda di rete sulla macchina virtuale di origine presenta una configurazione di IP statico e la subnet di destinazione dispone dello stesso IP, viene assegnato alla macchina virtuale di failover. Se la subnet di destinazione non dispone dello stesso IP, uno degli IP disponibili nella subnet viene riservato per questa macchina virtuale. È possibile specificare un IP fisso a scelta in 'Elemento replicato > Impostazioni > Calcolo e rete > Interfacce di rete'. È possibile selezionare la scheda di rete e specificare la subnet e l'IP di propria scelta.
IP dinamico| Supportato | Se la scheda di rete nella macchina virtuale di origine presenta una configurazione di IP dinamico, anche la scheda di rete sulla macchina virtuale di failover è dinamica per impostazione predefinita. È possibile specificare un IP fisso a scelta in 'Elemento replicato > Impostazioni > Calcolo e rete > Interfacce di rete'. È possibile selezionare la scheda di rete e specificare la subnet e l'IP di propria scelta.
Integrazione di Gestione traffico | Supportato | È possibile preconfigurare Gestione traffico in modo che il traffico venga regolarmente indirizzato all'endpoint nell'area di origine e all'endpoint nell'area di destinazione in caso di failover.
DNS gestito di Azure | Supportato |
DNS personalizzato  | Supportato |    
Proxy non autenticato | Supportato | Vedere il [documento sulle indicazioni per la connettività di rete.](site-recovery-azure-to-azure-networking-guidance.md)    
Proxy autenticato | Non supportate | Se la macchina virtuale utilizza un proxy autenticato per la connettività in uscita, non può essere replicata tramite Azure Site Recovery.    
VPN da sito a sito locale (con o senza ExpressRoute)| Supportato | Verificare che le route definite dall'utente e i gruppi di sicurezza di rete siano configurati in modo che il traffico di Site Recovery non venga instradato in locale. Vedere il [documento sulle indicazioni per la connettività di rete.](site-recovery-azure-to-azure-networking-guidance.md)  
Connessione da rete virtuale a rete virtuale | Supportato | Vedere il [documento sulle indicazioni per la connettività di rete.](site-recovery-azure-to-azure-networking-guidance.md)  


## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sulle [indicazioni per la connettività di rete per la replica delle macchine virtuali di Azure](site-recovery-azure-to-azure-networking-guidance.md)
- Iniziare a proteggere i carichi di lavoro [replicando le macchine virtuali di Azure](site-recovery-azure-to-azure.md)

