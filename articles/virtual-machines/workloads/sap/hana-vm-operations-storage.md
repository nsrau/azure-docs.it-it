---
title: SAP HANA configurazioni di archiviazione delle macchine virtuali di Azure | Microsoft Docs
description: Raccomandazioni di archiviazione per la macchina virtuale in cui sono stati SAP HANA distribuiti.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/11/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0ab25b7a6d723ed5f2e74ad60ff54f9bf6d0fe4c
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2019
ms.locfileid: "72300558"
---
# <a name="sap-hana-azure-virtual-machine-storage-configurations"></a>Configurazioni dell'archiviazione di macchine virtuali di Azure in SAP HANA

Azure offre diversi tipi di archiviazione adatti per le macchine virtuali di Azure che eseguono SAP HANA. I tipi di archiviazione di Azure che possono essere considerati per SAP HANA elenco di distribuzioni, ad esempio: 

- Unità disco SDD Standard (SSD)
- Unità SSD Premium
- [Disco Ultra](https://docs.microsoft.com/azure/virtual-machines/linux/disks-enable-ultra-ssd)
- [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) 

Per informazioni su questi tipi di dischi, vedere l'articolo [selezionare un tipo di disco](https://docs.microsoft.com/azure/virtual-machines/linux/disks-types)

Azure offre due metodi di distribuzione per VHD su Archiviazione Standard e Premium di Azure. Se lo scenario lo permette, sfruttare i vantaggi offerti dalle distribuzioni di [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/). 

Per un elenco dei tipi di archiviazione e dei relativi contratti di servizio per operazioni di I/O al secondo e velocità effettiva di archiviazione, vedere la [documentazione di Azure per Managed Disks](https://azure.microsoft.com/pricing/details/managed-disks/).

Per l'utilizzo con HANA, questi tre tipi di archiviazione sono certificati con SAP:

- Archiviazione Premium di Azure:/Hana/log deve essere memorizzato nella cache con Azure [acceleratore di scrittura](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator)
- Disco Ultra di Azure
- Volumi NFS v 4.1 sopra Azure NetApp Files per/Hana/log e/Hana/data

È possibile combinare alcuni tipi di archiviazione. ad esempio è possibile inserire/Hana/data nell'archiviazione Premium e/Hana/log può essere inserito in una risorsa di archiviazione su disco Ultra per ottenere la bassa latenza richiesta. Tuttavia, non è consigliabile combinare volumi NFS, ad esempio/Hana/data, e usare uno degli altri tipi di archiviazione certificati per/Hana/log

Nel mondo locale, raramente era necessario preoccuparsi dei sottosistemi di I/O e delle relative funzionalità. perché il fornitore dell'appliance deve assicurarsi che siano soddisfatti i requisiti di archiviazione minima per SAP HANA. Quando si compila autonomamente l'infrastruttura di Azure, è necessario tenere presente alcuni di questi requisiti. Alcune delle caratteristiche minime della velocità effettiva richieste sono la necessità di:

- Abilita lettura/scrittura su **/Hana/log** di 250 MB/sec con dimensioni di I/O di 1 MB
- Abilitare l'attività di lettura per minimo 400 MB/sec per **/hana/data** con dimensioni di I/O di 16 MB e 64 MB
- Abilitare l'attività di scrittura per minimo 250 MB/sec per **/hana/data** con dimensioni di I/O di 16 MB e 64 MB

Detto questo, una bassa latenza di archiviazione è fondamentale per i sistemi DBMS, anche perché i sistemi DBMS, come SAP HANA, mantengono dati in memoria. Il percorso critico per l'archiviazione riguarda in genere le scritture nel log delle transazioni dei sistemi DBMS. Ma possono essere critiche anche operazioni come la scrittura di punti di salvataggio o il caricamento di dati in memoria dopo il ripristino da un arresto anomalo del sistema. Pertanto, è **obbligatorio** sfruttare i dischi Premium di Azure per i volumi **/Hana/data** e **/Hana/log** . Per ottenere la velocità effettiva minima di **/hana/log** e **/hana/data** come richiesto da SAP, è necessario configurare un sistema RAID 0 tramite MDADM o LVM su più dischi di Archiviazione Premium di Azure e usare i volumi RAID come volumi **/hana/data** e **/hana/log**. 

**Recommendation: Con le dimensioni di striping per il RAID 0, è consigliabile usare:**

- 64 KB o 128 KB per **/hana/data**
- 32 KB per **/hana/log**

> [!NOTE]
> Non è necessario configurare alcun livello di ridondanza con i volumi RAID perché l'Archiviazione Standard e Premium di Azure mantiene tre immagini di un disco rigido virtuale. L'utilizzo di un volume RAID è richiesto esclusivamente per configurare volumi in grado di offrire una velocità effettiva di I/O sufficiente.

Accumulare un certo numero di dischi rigidi virtuali di Azure in una configurazione RAID consente di ottenere valori cumulativi per le operazioni di I/O al secondo e la velocità effettiva di archiviazione. Pertanto, se si configura un sistema RAID 0 su 3 dischi di Archiviazione Premium di Azure P30, si dovrebbero ottenere livelli triplicati di operazioni di I/O al secondo e velocità effettiva di archiviazione rispetto a un singolo disco di Archiviazione Premium di Azure P30.

Durante il ridimensionamento o quando si sceglie una macchina virtuale, tenere presente anche la velocità effettiva di I/O complessiva delle macchine virtuali. Per informazioni sulla velocità effettiva di archiviazione complessiva delle macchine virtuali, vedere l'articolo [Dimensioni delle macchine virtuali ottimizzate per la memoria](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory).

## <a name="linux-io-scheduler-mode"></a>Modalità di pianificazione I/O Linux
Linux offre varie modalità di pianificazione I/O diverse. Una raccomandazione comune per i fornitori di Linux e SAP è riconfigurare la modalità di pianificazione I/O per i volumi del disco dalla modalità **CFQ** alla modalità **NoOp** . Informazioni dettagliate sono disponibili nella [nota SAP n. 1984798](https://launchpad.support.sap.com/#/notes/1984787). 


## <a name="solutions-with-premium-storage-and-azure-write-accelerator-for-azure-m-series-virtual-machines"></a>Soluzioni con archiviazione Premium e Azure acceleratore di scrittura per macchine virtuali di Azure serie M
Azure acceleratore di scrittura è una funzionalità disponibile esclusivamente per le macchine virtuali della serie M di Azure. Come indicato dal nome, lo scopo della funzionalità è migliorare la latenza di I/O delle scritture per l'Archiviazione Premium di Azure. Per SAP HANA, l'uso dell'acceleratore di scrittura è previsto solo sul volume **/hana/log**. Di conseguenza, **/Hana/data** e **/Hana/log** sono volumi separati con acceleratore di scrittura di Azure che supportano solo il volume **/Hana/log** . 

> [!IMPORTANT]
> Quando si usa archiviazione Premium di Azure, l'uso di Azure [acceleratore di scrittura](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator) o del volume/Hana/log è obbligatorio. Acceleratore di scrittura è disponibile solo per le macchine virtuali di archiviazione Premium e serie M e Mv2.

Gli elementi consigliati sulla memorizzazione nella cache riportati di seguito presuppongono caratteristiche di I/O per SAP HANA come:

- Non esiste quasi nessun carico di lavoro in lettura sui file di dati HANA, ad eccezione degli I/O di grandi dimensioni dopo il riavvio dell'istanza di HANA o quando i dati vengono caricati in HANA. Un altro caso di I/O in lettura di dimensioni maggiori sui file di dati è rappresentato dai backup di database HANA. Di conseguenza spesso la lettura della cache non ha senso in quanto, nella maggior parte dei casi, tutti i volumi dei file di dati devono essere letti completamente.
- La scrittura sui file di dati si verifica in burst in base ai punti di salvataggio HANA e al ripristino da arresto anomalo del sistema HANA. La scrittura di punti di salvataggio è un'operazione asincrona e non impedisce le transazioni dell'utente. La scrittura di dati durante il ripristino da arresto anomalo del sistema è un'operazione critica a livello di prestazioni per ottenere che il sistema risponda di nuovo rapidamente. Tuttavia, il ripristino da arresto anomalo del sistema dovrebbe essere un caso piuttosto eccezionale.
- Esistono pochissime operazioni di lettura dai file di rollforward HANA. Le eccezioni sono gli I/O di grandi dimensioni quando si eseguono backup dei log di transazioni, il ripristino da arresto anomalo del sistema o nella fase di riavvio di un'istanza HANA.  
- Il carico principale sul file di log di rollforward SAP HANA è rappresentato delle operazioni di scrittura. A seconda del tipo di carico di lavoro, è possibile che le operazioni di I/O siano di piccole dimensioni, ad esempio 4 kB o, in altri casi, di almeno 1 MB. La latenza di scrittura rispetto al log di rollforward SAP HANA è critica dal punto di vista delle prestazioni.
- Tutti i dati delle scritture devono essere salvati in modo permanente su disco in modo affidabile.

**Recommendation: In seguito a questi modelli di I/O osservati SAP HANA, la memorizzazione nella cache per i diversi volumi con archiviazione Premium di Azure deve essere impostata come:**

- **/hana/data**: nessuna memorizzazione nella cache
- **/Hana/log** -nessun caching-eccezione per la serie M e Mv2 in cui acceleratore di scrittura è abilitata come funzionalità di memorizzazione nella cache
- **/hana/shared**: lettura della cache

### <a name="production-recommended-storage-solution"></a>Soluzione di archiviazione consigliata per la produzione

> [!IMPORTANT]
> La certificazione SAP HANA per le macchine virtuali di Azure della serie M è valida esclusivamente con l'acceleratore di scrittura di Azure per il volume **/hana/log**. Di conseguenza, per le distribuzioni di SAP HANA in scenari di produzione nelle macchine virtuali di Azure della serie M è prevista la configurazione con l'acceleratore di scrittura di Azure per il volume **/hana/log**.  

> [!NOTE]
> Per gli scenari di produzione, controllare se un determinato tipo di macchina virtuale è supportato da SAP per SAP HANA nella [documentazione SAP per IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).



**Recommendation: Le configurazioni consigliate per gli scenari di produzione sono simili alle seguenti:**

| SKU di VM | RAM | Max. velocità effettiva<br /> Velocità effettiva | /hana/data | /hana/log | /hana/shared | volume /root | /usr/sap | hana/backup |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| M32ts | 192 GiB | 500 MB/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M32ls | 256 GiB | 500 MB/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M64ls | 512 GiB | 1000 MB/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P30 |
| M64s | 1000 GiB | 1000 MB/s | 4 x P20 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 |2 x P30 |
| M64ms | 1750 GiB | 1000 MB/s | 3 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 3 x P30 |
| M128s | 2000 GiB | 2000 MB/s |3 x P30 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 2 x P40 |
| M128ms | 3800 GiB | 2000 MB/s | 5 x P30 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 4 x P40 |
| M208s_v2 | GiB 2850 | 1000 MB/s | 4 x P30 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 3 x P40 |
| M208ms_v2 | GiB 5700 | 1000 MB/s | 4 x P40 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 3 x P50 |
| M416s_v2 | GiB 5700 | 2000 MB/s | 4 x P40 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 3 x P50 |
| M416ms_v2 | GiB 11400 | 2000 MB/s | 8 x P40 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 4 x P50 |

I tipi di VM M416xx_v2 non sono ancora resi disponibili da Microsoft al pubblico. Verificare che la velocità effettiva di archiviazione per i diversi volumi suggeriti soddisfi il carico di lavoro che si desidera eseguire. Se il carico di lavoro richiede volumi più elevati per **/hana/data** e **/hana/log**, è necessario aumentare il numero di dischi rigidi virtuali di Archiviazione Premium di Azure. Il dimensionamento di un volume con più dischi rigidi virtuali rispetto a quelli elencati aumenta il numero di IOPS e la velocità effettiva di I/O entro i limiti del tipo di macchina virtuale

L'acceleratore di scrittura di Azure funziona solo in combinazione con [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/). Almeno i dischi di Archiviazione Premium di Azure che costituiscono il volume **/hana/log** devono quindi essere distribuiti come dischi gestiti.

Esistono limiti per il numero di dischi rigidi virtuali di Archiviazione Premium di Azure per ogni macchina virtuale supportati dall'acceleratore di scrittura di Azure. I limiti correnti sono:

- 16 VHD per una macchina virtuale M128xx e M416xx
- 8 dischi rigidi virtuali per una VM M64xx e M208xx
- 4 dischi rigidi virtuali per una macchina virtuale M32xx

Istruzioni più dettagliate su come abilitare l'acceleratore di scrittura di Azure sono disponibili nell'articolo [Acceleratore di scrittura](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator).

La stessa documentazione include anche informazioni dettagliate e sulle restrizioni per l'acceleratore di scrittura di Azure.

**Recommendation: È necessario usare acceleratore di scrittura per i dischi che formano il volume/Hana/log @ no__t-0


### <a name="cost-conscious-azure-storage-configuration"></a>Configurazione di Archiviazione di Azure tenendo conto dei costi
La tabella seguente illustra una configurazione dei tipi di VM che i clienti usano anche per ospitare SAP HANA in macchine virtuali di Azure. Potrebbero essere presenti alcuni tipi di VM che potrebbero non soddisfare tutti i criteri di archiviazione minimi per SAP HANA o non sono ufficialmente supportati con SAP HANA da SAP. anche se offrono prestazioni adeguate per scenari non di produzione. **/Hana/data** e **/Hana/log** vengono combinati con un volume. Di conseguenza, l'uso di acceleratore di scrittura di Azure può diventare una limitazione in termini di IOPS.

> [!NOTE]
> Per gli scenari supportati da SAP, controllare se un determinato tipo di macchina virtuale è supportato per SAP HANA da SAP nella [documentazione SAP per IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).

> [!NOTE]
> Una modifica rispetto alle precedenti raccomandazioni per una soluzione economica, consiste nel passare da [azure HDD standard disks](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-hdd) a prestazioni migliori e a [dischi di Azure SDD standard](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-ssd) più affidabili


| SKU di VM | RAM | Max. velocità effettiva<br /> Velocità effettiva | /hana/data and /hana/log<br /> con striping con LVM o MDADM | /hana/shared | volume /root | /usr/sap | hana/backup |
| --- | --- | --- | --- | --- | --- | --- | -- |
| DS14v2 | 112 GiB | 768 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E15 |
| E16v3 | 128 GiB | 384 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E15 |
| E32v3 | 256 GiB | 768 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| E64v3 | 432 GiB | 1200 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E30 |
| GS5 | 448 GiB | 2000 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E30 |
| M32ts | 192 GiB | 500 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| M32ls | 256 GiB | 500 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| M64ls | 512 GiB | 1000 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 |1 x E30 |
| M64s | 1000 GiB | 1000 MB/s | 2 x P30 | 1 x E30 | 1 x E6 | 1 x E6 |2 x E30 |
| M64ms | 1750 GiB | 1000 MB/s | 3 x P30 | 1 x E30 | 1 x E6 | 1 x E6 | 3 x E30 |
| M128s | 2000 GiB | 2000 MB/s |3 x P30 | 1 x E30 | 1 x E10 | 1 x E6 | 2 x E40 |
| M128ms | 3800 GiB | 2000 MB/s | 5 x P30 | 1 x E30 | 1 x E10 | 1 x E6 | E50 2 x |
| M208s_v2 | GiB 2850 | 1000 MB/s | 4 x P30 | 1 x E30 | 1 x E10 | 1 x E6 |  3 x E40 |
| M208ms_v2 | GiB 5700 | 1000 MB/s | 4 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E40 |
| M416s_v2 | GiB 5700 | 2000 MB/s | 4 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E40 |
| M416ms_v2 | GiB 11400 | 2000 MB/s | 8 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  E50 4 x |


I tipi di VM M416xx_v2 non sono ancora resi disponibili da Microsoft al pubblico. I dischi consigliati per i tipi di macchine virtuali più piccoli con 3 x P20 superano in dimensioni i volumi riguardanti le indicazioni di spazio contenute nel [white paper sull'archiviazione di SAP TDI](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html). Tuttavia, la scelta indicata nella tabella è stata effettuata per offrire una velocità effettiva del disco sufficiente per SAP HANA. Se è necessario apportare modifiche al volume **/hana/backup**, che è stato ridimensionato per mantenere backup che rappresentano il doppio del volume di memoria, procedere liberamente.   
Verificare che la velocità effettiva di archiviazione per i diversi volumi suggeriti soddisfi il carico di lavoro che si desidera eseguire. Se il carico di lavoro richiede volumi più elevati per **/hana/data** e **/hana/log**, è necessario aumentare il numero di dischi rigidi virtuali di Archiviazione Premium di Azure. Il dimensionamento di un volume con più dischi rigidi virtuali rispetto a quelli elencati aumenta il numero di IOPS e la velocità effettiva di I/O entro i limiti del tipo di macchina virtuale 

> [!NOTE]
> Le configurazioni sopra indicate NON traggono vantaggio dal [contratto di servizio per macchine virtuali con istanza singola di macchina virtuale di Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/) perché usa in combinazione Archiviazione Premium di Azure e Archiviazione Standard di Azure. Tuttavia, la selezione è stata scelta per ottimizzare i costi. È necessario scegliere Archiviazione Premium per tutti i dischi sopra quello elencato come Archiviazione di Azure Standard (Sxx) per rendere la configurazione della macchina virtuale conforme al contratto di servizio per singole macchine virtuali di Azure.


> [!NOTE]
> I suggerimenti sulla configurazione del disco forniti fanno riferimento ai requisiti minimi definiti da SAP per i provider di infrastruttura. Nelle distribuzioni effettive dei clienti e negli scenari con carichi di lavoro reali, tuttavia, si sono verificate situazioni in cui questi suggerimenti non garantivano funzionalità sufficienti. Queste situazioni si possono verificare quando un cliente richiede un ricaricamento più veloce dei dati dopo un riavvio HANA o se una configurazione di backup richiede una larghezza di banda maggiore per l'archiviazione. Altri casi includono, ad esempio, percorsi **/hana/log** in cui 5000 IOPS non sono sufficienti per un carico di lavoro specifico. Considerare quindi queste indicazioni come punto di inizio e adattarle in base ai requisiti del carico di lavoro.
>  

## <a name="azure-ultra-disk-storage-configuration-for-sap-hana"></a>Configurazione dell'archiviazione su disco Ultra di Azure per SAP HANA
Microsoft sta implementando un nuovo tipo di archiviazione di Azure denominato [Azure ultra disk](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#ultra-disk). La grande differenza tra archiviazione di Azure offerta fino a questo momento e ultra disk è che le funzionalità del disco non sono più associate alle dimensioni del disco. I clienti possono definire queste funzionalità per il disco Ultra:

- Dimensioni di un disco compreso tra 4 GiB e 65.536 GiB
- Gli IOPS variano da 100 IOPS a 160K IOPS (il valore massimo dipende anche dai tipi di VM)
- Velocità effettiva di archiviazione da 300 MB/sec a 2.000 MB/sec

Il disco Ultra consente di definire un singolo disco che soddisfi le dimensioni, i IOPS e l'intervallo di velocità effettiva del disco. Anziché usare gestori di volumi logici come LVM o MDADM in archiviazione Premium di Azure per costruire volumi che soddisfino i requisiti di IOPS e velocità effettiva di archiviazione. È possibile eseguire una combinazione di configurazioni tra il disco Ultra e l'archiviazione Premium. Di conseguenza, è possibile limitare l'utilizzo del disco Ultra ai volumi/Hana/data e/Hana/log critici per le prestazioni e coprire gli altri volumi con archiviazione Premium di Azure.

Altri vantaggi di ultra disk possono essere la latenza di lettura migliore rispetto all'archiviazione Premium. La latenza di lettura più veloce può avere vantaggi quando si desidera ridurre i tempi di avvio di HANA e il successivo caricamento dei dati in memoria. I vantaggi dell'archiviazione su disco possono anche essere ritenuti quando HANA sta scrivendo salvataggio. Poiché i dischi di archiviazione Premium per/Hana/data non sono in genere acceleratore di scrittura memorizzati nella cache, la latenza di scrittura su/Hana/data in archiviazione Premium rispetto al disco Ultra è superiore. Si può prevedere che la scrittura salvataggio con ultra disk funzioni meglio su disco Ultra.

> [!IMPORTANT]
> Il disco Ultra non è ancora presente in tutte le aree di Azure e non supporta ancora tutti i tipi di VM elencati di seguito. Per informazioni dettagliate sulla disponibilità di ultra disk e sulle famiglie di macchine virtuali supportate, vedere l'articolo sui [tipi di dischi disponibili in Azure](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#ultra-disk).

### <a name="production-recommended-storage-solution-with-pure-ultra-disk-configuration"></a>Soluzione di archiviazione consigliata per la produzione con configurazione disco Ultra puro
In questa configurazione i volumi/Hana/data e/Hana/log vengono conservati separatamente. I valori suggeriti sono derivati dagli indicatori KPI che SAP deve certificare i tipi di VM per le configurazioni di SAP HANA e archiviazione come consigliato nel [white paper sull'archiviazione di SAP TDI](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html).

| SKU di VM | RAM | Max. velocità effettiva<br /> Velocità effettiva | volume/Hana/data | velocità effettiva di I/O/Hana/data | IOPS/Hana/data | volume/Hana/log | velocità effettiva di I/O/Hana/log | IOPS/Hana/log |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| E64s_v3 | 432 GiB | 1\.200 MB/s | 600 GB | 700 MBps | 7500 | 512 GB | 500 MBps  | 2000 |
| M32ts | 192 GiB | 500 MB/s | 250 GB | 400 MBps | 7500 | 256 GB | 250 MBps  | 2000 |
| M32ls | 256 GiB | 500 MB/s | 300 GB | 400 MBps | 7500 | 256 GB | 250 MBps  | 2000 |
| M64ls | 512 GiB | 1000 MB/s | 600 GB | 600 MBps | 7500 | 512 GB | 400 MBps  | 2500 |
| M64s | 1000 GiB | 1\.000 MB/s |  1200 GB | 600 MBps | 7500 | 512 GB | 400 MBps  | 2500 |
| M64ms | 1750 GiB | 1\.000 MB/s | 2100 GB | 600 MBps | 7500 | 512 GB | 400 MBps  | 2500 |
| M128s | 2000 GiB | 2\.000 MB/s |2400 GB | 1200 MBps |9000 | 512 GB | 800 MBps  | 3000 | 
| M128ms | 3800 GiB | 2\.000 MB/s | 4800 GB | 1200 MBps |9000 | 512 GB | 800 MBps  | 3000 | 
| M208s_v2 | GiB 2850 | 1\.000 MB/s | 3500 GB | 1000 MBps | 9000 | 512 GB | 400 MBps  | 2500 | 
| M208ms_v2 | GiB 5700 | 1\.000 MB/s | 7200 GB | 1000 MBps | 9000 | 512 GB | 400 MBps  | 2500 | 
| M416s_v2 | GiB 5700 | 2\.000 MB/s | 7200 GB | 1500MBps | 9000 | 512 GB | 800 MBps  | 3000 | 
| M416ms_v2 | GiB 11400 | 2\.000 MB/s | 14400 GB | 1500 MBps | 9000 | 512 GB | 800 MBps  | 3000 |   

I tipi di VM M416xx_v2 non sono ancora resi disponibili da Microsoft al pubblico. I valori elencati sono intesi come punto di partenza e devono essere valutati in base alle effettive esigenze. Il vantaggio di Azure ultra disk è che i valori per IOPS e velocità effettiva possono essere adattati senza la necessità di arrestare la macchina virtuale o di arrestare il carico di lavoro applicato al sistema.   

> [!NOTE]
> Fino a questo punto, gli snapshot di archiviazione con archiviazione su disco Ultra non sono disponibili. Blocca l'uso degli snapshot di VM con i servizi di backup di Azure

### <a name="production-recommended-storage-solution-with-pure-ultra-disk-configuration"></a>Soluzione di archiviazione consigliata per la produzione con configurazione disco Ultra puro
In questa configurazione, i volumi/Hana/data e/Hana/log sullo stesso disco. I valori suggeriti sono derivati dagli indicatori KPI che SAP deve certificare i tipi di VM per le configurazioni di SAP HANA e archiviazione come consigliato nel [white paper sull'archiviazione di SAP TDI](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html).

| SKU di VM | RAM | Max. velocità effettiva<br /> Velocità effettiva | Volume per/Hana/data e/log | /Hana/data e velocità effettiva di I/O del log | /Hana/data e IOPS log |
| --- | --- | --- | --- | --- | --- |
| E64s_v3 | 432 GiB | 1\.200 MB/s | 1\.200 GB | 1\.200 MBps | 9\.500 | 
| M32ts | 192 GiB | 500 MB/s | 512 GB | 400 MBps | 9\.500 | 
| M32ls | 256 GiB | 500 MB/s | 600 GB | 400 MBps | 9\.500 | 
| M64ls | 512 GiB | 1\.000 MB/s | 1\.100 GB | 900 MBps | 10,000 | 
| M64s | 1000 GiB | 1\.000 MB/s |  1\.700 GB | 900 MBps | 10,000 | 
| M64ms | 1750 GiB | 1\.000 MB/s | 2\.600 GB | 900 MBps | 10,000 | 
| M128s | 2000 GiB | 2\.000 MB/s |2\.900 GB | 1\.800 MBps |12000 | 
| M128ms | 3800 GiB | 2\.000 MB/s | 5\.300 GB | 1\.800 MBps |12000 |  
| M208s_v2 | GiB 2850 | 1\.000 MB/s | 4\.000 GB | 900 MBps | 10,000 |  
| M208ms_v2 | GiB 5700 | 1\.000 MB/s | 7\.700 GB | 900 MBps | 10,000 | 
| M416s_v2 | GiB 5700 | 2\.000 MB/s | 7\.700 GB | 1, 800 Mbps | 12000 |  
| M416ms_v2 | GiB 11400 | 2\.000 MB/s | 15.000 GB | 1\.800 MBps | 12000 |    

I tipi di VM M416xx_v2 non sono ancora resi disponibili da Microsoft al pubblico. I valori elencati sono intesi come punto di partenza e devono essere valutati in base alle effettive esigenze. Il vantaggio di Azure ultra disk è che i valori per IOPS e velocità effettiva possono essere adattati senza la necessità di arrestare la macchina virtuale o di arrestare il carico di lavoro applicato al sistema.  

## <a name="nfs-v41-volumes-on-azure-netapp-files"></a>Volumi NFS v 4.1 in Azure NetApp Files
Azure NetApp Files fornisce condivisioni NFS native che possono essere usate per i volumi/Hana/Shared,/Hana/data e/Hana/log. L'uso di condivisioni NFS basate su e per questi volumi richiede l'utilizzo del protocollo NFS v 4.1. il protocollo NFS v3 non è supportato per l'utilizzo di volumi correlati a HANA quando si basano le condivisioni in e. 

> [!IMPORTANT]
> il protocollo NFS v3 implementato in Azure NetApp Files non è supportato per l'uso di/Hana/Shared,/Hana/data e/Hana/log

Per soddisfare i requisiti di latenza di archiviazione, è essenziale che le macchine virtuali che usano tali volumi NFS per SAP HANA siano vicine all'infrastruttura e. Per ottenere questo risultato, le macchine virtuali devono essere inserite con l'aiuto di Microsoft nella vicinanza dell'infrastruttura di e. Per consentire a Microsoft di eseguire tale posizione di prossimità, Microsoft pubblicherà un modulo che richiederà alcuni dati e un set di disponibilità di Azure vuoto. Microsoft posiziona quindi il set di disponibilità vicino all'infrastruttura e, se necessario. 

L'infrastruttura e offre diverse categorie di prestazioni. Queste categorie sono documentate in [livelli di servizio per Azure NetApp files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels). 

> [!NOTE]
> È consigliabile usare la categoria e ultra storage per/Hana/data e/Hana/log. Per/Hana/Shared, la categoria standard o Premium è sufficiente

I suggerimenti per la velocità effettiva consigliata dei volumi NFS basati su e verranno pubblicati a breve.

La documentazione che descrive come creare più configurazioni di scalabilità orizzontale di HANA verrà pubblicata a breve.


## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni, vedere:

- [SAP Hana guida alla disponibilità elevata per macchine virtuali di Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview).
