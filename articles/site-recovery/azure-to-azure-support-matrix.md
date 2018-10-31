---
title: Matrice di supporto di Azure Site Recovery per la replica da Azure ad Azure | Microsoft Docs
description: Riepiloga le configurazioni e i sistemi operativi supportati per la replica di Azure Site Recovery di macchine virtuali di Azure (VM) da un'area a un'altra qualora si renda necessario un ripristino di emergenza.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.date: 09/10/2018
ms.author: sujayt
ms.openlocfilehash: 49773e076ed8bb06ff76f9f654b914a709051fb5
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2018
ms.locfileid: "49378619"
---
# <a name="support-matrix-for-replicating-from-one-azure-region-to-another"></a>Matrice di supporto per la replica da un'area di Azure a un'altra



Questo articolo riepiloga le configurazioni e i componenti supportati per la replica e il ripristino di macchine virtuali di Azure da un'area a un'altra usando il servizio [Azure Site Recovery](site-recovery-overview.md).

## <a name="user-interface-options"></a>Opzioni dell'interfaccia utente

**Interfaccia utente** |  **Supportato / Non supportato**
--- | ---
**Portale di Azure** | Supportato
**PowerShell** | [Replica da Azure ad Azure con PowerShell](azure-to-azure-powershell.md)
**API REST** | Attualmente non supportato
**CLI** | Attualmente non supportato


## <a name="resource-support"></a>Supporto delle risorse

**Tipo spostamento risorse** | **Dettagli**
--- | --- | ---
**Spostamento insieme di credenziali tra gruppi di risorse** | Non supportate<br/><br/> Non è possibile spostare l'insieme di credenziali di Servizi di ripristino tra gruppi di risorse.
**Spostamento di risorse di calcolo, archiviazione e rete tra gruppi di risorse** | Non supportati.<br/><br/> Se si sposta una macchina virtuale o componenti associati come archiviazione o rete dopo la replica, è necessario disabilitare la replica e riabilitarla per la macchina virtuale.
**Replica di macchine virtuali di Azure da una sottoscrizione a un'altra per il ripristino di emergenza** | Supportate nello stesso tenant di Azure Active Directory. Non supportata per le macchine virtuali classiche.
**Eseguire la migrazione di macchine virtuali tra aree all'interno dei cluster geografici supportati (all'interno e tra sottoscrizioni)** | Supportato nello stesso tenant di Azure Active Directory per le macchine virtuali con "Modello di distribuzione Resource manager". Non supportato per le macchine virtuali con "Modello di distribuzione classica".
**Migrazione di macchine virtuali all'interno della stessa area** | Non supportati.


## <a name="support-for-replicated-machine-os-versions"></a>Supporto per le versioni dei sistemi operativi dei computer replicati

Il supporto è applicabile a qualsiasi carico di lavoro in esecuzione nel sistema operativo indicato.

#### <a name="windows"></a>Windows

- Windows Server 2016 (Server Core, Server con Esperienza desktop)*
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2 con almeno SP1

>[!NOTE]
>
> \*Windows Server 2016 Nano Server non è supportato.

#### <a name="linux"></a>Linux

