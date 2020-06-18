---
title: Configurazioni di archiviazione delle macchine virtuali di Azure in SAP HANA | Microsoft Docs
description: Elementi consigliati per l'archiviazione di VM in cui è distribuito SAP HANA.
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
ms.date: 05/19/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: aa3096f43952c047620b310412b27c434fc5fb06
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83682595"
---
# <a name="sap-hana-azure-virtual-machine-storage-configurations"></a>Configurazioni dell'archiviazione di macchine virtuali di Azure in SAP HANA

Azure offre diversi tipi di archiviazione adatti per le VM di Azure che eseguono SAP HANA. I **tipi di archiviazione di Azure certificati per SAP HANA** che possono essere considerati per le distribuzioni SAP HANA sono: 

- SSD Premium di Azure  
- [Disco Ultra](https://docs.microsoft.com/azure/virtual-machines/linux/disks-enable-ultra-ssd)
- [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) 

Per informazioni su questi tipi di dischi, vedere l'articolo [Selezionare un tipo di disco](https://docs.microsoft.com/azure/virtual-machines/linux/disks-types)

Azure offre due metodi di distribuzione per VHD su Archiviazione Standard e Premium di Azure. Se lo scenario lo permette, sfruttare i vantaggi offerti dalle distribuzioni di [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/). 

Per un elenco dei tipi di archiviazione e dei relativi contratti di servizio per operazioni di I/O al secondo e velocità effettiva di archiviazione, vedere la [documentazione di Azure per Managed Disks](https://azure.microsoft.com/pricing/details/managed-disks/).

> [!IMPORTANT]
> Indipendentemente dal tipo di archiviazione di Azure scelto, il file system usato in tale risorsa di archiviazione deve essere supportato da SAP per il sistema operativo e il DBMS specifici. La [Nota di supporto SAP #405827](https://launchpad.support.sap.com/#/notes/405827) elenca i file system supportati per diversi sistemi operativi e database, tra cui SAP HANA. Questo vale per tutti i volumi SAP HANA accessibili in lettura e scrittura per qualsiasi attività. In particolare, usando NFS in Azure per SAP HANA, le restrizioni aggiuntive delle versioni di NFS si applicano come indicato più avanti in questo articolo 


Le condizioni minime SAP HANA certificate per i diversi tipi di archiviazione sono: 

- SSD Premium di Azure: /hana/log deve essere memorizzato nella cache con l'[acceleratore di scrittura](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator) di Azure. Il volume /hana/data potrebbe essere inserito in SSD Premium senza l'acceleratore di scrittura di Azure o nel disco Ultra
- Disco Ultra di Azure almeno per il volume /hana/log. Il volume /hana/data può essere inserito in SSD Premium senza l'acceleratore di scrittura di Azure o per ottenere tempi di riavvio del disco Ultra più rapidi
- Volumi **NFS v4.1** su Azure NetApp Files per /hana/log **e** /hana/data. Il volume di /hana/shared può usare il protocollo NFS v3 o NFS v4.1. Il protocollo NFS v4.1 è obbligatorio per i volumi /hana/data e /hana/log.

Alcuni tipi di risorsa di archiviazione possono essere combinati. Ad esempio, è possibile inserire /hana/data nell'Archiviazione Premium e /hana/log in una risorsa di archiviazione su disco Ultra per ottenere la bassa latenza richiesta. Tuttavia, se si usa un volume NFS v4.1 basato su Azure NetApp Files per /hana/data, è necessario usare un altro volume NFS v4.1 basato su Azure NetApp Files per /hana/log. L'uso di NFS su Azure NetApp Files per uno dei volumi (ad esempio /hana/data) e Archiviazione Premium di Azure o disco Ultra per l'altro volume (ad esempio /hana/log) **non è supportato**.

È raro che sia necessario preoccuparsi dei sottosistemi di I/O e delle relative funzionalità nei sistemi locali perché il fornitore dell'appliance deve assicurarsi che siano soddisfatti i requisiti di archiviazione minima per SAP HANA. Se si configura l'infrastruttura di Azure in autonomia, è necessario conoscere alcuni di questi requisiti per SAP. Alcune delle caratteristiche minime della velocità effettiva per SAP richiedono di:

- Abilitare lettura/scrittura in **/hana/log** per 250 MB/sec con dimensioni di I/O di 1 MB
- Abilitare l'attività di lettura per minimo 400 MB/sec per **/hana/data** con dimensioni di I/O di 16 MB e 64 MB
- Abilitare l'attività di scrittura per minimo 250 MB/sec per **/hana/data** con dimensioni di I/O di 16 MB e 64 MB

Detto questo, una bassa latenza di archiviazione è fondamentale per i sistemi DBMS, anche perché i sistemi DBMS, come SAP HANA, mantengono dati in memoria. Il percorso critico per l'archiviazione riguarda in genere le scritture nel log delle transazioni dei sistemi DBMS. Ma possono essere critiche anche operazioni come la scrittura di punti di salvataggio o il caricamento di dati in memoria dopo il ripristino da un arresto anomalo del sistema. È quindi **obbligatorio** usare Dischi Premium di Azure per i volumi **/hana/data** e **/hana/log**. Per ottenere la velocità effettiva minima di **/hana/log** e **/hana/data** come richiesto da SAP, è necessario configurare un sistema RAID 0 tramite MDADM o LVM su più dischi di Archiviazione Premium di Azure e usare i volumi RAID come volumi **/hana/data** e **/hana/log**. 

> [!IMPORTANT]
>I tre file system SAP HANA /data, /log e /shared non devono essere inseriti in un gruppo di volumi predefinito o radice.  È consigliabile seguire le linee guida per i fornitori Linux che in genere creano singoli gruppi di volumi per /data, /log e /shared.

**Raccomandazione: per le dimensioni di striping di RAID 0 è consigliabile l'uso di:**

- 256 KB per **/hana/data**
- 32 KB per **/hana/log**

> [!IMPORTANT]
> Le dimensioni di striping per /hana/data sono state modificate rispetto alle raccomandazioni precedenti da 64 KB o 128 KB a 256 KB in base alle esperienze dei clienti con versioni più recenti di Linux. Le dimensioni di 256 KB offrono prestazioni leggermente migliori

> [!NOTE]
> Non è necessario configurare alcun livello di ridondanza con i volumi RAID perché l'Archiviazione Standard e Premium di Azure mantiene tre immagini di un disco rigido virtuale. L'utilizzo di un volume RAID è richiesto esclusivamente per configurare volumi in grado di offrire una velocità effettiva di I/O sufficiente.

Accumulare un certo numero di dischi rigidi virtuali di Azure in una configurazione RAID consente di ottenere valori cumulativi per le operazioni di I/O al secondo e la velocità effettiva di archiviazione. Pertanto, se si configura un sistema RAID 0 su 3 dischi di Archiviazione Premium di Azure P30, si dovrebbero ottenere livelli triplicati di operazioni di I/O al secondo e velocità effettiva di archiviazione rispetto a un singolo disco di Archiviazione Premium di Azure P30.

Durante il ridimensionamento o quando si sceglie una macchina virtuale, tenere presente anche la velocità effettiva di I/O complessiva delle macchine virtuali. Per informazioni sulla velocità effettiva di archiviazione complessiva delle macchine virtuali, vedere l'articolo [Dimensioni delle macchine virtuali ottimizzate per la memoria](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory).

## <a name="linux-io-scheduler-mode"></a>Modalità di pianificazione I/O Linux
Linux offre varie modalità di pianificazione I/O diverse. Una raccomandazione comune per i fornitori di Linux e SAP consiste nel riconfigurare la modalità di pianificazione I/O per i volumi di dischi dalla modalità **mq-deadline** o **kyber** alla modalità **noop** (non multiqueue) o **none** per (multiqueue). Informazioni dettagliate sono disponibili nella [nota SAP #1984787](https://launchpad.support.sap.com/#/notes/1984787). 


## <a name="solutions-with-premium-storage-and-azure-write-accelerator-for-azure-m-series-virtual-machines"></a>Soluzioni con Archiviazione Premium e l'acceleratore di scrittura di Azure per macchine virtuali di Azure della serie M
L'acceleratore di scrittura di Azure è una funzionalità disponibile esclusivamente per le VM di Azure della serie M. Come indicato dal nome, lo scopo della funzionalità è migliorare la latenza di I/O delle scritture per l'Archiviazione Premium di Azure. Per SAP HANA, l'uso dell'acceleratore di scrittura è previsto solo sul volume **/hana/log**. Pertanto, **/hana/data** e **/hana/log** sono volumi separati con l'acceleratore di scrittura di Azure che supporta solo il volume **/hana/log**. 

> [!IMPORTANT]
> Quando si usa Archiviazione Premium di Azure, l'uso dell'[acceleratore di scrittura](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator) di Azure o del volume /hana/log è obbligatorio. L'acceleratore di scrittura è disponibile solo per Archiviazione Premium e per le VM della serie M e Mv2.

Gli elementi consigliati sulla memorizzazione nella cache riportati di seguito presuppongono caratteristiche di I/O per SAP HANA come:

- Non esiste quasi nessun carico di lavoro in lettura sui file di dati HANA, ad eccezione degli I/O di grandi dimensioni dopo il riavvio dell'istanza di HANA o quando i dati vengono caricati in HANA. Un altro caso di I/O in lettura di dimensioni maggiori sui file di dati è rappresentato dai backup di database HANA. Di conseguenza spesso la lettura della cache non ha senso in quanto, nella maggior parte dei casi, tutti i volumi dei file di dati devono essere letti completamente.
- La scrittura sui file di dati si verifica in burst in base ai punti di salvataggio HANA e al ripristino da arresto anomalo del sistema HANA. La scrittura di punti di salvataggio è un'operazione asincrona e non impedisce le transazioni dell'utente. La scrittura di dati durante il ripristino da arresto anomalo del sistema è un'operazione critica a livello di prestazioni per ottenere che il sistema risponda di nuovo rapidamente. Tuttavia, il ripristino da arresto anomalo del sistema dovrebbe essere un caso piuttosto eccezionale.
- Esistono pochissime operazioni di lettura dai file di rollforward HANA. Le eccezioni sono gli I/O di grandi dimensioni quando si eseguono backup dei log di transazioni, il ripristino da arresto anomalo del sistema o nella fase di riavvio di un'istanza HANA.  
- Il carico principale sul file di log di rollforward SAP HANA è rappresentato delle operazioni di scrittura. A seconda del tipo di carico di lavoro, è possibile che le operazioni di I/O siano di piccole dimensioni, ad esempio 4 kB o, in altri casi, di almeno 1 MB. La latenza di scrittura rispetto al log di rollforward SAP HANA è critica dal punto di vista delle prestazioni.
- Tutti i dati delle scritture devono essere salvati in modo permanente su disco in modo affidabile.

**Raccomandazione: tenendo conto di questi criteri di I/O osservati da SAP HANA, la memorizzazione nella cache per i diversi volumi tramite Archiviazione Premium di Azure deve essere impostata nel modo seguente:**

- **/hana/data**: nessuna memorizzazione nella cache
- **/hana/log**: nessuna memorizzazione nella cache ad eccezione della serie M e Mv2 in cui è necessario abilitare l'acceleratore di scrittura senza la memorizzazione nella cache di lettura. 
- **/hana/shared**: lettura della cache

### <a name="production-recommended-storage-solution"></a>Soluzione di archiviazione consigliata per la produzione

> [!IMPORTANT]
> La certificazione SAP HANA per le macchine virtuali di Azure della serie M è valida esclusivamente con l'acceleratore di scrittura di Azure per il volume **/hana/log**. Di conseguenza, per le distribuzioni di SAP HANA in scenari di produzione nelle macchine virtuali di Azure della serie M è prevista la configurazione con l'acceleratore di scrittura di Azure per il volume **/hana/log**.  

> [!NOTE]
> Per gli scenari di produzione, controllare se un determinato tipo di macchina virtuale è supportato da SAP per SAP HANA nella [documentazione SAP per IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).

Le raccomandazioni spesso superano i requisiti minimi di SAP indicati in precedenza in questo articolo. Le raccomandazioni elencate costituiscono un compromesso tra le raccomandazioni di SAP relative alle dimensioni e la velocità effettiva massima di archiviazione offerta dai diversi tipi di VM.

**Raccomandazione: le configurazioni consigliate per gli scenari di produzione sono simili alle seguenti:**

| SKU di VM | RAM | Max. velocità effettiva<br /> Velocità effettiva | /hana/data | /hana/log | /hana/shared | volume /root | /usr/sap | hana/backup |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| M32ts | 192 GiB | 500 MB/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M32ls | 256 GiB | 500 MB/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M64ls | 512 GiB | 1000 MB/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P30 |
| M64s | 1000 GiB | 1000 MB/s | 4 x P20 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 |2 x P30 |
| M64ms | 1750 GiB | 1000 MB/s | 3 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 3 x P30 |
| M128s | 2000 GiB | 2000 MB/s |3 x P30 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 2 x P40 |
| M128ms | 3800 GiB | 2000 MB/s | 5 x P30 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 4 x P40 |
| M208s_v2 | 2850 GiB | 1000 MB/s | 4 x P30 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 3 x P40 |
| M208ms_v2 | 5700 GiB | 1000 MB/s | 4 x P40 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 3 x P50 |
| M416s_v2 | 5700 GiB | 2000 MB/s | 4 x P40 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 3 x P50 |
| M416ms_v2 | 11400 GiB | 2000 MB/s | 8 x P40 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 4 x P50 |

Controllare se la velocità effettiva di archiviazione per i diversi volumi suggeriti soddisfa i requisiti del carico di lavoro che si vuole eseguire. Se il carico di lavoro richiede volumi più elevati per **/hana/data** e **/hana/log**, è necessario aumentare il numero di dischi rigidi virtuali di Archiviazione Premium di Azure. Il dimensionamento di un volume con più dischi rigidi virtuali di quelli elencati consente di aumentare le operazioni di I/O al secondo e la velocità effettiva di I/O entro i limiti del tipo di macchina virtuale di Azure.

L'acceleratore di scrittura di Azure funziona solo in combinazione con [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/). Almeno i dischi di Archiviazione Premium di Azure che costituiscono il volume **/hana/log** devono quindi essere distribuiti come dischi gestiti.

Esistono limiti per il numero di dischi rigidi virtuali di Archiviazione Premium di Azure per ogni macchina virtuale supportati dall'acceleratore di scrittura di Azure. I limiti correnti sono:

- 16 dischi rigidi virtuali per una VM M128xx e M416xx
- 8 dischi rigidi virtuali per una VM M64xx e M208xx
- 4 dischi rigidi virtuali per una macchina virtuale M32xx

Istruzioni più dettagliate su come abilitare l'acceleratore di scrittura di Azure sono disponibili nell'articolo [Acceleratore di scrittura](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator).

La stessa documentazione include anche informazioni dettagliate e sulle restrizioni per l'acceleratore di scrittura di Azure.

**Raccomandazione: è necessario usare l'acceleratore di scrittura per i dischi che formano il volume /hana/log**


### <a name="cost-conscious-azure-storage-configuration"></a>Configurazione di Archiviazione di Azure tenendo conto dei costi
La tabella seguente illustra una configurazione di tipi di macchine virtuali comunemente usati dai clienti per ospitare SAP HANA in VM di Azure. Potrebbero essere presenti alcuni tipi di VM che non soddisfano tutti i criteri minimi per SAP HANA o non sono ufficialmente supportati con SAP HANA da SAP, anche se offrono prestazioni adeguate per scenari non di produzione. **/hana/data** e **/hana/log** vengono combinati in un solo volume. Di conseguenza, l'uso dell'acceleratore di scrittura di Azure può diventare una limitazione in termini di operazioni di I/O al secondo.

> [!NOTE]
> Per gli scenari SAP supportati, controllare se un determinato tipo di VM è supportato da SAP per SAP HANA nella [documentazione SAP per IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).

> [!NOTE]
> Una modifica rispetto alle raccomandazioni precedenti per una soluzione economica consiste nel passare dai [dischi HDD Standard di Azure](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-hdd) ai [dischi SDD Standard di Azure](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-ssd) per prestazioni migliori e più affidabili

Le raccomandazioni spesso superano i requisiti minimi di SAP indicati in precedenza in questo articolo. Le raccomandazioni elencate costituiscono un compromesso tra le raccomandazioni di SAP relative alle dimensioni e la velocità effettiva massima di archiviazione offerta dai diversi tipi di VM.

| SKU di VM | RAM | Max. velocità effettiva<br /> Velocità effettiva | /hana/data and /hana/log<br /> con striping con LVM o MDADM | /hana/shared | volume /root | /usr/sap | hana/backup |
| --- | --- | --- | --- | --- | --- | --- | -- |
| DS14v2 | 112 GiB | 768 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E15 |
| E16v3 | 128 GiB | 384 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E15 |
| E32v3 | 256 GiB | 768 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| E64v3 | 432 GiB | 1\.200 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E30 |
| GS5 | 448 GiB | 2\.000 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E30 |
| M32ts | 192 GiB | 500 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| M32ls | 256 GiB | 500 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| M64ls | 512 GiB | 1\.000 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 |1 x E30 |
| M64s | 1\.000 GiB | 1\.000 MB/s | 2 x P30 | 1 x E30 | 1 x E6 | 1 x E6 |2 x E30 |
| M64ms | 1\.750 GiB | 1\.000 MB/s | 3 x P30 | 1 x E30 | 1 x E6 | 1 x E6 | 3 x E30 |
| M128s | 2\.000 GiB | 2\.000 MB/s |3 x P30 | 1 x E30 | 1 x E10 | 1 x E6 | 2 x E40 |
| M128ms | 3\.800 GiB | 2\.000 MB/s | 5 x P30 | 1 x E30 | 1 x E10 | 1 x E6 | 2 x E50 |
| M208s_v2 | 2\.850 GiB | 1\.000 MB/s | 4 x P30 | 1 x E30 | 1 x E10 | 1 x E6 |  3 x E40 |
| M208ms_v2 | 5\.700 GiB | 1\.000 MB/s | 4 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E40 |
| M416s_v2 | 5\.700 GiB | 2\.000 MB/s | 4 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E40 |
| M416ms_v2 | 11.400 GiB | 2\.000 MB/s | 8 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E50 |


I dischi consigliati per i tipi di macchine virtuali più piccoli con 3 x P20 superano in dimensioni i volumi riguardanti le indicazioni di spazio contenute nel [white paper sull'archiviazione di SAP TDI](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html). Tuttavia, la scelta indicata nella tabella è stata effettuata per offrire una velocità effettiva del disco sufficiente per SAP HANA. Se è necessario apportare modifiche al volume **/hana/backup**, che è stato ridimensionato per mantenere backup che rappresentano il doppio del volume di memoria, procedere liberamente.   
Controllare se la velocità effettiva di archiviazione per i diversi volumi suggeriti soddisfa i requisiti del carico di lavoro che si vuole eseguire. Se il carico di lavoro richiede volumi più elevati per **/hana/data** e **/hana/log**, è necessario aumentare il numero di dischi rigidi virtuali di Archiviazione Premium di Azure. Il dimensionamento di un volume con più dischi rigidi virtuali di quelli elencati consente di aumentare le operazioni di I/O al secondo e la velocità effettiva di I/O entro i limiti del tipo di macchina virtuale di Azure. 

> [!NOTE]
> Le configurazioni sopra indicate NON traggono vantaggio dal [contratto di servizio per macchine virtuali con istanza singola di macchina virtuale di Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/) perché usa in combinazione Archiviazione Premium di Azure e Archiviazione Standard di Azure. Tuttavia, la selezione è stata scelta per ottimizzare i costi. È necessario scegliere Archiviazione Premium per tutti i dischi sopra quello elencato come Archiviazione di Azure Standard (Sxx) per rendere la configurazione della macchina virtuale conforme al contratto di servizio per singole macchine virtuali di Azure.


> [!NOTE]
> I suggerimenti sulla configurazione del disco forniti fanno riferimento ai requisiti minimi definiti da SAP per i provider di infrastruttura. Nelle distribuzioni effettive dei clienti e negli scenari con carichi di lavoro reali, tuttavia, si sono verificate situazioni in cui questi suggerimenti non garantivano funzionalità sufficienti. Queste situazioni si possono verificare quando un cliente richiede un ricaricamento più veloce dei dati dopo un riavvio HANA o se una configurazione di backup richiede una larghezza di banda maggiore per l'archiviazione. Altri casi includono, ad esempio, percorsi **/hana/log** in cui 5000 IOPS non sono sufficienti per un carico di lavoro specifico. Considerare quindi queste indicazioni come punto di inizio e adattarle in base ai requisiti del carico di lavoro.
>  

## <a name="azure-ultra-disk-storage-configuration-for-sap-hana"></a>Configurazione dell'archiviazione su disco Ultra di Azure per SAP HANA
Microsoft sta implementando un nuovo tipo di archiviazione di Azure denominato [disco Ultra di Azure](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#ultra-disk). La differenza significativa tra l'archiviazione di Azure offerta fino a questo momento e il disco Ultra è che le capacità del disco non sono più associate alle sue dimensioni. I clienti possono definire le capacità seguenti per il disco Ultra:

- Dimensioni di un disco compreso tra 4 GiB e 65.536 GiB
- Intervallo di operazioni di I/O al secondo da 100 a 160.000 (il valore massimo dipende anche dai tipi di VM)
- Velocità effettiva di archiviazione da 300 MB/sec a 2.000 MB/sec

Il disco Ultra consente di definire un singolo disco che soddisfi le dimensioni, le operazioni di I/O al secondo e l'intervallo di velocità effettiva del disco. Anziché usare gestori di volumi logici come LVM o MDADM in Archiviazione Premium di Azure per costruire volumi che soddisfino i requisiti di operazioni di I/O al secondo e velocità effettiva di archiviazione, è possibile eseguire una combinazione di configurazioni tra il disco Ultra e Archiviazione Premium. Di conseguenza, è possibile limitare l'utilizzo del disco Ultra ai volumi /hana/data e /hana/log critici per le prestazioni e coprire gli altri volumi con Archiviazione Premium di Azure.

Altri vantaggi offerti dal disco Ultra possono essere la latenza di lettura migliore rispetto ad Archiviazione Premium. La latenza di lettura più veloce può avere vantaggi quando si vogliono ridurre i tempi di avvio di HANA e il caricamento dei dati in memoria. I vantaggi dell'archiviazione nel disco Ultra si hanno anche quando HANA scrive punti di salvataggio. Poiché i dischi di Archiviazione Premium per /hana/data non sono in genere memorizzati nella cache dell'acceleratore di scrittura, la latenza di scrittura in /hana/data in Archiviazione Premium rispetto al disco Ultra è superiore. Si può prevedere che la scrittura di punti di salvataggio con il disco Ultra abbia prestazioni migliori.

> [!IMPORTANT]
> Il disco Ultra non è ancora presente in tutte le aree di Azure e non supporta ancora tutti i tipi di VM elencati di seguito. Per informazioni dettagliate sulla disponibilità del disco Ultra e sulle famiglie di VM supportate, vedere l'articolo [Tipi di disco disponibili in Azure](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#ultra-disk).

### <a name="production-recommended-storage-solution-with-pure-ultra-disk-configuration"></a>Soluzione di archiviazione consigliata per la produzione con configurazione disco Ultra puro
In questa configurazione i volumi /hana/data e /hana/log vengono conservati separatamente. I valori suggeriti sono derivati dagli indicatori KPI che SAP ha per certificare i tipi di VM per le configurazioni SAP HANA e di archiviazione come consigliato nel documento [SAP TDI Storage Whitepaper](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) (Withepaper sull'archiviazione SAP TDI).

Le raccomandazioni spesso superano i requisiti minimi di SAP indicati in precedenza in questo articolo. Le raccomandazioni elencate costituiscono un compromesso tra le raccomandazioni di SAP relative alle dimensioni e la velocità effettiva massima di archiviazione offerta dai diversi tipi di VM.

| SKU di VM | RAM | Max. velocità effettiva<br /> Velocità effettiva | Volume /hana/data | Velocità effettiva di I/O /hana/data | Operazioni di I/O al secondo /hana/data | Volume /hana/log | Velocità effettiva di I/O /hana/log | Operazioni di I/O al secondo /hana/log |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| E64s_v3 | 432 GiB | 1\.200 MB/s | 600 GB | 700 MBps | 7\.500 | 512 GB | 500 MBps  | 2\.000 |
| M32ts | 192 GiB | 500 MB/s | 250 GB | 400 MBps | 7\.500 | 256 GB | 250 MBps  | 2\.000 |
| M32ls | 256 GiB | 500 MB/s | 300 GB | 400 MBps | 7\.500 | 256 GB | 250 MBps  | 2\.000 |
| M64ls | 512 GiB | 1\.000 MB/s | 600 GB | 600 MBps | 7\.500 | 512 GB | 400 MBps  | 2\.500 |
| M64s | 1\.000 GiB | 1\.000 MB/s |  1\.200 GB | 600 MBps | 7\.500 | 512 GB | 400 MBps  | 2\.500 |
| M64ms | 1\.750 GiB | 1\.000 MB/s | 2\.100 GB | 600 MBps | 7\.500 | 512 GB | 400 MBps  | 2\.500 |
| M128s | 2\.000 GiB | 2\.000 MB/s |2\.400 GB | 1\.200 MBps |9000 | 512 GB | 800 MBps  | 3,000 | 
| M128ms | 3\.800 GiB | 2\.000 MB/s | 4\.800 GB | 1\.200 MBps |9000 | 512 GB | 800 MBps  | 3,000 | 
| M208s_v2 | 2\.850 GiB | 1\.000 MB/s | 3\.500 GB | 1\.000 MBps | 9000 | 512 GB | 400 MBps  | 2\.500 | 
| M208ms_v2 | 5\.700 GiB | 1\.000 MB/s | 7\.200 GB | 1\.000 MBps | 9000 | 512 GB | 400 MBps  | 2\.500 | 
| M416s_v2 | 5\.700 GiB | 2\.000 MB/s | 7\.200 GB | 1\.500 MBps | 9000 | 512 GB | 800 MBps  | 3,000 | 
| M416ms_v2 | 11.400 GiB | 2\.000 MB/s | 14.400 GB | 1\.500 MBps | 9000 | 512 GB | 800 MBps  | 3,000 |   

I valori elencati sono intesi come punto di partenza e devono essere valutati in base alle esigenze effettive. Il vantaggio del disco Ultra di Azure è che i valori per operazioni di I/O al secondo e velocità effettiva possono essere adattati senza la necessità di arrestare la VM o il carico di lavoro applicato al sistema.   

> [!NOTE]
> Al momento gli snapshot di archiviazione con l'archiviazione su disco Ultra non sono disponibili. Questo impedisce l'uso degli snapshot delle VM con i servizi di Backup di Azure

### <a name="cost-conscious-storage-solution-with-pure-ultra-disk-configuration"></a>Soluzione di archiviazione a costi contenuti con configurazione disco Ultra puro
In questa configurazione i volumi /hana/data e /hana/log vengono conservati nello stesso disco. I valori suggeriti sono derivati dagli indicatori KPI che SAP ha per certificare i tipi di VM per le configurazioni SAP HANA e di archiviazione come consigliato nel documento [SAP TDI Storage Whitepaper](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) (Withepaper sull'archiviazione SAP TDI). 

Le raccomandazioni spesso superano i requisiti minimi di SAP indicati in precedenza in questo articolo. Le raccomandazioni elencate costituiscono un compromesso tra le raccomandazioni di SAP relative alle dimensioni e la velocità effettiva massima di archiviazione offerta dai diversi tipi di VM.

| SKU di VM | RAM | Max. velocità effettiva<br /> Velocità effettiva | Volume per /hana/data e /log | Velocità effettiva di I/O /hana/data e log | Operazioni di I/O al secondo /hana/data e log |
| --- | --- | --- | --- | --- | --- |
| E64s_v3 | 432 GiB | 1\.200 MB/s | 1\.200 GB | 1\.200 MBps | 9\.500 | 
| M32ts | 192 GiB | 500 MB/s | 512 GB | 400 MBps | 9\.500 | 
| M32ls | 256 GiB | 500 MB/s | 600 GB | 400 MBps | 9\.500 | 
| M64ls | 512 GiB | 1\.000 MB/s | 1\.100 GB | 900 MBps | 10,000 | 
| M64s | 1\.000 GiB | 1\.000 MB/s |  1\.700 GB | 900 MBps | 10,000 | 
| M64ms | 1\.750 GiB | 1\.000 MB/s | 2\.600 GB | 900 MBps | 10,000 | 
| M128s | 2\.000 GiB | 2\.000 MB/s |2\.900 GB | 1\.800 Mbps |12000 | 
| M128ms | 3\.800 GiB | 2\.000 MB/s | 5\.300 GB | 1\.800 Mbps |12000 |  
| M208s_v2 | 2\.850 GiB | 1\.000 MB/s | 4\.000 GB | 900 MBps | 10,000 |  
| M208ms_v2 | 5\.700 GiB | 1\.000 MB/s | 7\.700 GB | 900 MBps | 10,000 | 
| M416s_v2 | 5\.700 GiB | 2\.000 MB/s | 7\.700 GB | 1\.800 MBps | 12000 |  
| M416ms_v2 | 11.400 GiB | 2\.000 MB/s | 15.000 GB | 1\.800 Mbps | 12000 |    

I valori elencati sono intesi come punto di partenza e devono essere valutati in base alle esigenze effettive. Il vantaggio del disco Ultra di Azure è che i valori per operazioni di I/O al secondo e velocità effettiva possono essere adattati senza la necessità di arrestare la VM o il carico di lavoro applicato al sistema.  

## <a name="nfs-v41-volumes-on-azure-netapp-files"></a>Volumi NFS v4.1 in Azure NetApp Files
Azure NetApp Files offre condivisioni NFS native che possono essere usate per i volumi /hana/shared, /hana/data e /hana/log. L'uso di condivisioni NFS basate su Azure NetApp Files per i volumi /hana/data e /hana/log richiede l'utilizzo del protocollo NFS v4.1. Il protocollo NFS v3 non è supportato per l'utilizzo di volumi /hana/data e /hana/log quando le condivisioni si basano su Azure NetAppFiles. 

> [!IMPORTANT]
> Il protocollo NFS v3 implementato in Azure NetApp Files **non** è supportato per l'uso con /hana/data e /hana/log. L'utilizzo di NFS 4.1 è obbligatorio per i volumi /hana/data e /hana/log da un punto di vista funzionale. Mentre per il volume /hana/shared il protocollo NFS v3 o NFS v4.1 può essere usato da un punto di vista funzionale.

### <a name="important-considerations"></a>Considerazioni importanti
Quando si prende in considerazione Azure NetApp Files per SAP NetWeaver e SAP HANA, tenere presente le considerazioni importanti seguenti:

- La capacità minima del pool è di 4 TiB.  
- Le dimensioni minime del volume sono di 100 GiB.
- Azure NetApp Files e tutte le macchine virtuali in cui verranno montati i volumi Azure NetApp Files devono trovarsi nella stessa Rete virtuale di Azure o in [reti virtuali con peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) nella stessa area.  
- La rete virtuale selezionata deve avere una subnet delegata ad Azure NetApp Files.
- La velocità effettiva di un volume di Azure NetApp è una funzione della quota del volume e del livello di servizio, come documentato in [Livelli di servizio per Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels). Quando si ridimensionano i volumi di Azure NetApp di HANA, verificare che la velocità effettiva risultante soddisfi i requisiti di sistema HANA.  
- Azure NetApp Files offre [criteri di esportazione](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-configure-export-policy): è possibile controllare i client consentiti, il tipo di accesso, come ad esempio lettura e scrittura, sola lettura e così via. 
- La funzionalità Azure NetApp Files non è ancora in grado di riconoscere la zona. Attualmente la funzionalità Azure NetApp Files non viene distribuita in tutte le zone di disponibilità in un'area di Azure. Tenere presente le implicazioni di latenza potenziali in alcune aree di Azure.  
- È importante che le macchine virtuali vengano distribuite in prossimità dell'archiviazione di Azure NetApp per una bassa latenza. 
- L'ID utente per <b>sid</b>adm e l'ID gruppo per `sapsys` nelle macchine virtuali devono corrispondere alla configurazione in Azure NetApp Files. 

> [!IMPORTANT]
> Per carichi di lavoro SAP HANA, è fondamentale una bassa latenza. Collaborare con il rappresentante Microsoft per assicurarsi che le macchine virtuali e i volumi Azure NetApp Files vengano distribuiti in prossimità.  

> [!IMPORTANT]
> Se non vi è corrispondenza tra l'ID utente per <b>sid</b>adm e l'ID gruppo per `sapsys` tra la macchina virtuale e la configurazione di Azure NetApp, le autorizzazioni per i file nei volumi Azure NetApp, montati in macchine virtuali, verranno visualizzate come `nobody`. Assicurarsi di specificare l'ID utente corretto per <b>sid</b>adm e l'ID gruppo per `sapsys` quando [si acquisisce un nuovo sistema](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxjSlHBUxkJBjmARn57skvdUQlJaV0ZBOE1PUkhOVk40WjZZQVJXRzI2RC4u) in Azure NetApp Files.

### <a name="sizing-for-hana-database-on-azure-netapp-files"></a>Dimensionamento per il database HANA in Azure NetApp Files

La velocità effettiva di un volume di Azure NetApp è una funzione delle dimensioni del volume e del livello di servizio, come documentato in [Livelli di servizio per Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels). 

Quando si progetta l'infrastruttura per SAP in Azure, è necessario conoscere alcuni requisiti minimi di velocità effettiva di archiviazione da parte di SAP, che si traduce nelle caratteristiche minime di velocità effettiva seguenti:

- Abilitare lettura/scrittura in /hana/log per 250 MB/sec con dimensioni di I/O di 1 MB  
- Abilitare l'attività di lettura per almeno 400 MB/sec per /hana/data con dimensioni di I/O di 16 MB e 64 MB  
- Abilitare l'attività di scrittura per almeno 250 MB/sec per /hana/data con dimensioni di I/O di 16 MB e 64 MB  

I [limiti di velocità effettiva di Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels) per 1 TiB della quota del volume sono:
- Livello Archiviazione Premium: 64 MiB/s  
- Livello di archiviazione Ultra: 128 MiB/s  

> [!IMPORTANT]
> Indipendentemente dalla capacità che si distribuisce in un singolo volume NFS, la velocità effettiva è prevista per un livello di larghezza di banda compreso tra 1,2 e 1,4 GB/sec, usata da un consumer in una macchina virtuale. Questo dipende dall'architettura sottostante dell'offerta di Azure NetApp Files e dai limiti della sessione Linux correlata in base a NFS. I numeri relativi a prestazioni e velocità effettiva, come documentato nell'articolo [Risultati dei test di benchmark delle prestazioni per Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/performance-benchmarks-linux) sono stati eseguiti su un volume NFS condiviso con più macchine virtuali client e, di conseguenza, più sessioni. Questo scenario è diverso dallo scenario misurato in SAP dove misuriamo la velocità effettiva da una singola VM a un volume NFS ospitato in Azure NetApp Files.

Per soddisfare i requisiti di velocità effettiva minima SAP per dati e log e in base alle linee guida per `/hana/shared`, le dimensioni consigliate sono le seguenti:

| Volume | Dimensione<br /> Livello Archiviazione Premium | Dimensione<br /> Livello Archiviazione Ultra | Protocollo NFS supportato |
| --- | --- | --- |
| /hana/log/ | 4 TiB | 2 TiB | v4.1 |
| /hana/data | 6,3 TiB | 3,2 TiB | v4.1 |
| /hana/shared | Massima (512 GB, 1xRAM) per 4 nodi di lavoro | Massima (512 GB, 1xRAM) per 4 nodi di lavoro | v3 o v4.1 |


> [!NOTE]
> Le raccomandazioni per il dimensionamento di Azure NetApp Files fanno riferimento ai requisiti minimi definiti da SAP per i provider di infrastruttura. Nelle distribuzioni reali dei clienti e negli scenari di carico di lavoro, questo potrebbe non essere sufficiente. Considerare quindi queste indicazioni come punto di inizio e adattarle in base ai requisiti del carico di lavoro specifico.  

È pertanto possibile prendere in considerazione la distribuzione di una velocità effettiva simile per i volumi Azure NetApp Files, come elencato già per l'archiviazione su disco Ultra. Prendere in considerazione anche le dimensioni elencate per i volumi per i diversi SKU di VM, come già fatto nelle tabelle del disco Ultra.

> [!TIP]
> È possibile ridimensionare i volumi Azure NetApp Files in modo dinamico, senza dover eseguire l'operazione `unmount` per i volumi, arrestare le macchine virtuali o SAP HANA. Questo consente la flessibilità di soddisfare le esigenze di velocità effettiva previste e non previste per le applicazioni.

La documentazione su come distribuire una configurazione con scalabilità orizzontale SAP HANA con un nodo standby usando i volumi NFS v4.1 ospitati in Azure NetApp Files è pubblicata in [Distribuire un sistema di SAP HANA con scalabilità orizzontale con un nodo standby in macchine virtuali di Azure usando Azure NetApp Files su SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse).


## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni, vedere:

- [Guida alla disponibilità elevata di SAP HANA per macchine virtuali di Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview).
