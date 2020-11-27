---
title: Tipi di archiviazione di Azure per carichi di lavoro SAP
description: Pianificazione dei tipi di archiviazione di Azure per carichi di lavoro SAP
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: d7c59cc1-b2d0-4d90-9126-628f9c7a5538
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 11/26/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 325e28b9fde349fc4bf01d2b130bee0be0684962
ms.sourcegitcommit: 5e2f5efba1957ba40bd951c3dcad42f4a00734ff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/27/2020
ms.locfileid: "96299599"
---
# <a name="azure-storage-types-for-sap-workload"></a>Tipi di Archiviazione di Azure per carichi di lavoro SAP
Azure dispone di numerosi tipi di archiviazione che variano notevolmente in funzionalità, velocità effettiva, latenza e prezzi. Alcuni tipi di archiviazione non sono o di utilizzo limitato per gli scenari SAP. Mentre alcuni tipi di archiviazione di Azure sono particolarmente adatti o ottimizzati per scenari specifici del carico di lavoro SAP. In particolare per SAP HANA, alcuni tipi di archiviazione di Azure sono stati certificati per l'utilizzo con SAP HANA. In questo documento vengono esaminati i diversi tipi di archiviazione e ne viene descritta la funzionalità e l'usabilità con i carichi di lavoro SAP e i componenti SAP.

