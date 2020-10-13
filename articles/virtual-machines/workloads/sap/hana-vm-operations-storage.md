---
title: Configurazioni di archiviazione delle macchine virtuali di Azure in SAP HANA | Microsoft Docs
description: Elementi consigliati per l'archiviazione di VM in cui è distribuito SAP HANA.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: SAP, Azure HANA, storage ultra disk, archiviazione Premium
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/28/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 62faec3fd9ee36cb7a2b5da7e6bae07c6c8e06af
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91449387"
---
# <a name="sap-hana-azure-virtual-machine-storage-configurations"></a>Configurazioni dell'archiviazione di macchine virtuali di Azure in SAP HANA

Azure offre diversi tipi di archiviazione adatti per le VM di Azure che eseguono SAP HANA. I **tipi di archiviazione di Azure certificati per SAP HANA** che possono essere considerati per le distribuzioni SAP HANA sono: 

- Archiviazione Premium o SSD di Azure 
- [Disco Ultra](../../disks-enable-ultra-ssd.md)
- [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) 

Per informazioni su questi tipi di dischi, vedere l'articolo [tipi di archiviazione di Azure per il carico di lavoro SAP](./planning-guide-storage.md) e [selezionare un tipo di disco](../../disks-types.md)

Azure offre due metodi di distribuzione per i dischi rigidi virtuali in archiviazione standard e Premium di Azure. Si prevede di sfruttare i vantaggi del [disco gestito di Azure](https://azure.microsoft.com/services/managed-disks/) per le distribuzioni di archiviazione a blocchi di Azure. 

Per un elenco dei tipi di archiviazione e dei relativi contratti di servizio per operazioni di I/O al secondo e velocità effettiva di archiviazione, vedere la [documentazione di Azure per Managed Disks](https://azure.microsoft.com/pricing/details/managed-disks/).

> [!IMPORTANT]
> Indipendentemente dal tipo di archiviazione di Azure scelto, il file system usato in tale risorsa di archiviazione deve essere supportato da SAP per il sistema operativo e il DBMS specifici. La [Nota di supporto SAP #405827](https://launchpad.support.sap.com/#/notes/405827) elenca i file system supportati per diversi sistemi operativi e database, tra cui SAP HANA. Questo vale per tutti i volumi SAP HANA accessibili in lettura e scrittura per qualsiasi attività. In particolare, usando NFS in Azure per SAP HANA, le restrizioni aggiuntive delle versioni di NFS si applicano come indicato più avanti in questo articolo 


Le condizioni minime SAP HANA certificate per i diversi tipi di archiviazione sono: 

- Archiviazione Premium di Azure: è necessario che **/Hana/log** sia supportato da Azure [acceleratore di scrittura](../../how-to-enable-write-accelerator.md). Il volume **/Hana/data** può essere inserito in archiviazione premium senza acceleratore di scrittura di Azure o su disco Ultra
- Azure ultra disk almeno per il volume **/Hana/log** . Il volume **/Hana/data** può essere inserito in una risorsa di archiviazione premium senza acceleratore di scrittura di Azure o per ottenere tempi di riavvio più rapidi.
- I volumi **NFS v 4.1** sopra Azure NetApp files per **/Hana/log e/Hana/data**. Il volume di/Hana/Shared può utilizzare il protocollo NFS v3 o NFS v 4.1

Alcuni tipi di risorsa di archiviazione possono essere combinati. Ad esempio, è possibile inserire **/Hana/data** nell'archiviazione Premium e **/Hana/log** può essere collocato in una risorsa di archiviazione su disco Ultra per ottenere la bassa latenza richiesta. Se si usa un volume basato su e per **/Hana/data**, il volume  **/Hana/log** deve essere basato su NFS anche in e. L'uso di NFS su e per uno dei volumi (ad esempio/Hana/Data) e l'archiviazione Premium di Azure o ultra disk per l'altro volume (come **/Hana/log**) **non è supportato**.

È raro che sia necessario preoccuparsi dei sottosistemi di I/O e delle relative funzionalità nei sistemi locali perché il fornitore dell'appliance deve assicurarsi che siano soddisfatti i requisiti di archiviazione minima per SAP HANA. Se si configura l'infrastruttura di Azure in autonomia, è necessario conoscere alcuni di questi requisiti per SAP. Di seguito sono riportate alcune delle caratteristiche di velocità effettiva minime consigliate da SAP:

- Lettura/scrittura su **/Hana/log** di 250 MB/sec con dimensioni di I/O di 1 MB
- Attività di lettura di almeno 400 MB/sec per **/Hana/data** per le dimensioni di I/O di 16 mb e 64 MB
- Attività di scrittura di almeno 250 MB/sec per **/Hana/data** con dimensioni di I/O di 16 mb e 64 MB

Detto questo, una bassa latenza di archiviazione è fondamentale per i sistemi DBMS, anche perché i sistemi DBMS, come SAP HANA, mantengono dati in memoria. Il percorso critico per l'archiviazione riguarda in genere le scritture nel log delle transazioni dei sistemi DBMS. Ma possono essere critiche anche operazioni come la scrittura di punti di salvataggio o il caricamento di dati in memoria dopo il ripristino da un arresto anomalo del sistema. Pertanto, è **obbligatorio** sfruttare archiviazione Premium di Azure, ultra disk o e per i volumi **/Hana/data** e **/Hana/log** . 


Alcuni principi guida per la selezione della configurazione di archiviazione per HANA possono essere elencati come segue:

- Decidere il tipo di archiviazione in base ai [tipi di archiviazione di Azure per il carico di lavoro SAP](./planning-guide-storage.md) e [selezionare un tipo di disco](../../disks-types.md)
- La velocità effettiva complessiva di I/O delle macchine virtuali e i limiti di IOPS quando si dimensiona o si decide per una macchina virtuale. La velocità effettiva complessiva di archiviazione delle VM è documentata nell'articolo [dimensioni delle macchine virtuali con ottimizzazione](../../sizes-memory.md) per la memoria
- Quando si decide la configurazione dell'archiviazione, provare a rimanere sotto la velocità effettiva complessiva della VM con la configurazione del volume **/Hana/data** . La scrittura di salvataggio, SAP HANA può essere un sistema di I/o di emissione aggressivo. Quando si scrive un salvataggio, è possibile eseguire facilmente il push fino a limiti di velocità effettiva del volume **/Hana/data** . Se i dischi che compilano il volume **/Hana/data** hanno una velocità effettiva superiore a quella consentita dalla VM, è possibile che si verifichino situazioni in cui la velocità effettiva utilizzata dalla scrittura salvataggio interferisca con le richieste di velocità effettiva delle Scritture del log di rollforward. Situazione che può influisca sulla velocità effettiva dell'applicazione
- Se si usa archiviazione Premium di Azure, la configurazione meno costosa consiste nell'usare i gestori di volumi logici per creare set di striping per compilare i volumi **/Hana/data** e **/Hana/log**

> [!IMPORTANT]
> I suggerimenti per le configurazioni di archiviazione sono intesi come istruzioni per iniziare con. L'esecuzione del carico di lavoro e l'analisi dei modelli di utilizzo dello spazio di archiviazione, si potrebbe notare che non si utilizza tutta la larghezza di banda di archiviazione o IOPS fornita. È possibile prendere in considerazione la riduzione dell'archiviazione. Oppure, al contrario, il carico di lavoro potrebbe richiedere una maggiore velocità effettiva di archiviazione rispetto a quanto suggerito con queste configurazioni. Di conseguenza, potrebbe essere necessario distribuire più capacità, IOPS o velocità effettiva. Nel campo del tensionamento tra capacità di archiviazione necessaria, latenza di archiviazione necessaria, velocità effettiva di archiviazione e IOPS necessari e configurazione meno costosa, Azure offre un numero sufficiente di tipi di archiviazione con diverse funzionalità e punti di prezzo diversi per trovare e adattare il compromesso giusto per l'utente e il carico di lavoro HANA.

## <a name="linux-io-scheduler-mode"></a>Modalità di pianificazione I/O Linux
Linux offre varie modalità di pianificazione I/O diverse. Una raccomandazione comune per i fornitori di Linux e SAP consiste nel riconfigurare la modalità di pianificazione I/O per i volumi di dischi dalla modalità **mq-deadline** o **kyber** alla modalità **noop** (non multiqueue) o **none** per (multiqueue). Informazioni dettagliate sono disponibili nella [nota SAP #1984787](https://launchpad.support.sap.com/#/notes/1984787). 


## <a name="solutions-with-premium-storage-and-azure-write-accelerator-for-azure-m-series-virtual-machines"></a>Soluzioni con archiviazione Premium e Azure acceleratore di scrittura per macchine virtuali di Azure serie M
L'acceleratore di scrittura di Azure è una funzionalità disponibile esclusivamente per le VM di Azure della serie M. Come indicato dal nome, lo scopo della funzionalità è migliorare la latenza di I/O delle Scritture per l'archiviazione Premium di Azure. Per SAP HANA, l'uso dell'acceleratore di scrittura è previsto solo sul volume **/hana/log**. Pertanto, **/hana/data** e **/hana/log** sono volumi separati con l'acceleratore di scrittura di Azure che supporta solo il volume **/hana/log**. 

> [!IMPORTANT]
> Quando si usa archiviazione Premium di Azure, l'uso di [acceleratore di scrittura](../../how-to-enable-write-accelerator.md) di Azure per il volume **/Hana/log** è obbligatorio. Acceleratore di scrittura è disponibile solo per le macchine virtuali di archiviazione Premium e serie M e Mv2-Series. Acceleratore di scrittura non funziona in combinazione con altre famiglie di macchine virtuali di Azure, ad esempio Esv3 o Edsv4.

Le raccomandazioni relative alla memorizzazione nella cache per i dischi Premium di Azure riportati di seguito presuppongono le caratteristiche di I/O per SAP HANA elenco, ad esempio:

- Non esiste quasi nessun carico di lavoro in lettura sui file di dati HANA, ad eccezione degli I/O di grandi dimensioni dopo il riavvio dell'istanza di HANA o quando i dati vengono caricati in HANA. Un altro caso di I/O in lettura di dimensioni maggiori sui file di dati è rappresentato dai backup di database HANA. Di conseguenza spesso la lettura della cache non ha senso in quanto, nella maggior parte dei casi, tutti i volumi dei file di dati devono essere letti completamente. 
- La scrittura sui file di dati si verifica in burst in base ai punti di salvataggio HANA e al ripristino da arresto anomalo del sistema HANA. La scrittura di punti di salvataggio è un'operazione asincrona e non impedisce le transazioni dell'utente. La scrittura di dati durante il ripristino da arresto anomalo del sistema è un'operazione critica a livello di prestazioni per ottenere che il sistema risponda di nuovo rapidamente. Tuttavia, il ripristino da arresto anomalo del sistema dovrebbe essere un caso piuttosto eccezionale.
- Esistono pochissime operazioni di lettura dai file di rollforward HANA. Le eccezioni sono gli I/O di grandi dimensioni quando si eseguono backup dei log di transazioni, il ripristino da arresto anomalo del sistema o nella fase di riavvio di un'istanza HANA.  
- Il carico principale sul file di log di rollforward SAP HANA è rappresentato delle operazioni di scrittura. A seconda del tipo di carico di lavoro, è possibile che le operazioni di I/O siano di piccole dimensioni, ad esempio 4 kB o, in altri casi, di almeno 1 MB. La latenza di scrittura rispetto al log di rollforward SAP HANA è critica dal punto di vista delle prestazioni.
- Tutti i dati delle scritture devono essere salvati in modo permanente su disco in modo affidabile.

**Raccomandazione: in seguito a questi modelli di I/O osservati SAP HANA, la memorizzazione nella cache per i diversi volumi con archiviazione Premium di Azure deve essere impostata come segue:**

- **/Hana/data** -No caching o Read Caching
- **/Hana/log** -nessun caching-eccezione per le macchine virtuali M-e Mv2-Series in cui deve essere abilitato Azure acceleratore di scrittura 
- **/hana/shared**: lettura della cache
- **Disco del sistema operativo** : non modificare la memorizzazione nella cache predefinita impostata da Azure al momento della creazione della macchina virtuale


Se si usa LVM o mdadm per creare set di striping in diversi dischi Premium di Azure, è necessario definire le dimensioni dello striping. Queste dimensioni sono diverse tra **/Hana/data** e **/Hana/log**. **Raccomandazione: con le dimensioni di striping, è consigliabile usare:**

- 256 KB per **/hana/data**
- 64 KB per **/Hana/log**

> [!NOTE]
> Le dimensioni di striping per **/Hana/data** sono state modificate rispetto alle raccomandazioni precedenti per la chiamata di 64 kb o 128 kb a 256 KB in base alle esperienze dei clienti con versioni più recenti di Linux. Le dimensioni di 256 KB offrono prestazioni leggermente migliori. È stata anche modificata la raccomandazione per le dimensioni di striping di **/Hana/log** da 32 kb a 64 KB, per ottenere una velocità effettiva sufficiente con dimensioni di I/O maggiori.

> [!NOTE]
> Non è necessario configurare alcun livello di ridondanza usando i volumi RAID poiché archiviazione blocchi di Azure mantiene tre immagini di un disco rigido virtuale. L'uso di un set di striping con i dischi Premium di Azure è esclusivamente per la configurazione di volumi che forniscono operazioni di I/o e/o velocità effettiva sufficienti.

L'accumulo di un numero di dischi rigidi virtuali di Azure sotto un set di striping è un accumulo da un lato di IOPS e velocità effettiva di archiviazione. Se quindi si inserisce un set di striping tra più dischi di archiviazione Premium di Azure P30, è consigliabile ottenere tre volte le operazioni di i/o al secondo e la velocità effettiva di archiviazione di un singolo disco P30 di archiviazione Premium di Azure.

> [!IMPORTANT]
> Se si usa LVM o mdadm come volume Manager per creare set di striping tra più dischi Premium di Azure, i tre SAP HANA filesystem/data,/log e/documenti condivisi non devono essere inseriti in un gruppo di volumi predefinito o radice. È consigliabile seguire le linee guida per i fornitori Linux che in genere creano singoli gruppi di volumi per /data, /log e /shared.


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


### <a name="production-recommended-storage-solution-based-on-azure-premium-storage"></a>Soluzione di archiviazione consigliata di produzione basata sull'archiviazione Premium di Azure

> [!IMPORTANT]
> La certificazione SAP HANA per le macchine virtuali di Azure della serie M è valida esclusivamente con l'acceleratore di scrittura di Azure per il volume **/hana/log**. Di conseguenza, per le distribuzioni di SAP HANA in scenari di produzione nelle macchine virtuali di Azure della serie M è prevista la configurazione con l'acceleratore di scrittura di Azure per il volume **/hana/log**.  

> [!NOTE]
> Negli scenari che coinvolgono archiviazione Premium di Azure, stiamo implementando funzionalità di espansione nella configurazione. Man mano che si usano gli strumenti di test di archiviazione di qualsiasi forma o forma, tenere presente il modo in cui il [disco Premium di Azure](../../linux/disk-bursting.md) è in grado di funzionare. Eseguendo i test di archiviazione recapitati tramite lo strumento SAP HWCCT o HCMT, non è previsto che tutti i test superino i criteri poiché alcuni dei test supereranno i crediti di espansione che è possibile accumulare. Soprattutto quando tutti i test vengono eseguiti in modo sequenziale senza interruzioni.


> [!NOTE]
> Per gli scenari di produzione, controllare se un determinato tipo di macchina virtuale è supportato da SAP per SAP HANA nella [documentazione SAP per IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).

**Raccomandazione: le configurazioni consigliate con archiviazione Premium di Azure per scenari di produzione sono simili alle seguenti:**

Configurazione per il volume **/Hana/data** di SAP:

| SKU di VM | RAM | Max. velocità effettiva<br /> Velocità effettiva | /hana/data | Velocità effettiva massima di espansione | Operazioni di I/O al secondo | IOPS a impulsi |
| --- | --- | --- | --- | --- | --- | --- | 
| M32ts | 192 GiB | 500 MBps | 4 x P6 | 680 MBps | 960 | 14.000 |
| M32ls | 256 GiB | 500 MBps | 4 x P6 | 680 MBps | 960 | 14.000 |
| M64ls | 512 GiB | 1\.000 MBps | 4 x P10 |  680 MBps | 2.000 | 14.000 |
| M64s | 1\.000 GiB | 1\.000 MBps | 4 x P15 | 680 MBps | 4.400 | 14.000 |
| M64ms | 1\.750 GiB | 1\.000 MBps | 4 x P20 | 680 MBps | 9.200 | 14.000 |  
| M128s | 2\.000 GiB | 2.000 MBps | 4 x P20 | 680 MBps | 9.200| 14.000 | 
| M128ms | 3\.800 GiB | 2.000 MBps | 4 x P30 | 800 MBps (con provisioning) | 20.000 | Nessun impulso | 
| M208s_v2 | 2\.850 GiB | 1\.000 MBps | 4 x P30 | 800 MBps (con provisioning) | 20.000| Nessun impulso | 
| M208ms_v2 | 5\.700 GiB | 1\.000 MBps | 4 x P40 | 1.000 MBps (con provisioning) | 25,000 | Nessun impulso |
| M416s_v2 | 5\.700 GiB | 2.000 MBps | 4 x P40 | 1.000 MBps (con provisioning) | 25,000 | Nessun impulso |
| M416ms_v2 | 11.400 GiB | 2.000 MBps | 4 x P50 | 2.000 MBps (con provisioning) | 25,000 | Nessun impulso |


Per il volume **/Hana/log** . la configurazione avrà un aspetto simile al seguente:

| SKU di VM | RAM | Max. velocità effettiva<br /> Velocità effettiva | volume **/Hana/log** | Velocità effettiva massima di espansione | Operazioni di I/O al secondo | IOPS a impulsi |
| --- | --- | --- | --- | --- | --- | --- | 
| M32ts | 192 GiB | 500 MBps | 3 x P10 | 510 MBps | 1\.500 | 10.500 | 
| M32ls | 256 GiB | 500 MBps | 3 x P10 | 510 MBps | 1\.500 | 10.500 | 
| M64ls | 512 GiB | 1\.000 MBps | 3 x P10 | 510 MBps | 1\.500 | 10.500 | 
| M64s | 1\.000 GiB | 1\.000 MBps | 3 x P15 | 510 MBps | 3.300 | 10.500 | 
| M64ms | 1\.750 GiB | 1\.000 MBps | 3 x P15 | 510 MBps | 3.300 | 10.500 |  
| M128s | 2\.000 GiB | 2.000 MBps | 3 x P15 | 510 MBps | 3.300 | 10.500|  
| M128ms | 3\.800 GiB | 2.000 MBps | 3 x P15 | 510 MBps | 3.300 | 10.500 | 
| M208s_v2 | 2\.850 GiB | 1\.000 MBps | 3 x P15 | 510 MBps | 3.300 | 10.500 |  
| M208ms_v2 | 5\.700 GiB | 1\.000 MBps | 3 x P15 | 510 MBps | 3.300 | 10.500 |  
| M416s_v2 | 5\.700 GiB | 2.000 MBps | 3 x P15 | 510 MBps | 3.300 | 10.500 |  
| M416ms_v2 | 11.400 GiB | 2.000 MBps | 3 x P15 | 510 MBps | 3.300 | 10.500 | 


Per gli altri volumi, la configurazione sarà simile alla seguente:

| SKU di VM | RAM | Max. velocità effettiva<br /> Velocità effettiva | /hana/shared | volume /root | /usr/sap |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| M32ts | 192 GiB | 500 MBps | 1 x P20 | 1 x P6 | 1 x P6 |
| M32ls | 256 GiB | 500 MBps |  1 x P20 | 1 x P6 | 1 x P6 |
| M64ls | 512 GiB | 1000 MBps | 1 x P20 | 1 x P6 | 1 x P6 |
| M64s | 1\.000 GiB | 1\.000 MBps | 1 x P30 | 1 x P6 | 1 x P6 |
| M64ms | 1\.750 GiB | 1\.000 MBps | 1 x P30 | 1 x P6 | 1 x P6 | 
| M128s | 2\.000 GiB | 2.000 MBps | 1 x P30 | 1 x P10 | 1 x P6 | 
| M128ms | 3\.800 GiB | 2.000 MBps | 1 x P30 | 1 x P10 | 1 x P6 |
| M208s_v2 | 2\.850 GiB | 1\.000 MBps |  1 x P30 | 1 x P10 | 1 x P6 |
| M208ms_v2 | 5\.700 GiB | 1\.000 MBps | 1 x P30 | 1 x P10 | 1 x P6 | 
| M416s_v2 | 5\.700 GiB | 2.000 MBps |  1 x P30 | 1 x P10 | 1 x P6 | 
| M416ms_v2 | 11.400 GiB | 2.000 MBps | 1 x P30 | 1 x P10 | 1 x P6 | 


Controllare se la velocità effettiva di archiviazione per i diversi volumi suggeriti soddisfa i requisiti del carico di lavoro che si vuole eseguire. Se il carico di lavoro richiede volumi più elevati per **/Hana/data** e **/Hana/log**, è necessario aumentare il numero di dischi rigidi virtuali di archiviazione Premium di Azure. Il dimensionamento di un volume con più dischi rigidi virtuali di quelli elencati consente di aumentare le operazioni di I/O al secondo e la velocità effettiva di I/O entro i limiti del tipo di macchina virtuale di Azure.

L'acceleratore di scrittura di Azure funziona solo in combinazione con [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/). Quindi, almeno i dischi di archiviazione Premium di Azure che formano il volume **/Hana/log** devono essere distribuiti come Managed Disks. Per istruzioni più dettagliate e restrizioni di Azure acceleratore di scrittura, vedere l'articolo [acceleratore di scrittura](../../how-to-enable-write-accelerator.md).

Per le VM certificate HANA della famiglia Azure [Esv3](../../ev3-esv3-series.md?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json#esv3-series) e [Edsv4](../../edv4-edsv4-series.md?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json#edsv4-series), è necessario e per il volume **/Hana/data** e **/Hana/log** . In alternativa, è necessario usare l'archiviazione su disco Ultra di Azure anziché archiviazione Premium di Azure solo per il volume **/Hana/log** . Di conseguenza, le configurazioni per il volume **/Hana/data** in archiviazione Premium di Azure possono avere un aspetto simile al seguente:

| SKU di VM | RAM | Max. velocità effettiva<br /> Velocità effettiva | /hana/data | Velocità effettiva massima di espansione | Operazioni di I/O al secondo | IOPS a impulsi |
| --- | --- | --- | --- | --- | --- | --- |
| E20ds_v4 | GiB 160 | 480 MBps | 3 x P10 | 510 MBps | 1\.500 | 10.500 |
| E32ds_v4 | 256 GiB | 768 MBps | 3 x P10 |  510 MBps | 1\.500 | 10.500|
| E48ds_v4 | GiB 384 | 1.152 MBps | 3 x P15 |  510 MBps | 3.300  | 10.500 | 
| E64ds_v4 | GiB 504 | 1\.200 MBps | 3 x P15 |  510 MBps | 3.300 | 10.500 | 
| E64s_v3 | 432 GiB | 1\.200 MB/s | 3 x P15 |  510 MBps | 3.300 | 10.500 | 

Per gli altri volumi, incluso **/Hana/log** su disco Ultra, la configurazione potrebbe essere simile alla seguente:

| SKU di VM | RAM | Max. velocità effettiva<br /> Velocità effettiva | Volume /hana/log | Velocità effettiva di I/O /hana/log | Operazioni di I/O al secondo /hana/log | /hana/shared | volume /root | /usr/sap |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| E20ds_v4 | GiB 160 | 480 MBps | 80 GB | 250 MBps | 1.800 | 1 x P15 | 1 x P6 | 1 x P6 |
| E32ds_v4 | 256 GiB | 768 MBps | 128 GB | 250 MBps | 1.800 | 1 x P15 | 1 x P6 | 1 x P6 |
| E48ds_v4 | GiB 384 | 1.152 MBps | 192 GB | 250 MBps | 1.800 | 1 x P20 | 1 x P6 | 1 x P6 |
| E64ds_v4 | GiB 504 | 1\.200 MBps | 256 GB | 250 MBps | 1.800 | 1 x P20 | 1 x P6 | 1 x P6 |
| E64s_v3 | 432 GiB | 1\.200 MBps | 220 GB | 250 MBps | 1.800 | 1 x P20 | 1 x P6 | 1 x P6 |


## <a name="azure-ultra-disk-storage-configuration-for-sap-hana"></a>Configurazione dell'archiviazione su disco Ultra di Azure per SAP HANA
Un altro tipo di archiviazione di Azure è denominato [Azure ultra disk](../../disks-types.md#ultra-disk). La differenza significativa tra l'archiviazione di Azure offerta fino a questo momento e il disco Ultra è che le capacità del disco non sono più associate alle sue dimensioni. I clienti possono definire le capacità seguenti per il disco Ultra:

- Dimensioni di un disco compreso tra 4 GiB e 65.536 GiB
- Intervallo di operazioni di I/O al secondo da 100 a 160.000 (il valore massimo dipende anche dai tipi di VM)
- Velocità effettiva di archiviazione da 300 MB/sec a 2.000 MB/sec

Il disco Ultra consente di definire un singolo disco che soddisfi le dimensioni, le operazioni di I/O al secondo e l'intervallo di velocità effettiva del disco. Anziché usare gestori di volumi logici come LVM o MDADM in archiviazione Premium di Azure per costruire volumi che soddisfino i requisiti di IOPS e velocità effettiva di archiviazione. È possibile eseguire una combinazione di configurazioni tra il disco Ultra e l'archiviazione Premium. Di conseguenza, è possibile limitare l'utilizzo del disco Ultra ai volumi **/Hana/data** e **/Hana/log** critici per le prestazioni e coprire gli altri volumi con archiviazione Premium di Azure.

Altri vantaggi di ultra disk possono essere la latenza di lettura migliore rispetto all'archiviazione Premium. La latenza di lettura più veloce può avere vantaggi quando si vogliono ridurre i tempi di avvio di HANA e il caricamento dei dati in memoria. I vantaggi dell'archiviazione nel disco Ultra si hanno anche quando HANA scrive punti di salvataggio. 

> [!NOTE]
> Il disco Ultra non è ancora presente in tutte le aree di Azure e non supporta ancora tutti i tipi di VM elencati di seguito. Per informazioni dettagliate sulla disponibilità del disco Ultra e sulle famiglie di VM supportate, vedere l'articolo [Tipi di disco disponibili in Azure](../../windows/disks-types.md#ultra-disk).

### <a name="production-recommended-storage-solution-with-pure-ultra-disk-configuration"></a>Soluzione di archiviazione consigliata per la produzione con configurazione disco Ultra puro
In questa configurazione i volumi **/Hana/data** e **/Hana/log** vengono conservati separatamente. I valori suggeriti sono derivati dagli indicatori KPI che SAP ha per certificare i tipi di VM per le configurazioni SAP HANA e di archiviazione come consigliato nel documento [SAP TDI Storage Whitepaper](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) (Withepaper sull'archiviazione SAP TDI).

Le raccomandazioni spesso superano i requisiti minimi di SAP indicati in precedenza in questo articolo. Le raccomandazioni elencate costituiscono un compromesso tra le raccomandazioni di SAP relative alle dimensioni e la velocità effettiva massima di archiviazione offerta dai diversi tipi di VM.

> [!NOTE]
> Il disco Ultra di Azure impone un minimo di 2 IOPS per la capacità gigabyte di un disco


| SKU di VM | RAM | Max. velocità effettiva<br /> Velocità effettiva | Volume /hana/data | Velocità effettiva di I/O /hana/data | Operazioni di I/O al secondo /hana/data | Volume /hana/log | Velocità effettiva di I/O /hana/log | Operazioni di I/O al secondo /hana/log |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| E20ds_v4 | GiB 160 | 480 MB/s | 200 GB | 400 MBps | 2\.500 | 80 GB | 250 MB | 1.800 |
| E32ds_v4 | 256 GiB | 768 MB/s | 300 GB | 400 MBps | 2\.500 | 128 GB | 250 MBps | 1.800 |
| E48ds_v4 | GiB 384 | 1152 MB/s | 460 GB | 400 MBps | 3,000 | 192 GB | 250 MBps | 1.800 |
| E64ds_v4 | GiB 504 | 1200 MB/s | 610 GB | 400 MBps | 3\.500 |  256 GB | 250 MBps | 1.800 |
| E64s_v3 | 432 GiB | 1\.200 MB/s | 610 GB | 400 MBps | 3\.500 | 220 GB | 250 MB | 1.800 |
| M32ts | 192 GiB | 500 MB/s | 250 GB | 400 MBps | 2\.500 | 96 GB | 250 MBps  | 1.800 |
| M32ls | 256 GiB | 500 MB/s | 300 GB | 400 MBps | 2\.500 | 256 GB | 250 MBps  | 1.800 |
| M64ls | 512 GiB | 1\.000 MB/s | 620 GB | 400 MBps | 3\.500 | 256 GB | 250 MBps  | 1.800 |
| M64s | 1\.000 GiB | 1\.000 MB/s |  1\.200 GB | 600 MBps | 5\.000 | 512 GB | 250 MBps  | 2\.500 |
| M64ms | 1\.750 GiB | 1\.000 MB/s | 2\.100 GB | 600 MBps | 5\.000 | 512 GB | 250 MBps  | 2\.500 |
| M128s | 2\.000 GiB | 2\.000 MB/s |2\.400 GB | 750 MBps | 7.000 | 512 GB | 250 MBps  | 2\.500 | 
| M128ms | 3\.800 GiB | 2\.000 MB/s | 4\.800 GB | 750 MBps |9.600 | 512 GB | 250 MBps  | 2\.500 | 
| M208s_v2 | 2\.850 GiB | 1\.000 MB/s | 3\.500 GB | 750 MBps | 7.000 | 512 GB | 250 MBps  | 2\.500 | 
| M208ms_v2 | 5\.700 GiB | 1\.000 MB/s | 7\.200 GB | 750 MBps | 14.400 | 512 GB | 250 MBps  | 2\.500 | 
| M416s_v2 | 5\.700 GiB | 2\.000 MB/s | 7\.200 GB | 1\.000 MBps | 14.400 | 512 GB | 400 MBps  | 4.000 | 
| M416ms_v2 | 11.400 GiB | 2\.000 MB/s | 14.400 GB | 1\.500 MBps | 28.800 | 512 GB | 400 MBps  | 4.000 |   

**I valori elencati sono intesi come punto di partenza e devono essere valutati in base alle esigenze effettive.** Il vantaggio del disco Ultra di Azure è che i valori per operazioni di I/O al secondo e velocità effettiva possono essere adattati senza la necessità di arrestare la VM o il carico di lavoro applicato al sistema.   

> [!NOTE]
> Al momento gli snapshot di archiviazione con l'archiviazione su disco Ultra non sono disponibili. Questo impedisce l'uso degli snapshot delle VM con i servizi di Backup di Azure


## <a name="nfs-v41-volumes-on-azure-netapp-files"></a>Volumi NFS v4.1 in Azure NetApp Files
Per informazioni dettagliate su e per HANA, vedere il documento relativo ai [volumi NFS v 4.1 su Azure NetApp files per SAP Hana](./hana-vm-operations-netapp.md)




## <a name="cost-conscious-solution-with-azure-premium-storage"></a>Soluzione di costo cosciente con archiviazione Premium di Azure
Fino ad ora, la soluzione di archiviazione Premium di Azure descritta in questo documento nella sezione [soluzioni con archiviazione Premium e azure acceleratore di scrittura per le macchine virtuali della serie M](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage#solutions-with-premium-storage-and-azure-write-accelerator-for-azure-m-series-virtual-machines) di Azure è stata concepita per SAP Hana scenari di produzione supportati. Una delle caratteristiche delle configurazioni supportate per l'ambiente di produzione è la separazione dei volumi per i dati SAP HANA e il rollforward log in due volumi diversi. Il motivo di tale separazione è che le caratteristiche del carico di lavoro sui volumi sono diverse. Con le configurazioni di produzione suggerite, potrebbe essere necessario un tipo diverso di memorizzazione nella cache o anche tipi diversi di archiviazione a blocchi di Azure. Le configurazioni supportate per la produzione che usano la destinazione di archiviazione blocchi di Azure devono essere conformi al [contratto di Service VM singolo per le macchine virtuali di Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) .  Per gli scenari non di produzione, alcune considerazioni adottate per i sistemi di produzione potrebbero non essere valide per i sistemi non di produzione più bassi. Di conseguenza, è possibile combinare i dati e il volume di log di HANA. Anche se alla fine con alcuni colpevoli, come alla fine non soddisfano determinati indicatori KPI di velocità effettiva o latenza richiesti per i sistemi di produzione. Un altro aspetto per ridurre i costi in questi ambienti può essere l'utilizzo dell' [archiviazione di Azure SDD standard](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide-storage#azure-standard-ssd-storage). Tuttavia, si tratta di una scelta che invalida il [contratto di Service per macchina virtuale di Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/). 

Un'alternativa meno costosa per queste configurazioni potrebbe essere simile alla seguente:


| SKU di VM | RAM | Max. velocità effettiva<br /> Velocità effettiva | /hana/data and /hana/log<br /> con striping con LVM o MDADM | /hana/shared | volume /root | /usr/sap | comments |
| --- | --- | --- | --- | --- | --- | --- | -- |
| DS14v2 | 112 GiB | 768 MB/s | 4 x P6 | 1 x E10 | 1 x E6 | 1 x E6 | Non otterrà una latenza di archiviazione inferiore a 1 MS<sup>1</sup> |
| E16v3 | 128 GiB | 384 MB/s | 4 x P6 | 1 x E10 | 1 x E6 | 1 x E6 | Tipo di VM non certificato HANA <br /> Non otterrà una latenza di archiviazione inferiore a 1 MS<sup>1</sup> |
| M32ts | 192 GiB | 500 MB/s | 3 x P10 | 1 x E15 | 1 x E6 | 1 x E6 | Se si usa acceleratore di scrittura per i dati combinati e il volume di log, la velocità IOPS viene limitata a 5.000<sup>2</sup> |
| E20ds_v4 | GiB 160 | 480 MB/s | 4 x P6 | 1 x E15 | 1 x E6 | 1 x E6 | Non otterrà una latenza di archiviazione inferiore a 1 MS<sup>1</sup> |
| E32v3 | 256 GiB | 768 MB/s | 4 x P10 | 1 x E15 | 1 x E6 | 1 x E6 | Tipo di VM non certificato HANA <br /> Non otterrà una latenza di archiviazione inferiore a 1 MS<sup>1</sup> |
| E32ds_v4 | 256 GiB | 768 MBps | 4 x P10 | 1 x E15 | 1 x E6 | 1 x E6 | Non otterrà una latenza di archiviazione inferiore a 1 MS<sup>1</sup> |
| M32ls | 256 GiB | 500 MB/s | 4 x P10 | 1 x E15 | 1 x E6 | 1 x E6 | Se si usa acceleratore di scrittura per i dati combinati e il volume di log, la velocità IOPS viene limitata a 5.000<sup>2</sup> |
| E48ds_v4 | GiB 384 | 1.152 MBps | 6 x P10 | 1 x E20 | 1 x E6 | 1 x E6 | Non otterrà una latenza di archiviazione inferiore a 1 MS<sup>1</sup> |
| E64v3 | 432 GiB | 1\.200 MB/s | 6 x P10 | 1 x E20 | 1 x E6 | 1 x E6 | Non otterrà una latenza di archiviazione inferiore a 1 MS<sup>1</sup> |
| E64ds_v4 | GiB 504 | 1200 MB/s |  7 x P10 | 1 x E20 | 1 x E6 | 1 x E6 | Non otterrà una latenza di archiviazione inferiore a 1 MS<sup>1</sup> |
| M64ls | 512 GiB | 1\.000 MB/s | 7 x P10 | 1 x E20 | 1 x E6 | 1 x E6 | Se si usa acceleratore di scrittura per i dati combinati e il volume di log, la velocità IOPS viene limitata a 10.000<sup>2</sup> |
| M64s | 1\.000 GiB | 1\.000 MB/s | 7 x P15 | 1 x E30 | 1 x E6 | 1 x E6 | Se si usa acceleratore di scrittura per i dati combinati e il volume di log, la velocità IOPS viene limitata a 10.000<sup>2</sup> |
| M64ms | 1\.750 GiB | 1\.000 MB/s | P20 6 x | 1 x E30 | 1 x E6 | 1 x E6 | Se si usa acceleratore di scrittura per i dati combinati e il volume di log, la velocità IOPS viene limitata a 10.000<sup>2</sup> |
| M128s | 2\.000 GiB | 2\.000 MB/s |P20 6 x | 1 x E30 | 1 x E10 | 1 x E6 | Se si usa acceleratore di scrittura per i dati combinati e il volume di log, la velocità IOPS viene limitata a 20.000<sup>2</sup> |
| M208s_v2 | 2\.850 GiB | 1\.000 MB/s | 4 x P30 | 1 x E30 | 1 x E10 | 1 x E6 | Se si usa acceleratore di scrittura per i dati combinati e il volume di log, la velocità IOPS viene limitata a 10.000<sup>2</sup> |
| M128ms | 3\.800 GiB | 2\.000 MB/s | 5 x P30 | 1 x E30 | 1 x E10 | 1 x E6 | Se si usa acceleratore di scrittura per i dati combinati e il volume di log, la velocità IOPS viene limitata a 20.000<sup>2</sup> |
| M208ms_v2 | 5\.700 GiB | 1\.000 MB/s | 4 x P40 | 1 x E30 | 1 x E10 | 1 x E6 | Se si usa acceleratore di scrittura per i dati combinati e il volume di log, la velocità IOPS viene limitata a 10.000<sup>2</sup> |
| M416s_v2 | 5\.700 GiB | 2\.000 MB/s | 4 x P40 | 1 x E30 | 1 x E10 | 1 x E6 | Se si usa acceleratore di scrittura per i dati combinati e il volume di log, la velocità IOPS viene limitata a 20.000<sup>2</sup> |
| M416ms_v2 | 11.400 GiB | 2\.000 MB/s | 7 x P40 | 1 x E30 | 1 x E10 | 1 x E6 | Se si usa acceleratore di scrittura per i dati combinati e il volume di log, la velocità IOPS viene limitata a 20.000<sup>2</sup> |


<sup>1</sup> [acceleratore di scrittura di Azure](../../how-to-enable-write-accelerator.md) non può essere usato con le famiglie di macchine virtuali Ev4 e Ev4. In seguito all'uso di archiviazione Premium di Azure, la latenza di I/O non sarà inferiore a 1 ms

<sup>2</sup> la famiglia di VM supporta [Azure acceleratore di scrittura](../../how-to-enable-write-accelerator.md), ma è possibile che il limite di IOPS dell'acceleratore di scrittura limiti le funzionalità di IOPS delle configurazioni del disco

Se si combinano i dati e il volume di log per SAP HANA, i dischi che compilano il volume con striping non devono avere la cache di lettura o di lettura/scrittura abilitata.

Sono elencati i tipi di macchine virtuali che non sono certificati con SAP e che non sono elencati nella cosiddetta [directory hardware SAP Hana](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Il feedback dei clienti era che i tipi di VM non elencati venivano usati correttamente per alcune attività non di produzione.


## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni, vedere:

- [Guida alla disponibilità elevata di SAP HANA per macchine virtuali di Azure](./sap-hana-availability-overview.md).