- Red Hat Enterprise Linux 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5   
- CentOS 6.5, 6.6, 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3,7.4, 7.5
- Server Ubuntu 14.04 LTS[ (versioni del kernel supportate)](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)
- Server Ubuntu 16.04 LTS[ (versioni del kernel supportate)](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)
- Debian 7 [ (versioni del kernel supportate)](#supported-debian-kernel-versions-for-azure-virtual-machines)
- Debian 8 [ (versioni del kernel supportate)](#supported-debian-kernel-versions-for-azure-virtual-machines)
- SUSE Linux Enterprise Server 12 SP1, SP2, SP3 [ (versioni del kernel supportate)](#supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines)
- SUSE Linux Enterprise Server 11 SP3
- SUSE Linux Enterprise Server 11 SP4
- Oracle Linux 6.4, 6.5, 6.6, 6.7 che esegue il kernel compatibile Red Hat o Unbreakable Enterprise Kernel versione 3 (UEK3)

(L'aggiornamento dei computer di replica da SLES 11 SP3 a SLES 11 SP4 non è supportato. Se un computer replicato è stato aggiornato da 11SP3 SLES a SLES 11 SP4, è necessario disabilitare la replica e proteggere di nuovo il computer dopo l'aggiornamento.)

>[!NOTE]
>
> Per i server Ubuntu che usano l'accesso e l'autenticazione basati su password e che usano il pacchetto cloud-init per configurare le macchine virtuali nel cloud, è possibile che l'accesso basato su password venga disabilitato in caso di failover, a seconda della configurazione di cloudinit. L'accesso basato su password può essere riabilitato nella macchina virtuale reimpostando la password dal menu Impostazioni (nella sezione SUPPORTO + RISOLUZIONE DEI PROBLEMI) di tale macchina virtuale con failover nel portale di Azure.

### <a name="supported-ubuntu-kernel-versions-for-azure-virtual-machines"></a>Versioni del kernel Ubuntu supportate per macchine virtuali di Azure

**Versione** | **Versione del servizio Mobility** | **Versione del kernel** |
--- | --- | --- |
14.04 LTS | 9.19 | Da 3.13.0-24 generica a 3.13.0-153 generica<br/>Da 3.16.0-25 generica a 3.16.0-77 generica<br/>Da 3.19.0-18 generica a 3.19.0-80 generica<br/>Da 4.2.0-18 generica a 4.2.0-42 generica<br/>Da 4.4.0-21 generica a 4.4.0-131 generica |
14.04 LTS | 9.18 | Da 3.13.0-24 generica a 3.13.0-151 generica<br/>Da 3.16.0-25 generica a 3.16.0-77 generica<br/>Da 3.19.0-18 generica a 3.19.0-80 generica<br/>Da 4.2.0-18 generica a 4.2.0-42 generica<br/>Da 4.4.0-21 generica a 4.4.0-128 generica |
14.04 LTS | 9.17 | Da 3.13.0-24 generica a 3.13.0-147 generica<br/>Da 3.16.0-25 generica a 3.16.0-77 generica<br/>Da 3.19.0-18 generica a 3.19.0-80 generica<br/>Da 4.2.0-18 generica a 4.2.0-42 generica<br/>Da 4.4.0-21 generica a 4.4.0-124 generica |
14.04 LTS | 9.16 | Da 3.13.0-24 generica a 3.13.0-144 generica<br/>Da 3.16.0-25 generica a 3.16.0-77 generica<br/>Da 3.19.0-18 generica a 3.19.0-80 generica<br/>Da 4.2.0-18 generica a 4.2.0-42 generica<br/>Da 4.4.0-21 generica a 4.4.0-119 generica |
|||
16.04 LTS | 9.19 | Da 4.4.0-21 generica a 4.4.0-131 generica<br/>Da 4.8.0-34 generica a 4.8.0-58 generica<br/>Da 4.10.0-14 generica a 4.10.0-42 generica<br/>Da 4.11.0-13 generica a 4.11.0-14 generica<br/>Da 4.13.0-16 generica a 4.13.0-45 generica<br/>Da 4.15.0-13 generica a 4.15.0-30 generica<br/>Da 4.11.0-1009 Azure a 4.11.0-1016 Azure<br/>Da 4.13.0-1005 Azure a 4.13.0-1018 Azure <br/>Da 4.15.0-1012 Azure a 4.15.0-1019 Azure|
16.04 LTS | 9.18 | Da 4.4.0-21 generica a 4.4.0-128 generica<br/>Da 4.8.0-34 generica a 4.8.0-58 generica<br/>Da 4.10.0-14 generica a 4.10.0-42 generica<br/>Da 4.11.0-13 generica a 4.11.0-14 generica<br/>Da 4.13.0-16 generica a 4.13.0-45 generica<br/>Da 4.11.0-1009 Azure a 4.11.0-1016 Azure<br/>Da 4.13.0-1005 Azure a 4.13.0-1018 Azure |
16.04 LTS | 9.17 | Da 4.4.0-21 generica a 4.4.0-124 generica<br/>Da 4.8.0-34 generica a 4.8.0-58 generica<br/>Da 4.10.0-14 generica a 4.10.0-42 generica<br/>Da 4.11.0-13 generica a 4.11.0-14 generica<br/>Da 4.13.0-16 generica a 4.13.0-41 generica<br/>Da 4.11.0-1009 Azure a 4.11.0-1016 Azure<br/>Da 4.13.0-1005 Azure a 4.13.0-1016 Azure |
16.04 LTS | 9.16 | Da 4.4.0-21 generica a 4.4.0-119 generica<br/>Da 4.8.0-34 generica a 4.8.0-58 generica<br/>Da 4.10.0-14 generica a 4.10.0-42 generica<br/>Da 4.11.0-13 generica a 4.11.0-14 generica<br/>Da 4.13.0-16 generica a 4.13.0-38 generica<br/>Da 4.11.0-1009 Azure a 4.11.0-1016 Azure<br/>Da 4.13.0-1005 Azure a 4.13.0-1012 Azure |


### <a name="supported-debian-kernel-versions-for-azure-virtual-machines"></a>Versioni del kernel Debian supportate per macchine virtuali di Azure

**Versione** | **Versione del servizio Mobility** | **Versione del kernel** |
--- | --- | --- |
Debian 7 | 9.17, 9.18, 9.19 | Da 3.2.0-4-amd64 a 3.2.0-6-amd64, 3.16.0-0.bpo.4-amd64 |
Debian 7 | 9.16 | Da 3.2.0-4-amd64 a 3.2.0-5-amd64, 3.16.0-0.bpo.4-amd64 |
|||
Debian 8 | 9.19 | Da 3.16.0-4-amd64 a 3.16.0-6-amd64, da 4.9.0-0.bpo.4-amd64 a 4.9.0-0.bpo.7-amd64 |
Debian 8 | 9.17, 9.18 | Da 3.16.0-4-amd64 a 3.16.0-6-amd64, da 4.9.0-0.bpo.4-amd64 a 4.9.0-0.bpo.6-amd64 |
Debian 8 | 9.16 | Da 3.16.0-4-amd64 a 3.16.0-5-amd64, da 4.9.0-0.bpo.4-amd64 a 4.9.0-0.bpo.5-amd64 |

### <a name="supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines"></a>Versioni del kernel SUSE Linux Enterprise Server 12 supportate per macchine virtuali di Azure

**Versione** | **Versione del servizio Mobility** | **Versione del kernel** |
--- | --- | --- |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3) | 9.19 | Da 3.12.49-11 SP1 predefinita a 3.12.74-60.64.40 predefinita</br></br> Da 3.12.74-60.64.45 SP1 (LTSS) predefinita a 3.12.74-60.64.93 predefinita</br></br> Da 4.4.21-69 SP2 predefinita a 4.4.120-92.70 predefinita</br></br>Da 4.4.121-92.73 SP2(LTSS) predefinita a 4.4.121-92.80 predefinita</br></br>Da 4.4.73-5 SP3 predefinita a 4.4.140-94.42 predefinita |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3) | 9.18 | Da 3.12.49-11 SP1 predefinita a 3.12.74-60.64.40 predefinita</br></br> Da 3.12.74-60.64.45 SP1 (LTSS) predefinita a 3.12.74-60.64.93 predefinita</br></br> Da 4.4.21-69 SP2 predefinita a 4.4.120-92.70 predefinita</br></br>Da 4.4.121-92.73 SP2(LTSS) predefinita a 4.4.121-92.80 predefinita</br></br>Da 4.4.73-5 SP3 predefinita a 4.4.138-94.39 predefinita |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3) | 9.17 | Da 3.12.49-11 SP1 predefinita a 3.12.74-60.64.40 predefinita</br></br> Da 3.12.74-60.64.45 SP1 (LTSS) predefinita a 3.12.74-60.64.88 predefinita</br></br> Da 4.4.21-69 SP2 predefinita a 4.4.120-92.70 predefinita</br></br>4.4.121-92.73 SP2 (LTSS) predefinita</br></br>Da 4.4.73-5 SP3 predefinita a 4.4.126-94.22 predefinita |

