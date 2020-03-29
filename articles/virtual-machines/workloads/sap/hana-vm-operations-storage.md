---
title: Configurazioni di archiviazione della macchina virtuale SAP HANA Azure - Documenti Microsoft
description: Consigli di archiviazione per le macchine virtuali in cui è distribuito SAP HANA.
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
ms.date: 03/10/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4b469c098db4f8d90147b491bcb54bd55d326b03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79080309"
---
# <a name="sap-hana-azure-virtual-machine-storage-configurations"></a>Configurazioni dell'archiviazione di macchine virtuali di Azure in SAP HANA

Azure offre diversi tipi di archiviazione adatti alle macchine virtuali di Azure che eseguono SAP HANA. I tipi di **archiviazione di Azure certificati SAP HANA** che possono essere considerati per le distribuzioni SAP HANA elencano come: 

- SSD Premium di AzureAzure Premium SSD  
- [Disco Ultra](https://docs.microsoft.com/azure/virtual-machines/linux/disks-enable-ultra-ssd)
- [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) 

Per informazioni su questi tipi di disco, vedere l'articolo [Selezionare un tipo di disco](https://docs.microsoft.com/azure/virtual-machines/linux/disks-types)

Azure offre due metodi di distribuzione per VHD su Archiviazione Standard e Premium di Azure. Se lo scenario lo permette, sfruttare i vantaggi offerti dalle distribuzioni di [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/). 

Per un elenco dei tipi di archiviazione e dei relativi contratti di servizio per operazioni di I/O al secondo e velocità effettiva di archiviazione, vedere la [documentazione di Azure per Managed Disks](https://azure.microsoft.com/pricing/details/managed-disks/).

> [!IMPORTANT]
> Indipendentemente dal tipo di archiviazione di Azure scelto, il file system usato in tale archiviazione deve essere supportato da SAP per il sistema operativo e il dbMS specifici. [Nota #405827 del supporto SAP](https://launchpad.support.sap.com/#/notes/405827) elenca i file system supportati per diversi sistemi operativi e database, incluso SAP HANA. Questo vale per tutti i volumi a cui SAP HANA potrebbe accedere per la lettura e la scrittura per qualsiasi attività. In particolare utilizzando NFS in Azure per SAP HANA, si applicano ulteriori restrizioni delle versioni di NFS come indicato più avanti in questo articolo 


Le condizioni minime certificate SAP HANA per i diversi tipi di storage sono: 

- Azure Premium SSD- /hana/log deve essere memorizzato nella cache con Azure [Write Accelerator](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator). Il volume /hana/data potrebbe essere inserito in SSD Premium senza Azure Write Accelerator o su disco Ultra
- Disco Ultra di Azure almeno per il volume /hana/log. Il volume /hana/data può essere inserito in un SSD Premium senza Azure Write Accelerator o per ottenere tempi di riavvio più rapidi Disco ultra
- I volumi **NFS v4.1** si uniranno ai file NetApp di Azure per /hana/log **e** /hana/data. Il volume di /hana/shared può utilizzare il protocollo NFS v3 o NFS v4.1. Il protocollo NFS v4.1 è obbligatorio per i volumi /hana/data e/hana/log.

Alcuni dei tipi di archiviazione possono essere combinati. Ad esempio, è possibile inserire /hana/data in Premium Storage e /hana/log possono essere inseriti nello spazio di archiviazione su disco Ultra per ottenere la bassa latenza richiesta. Tuttavia, se si utilizza un volume NFS v4.1 basato su ANF per /hana/data, è necessario utilizzare un altro volume NFS v4.1 basato su ANF per /hana/log. L'utilizzo di NFS su ANF per uno dei volumi (ad esempio /hana/data) e Azure Premium Storage o Disco Ultra per l'altro volume (ad esempio /hana/log) non è **supportato.**

Nel mondo locale, raramente era necessario preoccuparsi dei sottosistemi di I/O e delle relative funzionalità. perché il fornitore dell'appliance deve assicurarsi che siano soddisfatti i requisiti di archiviazione minima per SAP HANA. Quando si crea manualmente l'infrastruttura di Azure, è necessario essere a conoscenza di alcuni di questi requisiti emessi da SAP. Alcune delle caratteristiche di velocità effettiva minima richieste da SAP comportano la necessità di:

- Abilita lettura/scrittura su **/hana/log** di un valore 250 MB/sec con dimensioni di I/O di 1 MB
- Abilitare l'attività di lettura per minimo 400 MB/sec per **/hana/data** con dimensioni di I/O di 16 MB e 64 MB
- Abilitare l'attività di scrittura per minimo 250 MB/sec per **/hana/data** con dimensioni di I/O di 16 MB e 64 MB

Detto questo, una bassa latenza di archiviazione è fondamentale per i sistemi DBMS, anche perché i sistemi DBMS, come SAP HANA, mantengono dati in memoria. Il percorso critico per l'archiviazione riguarda in genere le scritture nel log delle transazioni dei sistemi DBMS. Ma possono essere critiche anche operazioni come la scrittura di punti di salvataggio o il caricamento di dati in memoria dopo il ripristino da un arresto anomalo del sistema. Pertanto, è **obbligatorio** sfruttare i dischi Premium di Azure per i volumi **/hana/data** e **/hana/log.** Per ottenere la velocità effettiva minima di **/hana/log** e **/hana/data** come richiesto da SAP, è necessario configurare un sistema RAID 0 tramite MDADM o LVM su più dischi di Archiviazione Premium di Azure e usare i volumi RAID come volumi **/hana/data** e **/hana/log**. 

**Consiglio: Come stripe dimensioni per il RAID 0 la raccomandazione è quella di utilizzare:**

- 256 KB per **/hana/dati**
- 32 KB per **/hana/log**

> [!IMPORTANT]
> La dimensione dello stripe per /hana/dati è stata modificata rispetto alle raccomandazioni precedenti che richiedevano 64 KB o 128 KB a 256 KB in base alle esperienze dei clienti con versioni Linux più recenti. La dimensione di 256 KB fornisce prestazioni leggermente migliori

> [!NOTE]
> Non è necessario configurare alcun livello di ridondanza con i volumi RAID perché l'Archiviazione Standard e Premium di Azure mantiene tre immagini di un disco rigido virtuale. L'utilizzo di un volume RAID è richiesto esclusivamente per configurare volumi in grado di offrire una velocità effettiva di I/O sufficiente.

Accumulare un certo numero di dischi rigidi virtuali di Azure in una configurazione RAID consente di ottenere valori cumulativi per le operazioni di I/O al secondo e la velocità effettiva di archiviazione. Pertanto, se si configura un sistema RAID 0 su 3 dischi di Archiviazione Premium di Azure P30, si dovrebbero ottenere livelli triplicati di operazioni di I/O al secondo e velocità effettiva di archiviazione rispetto a un singolo disco di Archiviazione Premium di Azure P30.

Durante il ridimensionamento o quando si sceglie una macchina virtuale, tenere presente anche la velocità effettiva di I/O complessiva delle macchine virtuali. Per informazioni sulla velocità effettiva di archiviazione complessiva delle macchine virtuali, vedere l'articolo [Dimensioni delle macchine virtuali ottimizzate per la memoria](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory).

## <a name="linux-io-scheduler-mode"></a>Modalità di pianificazione I/O Linux
Linux offre varie modalità di pianificazione I/O diverse. Consigli comuni tramite fornitori Linux e SAP è quello di riconfigurare la modalità di pianificazione I/O per i volumi del disco dalla modalità **cfq** al **noop** (non multicoda) o **nessuno** per la modalità (multiqueue). I dettagli sono indicati in [SAP Note #1984787](https://launchpad.support.sap.com/#/notes/1984787). 


## <a name="solutions-with-premium-storage-and-azure-write-accelerator-for-azure-m-series-virtual-machines"></a>Soluzioni con archiviazione Premium e acceleratore di scrittura di Azure per macchine virtuali della serie M di AzureSolutions with Premium Storage and Azure Write Accelerator for Azure M-Series virtual machines
Azure Write Accelerator è una funzionalità disponibile esclusivamente per le macchine virtuali di Azure Serie M.Azure Write Accelerator is a functionality that is available for Azure M-Series VMs exclusively. Come indicato dal nome, lo scopo della funzionalità è migliorare la latenza di I/O delle scritture per l'Archiviazione Premium di Azure. Per SAP HANA, l'uso dell'acceleratore di scrittura è previsto solo sul volume **/hana/log**. Pertanto, **/hana/data** e **/hana/log** sono volumi separati con Azure Write Accelerator che supporta solo il volume **/hana/log.** 

> [!IMPORTANT]
> Quando si usa Archiviazione Premium di Azure, l'utilizzo di Azure [Write Accelerator](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator) o del volume /hana/log è obbligatorio. Write Accelerator è disponibile solo per le macchine virtuali Premium Storage e M-Series e Mv2-Series.

Gli elementi consigliati sulla memorizzazione nella cache riportati di seguito presuppongono caratteristiche di I/O per SAP HANA come:

- Non esiste quasi nessun carico di lavoro in lettura sui file di dati HANA, ad eccezione degli I/O di grandi dimensioni dopo il riavvio dell'istanza di HANA o quando i dati vengono caricati in HANA. Un altro caso di I/O in lettura di dimensioni maggiori sui file di dati è rappresentato dai backup di database HANA. Di conseguenza spesso la lettura della cache non ha senso in quanto, nella maggior parte dei casi, tutti i volumi dei file di dati devono essere letti completamente.
- La scrittura sui file di dati si verifica in burst in base ai punti di salvataggio HANA e al ripristino da arresto anomalo del sistema HANA. La scrittura di punti di salvataggio è un'operazione asincrona e non impedisce le transazioni dell'utente. La scrittura di dati durante il ripristino da arresto anomalo del sistema è un'operazione critica a livello di prestazioni per ottenere che il sistema risponda di nuovo rapidamente. Tuttavia, il ripristino da arresto anomalo del sistema dovrebbe essere un caso piuttosto eccezionale.
- Esistono pochissime operazioni di lettura dai file di rollforward HANA. Le eccezioni sono gli I/O di grandi dimensioni quando si eseguono backup dei log di transazioni, il ripristino da arresto anomalo del sistema o nella fase di riavvio di un'istanza HANA.  
- Il carico principale sul file di log di rollforward SAP HANA è rappresentato delle operazioni di scrittura. A seconda del tipo di carico di lavoro, è possibile che le operazioni di I/O siano di piccole dimensioni, ad esempio 4 kB o, in altri casi, di almeno 1 MB. La latenza di scrittura rispetto al log di rollforward SAP HANA è critica dal punto di vista delle prestazioni.
- Tutti i dati delle scritture devono essere salvati in modo permanente su disco in modo affidabile.

**Consiglio: come risultato di questi modelli di I/O osservati da SAP HANA, la memorizzazione nella cache per i diversi volumi che usano Archiviazione Premium di Azure deve essere impostata come:**

- **/hana/data**: nessuna memorizzazione nella cache
- **/hana/log** - nessuna memorizzazione nella cache - eccezione per M- e Mv2-Series in cui L'acceleratore di scrittura deve essere abilitato senza la memorizzazione nella cache di lettura. 
- **/hana/shared**: lettura della cache

### <a name="production-recommended-storage-solution"></a>Soluzione di storage consigliata per la produzione

> [!IMPORTANT]
> La certificazione SAP HANA per le macchine virtuali di Azure della serie M è valida esclusivamente con l'acceleratore di scrittura di Azure per il volume **/hana/log**. Di conseguenza, per le distribuzioni di SAP HANA in scenari di produzione nelle macchine virtuali di Azure della serie M è prevista la configurazione con l'acceleratore di scrittura di Azure per il volume **/hana/log**.  

> [!NOTE]
> Per gli scenari di produzione, controllare se un determinato tipo di macchina virtuale è supportato da SAP per SAP HANA nella [documentazione SAP per IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).

I consigli spesso superano i requisiti minimi SAP come indicato in precedenza in questo articolo. Le raccomandazioni elencate sono un compromesso tra le raccomandazioni di dimensione di SAP e la velocità effettiva di archiviazione massima fornita dai diversi tipi di macchine virtuali.

**Consiglio: le configurazioni consigliate per gli scenari di produzione sono simili alle seguenti:Recommendation: The recommended configurations for production scenarios look like:**

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

Verificare se la velocità effettiva di archiviazione per i diversi volumi suggeriti soddisfa il carico di lavoro che si desidera eseguire. Se il carico di lavoro richiede volumi più elevati per **/hana/data** e **/hana/log**, è necessario aumentare il numero di dischi rigidi virtuali di Archiviazione Premium di Azure. Il ridimensionamento di un volume con più dischi rigidi virtuali di quelli elencati aumenta la velocità effettiva di IOPS e I/O entro i limiti del tipo di macchina virtuale di Azure.Sizing a volume with more VHDs than listed increases the IOPS and I/O throughput within the limits of the Azure virtual machine type.

L'acceleratore di scrittura di Azure funziona solo in combinazione con [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/). Almeno i dischi di Archiviazione Premium di Azure che costituiscono il volume **/hana/log** devono quindi essere distribuiti come dischi gestiti.

Esistono limiti per il numero di dischi rigidi virtuali di Archiviazione Premium di Azure per ogni macchina virtuale supportati dall'acceleratore di scrittura di Azure. I limiti correnti sono:

- 16 dischi rigidi virtuali per una macchina virtuale M128xx e M416xx
- 8 dischi rigidi virtuali per una macchina virtuale M64xx e M208xx
- 4 dischi rigidi virtuali per una macchina virtuale M32xx

Istruzioni più dettagliate su come abilitare l'acceleratore di scrittura di Azure sono disponibili nell'articolo [Acceleratore di scrittura](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator).

La stessa documentazione include anche informazioni dettagliate e sulle restrizioni per l'acceleratore di scrittura di Azure.

**Consiglio: è necessario utilizzare L'acceleratore di scrittura per i dischi che formano il volume /hana/log**


### <a name="cost-conscious-azure-storage-configuration"></a>Configurazione di Archiviazione di Azure tenendo conto dei costi
La tabella seguente mostra una configurazione di tipi di macchine virtuali che i clienti usano anche per ospitare SAP HANA nelle macchine virtuali di Azure.The following table shows a configuration of VM types that customers also use to host SAP HANA on Azure VMs. Alcuni tipi di VM potrebbero non soddisfare tutti i criteri di archiviazione minimi per SAP HANA o non sono ufficialmente supportati con SAP HANA da SAP. anche se offrono prestazioni adeguate per scenari non di produzione. **I /hana/data** e **/hana/log** vengono combinati in un unico volume. Di conseguenza, l'utilizzo di Acceleratore di scrittura di Azure può diventare una limitazione in termini di operazioni di I/O al secondo.

> [!NOTE]
> Per gli scenari supportati da SAP, verificare se un determinato tipo di macchina virtuale è supportato per SAP HANA by SAP nella [documentazione SAP per IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).

> [!NOTE]
> Una modifica rispetto alle precedenti raccomandazioni per una soluzione consapevole dei costi, consiste nel passare dai [dischi HDD standard](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-hdd) di Azure a [dischi SSD standard](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-ssd) di Azure con prestazioni migliori e affidabili

I consigli spesso superano i requisiti minimi SAP come indicato in precedenza in questo articolo. Le raccomandazioni elencate sono un compromesso tra le raccomandazioni di dimensione di SAP e la velocità effettiva di archiviazione massima fornita dai diversi tipi di macchine virtuali.

| SKU di VM | RAM | Max. velocità effettiva<br /> Velocità effettiva | /hana/data and /hana/log<br /> con striping con LVM o MDADM | /hana/shared | volume /root | /usr/sap | hana/backup |
| --- | --- | --- | --- | --- | --- | --- | -- |
| DS14v2 | 112 GiB | 768 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E15 |
| E16v3 | 128 GiB | 384 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E15 |
| E32v3 | 256 GiB | 768 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| E64v3 | 432 GiB | 1.200 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E30 |
| GS5 | 448 GiB | 2.000 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E30 |
| M32ts | 192 GiB | 500 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| M32ls | 256 GiB | 500 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| M64ls | 512 GiB | 1.000 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 |1 x E30 |
| M64s | 1.000 GiB | 1.000 MB/s | 2 x P30 | 1 x E30 | 1 x E6 | 1 x E6 |2 x E30 |
| M64ms | 1.750 GiB | 1.000 MB/s | 3 x P30 | 1 x E30 | 1 x E6 | 1 x E6 | 3 x E30 |
| M128s | 2.000 GiB | 2.000 MB/s |3 x P30 | 1 x E30 | 1 x E10 | 1 x E6 | 2 x E40 |
| M128ms | 3.800 GiB | 2.000 MB/s | 5 x P30 | 1 x E30 | 1 x E10 | 1 x E6 | 2 x E50 |
| M208s_v2 | 2.850 GiB | 1.000 MB/s | 4 x P30 | 1 x E30 | 1 x E10 | 1 x E6 |  3 x E40 |
| M208ms_v2 | 5.700 GiB | 1.000 MB/s | 4 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E40 |
| M416s_v2 | 5.700 GiB | 2.000 MB/s | 4 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E40 |
| M416ms_v2 | 11400 GiB | 2.000 MB/s | 8 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E50 |


I dischi consigliati per i tipi di macchine virtuali più piccoli con 3 x P20 superano in dimensioni i volumi riguardanti le indicazioni di spazio contenute nel [white paper sull'archiviazione di SAP TDI](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html). Tuttavia, la scelta indicata nella tabella è stata effettuata per offrire una velocità effettiva del disco sufficiente per SAP HANA. Se sono necessarie modifiche al volume **/hana/backup,** che è stato dimensionato per mantenere i backup che rappresentano il doppio del volume di memoria, non esichetizzato regolare.   
Verificare se la velocità effettiva di archiviazione per i diversi volumi suggeriti soddisfa il carico di lavoro che si desidera eseguire. Se il carico di lavoro richiede volumi più elevati per **/hana/data** e **/hana/log**, è necessario aumentare il numero di dischi rigidi virtuali di Archiviazione Premium di Azure. Il ridimensionamento di un volume con più dischi rigidi virtuali di quelli elencati aumenta la velocità effettiva di IOPS e I/O entro i limiti del tipo di macchina virtuale di Azure.Sizing a volume with more VHDs than listed increases the IOPS and I/O throughput within the limits of the Azure virtual machine type. 

> [!NOTE]
> Le configurazioni sopra indicate NON traggono vantaggio dal [contratto di servizio per macchine virtuali con istanza singola di macchina virtuale di Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/) perché usa in combinazione Archiviazione Premium di Azure e Archiviazione Standard di Azure. Tuttavia, la selezione è stata scelta per ottimizzare i costi. È necessario scegliere Archiviazione Premium per tutti i dischi sopra quello elencato come Archiviazione di Azure Standard (Sxx) per rendere la configurazione della macchina virtuale conforme al contratto di servizio per singole macchine virtuali di Azure.


> [!NOTE]
> I suggerimenti sulla configurazione del disco forniti fanno riferimento ai requisiti minimi definiti da SAP per i provider di infrastruttura. Nelle distribuzioni effettive dei clienti e negli scenari con carichi di lavoro reali, tuttavia, si sono verificate situazioni in cui questi suggerimenti non garantivano funzionalità sufficienti. Queste situazioni si possono verificare quando un cliente richiede un ricaricamento più veloce dei dati dopo un riavvio HANA o se una configurazione di backup richiede una larghezza di banda maggiore per l'archiviazione. Altri casi includono, ad esempio, percorsi **/hana/log** in cui 5000 IOPS non sono sufficienti per un carico di lavoro specifico. Considerare quindi queste indicazioni come punto di inizio e adattarle in base ai requisiti del carico di lavoro.
>  

## <a name="azure-ultra-disk-storage-configuration-for-sap-hana"></a>Azure Ultra disk storage configuration for SAP HANA
Microsoft sta implementando un nuovo tipo di archiviazione di Azure denominato [Disco Di Azure Ultra](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#ultra-disk). La differenza significativa tra l'archiviazione di Azure offerta finora e il disco Ultra è che le funzionalità del disco non sono più associate alle dimensioni del disco. In qualità di cliente, puoi definire queste funzionalità per ultra disco:

- Dimensioni di un disco che vanno da 4 GiB a 65.536 GiB
- Le operazioni di I/O al secondo vanno da 100 operazioni di I/O al secondo a 160K di IOPS (il massimo dipende anche dai tipi di macchina virtuale)IOPS range from 100 IOPS to 160K IOPS (maximum depends on VM types as well)
- Velocità effettiva di archiviazione da 300 MB/sec a 2.000 MB/secStorage throughput from 300 MB/sec to 2,000 MB/sec

Disco ultra offre la possibilità di definire un singolo disco che soddisfa le dimensioni, le operazioni di I/O al secondo e l'intervallo di velocità effettiva del disco. Invece di usare gestori di volumi logici come LVM o MDADM in Azure Premium Storage per costruire volumi che soddisfino i requisiti di IOPS e velocità effettiva di archiviazione. È possibile eseguire una combinazione di configurazione tra Ultra disco e Premium Storage. Di conseguenza, è possibile limitare l'utilizzo di Ultra disco ai volumi di prestazioni critici /hana/data e /hana/log e coprire gli altri volumi con Archiviazione Premium di Azure

Altri vantaggi di Ultra disk possono essere la migliore latenza di lettura rispetto a Premium Storage. La latenza di lettura più veloce può presentare vantaggi quando si desidera ridurre i tempi di avvio HANA e il successivo caricamento dei dati in memoria. I vantaggi dell'archiviazione ultra disco possono essere percepiti anche quando HANA sta scrivendo savepoint. Poiché i dischi di archiviazione Premium per /hana/data non sono in genere memorizzati nell'acceleratore di scrittura, la latenza di scrittura in /hana/data nell'archiviazione Premium rispetto al disco Ultra è maggiore. Ci si può aspettare che la scrittura del savepoint con disco Ultra funzioni meglio su Disco Ultra.

> [!IMPORTANT]
> Ultra disco non è ancora presente in tutte le aree di Azure e non supporta ancora tutti i tipi di macchine virtuali elencati di seguito. Per informazioni dettagliate su dove è disponibile Ultra disk e quali famiglie di VM sono supportate, vedere l'articolo [Quali tipi di disco sono disponibili in Azure?](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#ultra-disk).

### <a name="production-recommended-storage-solution-with-pure-ultra-disk-configuration"></a>Soluzione di storage consigliata per la produzione con configurazione disco Ultra pura
In questa configurazione, si mantengono i volumi /hana/data e /hana/log separatamente. I valori suggeriti derivano dagli KPI che SAP deve certificare i tipi di VM per SAP HANA e le configurazioni di archiviazione come consigliato nel [white paper sull'archiviazione di SAP TDI.](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)

I consigli spesso superano i requisiti minimi SAP come indicato in precedenza in questo articolo. Le raccomandazioni elencate sono un compromesso tra le raccomandazioni di dimensione di SAP e la velocità effettiva di archiviazione massima fornita dai diversi tipi di macchine virtuali.

| SKU di VM | RAM | Max. velocità effettiva<br /> Velocità effettiva | /hana/volume dati | Velocità effettiva I/O dati/hana/dati | /hana/Dati IOPS | /hana/registra volume | Velocità effettiva i/O/log /hana/log | /hana/log IOPS |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| E64s_v3 | 432 GiB | 1.200 MB/s | 600 GB | 700 MBps | 7.500 | 512 GB | 500 MBps  | 2.000 |
| M32ts | 192 GiB | 500 MB/s | 250 GB | 400 MBps | 7.500 | 256 GB | 250 MBps  | 2.000 |
| M32ls | 256 GiB | 500 MB/s | 300 GB | 400 MBps | 7.500 | 256 GB | 250 MBps  | 2.000 |
| M64ls | 512 GiB | 1.000 MB/s | 600 GB | 600 MBps | 7.500 | 512 GB | 400 MBps  | 2.500 |
| M64s | 1.000 GiB | 1.000 MB/s |  1.200 GB | 600 MBps | 7.500 | 512 GB | 400 MBps  | 2.500 |
| M64ms | 1.750 GiB | 1.000 MB/s | 2.100 GB | 600 MBps | 7.500 | 512 GB | 400 MBps  | 2.500 |
| M128s | 2.000 GiB | 2.000 MB/s |2.400 GB | 1.200 Mbps |9000 | 512 GB | 800 MBps  | 3,000 | 
| M128ms | 3.800 GiB | 2.000 MB/s | 4.800 GB | 1200 Mbps |9000 | 512 GB | 800 MBps  | 3,000 | 
| M208s_v2 | 2.850 GiB | 1.000 MB/s | 3.500 GB | 1.000 Mbps | 9000 | 512 GB | 400 MBps  | 2.500 | 
| M208ms_v2 | 5.700 GiB | 1.000 MB/s | 7.200 GB | 1.000 Mbps | 9000 | 512 GB | 400 MBps  | 2.500 | 
| M416s_v2 | 5.700 GiB | 2.000 MB/s | 7.200 GB | 1.500 Mbps | 9000 | 512 GB | 800 MBps  | 3,000 | 
| M416ms_v2 | 11.400 GiB | 2.000 MB/s | 14.400 GB | 1.500 Mbps | 9000 | 512 GB | 800 MBps  | 3,000 |   

I valori elencati devono essere un punto di partenza e devono essere valutati in base alle esigenze reali. Il vantaggio con il disco Azure Ultra è che i valori per Le operazioni di I/O al secondo e velocità effettiva possono essere adattati senza la necessità di arrestare la macchina virtuale o arrestare il carico di lavoro applicato al sistema.   

> [!NOTE]
> Finora, le istantanee di archiviazione con spazio di archiviazione su disco Ultra non sono disponibili. Ciò blocca l'utilizzo degli snapshot della macchina virtuale con i servizi di Backup di AzureThis blocks the usage of VM snapshots with Azure Backup Services

### <a name="cost-conscious-storage-solution-with-pure-ultra-disk-configuration"></a>Soluzione di storage consapevole in termini di costi con configurazione disco Ultra pura
In questa configurazione, i volumi /hana/data e /hana/log sullo stesso disco. I valori suggeriti derivano dagli KPI che SAP deve certificare i tipi di VM per SAP HANA e le configurazioni di archiviazione come consigliato nel [white paper sull'archiviazione di SAP TDI.](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) 

I consigli spesso superano i requisiti minimi SAP come indicato in precedenza in questo articolo. Le raccomandazioni elencate sono un compromesso tra le raccomandazioni di dimensione di SAP e la velocità effettiva di archiviazione massima fornita dai diversi tipi di macchine virtuali.

| SKU di VM | RAM | Max. velocità effettiva<br /> Velocità effettiva | Volume per /hana/data e /log | /hana/dati e velocità effettiva di I/O del log | /hana/dati e iOPS di registro |
| --- | --- | --- | --- | --- | --- |
| E64s_v3 | 432 GiB | 1.200 MB/s | 1.200 GB | 1.200 Mbps | 9,500 | 
| M32ts | 192 GiB | 500 MB/s | 512 GB | 400 MBps | 9,500 | 
| M32ls | 256 GiB | 500 MB/s | 600 GB | 400 MBps | 9,500 | 
| M64ls | 512 GiB | 1.000 MB/s | 1.100 GB | 900 MBps | 10,000 | 
| M64s | 1.000 GiB | 1.000 MB/s |  1.700 GB | 900 MBps | 10,000 | 
| M64ms | 1.750 GiB | 1.000 MB/s | 2.600 GB | 900 MBps | 10,000 | 
| M128s | 2.000 GiB | 2.000 MB/s |2.900 GB | 1.800 Mbps |12000 | 
| M128ms | 3.800 GiB | 2.000 MB/s | 5.300 GB | 1.800 Mbps |12000 |  
| M208s_v2 | 2.850 GiB | 1.000 MB/s | Da 4.000 GB | 900 MBps | 10,000 |  
| M208ms_v2 | 5.700 GiB | 1.000 MB/s | 7.700 GB | 900 MBps | 10,000 | 
| M416s_v2 | 5.700 GiB | 2.000 MB/s | 7.700 GB | 1.800MBps | 12000 |  
| M416ms_v2 | 11.400 GiB | 2.000 MB/s | 15.000 GB | 1.800 Mbps | 12000 |    

I valori elencati devono essere un punto di partenza e devono essere valutati in base alle esigenze reali. Il vantaggio con il disco Azure Ultra è che i valori per Le operazioni di I/O al secondo e velocità effettiva possono essere adattati senza la necessità di arrestare la macchina virtuale o arrestare il carico di lavoro applicato al sistema.  

## <a name="nfs-v41-volumes-on-azure-netapp-files"></a>Volumi NFS v4.1 nei file NetApp di Azure
File NetApp di Azure fornisce condivisioni NFS native che possono essere usate per i volumi /hana/shared, /hana/data e /hana/log. L'utilizzo di condivisioni NFS basate su ANF per i volumi /hana/data e /hana/log richiede l'utilizzo del protocollo NFS v4.1. Il protocollo NFS v3 non è supportato per l'utilizzo di /hana/data e volumi /hana/log quando si basano le condivisioni su ANF. 

> [!IMPORTANT]
> Il protocollo NFS v3 implementato nei file NetApp di Azure **non** è supportato per l'utilizzo per /hana/data e /hana/log. L'utilizzo di NFS 4.1 è obbligatorio per i volumi /hana/data e /hana/log da un punto di vista funzionale. Mentre per il volume /hana/condiviso il protocollo NFS v3 o NFS v4.1 può essere utilizzato da un punto di vista funzionale.

### <a name="important-considerations"></a>Considerazioni importanti
Quando si considerano i file NetApp di Azure per SAP Netweaver e SAP HANA, tenere presenti le considerazioni importanti seguenti:

- Il pool di capacità minima è 4 TiB.  
- La dimensione minima del volume è 100 GiB
- I file NetApp di Azure e tutte le macchine virtuali in cui verranno montati i volumi di File NetApp di Azure devono trovarsi nella stessa rete virtuale di Azure o in [reti virtuali con peernella](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) stessa area.  
- La rete virtuale selezionata deve avere una subnet, delegata a File NetApp di Azure.The selected virtual network must have a subnet, delegated to Azure NetApp Files.
- La velocità effettiva di un volume NetApp di Azure è una funzione della quota di volume e del livello di servizio, come documentato nel livello di servizio [per i file NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels)di Azure. Quando si ridimensionano i volumi NETApp di HANA Azure, assicurarsi che la velocità effettiva risultante soddisfi i requisiti di sistema HANA.  
- File NetApp di Azure offre criteri di [esportazione:](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-configure-export-policy)è possibile controllare i client consentiti, il tipo di accesso (Lettura&Scrittura, Sola lettura e così via). 
- La funzionalità File NetApp di Azure non è ancora in grado di riconoscere l'area. Attualmente la funzionalità File NetApp di Azure non è distribuita in tutte le aree di disponibilità in un'area di Azure.Currently Azure NetApp Files feature isn't deployed in all Availability zones in an Azure region. Tenere presente le potenziali implicazioni di latenza in alcune aree di Azure.Be aware of the potential latency implications in some Azure regions.  
- È importante distribuire le macchine virtuali in prossimità dell'archiviazione NetApp di Azure per una bassa latenza. 
- L'ID utente per <b>sid</b>adm `sapsys` e l'ID gruppo per nelle macchine virtuali devono corrispondere alla configurazione in File NetApp di Azure.The User ID for sid adm and the Group ID for on the virtual machines must match the configuration in Azure NetApp Files. 

> [!IMPORTANT]
> Per i carichi di lavoro SAP HANA, una bassa latenza è fondamentale. Collaborare con il rappresentante Microsoft per assicurarsi che le macchine virtuali e i volumi di File NetApp di Azure vengano distribuiti in prossimità.  

> [!IMPORTANT]
> Se esiste una mancata corrispondenza tra l'ID utente `sapsys` per <b>sid</b>adm e l'ID gruppo per tra la macchina virtuale e la configurazione `nobody`di Azure NetApp, le autorizzazioni per i file nei volumi NetApp di Azure, montate su macchine virtuali, verranno visualizzate come . Assicurarsi di specificare l'ID utente corretto per `sapsys` <b>sid</b>adm e l'ID gruppo per , durante l'onboarding di un nuovo [sistema](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxjSlHBUxkJBjmARn57skvdUQlJaV0ZBOE1PUkhOVk40WjZZQVJXRzI2RC4u) in File NetApp di Azure.

### <a name="sizing-for-hana-database-on-azure-netapp-files"></a>Ridimensionamento per il database HANA nei file NetApp di AzureSizing for HANA database on Azure NetApp Files

La velocità effettiva di un volume NetApp di Azure è una funzione delle dimensioni del volume e del livello di servizio, come documentato nel livello di servizio [per i file NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels)di Azure. 

Quando si progetta l'infrastruttura per SAP in Azure, è necessario conoscere alcuni requisiti minimi di velocità effettiva di archiviazione da parte di SAP, che si traducono in caratteristiche di velocità effettiva minima:As you design the infrastructure for SAP in Azure you should be aware of some minimum storage throughput requirements by SAP, which translate into minimum throughput characteristics of:

- Abilita lettura/scrittura su /hana/log di un valore 250 MB/sec con dimensioni di I/O di 1 MB  
- Abilitare l'attività di lettura per minimo 400 MB/sec per /hana/data con dimensioni di I/O di 16 MB e 64 MB  
- Abilitare l'attività di scrittura per minimo 250 MB/sec per /hana/data con dimensioni di I/O di 16 MB e 64 MB  

I limiti di velocità effettiva dei file netApp di Azure per 1 TiB della quota del volume sono:The [Azure NetApp Files throughput limits](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels) per 1 TiB of volume quota are:
- Livello di archiviazione Premium - 64 MiB/s  
- Livello di archiviazione ultra - 128 MiB/s  

> [!IMPORTANT]
> Indipendentemente dalla capacità distribuita in un singolo volume NFS, si prevede che la velocità effettiva si appaltierà la larghezza di banda di 1,2-1,4 GB/sec utilizzata da un consumer in una macchina virtuale. Questo ha a che fare con l'architettura sottostante dell'offerta ANF e relativi limiti di sessione Linux intorno nFS. I numeri di prestazioni e velocità effettiva documentati nell'articolo Risultati del test di [benchmark delle prestazioni per i file NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-performance-benchmarks) di Azure sono stati condotti su un volume NFS condiviso con più macchine virtuali client e, di conseguenza, con più sessioni. Questo scenario è diverso dallo scenario misurato in SAP. Dove si misura la velocità effettiva da una singola macchina virtuale in un volume NFS. ospitati su ANF.

Per soddisfare i requisiti minimi di velocità effettiva SAP per `/hana/shared`dati e log e in base alle linee guida per , le dimensioni consigliate sono simili alle seguenti:

| Volume | Dimensione<br /> Livello Archiviazione Premium | Dimensione<br /> Livello di archiviazione ultra | Protocollo NFS supportato |
| --- | --- | --- |
| /hana/log/ | 4 TiB | 2 TiB | v4.1 (in questo v) |
| /hana/data | 6.3 TiB (in inglese) | 3.2 TiB | v4.1 (in questo v) |
| /hana/shared | Max(512 GB, 1xRAM) per 4 nodi di lavoroMax(512 GB, 1xRAM) per 4 worker nodes | Max(512 GB, 1xRAM) per 4 nodi di lavoroMax(512 GB, 1xRAM) per 4 worker nodes | v3 o v4.1 |


> [!NOTE]
> I consigli di dimensionamento dei file di Azure NetApp indicati di seguito sono destinati a soddisfare i requisiti minimi che SAP esprime nei confronti dei provider di infrastruttura. Nelle distribuzioni reali dei clienti e negli scenari di carico di lavoro, ciò potrebbe non essere sufficiente. Usare questi consigli come punto di partenza e adattarsi in base ai requisiti del carico di lavoro specifico.  

Pertanto è possibile distribuire una velocità effettiva simile per i volumi ANF come già elencato per l'archiviazione su disco Ultra. Considerare inoltre le dimensioni per le dimensioni elencate per i volumi per i diversi SKU delle macchine virtuali già presenti nelle tabelle disco Ultra.

> [!TIP]
> È possibile ridimensionare i volumi dei file netApp `unmount` di Azure in modo dinamico, senza la necessità di arrestare i volumi, arrestare le macchine virtuali o arrestare SAP HANA.You can re-size Azure NetApp Files volumes dynamically, without the need to the volumes, stop the virtual machines or stop SAP HANA. Ciò consente flessibilità per soddisfare le richieste di velocità effettiva previste e impreviste dell'applicazione.

La documentazione su come distribuire una configurazione con scalabilità orizzontale SAP HANA con nodo standby utilizzando i volumi NFS v4.1 ospitati in ANF viene pubblicata in [SAP HANA scale-out con nodo standby nelle macchine virtuali](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse)di Azure con file NetApp di Azure in SUSE Linux Enterprise Server .


## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni, vedere:

- [Guida SAP HANA High Availability per macchine virtuali di Azure.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview)