Evidenziare le unità usate in questo articolo. I fornitori di cloud pubblici sono passati a usare GiB ([gibibyte](https://en.wikipedia.org/wiki/Gibibyte)) o TIB ([tebibyte](https://en.wikipedia.org/wiki/Tebibyte) come unità di dimensioni, anziché gigabyte o terabyte. Quindi, tutte le premi e la documentazione di Azure usano tali unità.  In tutto il documento si fa riferimento a queste unità di dimensioni di unità MiB, GiB e TiB in modo esclusivo. Potrebbe essere necessario pianificare con MB, GB e TB. Quindi, tenere presente alcune piccole differenze nei calcoli se è necessario ridimensionare la velocità effettiva di 400 MiB/sec, anziché una velocità effettiva di 250 MiB/sec.

## <a name="microsoft-azure-storage-resiliency"></a>Resilienza di Archiviazione di Microsoft Azure

Microsoft Azure archiviazione di HDD Standard, SDD Standard, archiviazione Premium di Azure e ultra disk mantiene il disco rigido virtuale di base (con il sistema operativo) e i dischi dati o i VHD collegati alla VM in tre copie in tre diversi nodi di archiviazione. Il failover a un'altra replica e il seeding di una nuova replica in caso di errore di un nodo di archiviazione sono trasparenti. In seguito a questa ridondanza, **non** è necessario usare alcun tipo di livello di ridondanza dell'archiviazione tra più dischi di Azure. Questo approccio è denominato archiviazione con ridondanza locale (LRS). CON ridondanza locale è il valore predefinito per questi tipi di archiviazione in Azure. [Azure NetApp files](https://azure.microsoft.com/services/netapp/) garantisce una ridondanza sufficiente per ottenere gli stessi contratti di Service con altri archivi nativi di Azure.

Sono disponibili diversi metodi di ridondanza, descritti nell'articolo replica di archiviazione di [Azure](../../../storage/common/storage-redundancy.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json) che si applicano ad alcuni dei diversi tipi di archiviazione offerti da Azure. 

Tenere inoltre presente che i diversi tipi di archiviazione di Azure influiscono sui contratti di contratto di disponibilità di una singola macchina virtuale, come rilasciati [per le macchine virtuali](https://azure.microsoft.com/support/legal/sla/virtual-machines)

### <a name="azure-managed-disks"></a>Azure Managed Disks

I dischi gestiti sono un tipo di risorsa in Azure Resource Manager che è possibile usare al posto dei dischi rigidi virtuali archiviati negli account di archiviazione di Azure. Managed Disks si allineano automaticamente al [set di disponibilità] [Virtual-Machines-Manage-availability] della macchina virtuale a cui sono collegati e pertanto aumentano la disponibilità della macchina virtuale e dei servizi in esecuzione nella macchina virtuale. Per altre informazioni, leggere l’[articolo introduttivo](../../managed-disks-overview.md).

Per quanto riguarda la resilienza, in questo esempio viene illustrato il vantaggio dei dischi gestiti:

- Si distribuiscono due macchine virtuali DBMS per il sistema SAP in un set di disponibilità di Azure 
- Quando Azure distribuisce le VM, il disco con l'immagine del sistema operativo verrà inserito in un cluster di archiviazione diverso. In questo modo si evita che entrambe le macchine virtuali siano interessate da un problema di un singolo cluster di archiviazione di Azure
- Quando si creano nuovi dischi gestiti assegnati a queste macchine virtuali per archiviare i file di dati e di log del database, questi nuovi dischi per le due VM vengono distribuiti anche in cluster di archiviazione distinti, quindi nessuno dei dischi della prima VM condivide i cluster di archiviazione con i dischi della seconda macchina virtuale

La distribuzione senza dischi gestiti negli account di archiviazione definiti dal cliente, l'allocazione dei dischi è arbitraria e non è in grado di comprendere il fatto che le macchine virtuali vengono distribuite in un AvSet per scopi di resilienza.

> [!NOTE]
> Per questo motivo e per diversi altri miglioramenti disponibili esclusivamente tramite Managed disks, è necessario che le nuove distribuzioni di macchine virtuali che usano l'archiviazione a blocchi di Azure per i dischi (tutti i dati di archiviazione di Azure ad eccezione di Azure NetApp Files) debbano usare Azure Managed disks per i dischi VHD/del sistema operativo di base, dischi dati che contengono file di database SAP. Indipendentemente dal fatto che le macchine virtuali vengano distribuite tramite il set di disponibilità, in zone di disponibilità o indipendentemente dai set e dalle zone. I dischi usati per l'archiviazione dei backup non devono necessariamente essere dischi gestiti.

> [!NOTE]
> Azure Managed Disks offre solo la ridondanza locale (con ridondanza locale). 


## <a name="storage-scenarios-with-sap-workloads"></a>Scenari di archiviazione con carichi di lavoro SAP
Lo spazio di archiviazione permanente è necessario nel carico di lavoro SAP in vari componenti dello stack distribuito in Azure. Questi scenari sono elencati come minimo:

- Persistente il VHD di base della macchina virtuale che include il sistema operativo e altro software installato nel disco. Questo disco/VHD è la radice della VM. Tutte le modifiche apportate ad esso devono essere rese permanente. Quindi, la volta successiva che si arresta e si riavvia la macchina virtuale, tutte le modifiche apportate prima di esistere ancora. In particolare nei casi in cui la macchina virtuale viene distribuita da Azure in un altro host rispetto a quando era in esecuzione originariamente
- Dischi dati salvati in permanenza. Questi dischi sono dischi rigidi virtuali collegati per archiviare i dati dell'applicazione in. I dati dell'applicazione potrebbero essere dati e file di log/rollforward di un database, file di backup o installazioni software. Indica qualsiasi disco oltre il disco rigido virtuale di base che include il sistema operativo
- Condivisioni file o dischi condivisi che contengono la directory di trasporto globale per NetWeaver o S/4HANA. Il contenuto di tali condivisioni viene utilizzato dal software in esecuzione in più macchine virtuali o viene utilizzato per compilare scenari di cluster di failover a disponibilità elevata
- Directory/sapmnt o condivisioni file comuni per processi EDI o simili. Il contenuto di tali condivisioni viene utilizzato dal software in esecuzione in più macchine virtuali o viene utilizzato per compilare scenari di cluster di failover a disponibilità elevata

Nelle prossime sezioni vengono illustrati i diversi tipi di archiviazione di Azure e la relativa usabilità per il carico di lavoro SAP che si applicano ai quattro scenari precedenti. Una categorizzazione generale del modo in cui devono essere usati i diversi tipi di archiviazione di Azure è documentata nell'articolo [quali tipi di dischi sono disponibili in Azure?](../../disks-types.md) I consigli per l'uso dei diversi tipi di archiviazione di Azure per il carico di lavoro SAP non saranno molto diversi.

Per le restrizioni del supporto per i tipi di archiviazione di Azure per SAP NetWeaver/livello applicazione di S/4HANA, vedere la [Nota di supporto sap 2015553](https://launchpad.support.sap.com/#/notes/2015553) per SAP Hana tipi di archiviazione di Azure certificati e supportati leggere l'articolo [SAP Hana configurazioni di archiviazione delle macchine virtuali di Azure](./hana-vm-operations-storage.md).

Le sezioni che descrivono i diversi tipi di archiviazione di Azure offrono più informazioni di base sulle restrizioni e le possibilità di utilizzo dell'archiviazione di SAP supportata. 

## <a name="storage-recommendations-for-sap-storage-scenarios"></a>Consigli sull'archiviazione per gli scenari di archiviazione SAP
Prima di entrare nei dettagli, verranno presentati il riepilogo e le raccomandazioni già all'inizio del documento. Mentre i dettagli per i particolari tipi di archiviazione di Azure seguono questa sezione del documento. Il riepilogo delle raccomandazioni di archiviazione per gli scenari di archiviazione SAP in una tabella ha un aspetto simile al seguente:

| Scenario di utilizzo | HDD Standard | SSD Standard | Archiviazione Premium | Disco Ultra | Azure NetApp Files |
| --- | --- | --- | --- | --- | --- |
| Disco del sistema operativo | non adatto |  adatto limitato (non prod) | Consigliato | non possibile | non possibile |
| Directory trasporto globale | non supportato | non supportato | Consigliato | Consigliato | Consigliato |
| /sapmnt | non adatto | adatto limitato (non prod) | Consigliato | Consigliato | Consigliato |
| Volume di dati DBMS SAP HANA le famiglie di VM M/Mv2 | non supportato | non supportato | Consigliato | Consigliato | consigliato<sup>2</sup> |
| Volume di log DBMS SAP HANA famiglie di VM M/Mv2 | non supportato | non supportato | consigliato<sup>1</sup> | Consigliato | consigliato<sup>2</sup> | 
| Volume di dati DBMS SAP HANA famiglie di macchine virtuali Esv3/Edsv4 | non supportato | non supportato | Consigliato | Consigliato | consigliato<sup>2</sup> |
| Volume di log DBMS SAP HANA famiglie di macchine virtuali Esv3/Edsv4 | non supportato | non supportato | non supportato | Consigliato | consigliato<sup>2</sup> | 
| Volume di dati DBMS non HANA | Non supportato | adatto limitato (non prod) | Consigliato | Consigliato | Non supportato |
| Volume di log DBMS non-HANA M/Mv2 VM famiglie | Non supportato | adatto limitato (non prod) | consigliato<sup>1</sup> | Consigliato | Non supportato |
| Volume di log DBMS non-HANA famiglie di VM non M/Mv2 | Non supportato | adatto limitato (non prod) | adatto per carichi di lavoro fino a medium | Consigliato | Non supportato |


<sup>1</sup> con l'uso di [acceleratore di scrittura di Azure](../../how-to-enable-write-accelerator.md) per le famiglie di macchine virtuali M/Mv2 per i volumi di log di log/rollforward <sup>2</sup> con e è necessario/Hana/data e/Hana/log in e 

Caratteristiche che è possibile prevedere dall'elenco dei diversi tipi di archiviazione, ad esempio:

| Scenario di utilizzo | HDD Standard | SSD Standard | Archiviazione Premium | Disco Ultra | Azure NetApp Files |
| --- | --- | --- | --- | --- | --- |
| SLA velocità effettiva/IOPS | No | No | sì | sì | sì |
| Letture latenza | high | da medio a alto | low | millisecondi | millisecondi |
| Scritture latenza | high | da medio a alto  | bassa (sub-millisecond<sup>1</sup>) | millisecondi | millisecondi |
| HANA supportato | No | No | Sì<sup>1</sup> | sì | sì |
| Snapshot del disco possibili | sì | sì | sì | No | sì |
| Allocazione di dischi in cluster di archiviazione diversi quando si usano i set di disponibilità | tramite Managed Disks | tramite Managed Disks | tramite Managed Disks | tipo di disco non supportato con le macchine virtuali distribuite tramite i set di disponibilità | n.<sup>3</sup> |
| Allineato con zone di disponibilità | sì | sì | sì | sì | richiede il coinvolgimento di Microsoft |
| Ridondanza di zona | non per Managed Disks | non per Managed Disks | non per Managed Disks | No | No |
| Ridondanza geografica | non per Managed Disks | non per Managed Disks | No | No | No |


<sup>1</sup> con utilizzo del [acceleratore di scrittura di Azure](../../how-to-enable-write-accelerator.md) per le famiglie di macchine virtuali M/Mv2 per i volumi di log di log/rollforward

<sup>2</sup> i costi dipendono da IOPS e velocità effettiva con provisioning

<sup>3</sup> la creazione di pool di capacità e diversi non garantisce la distribuzione dei pool di capacità in unità di archiviazione diverse


> [!IMPORTANT]
> Per ottenere una latenza di I/O inferiore a 1 millisecondo usando Azure NetApp Files (e), è necessario collaborare con Microsoft per organizzare la posizione corretta tra le macchine virtuali e le condivisioni NFS in base a e. Fino a questo punto non è presente alcun meccanismo che fornisce una prossimità automatica tra una macchina virtuale distribuita e i volumi NFS ospitati in e. Data la configurazione diversa delle diverse aree di Azure, la latenza di rete aggiunta potrebbe comportare la latenza di I/O oltre 1 millisecondi se la macchina virtuale e la condivisione NFS non sono allocate in prossimità.


> [!IMPORTANT]
> Nessuno dei dischi gestiti basati sull'archiviazione blocchi di Azure attualmente offerti o Azure NetApp Files offre una ridondanza geografica o di zona. Di conseguenza, è necessario assicurarsi che le architetture di disponibilità elevata e di ripristino di emergenza non si basano su qualsiasi tipo di replica di archiviazione nativa di Azure per le condivisioni NFS o SMB.


## <a name="azure-premium-storage"></a>Archiviazione Premium di Azure
L'archiviazione SSD Premium di Azure è stata introdotta con l'obiettivo di fornire:

* Bassa latenza di I/O
* Contratti di contratto per IOPS e velocità effettiva
* Minor variabilità nella latenza di I/O

Questo tipo di archiviazione è destinato ai carichi di lavoro DBMS, al traffico di archiviazione che richiede una bassa latenza di millisecondi a singola cifra e ai contratti di licenza per IOPS e costo della velocità effettiva nel caso di archiviazione Premium di Azure non è il volume di dati effettivo archiviato in tali dischi, ma la categoria dimensioni di tale disco, indipendentemente dalla quantità di dati archiviati all'interno del disco. È anche possibile creare dischi in archiviazione Premium che non eseguono il mapping diretto alle categorie di dimensioni illustrate nell'articolo [SSD Premium](../../disks-types.md#premium-ssd). Le conclusioni di questo articolo sono:

- Lo spazio di archiviazione è organizzato in intervalli. Ad esempio, un disco nell'intervallo 513 GiB a 1024 GiB capacità condivide le stesse funzionalità e gli stessi costi mensili
- Le operazioni di i/o al secondo per GiB non vengono tracciate lineari nelle categorie di dimensioni. I dischi più piccoli sotto 32 GiB hanno una velocità di IOPS superiore per GiB. Per i dischi oltre 32 GiB a 1024 GiB, il tasso di IOPS per GiB è compreso tra 4-5 IOPS e GiB. Per dischi di dimensioni maggiori fino a 32.767 GiB, il tasso di IOPS per GiB è inferiore a 1
- La velocità effettiva di I/O per questa risorsa di archiviazione non è lineare con le dimensioni della categoria del disco. Per i dischi più piccoli, ad esempio la categoria tra 65 GiB e la capacità di 128 GiB, la velocità effettiva è intorno a 780KB/GiB. Mentre per i dischi di grandi dimensioni, ad esempio un disco GiB 32.767, la velocità effettiva è circa 28KB/GiB
- Non è possibile modificare i contratti di contratto di IOPS e velocità effettiva senza modificare la capacità del disco


La matrice di funzionalità per il carico di lavoro SAP è simile alla seguente:

| Funzionalità| Commento| Note/collegamenti | 
| --- | --- | --- | 
| VHD di base del sistema operativo | adatto | tutti i sistemi |
| Disco dati | adatto | tutti i sistemi, [specialmente per SAP Hana](../../how-to-enable-write-accelerator.md) |
| Directory di trasporto globale SAP | YES | [Supporto](https://launchpad.support.sap.com/#/notes/2015553) |
| Sapmnt SAP | adatto | tutti i sistemi |
| Archiviazione di backup | adatto | per l'archiviazione a breve termine dei backup |
| Condivisioni/disco condiviso | non disponibile | Necessita di file Premium di Azure o di terze parti |
| Resilienza | LRS | Non sono disponibili GRS o ZRS per i dischi |
| Latenza | da basso a medio | - |
| SLA IOPS | YES | - |
| IOPS lineare a capacità | semi lineare tra parentesi quadre  | [Prezzi del disco gestito](https://azure.microsoft.com/pricing/details/managed-disks/) |
| Numero massimo di operazioni di I/O al secondo per disco | 20.000 [dipendente dalla dimensione del disco](https://azure.microsoft.com/pricing/details/managed-disks/) | Prendere in considerazione anche i [limiti delle VM](../../sizes.md) |
| SLA velocità effettiva | YES | - |
| Velocità effettiva lineare a capacità | semi lineare tra parentesi quadre | [Prezzi del disco gestito](https://azure.microsoft.com/pricing/details/managed-disks/) |
| Certificazione HANA | YES | [appositamente per SAP HANA](../../how-to-enable-write-accelerator.md) |
| Snapshot del disco possibili | YES | - |
| Snapshot di VM di backup di Azure possibili | YES | ad eccezione di [acceleratore di scrittura](../../how-to-enable-write-accelerator.md) dischi memorizzati nella cache  |
| Costi | MEDIUM | - |

Archiviazione Premium di Azure non soddisfa SAP HANA indicatori KPI di latenza di archiviazione con i tipi di memorizzazione nella cache comuni offerti con archiviazione Premium di Azure. Per soddisfare gli indicatori KPI di latenza di archiviazione per SAP HANA scritture del log, è necessario usare la memorizzazione nella cache di acceleratore di scrittura di Azure, come descritto nell'articolo [abilitare acceleratore di scrittura](../../how-to-enable-write-accelerator.md). Azure acceleratore di scrittura avvantaggia tutti gli altri sistemi DBMS per le Scritture del log delle transazioni e le Scritture del log di ripristino. Pertanto, è consigliabile utilizzarlo in tutte le distribuzioni di SAP DBMS. Per SAP HANA, l'uso di acceleratore di scrittura di Azure in combinazione con archiviazione Premium di Azure è obbligatorio.



**Riepilogo:** Archiviazione Premium di Azure è uno dei tipi di archiviazione di Azure consigliati per il carico di lavoro SAP. Questa raccomandazione è valida per i sistemi di produzione e non di produzione. Archiviazione Premium di Azure è adatta per gestire i carichi di lavoro del database. L'uso di acceleratore di scrittura di Azure consentirà di migliorare sostanzialmente la latenza di scrittura in dischi Premium di Azure. Tuttavia, per i sistemi DBMS con velocità di IOPS e velocità effettiva elevate, è necessario eseguire l'overprovisioning della capacità di archiviazione oppure è necessario usare funzionalità come spazi di archiviazione di Windows o gestori di volumi logici in Linux per creare set di striping che consentono di ottenere la capacità desiderata da un lato, ma anche le operazioni di i/o al secondo e la velocità effettiva.


### <a name="azure-burst-functionality-for-premium-storage"></a>Funzionalità di Azure per archiviazione Premium
Per i dischi di archiviazione Premium di Azure di dimensioni minori o uguali a 512 GiB nella capacità, viene offerta la funzionalità di espansione. Il modo esatto in cui funziona l'espansione del disco è descritto nell'articolo relativo all'espansione del [disco](../../linux/disk-bursting.md). Quando si legge l'articolo, si comprende il concetto di accumulo di IOPS e velocità effettiva nei momenti in cui il carico di lavoro di I/O è inferiore al valore di IOPS nominale e alla velocità effettiva dei dischi (per informazioni dettagliate sulla velocità effettiva nominale, vedere [prezzi dei dischi gestiti](https://azure.microsoft.com/pricing/details/managed-disks/)). Si prevede di accumulare il Delta di IOPS e la velocità effettiva tra l'utilizzo corrente e i valori nominali del disco. I picchi sono limitati a un massimo di 30 minuti.

I casi ideali in cui è possibile pianificare questa funzionalità di espansione sono probabilmente i volumi o i dischi che contengono file di dati per il sistema DBMS diverso. Il carico di lavoro di I/O previsto rispetto a tali volumi, specialmente con i sistemi di piccole e medie dimensioni, dovrebbe avere un aspetto simile al seguente:

- Carico di lavoro di lettura da basso a moderato poiché i dati idealmente vengono memorizzati nella cache in memoria o come nel caso di HANA deve essere completamente in memoria
- Picchi di scrittura attivati da checkpoint del database o salvataggio emessi a intervalli regolari
- Carico di lavoro di backup che legge in un flusso continuo nei casi in cui i backup non vengono eseguiti tramite snapshot di archiviazione
- Per SAP HANA, caricare i dati in memoria dopo il riavvio di un'istanza

In particolare nei sistemi DBMS più piccoli, in cui il carico di lavoro gestisce solo poche centinaia di transazioni al secondo, una funzionalità di aumento delle prestazioni può essere utile anche per i dischi o i volumi che archiviano la transazione o il log di rollforward. Il carico di lavoro previsto rispetto a un disco o a un volume è simile al seguente:

- Scritture regolari sul disco che dipendono dal carico di lavoro e la natura del carico di lavoro, poiché ogni commit emesso dall'applicazione può attivare un'operazione di I/O
- Un carico di lavoro più elevato nella velocità effettiva per i casi di attività operative, ad esempio la creazione o la ricompilazione
- Lettura di picchi durante l'esecuzione di log delle transazioni o backup del log di rollforward


## <a name="azure-ultra-disk"></a>Disco Ultra di Azure
I dischi Ultra di Azure offrono una velocità effettiva elevata, un numero elevato di operazioni di I/O al secondo e archiviazione su disco con bassa latenza coerente per le macchine virtuali IaaS di Azure. Alcuni vantaggi aggiuntivi di ultra disks includono la possibilità di modificare dinamicamente i IOPS e la velocità effettiva del disco, insieme ai carichi di lavoro, senza dover riavviare le macchine virtuali (VM). I dischi Ultra sono adatti per carichi di lavoro a elevato utilizzo di dati, ad esempio il carico di lavoro DBMS SAP. I dischi Ultra possono essere usati solo come dischi dati e non possono essere usati come disco VHD di base che archivia il sistema operativo. Si consiglia l'utilizzo dell'archiviazione Premium di Azure come disco VHD basato su.

Quando si crea un disco Ultra, sono disponibili tre dimensioni che è possibile definire:

- Capacità del disco. Gli intervalli sono compresi tra 4 GiB e 65.536 GiB
- IOPS con provisioning per il disco. Per la capacità del disco si applicano valori massimi diversi. Per ulteriori informazioni, leggere l'articolo relativo all' [ultra disk](../../disks-types.md#ultra-disk)
- Larghezza di banda di archiviazione con provisioning. Viene applicata una larghezza di banda massima diversa a seconda della capacità del disco. Per ulteriori informazioni, leggere l'articolo relativo all' [ultra disk](../../disks-types.md#ultra-disk)

Il costo di un singolo disco è determinato dalle tre dimensioni che è possibile definire separatamente per i dischi specifici. 


La matrice di funzionalità per il carico di lavoro SAP è simile alla seguente:

| Funzionalità| Commento| Note/collegamenti | 
| --- | --- | --- | 
| VHD di base del sistema operativo | non funziona | - |
| Disco dati | adatto | tutti i sistemi  |
| Directory di trasporto globale SAP | YES | [Supporto](https://launchpad.support.sap.com/#/notes/2015553) |
| Sapmnt SAP | adatto | tutti i sistemi |
| Archiviazione di backup | adatto | per l'archiviazione a breve termine dei backup |
| Condivisioni/disco condiviso | non disponibile | Necessita di terze parti |
| Resilienza | LRS | Non sono disponibili GRS o ZRS per i dischi |
| Latenza | molto bassa | - |
| SLA IOPS | YES | - |
| IOPS lineare a capacità | semi lineare tra parentesi quadre  | [Prezzi del disco gestito](https://azure.microsoft.com/pricing/details/managed-disks/) |
| Numero massimo di operazioni di I/O al secondo per disco | da 1.200 a 160.000 | dipendente dalla capacità del disco |
| SLA velocità effettiva | YES | - |
| Velocità effettiva lineare a capacità | semi lineare tra parentesi quadre | [Prezzi del disco gestito](https://azure.microsoft.com/pricing/details/managed-disks/) |
| Certificazione HANA | YES | - |
| Snapshot del disco possibili | NO | - |
| Snapshot di VM di backup di Azure possibili | NO | - |
| Costi | Archiviazione Premium superiore | - |



**Riepilogo:** Azure ultra disks è uno spazio di archiviazione adatto con bassa latenza per tutti i tipi di carico di lavoro SAP. Fino a questo punto, il disco Ultra può essere usato solo in combinazioni con macchine virtuali distribuite tramite zone di disponibilità (distribuzione di zona). Il disco Ultra non supporta gli snapshot di archiviazione in questo momento. In contrario a tutti gli altri tipi di archiviazione, non è possibile usare ultra disk per il disco VHD di base. Il disco Ultra è ideale per i casi in cui il carico di lavoro di I/O fluttua molto e si vuole adattare la velocità effettiva di archiviazione distribuita o IOPS ai modelli del carico di lavoro di archiviazione anziché dimensionare il numero massimo di operazioni di I/O.


## <a name="azure-netapp-files-anf"></a>File di Azure NetApp (e)
[Azure NetApp files](https://azure.microsoft.com/services/netapp/) è il risultato di una collaborazione tra Microsoft e NetApp con l'obiettivo di offrire prestazioni elevate di Azure native NFS e SMB. L'accento è quello di fornire una larghezza di banda elevata e un'archiviazione a bassa latenza che consenta gli scenari di distribuzione DBMS e, nel tempo, abilitare le funzionalità operative tipiche dell'archiviazione NetApp anche tramite Azure. Le condivisioni NFS/SMB sono offerte in tre diversi livelli di servizio che differenziano la velocità effettiva di archiviazione e il prezzo. I livelli di servizio sono documentati nell'articolo [livelli di servizio per Azure NetApp files](../../../azure-netapp-files/azure-netapp-files-service-levels.md). Per i diversi tipi di carico di lavoro SAP, è consigliabile usare i livelli di servizio seguenti:

- Carico di lavoro SAP DBMS: prestazioni, idealmente ultra
- Condivisione SAPMNT: prestazioni, idealmente ultra
- Directory trasporto globale: prestazioni, idealmente ultra

> [!NOTE]
> La dimensione minima del provisioning è un'unità da 4 TiB denominata pool di capacità. Si creano quindi volumi da questo pool di capacità. Mentre il volume più piccolo che è possibile compilare è 100 GiB. È possibile espandere un pool di capacità in passaggi TiB. Per i prezzi, vedere l'articolo [Azure NetApp files prezzi](https://azure.microsoft.com/pricing/details/netapp/)

L'archiviazione e è attualmente supportata per diversi scenari di carico di lavoro SAP:

- Fornire condivisioni SMB o NFS per la directory di trasporto globale di SAP
- La condivisione sapmnt in scenari a disponibilità elevata, come descritto in:
    - [Disponibilità elevata per SAP NetWeaver in macchine virtuali di Azure in Windows con Azure NetApp Files (SMB) per le applicazioni SAP](./high-availability-guide-windows-netapp-files-smb.md)
    - [Disponibilità elevata per SAP NetWeaver su macchine virtuali di Azure su SUSE Linux Enterprise Server con Azure NetApp Files per applicazioni SAP](./high-availability-guide-suse-netapp-files.md)
    - [Disponibilità elevata di macchine virtuali di Azure per SAP NetWeaver in Red Hat Enterprise Linux con Azure NetApp Files per le applicazioni SAP](./high-availability-guide-rhel-netapp-files.md)
- SAP HANA le distribuzioni che usano le condivisioni NFS v 4.1 per i volumi/Hana/data e/Hana/log e/o i volumi NFS v 4.1 o NFS v3 per i volumi/Hana/Shared, come illustrato nell'articolo [SAP Hana configurazioni di archiviazione delle macchine virtuali di Azure](./hana-vm-operations-storage.md)

> [!NOTE]
> Non sono supportati altri carichi di lavoro DBMS per le condivisioni NFS o SMB basate su Azure NetApp Files. Verranno forniti aggiornamenti e modifiche se questa operazione verrà modificata.

Come già con archiviazione Premium di Azure, le dimensioni della velocità effettiva fissa o lineare per GB possono costituire un problema quando è necessario rispettare alcuni numeri minimi di velocità effettiva. Analogamente a quanto avviene per SAP HANA. Con e, questo problema può diventare più pronunciato rispetto al disco Premium di Azure. Nel caso di un disco Premium di Azure, è possibile usare diversi dischi di dimensioni inferiori con una velocità effettiva relativamente elevata per GiB ed eseguire lo striping su di essi, in modo da renderli convenienti e avere una velocità effettiva superiore a una capacità inferiore. Questo tipo di striping non funziona per le condivisioni NFS o SMB ospitate in e. Questa restrizione ha comportato la distribuzione di una sovraccapacità come la seguente:

- Per ottenere, ad esempio, una velocità effettiva di 250 MiB/sec in un volume NFS ospitato in e, è necessario distribuire la capacità di 1,95 TiB del livello di servizio ultra. 
- Per ottenere 400 MiB/sec, è necessario distribuire la capacità di 3,125 TiB. Tuttavia, potrebbe essere necessario il provisioning eccessivo della capacità per ottenere la velocità effettiva necessaria per il volume. Questo provisioning della capacità influisca sul prezzo di istanze di Hana più piccole. 
- Nello spazio in cui si usa NFS in e per la directory SAP/sapmnt, la capacità minima di 100 GiB a 150 GiB applicata da Azure NetApp Files è in genere molto alta. Tuttavia, l'esperienza dei clienti ha dimostrato che la velocità effettiva correlata di 12,8 MiB/sec (usando il livello di servizio Ultra) potrebbe non essere sufficiente e potrebbe avere un impatto negativo sulla stabilità del sistema SAP. In questi casi, i clienti potrebbero evitare problemi aumentando il volume del volume/sapmnt, in modo che venga fornita una maggiore velocità effettiva a tale volume.

La matrice di funzionalità per il carico di lavoro SAP è simile alla seguente:

| Funzionalità| Commento| Note/collegamenti | 
| --- | --- | --- | 
| VHD di base del sistema operativo | non funziona | - |
| Disco dati | adatto | Solo SAP HANA  |
| Directory di trasporto globale SAP | YES | SMB e NFS |
| Sapmnt SAP | adatto | tutti i sistemi SMB (solo Windows) o NFS (solo Linux) |
| Archiviazione di backup | adatto | - |
| Condivisioni/disco condiviso | YES | SMB 3,0, NFS v3 e NFS v 4.1 |
| Resilienza | LRS | Non sono disponibili GRS o ZRS per i dischi |
| Latenza | molto bassa | - |
| SLA IOPS | YES | - |
| IOPS lineare a capacità | strettamente lineare  | Dipendente dal [livello di servizio](../../../azure-netapp-files/azure-netapp-files-service-levels.md) |
| SLA velocità effettiva | YES | - |
| Velocità effettiva lineare a capacità | semi lineare tra parentesi quadre | Dipendente dal [livello di servizio](../../../azure-netapp-files/azure-netapp-files-service-levels.md) |
| Certificazione HANA | YES | - |
| Snapshot del disco possibili | YES | - |
| Snapshot di VM di backup di Azure possibili | NO | - |
| Costi | Archiviazione Premium superiore | - |


Funzionalità incorporate aggiuntive dell'archiviazione e:

- Funzionalità per l'esecuzione di snapshot del volume
- Clonazione di volumi e da snapshot
- Ripristinare i volumi da snapshot (ripristino di snap-in)

**Riepilogo**: Azure NetApp files è un'archiviazione a bassa latenza certificata di Hana che consente di distribuire volumi o condivisioni NFS e SMB. Lo spazio di archiviazione è costituito da tre diversi livelli di servizio che forniscono velocità effettiva e IOPS diverse in modo lineare per capacità GiB del volume. Lo spazio di archiviazione e consente di distribuire SAP HANA scenari con scalabilità orizzontale con un nodo standby. Lo spazio di archiviazione è adatto per fornire le condivisioni file necessarie per la directory di trasporto globale/sapmnt o SAP. Lo spazio di archiviazione e è disponibile con la disponibilità delle funzionalità disponibile come funzionalità NetApp nativa.  



## <a name="azure-standard-ssd-storage"></a>Archiviazione SSD standard di Azure
Rispetto all'archiviazione HDD standard di Azure, l'archiviazione SSD standard di Azure offre disponibilità, coerenza, affidabilità e latenza migliori. È ottimizzato per i carichi di lavoro che richiedono prestazioni coerenti a livelli di IOPS inferiori. Questa archiviazione è lo spazio di archiviazione minimo usato per i sistemi SAP non di produzione con requisiti di IOPS e velocità effettiva limitati. La matrice di funzionalità per il carico di lavoro SAP è simile alla seguente:

| Funzionalità| Commento| Note/collegamenti | 
| --- | --- | --- | 
| VHD di base del sistema operativo | adatto con restrizioni | sistemi non di produzione |
| Disco dati | adatto con restrizioni | alcuni sistemi non di produzione con requisiti di latenza e IOPS Bassi |
| Directory di trasporto globale SAP | NO | [Non supportato](https://launchpad.support.sap.com/#/notes/2015553) |
| Sapmnt SAP | adatto con restrizioni | sistemi non di produzione |
| Archiviazione di backup | adatto | - |
| Condivisioni/disco condiviso | non disponibile | Necessita di terze parti |
| Resilienza | LRS, GRS | Nessun ZRS disponibile per i dischi |
| Latenza | high | troppo elevata per la directory del trasporto globale SAP o per i sistemi di produzione |
| SLA IOPS | NO | - |
| Numero massimo di operazioni di I/O al secondo per disco | 500 | Indipendente dalle dimensioni del disco |
| SLA velocità effettiva | NO | - |
| Certificazione HANA | NO | - |
| Snapshot del disco possibili | YES | - |
| Snapshot di VM di backup di Azure possibili | YES | - |
| Costi | LOW | - |



**Riepilogo:** Archiviazione SSD standard di Azure è la raccomandazione minima per le macchine virtuali non di produzione per il disco rigido virtuale di base, eventuali distribuzioni DBMS con latenza relativa e/o velocità effettiva e velocità effettiva ridotte. Questo tipo di archiviazione di Azure non è più supportato per ospitare la directory di trasporto globale SAP. 



## <a name="azure-standard-hdd-storage"></a>Archiviazione HDD standard di Azure
Archiviazione HDD Standard di Azure è l'unico tipo di archiviazione quando l'infrastruttura di Azure è stata certificata per il carico di lavoro SAP NetWeaver nell'anno 2014. Nell'anno 2014, le macchine virtuali di Azure erano piccole e basse nella velocità effettiva di archiviazione. Pertanto, questo tipo di archiviazione è stato in grado di soddisfare le esigenze. Lo spazio di archiviazione è ideale per carichi di lavoro non sensibili alla latenza, che difficilmente si presentano nello spazio SAP. Con la velocità effettiva crescente delle macchine virtuali di Azure e l'aumento del carico di lavoro che queste VM producono, questo tipo di archiviazione non viene più considerato per l'utilizzo con gli scenari SAP. La matrice di funzionalità per il carico di lavoro SAP è simile alla seguente:

| Funzionalità| Commento| Note/collegamenti | 
| --- | --- | --- | 
| VHD di base del sistema operativo | non adatto | - |
| Disco dati | non adatto | - |
| Directory di trasporto globale SAP | NO | [Non supportato](https://launchpad.support.sap.com/#/notes/2015553) |
| Sapmnt SAP | NO | Non supportato |
| Archiviazione di backup | adatto | - |
| Condivisioni/disco condiviso | non disponibile | Necessità File di Azure o di terze parti |
| Resilienza | LRS, GRS | Nessun ZRS disponibile per i dischi |
| Latenza | high | troppo elevata per l'utilizzo di DBMS, la directory di trasporto globale di SAP o sapmnt/saploc |
| SLA IOPS | NO | - |
| Numero massimo di operazioni di I/O al secondo per disco | 500 | Indipendente dalle dimensioni del disco |
| SLA velocità effettiva | NO | - |
| Certificazione HANA | NO | - |
| Snapshot del disco possibili | YES | - |
| Snapshot di VM di backup di Azure possibili | YES | - |
| Costi | LOW | - |



**Riepilogo:** HDD Standard è un tipo di archiviazione di Azure che deve essere usato solo per archiviare i backup SAP. Deve essere usato solo come disco rigido virtuale di base per sistemi piuttosto inattivi, ad esempio i sistemi ritirati usati per la ricerca di dati qui e qui. Tuttavia, nessuna macchina virtuale di sviluppo, controllo di qualità o produzione deve essere basata su tale spazio di archiviazione. Né i file di database ospitati in tale risorsa di archiviazione


## <a name="azure-vm-limits-in-storage-traffic"></a>Limiti delle VM di Azure nel traffico di archiviazione
In modo contrario agli scenari locali, il singolo tipo di macchina virtuale che si sta selezionando svolge un ruolo essenziale nella larghezza di banda di archiviazione che è possibile ottenere. Per i diversi tipi di archiviazione, è necessario prendere in considerazione quanto segue:

| Tipo di archiviazione| Linux | Windows | Commenti |
| --- | --- | --- | --- |
| HDD Standard | [Dimensioni delle macchine virtuali Linux in Azure](../../sizes.md) | [Dimensioni delle macchine virtuali Windows in Azure](../../sizes.md) | Probabilmente è difficile toccare i limiti di archiviazione delle VM di medie o grandi dimensioni |
| SSD Standard | [Dimensioni delle macchine virtuali Linux in Azure](../../sizes.md) | [Dimensioni delle macchine virtuali Windows in Azure](../../sizes.md) | Probabilmente è difficile toccare i limiti di archiviazione delle VM di medie o grandi dimensioni |
| Archiviazione Premium | [Dimensioni delle macchine virtuali Linux in Azure](../../sizes.md) | [Dimensioni delle macchine virtuali Windows in Azure](../../sizes.md) | Facilità di utilizzo di IOPS o limiti di velocità effettiva di archiviazione con la configurazione dell'archiviazione |
| Archiviazione su disco Ultra | [Dimensioni delle macchine virtuali Linux in Azure](../../sizes.md) | [Dimensioni delle macchine virtuali Windows in Azure](../../sizes.md) | Facilità di utilizzo di IOPS o limiti di velocità effettiva di archiviazione con la configurazione dell'archiviazione |
| Azure NetApp Files | [Dimensioni delle macchine virtuali Linux in Azure](../../sizes.md) | [Dimensioni delle macchine virtuali Windows in Azure](../../sizes.md) | Il traffico di archiviazione sta usando la larghezza di banda della rete e non la larghezza di banda. |

Come limitazioni, è possibile notare quanto segue:

- Minore è la VM, minore è il numero di dischi che è possibile alleghire. Questa operazione non si applica a e. Poiché si montano le condivisioni NFS o SMB, non si verifica un limite di numero di volumi condivisi da collegare
- Le macchine virtuali hanno limiti di velocità effettiva e IOPS di I/O che possono essere superati facilmente con dischi di archiviazione Premium e dischi ultra
- Con e, il traffico verso i volumi condivisi sta consumando la larghezza di banda di rete della macchina virtuale e non la larghezza di banda di archiviazione
- Con volumi NFS di grandi dimensioni nello spazio di capacità a doppia cifra TiB, la velocità effettiva di accesso a tale volume di una singola macchina virtuale verrà allineata in base ai limiti di Linux per una singola sessione che interagisce con il volume condiviso. 

Quando si ridimensionano le macchine virtuali di Azure nel ciclo di vita di un sistema SAP, è necessario valutare i limiti di velocità effettiva di IOPS e archiviazione del tipo di VM nuovo e più grande. In alcuni casi, può anche essere utile modificare la configurazione di archiviazione per le nuove funzionalità della VM di Azure. 


## <a name="striping-or-not-striping"></a>Striping o non striping
La creazione di un set di striping su più dischi di Azure in un volume più grande consente di accumulare i IOPS e la velocità effettiva dei singoli dischi in un unico volume. Viene usato solo per archiviazione standard di Azure e per archiviazione Premium di Azure. Azure ultra disk, in cui è possibile configurare la velocità effettiva e IOPS indipendentemente dalla capacità di un disco, non richiede l'uso di set di striping. Non è possibile eseguire lo striping di volumi condivisi basati su NFS o SMB. A causa della natura non lineare della velocità effettiva e delle operazioni di i/o al secondo di archiviazione Premium di Azure, è possibile effettuare il provisioning di una capacità ridotta con gli stessi IOPS e velocità effettiva rispetto ai dischi di archiviazione Premium Questo è il metodo per ottenere maggiore velocità effettiva o IOPS a costi ridotti usando archiviazione Premium di Azure. Ad esempio, lo striping tra due dischi di archiviazione Premium P15 consente di ottenere una velocità effettiva: 

- 250 MiB/sec. Tale volume avrà una capacità di 512 GiB. Se si vuole avere un singolo disco che fornisce una velocità effettiva di 250 MiB al secondo, è necessario selezionare un disco P40 con capacità di 2 TiB. 
- 400 MiB/sec tramite striping di quattro dischi di archiviazione Premium P10 con una capacità complessiva di 512 GiB mediante striping. Se si vuole avere un disco singolo con una velocità effettiva di 500 MiB al secondo, è necessario selezionare un disco di archiviazione Premium P60 con 8 TiB. Poiché il costo di archiviazione Premium è quasi lineare con la capacità, è possibile percepire il risparmio sui costi tramite lo striping.

È necessario seguire alcune regole sullo striping:

- Non è necessario usare alcuna archiviazione configurata in-VM perché archiviazione di Azure mantiene già ridondante i dati
- Dischi a cui viene applicato il set di striping, che devono avere le stesse dimensioni

Lo striping su più dischi di dimensioni inferiori è il modo migliore per ottenere un rapporto prezzo/prestazioni ottimale con archiviazione Premium di Azure. Si noti che lo striping presenta un sovraccarico di distribuzione e gestione aggiuntivo.

Per indicazioni specifiche sulle dimensioni di striping, vedere la documentazione per i diversi sistemi DBMS, ad esempio [SAP Hana configurazioni di archiviazione delle macchine virtuali di Azure](./hana-vm-operations-storage.md).




## <a name="next-steps"></a>Passaggi successivi
Leggere gli articoli:

- [Considerazioni sulla distribuzione DBMS di macchine virtuali di Azure per un carico di lavoro SAP](./dbms_guide_general.md)
- [Configurazioni dell'archiviazione di macchine virtuali di Azure in SAP HANA](./hana-vm-operations-storage.md)
 