## <a name="supported-file-systems-and-guest-storage-configurations-on-azure-virtual-machines-running-linux-os"></a>File system e configurazioni di archiviazione guest supportati nelle macchine virtuali di Azure che eseguono il sistema operativo Linux

* File system: ext3, ext4, ReiserFS (solo Suse Linux Enterprise Server), XFS
* Gestore volumi: LVM2
* Software con percorsi multipli: mapper dispositivi

## <a name="region-support"></a>Supporto di area

È possibile eseguire la replica e il ripristino di macchine virtuali tra due aree qualsiasi all'interno dello stesso cluster geografico.

**Cluster geografico** | **Aree di Azure**
-- | --
America | Canada orientale, Canada centrale, Stati Uniti centro-meridionali, Stati Uniti centro-occidentali, Stati Uniti orientali, Stati Uniti orientali 2, Stati Uniti occidentali, Stati Uniti occidentali 2, Stati Uniti centrali, Stati Uniti centro-settentrionali
Europa | Regno Unito occidentale, Regno Unito meridionale, Europa settentrionale,Europa occidentale, Francia centrale, Francia meridionale
Asia | India meridionale, India centrale, Asia sud-orientale, Asia orientale, Giappone orientale, Giappone occidentale, Corea del Sud centrale, Corea del Sud meridionale
Australia   | Australia orientale, Australia sud-orientale, Australia centrale, Australia centrale 2
Azure Government    | US GOV Virginia, US GOV Iowa, US GOV Arizona, US GOV Texas, US DOD East, US DOD Central
Germania | Germania centrale, Germania nord-orientale
Cina | Cina orientale, Cina settentrionale

>[!NOTE]
>
> Per il Brasile meridionale, è possibile eseguire la replica e il failover solo in una delle aree di Stati Uniti centro-meridionali, Stati Uniti centro-occidentali, Stati Uniti orientali, Stati Uniti orientali 2, Stati Uniti occidentali, Stati Uniti occidentali 2 e Stati Uniti centro-settentrionali e failback.

## <a name="support-for-vmdisk-management"></a>Supporto della macchina virtuale e del disco

**Azione** | **Dettagli**
-- | ---
Ridimensionamento del disco nella macchina virtuale replicata | Supportato
Aggiunta del disco alla macchina virtuale replicata | Non supportati. È necessario disabilitare la replica per la macchina virtuale, aggiungere il disco e quindi riabilitare nuovamente la replica.


## <a name="support-for-compute-configuration"></a>Supporto per la configurazione del servizio di calcolo

**Configurazione** | **Supportato/Non supportato** | **Osservazioni:**
--- | --- | ---
Dimensione | Macchine virtuali di Azure di qualsiasi dimensione con almeno 2 core CPU e 1 GB di RAM | Vedere [Dimensioni delle macchine virtuali di Azure](../virtual-machines/windows/sizes.md)
Set di disponibilità | Supportato | Se si usa l'opzione predefinita durante il passaggio 'Abilita replica' sul portale, il set di disponibilità viene creato automaticamente in base alla configurazione dell'area di origine. È possibile modificare la disponibilità di destinazione impostata in 'Elemento replicato > Impostazioni > Calcolo e rete > Set di disponibilità' in qualsiasi momento.
Zone di disponibilità | Non supportate | Le macchine virtuali distribuite nelle zone di disponibilità non sono attualmente supportate.
Macchine virtuali con vantaggio Hybrid Use (HUB, Hybrid Use Benefit) | Supportato | Se la macchina virtuale di origine dispone di una licenza HUB abilitata, anche la macchina virtuale di failover o il failover di test userà la licenza HUB.
set di scalabilità di macchine virtuali | Non supportate |
Immagini della raccolta di Azure - Pubblicate da Microsoft | Supportato | Supportato purché la macchina virtuale sia eseguita in un sistema operativo supportato da Site Recovery
Immagini della raccolta di Azure - Pubblicate da terze parti | Supportato | Supportato purché la macchina virtuale venga eseguita in un sistema operativo supportato da Site Recovery.
Immagini personalizzate - Pubblicate da terze parti | Supportato | Supportato purché la macchina virtuale venga eseguita in un sistema operativo supportato da Site Recovery.
Macchine virtuali migrate tramite Site Recovery | Supportato | Se si tratta di una macchina fisica/VMware migrata in Azure tramite Site Recovery, è necessario disinstallare la versione precedente del servizio di mobilità e riavviare il computer prima di eseguirne la replica in un'altra area di Azure.

## <a name="support-for-storage-configuration"></a>Supporto per la configurazione dell'archiviazione

**Configurazione** | **Supportato/Non supportato** | **Osservazioni:**
--- | --- | ---
Dimensioni massime del disco del sistema operativo | 2048 GB | Vedere [Dischi usati dalle VM.](../virtual-machines/windows/about-disks-and-vhds.md#disks-used-by-vms)
Dimensioni massime del disco dati | 4095 GB | Vedere [Dischi usati dalle VM.](../virtual-machines/windows/about-disks-and-vhds.md#disks-used-by-vms)
Numero di dischi dati | Fino a 64 a seconda del numero massimo di dischi supportato in base alle specifiche dimensioni della macchina virtuale di Azure | Vedere [Dimensioni delle macchine virtuali di Azure](../virtual-machines/windows/sizes.md)
Disco temporaneo | Sempre escluso dalla replica | Il disco temporaneo è sempre escluso dalla replica. Non è opportuno inserire dati persistenti su un disco temporaneo in base alle indicazioni di Azure. Vedere [Disco temporaneo sulle macchine virtuali di Azure](../virtual-machines/windows/about-disks-and-vhds.md#temporary-disk) per ulteriori dettagli.
Frequenza di modifica dei dati sul disco | Un massimo di 10 MBps per disco per l'archiviazione Premium e di 2 MBps per disco per l'archiviazione Standard | Se la frequenza media di modifica dei dati nel disco supera costantemente 10 MBps (Premium) e 2 MBps (Standard), la replica non viene aggiornata. Se invece si tratta di un picco di dati occasionale e la frequenza di modifica dei dati supera i 10 MBps (Premium) e 2 MBps (Standard) solo per un breve intervallo, la replica viene aggiornata. In questo caso, potrebbero verificarsi punti di ripristino leggermente ritardati.
Dischi su account di archiviazione standard | Supportato |
Dischi su account di archiviazione premium | Supportato | Se una macchina virtuale dispone di dischi distribuiti tra account di archiviazione standard e premium, è possibile selezionare un account di archiviazione di destinazione diverso per ogni disco per assicurarsi di avere la stessa configurazione di archiviazione nell'area di destinazione
Dischi gestiti standard | Supportato nelle aree di Azure in cui è supportato Azure Site Recovery. |  
Dischi gestiti premium | Supportato nelle aree di Azure in cui è supportato Azure Site Recovery. |
Spazi di archiviazione | Supportato |         
Crittografia per dati inattivi (SSE) | Supportato | La crittografia per dati inattivi (SSE) è l'impostazione predefinita per gli account di archiviazione.   
Crittografia dischi di Azure (ADE) per sistema operativo Windows | Le macchine virtuali abilitate per [la crittografia con l'app di Azure AD](https://aka.ms/ade-aad-app) sono supportate |
Crittografia dischi di Azure (ADE) per sistema operativo Linux | Non supportate |
Aggiunta/rimozione a caldo disco | Non supportate | Se si aggiungono o rimuovono dischi dati dalla macchina virtuale, è necessario disabilitare la replica e abilitarla nuovamente per la macchina virtuale.
Esclusione disco | Non supportate|   Il disco temporaneo è escluso per impostazione predefinita.
Spazi di archiviazione diretta  | Non supportate|
File server di scalabilità orizzontale  | Non supportate|
Archiviazione con ridondanza locale | Supportato |
Archiviazione con ridondanza geografica | Supportato |
RA-GRS | Supportato |
ZRS | Non supportate |  
Archiviazione ad accesso frequente e sporadico | Non supportate | I dischi delle macchine virtuali non sono supportati per l'archiviazione ad accesso frequente e sporadico
Firewall di Archiviazione di Azure per reti virtuali  | No  | L'accesso alle reti virtuali di Azure specifiche negli account di archiviazione della cache usati per archiviare i dati replicati non è supportato.
Account di archiviazione V2 generico (livelli di accesso frequente e sporadico) | No  | Aumento sostanziale dei costi delle transazioni rispetto agli account di archiviazione V1 generici

>[!IMPORTANT]
> Per evitare problemi di prestazioni, assicurarsi di osservare gli obiettivi di scalabilità e prestazioni per il disco della macchina virtuale per le macchine virtuali [Linux](../virtual-machines/linux/disk-scalability-targets.md) o [Windows](../virtual-machines/windows/disk-scalability-targets.md). Se si seguono le impostazioni predefinite, Site Recovery crea gli account di archiviazione e i dischi necessari in base alla configurazione di origine. Se si personalizzano e si selezionano impostazioni specifiche, assicurarsi di rispettare gli obiettivi di scalabilità e prestazioni per i dischi delle macchine virtuali.

## <a name="support-for-network-configuration"></a>Supporto per la configurazione di rete
**Configurazione** | **Supportato/Non supportato** | **Osservazioni:**
--- | --- | ---
Interfaccia di rete (NIC) | Fino al numero massimo di schede di rete supportato in base alle specifiche dimensioni della macchina virtuale di Azure | Le schede di rete vengono create quando viene creata la macchina virtuale nell'ambito dell'operazione di failover o del failover di test. Il numero di schede di rete sulla macchina virtuale di failover dipende dal numero di schede di rete di cui dispone la macchina virtuale quando viene abilitata la replica. Eventuali aggiunte o rimozioni di schede di rete dopo aver abilitato la replica non influiscono sul numero di schede di rete sulla macchina virtuale di failover.
Servizio di bilanciamento del carico Internet | Supportato | È necessario associare il servizio di bilanciamento del carico preconfigurato tramite uno script di automazione di Azure in un piano di ripristino.
Servizio di bilanciamento del carico interno | Supportato | È necessario associare il servizio di bilanciamento del carico preconfigurato tramite uno script di automazione di Azure in un piano di ripristino.
IP pubblico| Supportato | È necessario associare un IP pubblico esistente alla scheda di rete o crearne uno e associarlo alla scheda di rete tramite uno script di automazione di Azure in un piano di ripristino.
Gruppo di sicurezza di rete (NSG) sulla scheda di rete (Resource Manager)| Supportato | È necessario associare il gruppo di sicurezza di rete alla scheda di rete tramite uno script di automazione di Azure in un piano di ripristino.  
Gruppo di sicurezza di rete (NSG) sulla subnet (Resource Manager e classica)| Supportato | È necessario associare il gruppo di sicurezza di rete alla subnet tramite uno script di automazione di Azure in un piano di ripristino.
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
Endpoint del servizio Rete virtuale | Supportato | I firewall di Archiviazione Azure per le reti virtuali non sono supportati. L'accesso alle reti virtuali di Azure specifiche negli account di archiviazione della cache usati per archiviare i dati replicati non è supportato.
Rete accelerata | Supportato | La rete accelerata deve essere abilitata su una macchina virtuale di origine. [Altre informazioni](azure-vm-disaster-recovery-with-accelerated-networking.md)


## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sulle [indicazioni per la connettività di rete per la replica delle macchine virtuali di Azure](site-recovery-azure-to-azure-networking-guidance.md)
- Iniziare a proteggere i carichi di lavoro [replicando le macchine virtuali di Azure](site-recovery-azure-to-azure.md)
