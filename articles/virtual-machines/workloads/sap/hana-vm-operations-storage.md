---
title: Configurazioni di archiviazione macchina virtuale di SAP HANA in Azure | Microsoft Docs
description: Raccomandazioni di archiviazione per VM con SAP HANA distribuiti in essi contenuti.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/05/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d062b6fff9693d5bda75edd65b8fe88d834eff57
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/06/2019
ms.locfileid: "66735512"
---
# <a name="sap-hana-azure-virtual-machine-storage-configurations"></a>Configurazioni di archiviazione macchina virtuale di SAP HANA in Azure

Azure offre diversi tipi di archiviazione adatti per le macchine virtuali di Azure che eseguono SAP HANA. I tipi di archiviazione di Azure che possono essere considerati per l'elenco di distribuzioni di SAP HANA, ad esempio: 

- Nelle unità disco SSD standard (unità SSD)
- Unità SSD Premium
- SSD extra in anteprima pubblica e non ancora supportato con le applicazioni SAP di produzione

Per informazioni su questi tipi di dischi, vedere l'articolo [selezionare un tipo di disco](https://docs.microsoft.com/azure/virtual-machines/linux/disks-types)

Azure offre due metodi di distribuzione per VHD su Archiviazione Standard e Premium di Azure. Se lo scenario lo permette, sfruttare i vantaggi offerti dalle distribuzioni di [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/). 

Per un elenco dei tipi di archiviazione e dei relativi contratti di servizio per operazioni di I/O al secondo e velocità effettiva di archiviazione, vedere la [documentazione di Azure per Managed Disks](https://azure.microsoft.com/pricing/details/managed-disks/).

**Raccomandazione: Usare archiviazione Premium di Azure in combinazione con l'acceleratore di scrittura di Azure e usare Azure Managed Disks per la distribuzione**

Nel mondo in locale, raramente era necessario preoccuparsi di sottosistemi i/o e le relative funzionalità. perché il fornitore dell'appliance deve assicurarsi che siano soddisfatti i requisiti di archiviazione minima per SAP HANA. Mentre si compila l'infrastruttura di Azure manualmente, è necessario essere consapevoli di alcuni di questi requisiti. Alcune delle caratteristiche di velocità effettiva minima richieste derivano dalla necessità di:

- Abilitare la lettura/scrittura sul **hana/log** un 250 MB/sec con dimensioni dei / o di 1 MB
- Abilitare l'attività di lettura per minimo 400 MB/sec per **/hana/data** con dimensioni di I/O di 16 MB e 64 MB
- Abilitare l'attività di scrittura per minimo 250 MB/sec per **/hana/data** con dimensioni di I/O di 16 MB e 64 MB

Detto questo, una bassa latenza di archiviazione è fondamentale per i sistemi DBMS, anche perché i sistemi DBMS, come SAP HANA, mantengono dati in memoria. Il percorso critico per l'archiviazione riguarda in genere le scritture nel log delle transazioni dei sistemi DBMS. Ma possono essere critiche anche operazioni come la scrittura di punti di salvataggio o il caricamento di dati in memoria dopo il ripristino da un arresto anomalo del sistema. È quindi **obbligatori** dischi Premium di Azure per sfruttare **/hana/data** e **hana/log** volumi. Per ottenere la velocità effettiva minima di **/hana/log** e **/hana/data** come richiesto da SAP, è necessario configurare un sistema RAID 0 tramite MDADM o LVM su più dischi di Archiviazione Premium di Azure e usare i volumi RAID come volumi **/hana/data** e **/hana/log**. 

**Raccomandazione: Dimensioni di striping di RAID 0 la raccomandazione è possibile usare:**

- 64 KB o 128 KB per **/hana/data**
- 32 KB per **/hana/log**

> [!NOTE]
> Non è necessario configurare alcun livello di ridondanza con i volumi RAID perché l'Archiviazione Standard e Premium di Azure mantiene tre immagini di un disco rigido virtuale. L'utilizzo di un volume RAID è richiesto esclusivamente per configurare volumi in grado di offrire una velocità effettiva di I/O sufficiente.

Accumulare un certo numero di dischi rigidi virtuali di Azure in una configurazione RAID consente di ottenere valori cumulativi per le operazioni di I/O al secondo e la velocità effettiva di archiviazione. Pertanto, se si configura un sistema RAID 0 su 3 dischi di Archiviazione Premium di Azure P30, si dovrebbero ottenere livelli triplicati di operazioni di I/O al secondo e velocità effettiva di archiviazione rispetto a un singolo disco di Archiviazione Premium di Azure P30.

Gli elementi consigliati sulla memorizzazione nella cache riportati di seguito presuppongono caratteristiche di I/O per SAP HANA come:

- Non esiste quasi nessun carico di lavoro in lettura sui file di dati HANA, ad eccezione degli I/O di grandi dimensioni dopo il riavvio dell'istanza di HANA o quando i dati vengono caricati in HANA. Un altro caso di I/O in lettura di dimensioni maggiori sui file di dati è rappresentato dai backup di database HANA. Di conseguenza spesso la lettura della cache non ha senso in quanto, nella maggior parte dei casi, tutti i volumi dei file di dati devono essere letti completamente.
- La scrittura sui file di dati si verifica in burst in base ai punti di salvataggio HANA e al ripristino da arresto anomalo del sistema HANA. La scrittura di punti di salvataggio è un'operazione asincrona e non impedisce le transazioni dell'utente. La scrittura di dati durante il ripristino da arresto anomalo del sistema è un'operazione critica a livello di prestazioni per ottenere che il sistema risponda di nuovo rapidamente. Tuttavia, il ripristino da arresto anomalo del sistema dovrebbe essere un caso piuttosto eccezionale.
- Esistono pochissime operazioni di lettura dai file di rollforward HANA. Le eccezioni sono gli I/O di grandi dimensioni quando si eseguono backup dei log di transazioni, il ripristino da arresto anomalo del sistema o nella fase di riavvio di un'istanza HANA.  
- Il carico principale sul file di log di rollforward SAP HANA è rappresentato delle operazioni di scrittura. A seconda del tipo di carico di lavoro, è possibile che le operazioni di I/O siano di piccole dimensioni, ad esempio 4 kB o, in altri casi, di almeno 1 MB. La latenza di scrittura rispetto al log di rollforward SAP HANA è critica dal punto di vista delle prestazioni.
- Tutti i dati delle scritture devono essere salvati in modo permanente su disco in modo affidabile.

**Raccomandazione: In seguito a questi modelli dei / o osservati da SAP HANA, è necessario impostare la memorizzazione nella cache per i diversi volumi usando archiviazione Premium di Azure, ad esempio:**

- **/hana/data**: nessuna memorizzazione nella cache
- **/hana/log**: nessuna memorizzazione nella cache, ad eccezione della serie M come illustrato più avanti in questo documento
- **/hana/shared**: lettura della cache


Durante il ridimensionamento o quando si sceglie una macchina virtuale, tenere presente anche la velocità effettiva di I/O complessiva delle macchine virtuali. Per informazioni sulla velocità effettiva di archiviazione complessiva delle macchine virtuali, vedere l'articolo [Dimensioni delle macchine virtuali ottimizzate per la memoria](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory).

## <a name="linux-io-scheduler-mode"></a>Modalità di pianificazione I/O Linux
Linux offre varie modalità di pianificazione I/O diverse. Comuni tramite i fornitori di Linux e SAP si consiglia di riconfigurare la modalità di utilità di pianificazione dei / o per i volumi di disco dal **cfq** modalità per il **noop** modalità. Informazioni dettagliate sono disponibili nella [nota SAP n. 1984798](https://launchpad.support.sap.com/#/notes/1984787). 


## <a name="production-storage-solution-with-azure-write-accelerator-for-azure-m-series-virtual-machines"></a>Soluzione di archiviazione di produzione con l'acceleratore di scrittura di Azure per le macchine virtuali di Azure della serie M
L'acceleratore di scrittura di Azure è una funzionalità implementata esclusivamente per le macchine virtuali di Azure della serie M. Come indicato dal nome, lo scopo della funzionalità è migliorare la latenza di I/O delle scritture per l'Archiviazione Premium di Azure. Per SAP HANA, l'uso dell'acceleratore di scrittura è previsto solo sul volume **/hana/log**. Pertanto, il **/hana/data** e **hana/log** sono volumi separati con acceleratore di scrittura di Azure che supportano il **hana/log** solo sul volume. 

> [!IMPORTANT]
> La certificazione SAP HANA per le macchine virtuali di Azure della serie M è valida esclusivamente con l'acceleratore di scrittura di Azure per il volume **/hana/log**. Di conseguenza, per le distribuzioni di SAP HANA in scenari di produzione nelle macchine virtuali di Azure della serie M è prevista la configurazione con l'acceleratore di scrittura di Azure per il volume **/hana/log**.  

> [!NOTE]
> Per gli scenari di produzione, controllare se un determinato tipo di macchina virtuale è supportato da SAP per SAP HANA nella [documentazione SAP per IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).

**Raccomandazione: Le configurazioni consigliate per gli scenari di produzione simile:**

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

Tipi di VM M416xx_v2 non vengono ancora resi disponibili da Microsoft al pubblico. Controllare se la velocità effettiva di archiviazione per i diversi volumi suggeriti soddisfa il carico di lavoro che si desidera eseguire. Se il carico di lavoro richiede volumi più elevati per **/hana/data** e **/hana/log**, è necessario aumentare il numero di dischi rigidi virtuali di Archiviazione Premium di Azure. Ridimensionamento di un volume con più dischi rigidi virtuali rispetto a elencati aumenta il numero di IOPS e velocità effettiva dei / o entro i limiti del tipo di macchina virtuale di Azure.

L'acceleratore di scrittura di Azure funziona solo in combinazione con [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/). Almeno i dischi di Archiviazione Premium di Azure che costituiscono il volume **/hana/log** devono quindi essere distribuiti come dischi gestiti.

Esistono limiti per il numero di dischi rigidi virtuali di Archiviazione Premium di Azure per ogni macchina virtuale supportati dall'acceleratore di scrittura di Azure. I limiti correnti sono:

- 16 dischi rigidi virtuali per una macchina virtuale M128xx e M416xx
- 8 dischi rigidi virtuali per una macchina virtuale M64xx e M208xx
- 4 dischi rigidi virtuali per una macchina virtuale M32xx

Istruzioni più dettagliate su come abilitare l'acceleratore di scrittura di Azure sono disponibili nell'articolo [Acceleratore di scrittura](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator).

La stessa documentazione include anche informazioni dettagliate e sulle restrizioni per l'acceleratore di scrittura di Azure.

**Raccomandazione: È necessario usare l'acceleratore di scrittura per i dischi che costituiscono il volume /Hana/log.**


## <a name="cost-conscious-azure-storage-configuration"></a>Configurazione di Archiviazione di Azure tenendo conto dei costi
Nella tabella seguente viene illustrata una configurazione dei tipi di VM usate anche dai clienti per ospitare SAP HANA in macchine virtuali di Azure. Potrebbero essere presenti alcuni tipi di VM che potrebbero non soddisfare tutti i criteri di archiviazione minima per SAP HANA o non sono ufficialmente supportati con SAP HANA da SAP. anche se offrono prestazioni adeguate per scenari non di produzione. Il **/hana/data** e **hana/log** vengono combinati in un volume. Di conseguenza l'uso dell'acceleratore di scrittura di Azure può diventare una limitazione in termini di IOPS.

> [!NOTE]
> Per gli scenari supportati da SAP, controllare se un determinato tipo di macchina virtuale è supportato per SAP HANA da SAP, vedere la [documentazione SAP per IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).

> [!NOTE]
> Una differenza rispetto alla precedente consigli per una soluzione consapevoli dei costi, è possibile spostare dalla [i dischi di Azure Standard HDD](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-hdd) a con prestazioni migliori e più affidabile [dischi SSD Standard di Azure](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-ssd)


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
| M128ms | 3800 GiB | 2000 MB/s | 5 x P30 | 1 x E30 | 1 x E10 | 1 x E6 | 2 x E50 |
| M208s_v2 | 2850 GiB | 1000 MB/s | 4 x P30 | 1 x E30 | 1 x E10 | 1 x E6 |  3 x E40 |
| M208ms_v2 | 5700 GiB | 1000 MB/s | 4 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E40 |
| M416s_v2 | 5700 GiB | 2000 MB/s | 4 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E40 |
| M416ms_v2 | 11400 GiB | 2000 MB/s | 8 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E50 |


Tipi di VM M416xx_v2 non vengono ancora resi disponibili da Microsoft al pubblico. I dischi consigliati per i tipi di macchine virtuali più piccoli con 3 x P20 superano in dimensioni i volumi riguardanti le indicazioni di spazio contenute nel [white paper sull'archiviazione di SAP TDI](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html). Tuttavia, la scelta indicata nella tabella è stata effettuata per offrire una velocità effettiva del disco sufficiente per SAP HANA. Se è necessario apportare modifiche al volume **/hana/backup**, che è stato ridimensionato per mantenere backup che rappresentano il doppio del volume di memoria, procedere liberamente.   
Controllare se la velocità effettiva di archiviazione per i diversi volumi suggeriti soddisfa il carico di lavoro che si desidera eseguire. Se il carico di lavoro richiede volumi più elevati per **/hana/data** e **/hana/log**, è necessario aumentare il numero di dischi rigidi virtuali di Archiviazione Premium di Azure. Ridimensionamento di un volume con più dischi rigidi virtuali rispetto a elencati aumenta il numero di IOPS e velocità effettiva dei / o entro i limiti del tipo di macchina virtuale di Azure. 

> [!NOTE]
> Le configurazioni sopra indicate NON traggono vantaggio dal [contratto di servizio per macchine virtuali con istanza singola di macchina virtuale di Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/) perché usa in combinazione Archiviazione Premium di Azure e Archiviazione Standard di Azure. Tuttavia, la selezione è stata scelta per ottimizzare i costi. È necessario scegliere Archiviazione Premium per tutti i dischi sopra quello elencato come Archiviazione di Azure Standard (Sxx) per rendere la configurazione della macchina virtuale conforme al contratto di servizio per singole macchine virtuali di Azure.


> [!NOTE]
> I suggerimenti sulla configurazione del disco forniti fanno riferimento ai requisiti minimi definiti da SAP per i provider di infrastruttura. Nelle distribuzioni effettive dei clienti e negli scenari con carichi di lavoro reali, tuttavia, si sono verificate situazioni in cui questi suggerimenti non garantivano funzionalità sufficienti. Queste situazioni si possono verificare quando un cliente richiede un ricaricamento più veloce dei dati dopo un riavvio HANA o se una configurazione di backup richiede una larghezza di banda maggiore per l'archiviazione. Altri casi includono, ad esempio, percorsi **/hana/log** in cui 5000 IOPS non sono sufficienti per un carico di lavoro specifico. Considerare quindi queste indicazioni come punto di inizio e adattarle in base ai requisiti del carico di lavoro.
>  

## <a name="azure-ultra-ssd-storage-configuration-for-sap-hana"></a>Configurazione dell'archiviazione SSD extra Azure per SAP HANA
Microsoft è in corso l'introduzione di un nuovo tipo di archiviazione di Azure chiamato [SSD extra Azure](https://azure.microsoft.com/updates/ultra-ssd-new-azure-managed-disks-offering-for-your-most-latency-sensitive-workloads/). La grande differenza tra archiviazione di Azure offerti finora e Ultra-unità SSD è che le capacità del disco non sono associate per le dimensioni del disco più. I clienti è possibile definire queste funzionalità per unità SSD extra:

- Dimensioni di un disco compreso tra 4 GiB 65.536 GiB
- Intervallo IOPS da 100 IOPS a 160 KB IOPS (valore massimo dipende anche i tipi di VM)
- Velocità effettiva di archiviazione da 300 MB/sec a 2.000 MB/sec

Per ulteriori dettagli cercare l'articolo [Announcing SSD Ultra-la prossima generazione di tecnologia di dischi di Azure (anteprima)](https://azure.microsoft.com/blog/announcing-ultra-ssd-the-next-generation-of-azure-disks-technology-preview/)

UltraSSD ti offre la possibilità di definire un singolo disco che soddisfa le dimensioni, IOPS e intervallo di velocità effettiva del disco. Invece di usare i responsabili di volume logico, ad esempio LVM o MDADM su archiviazione Premium di Azure per creare i volumi che soddisfano i requisiti di velocità effettiva IOPS e di archiviazione. È possibile eseguire una combinazione di configurazione tra UltraSSD e l'archiviazione Premium. Di conseguenza, è possibile limitare l'uso di UltraSSD con prestazioni critiche /hana/data e /hana/log volumi e illustrano altri volumi con archiviazione Premium di Azure

| SKU di VM | RAM | Max. velocità effettiva<br /> Velocità effettiva | volume di dati Hana | velocità effettiva dei / o dati Hana | /hana/data IOPS | volume dei log Hana | velocità effettiva dei / o log Hana | hana/log IOPS |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| M32ts | 192 GiB | 500 MB/s | 250 GB | 500 MBps | 7500 | 256 GB | 500 MBps  | 2000 |
| M32ls | 256 GiB | 500 MB/s | 300 GB | 500 MBps | 7500 | 256 GB | 500 MBps  | 2000 |
| M64ls | 512 GiB | 1000 MB/s | 600 GB | 500 MBps | 7500 | 512 GB | 500 MBps  | 2000 |
| M64s | 1000 GiB | 1.000 MB/s |  1200 GB | 500 MBps | 7500 | 512 GB | 500 MBps  | 2000 |
| M64ms | 1750 GiB | 1.000 MB/s | 2100 GB | 500 MBps | 7500 | 512 GB | 500 MBps  | 2000 |
| M128s | 2000 GiB | 2.000 MB/s |2400 GB | 1200 MBps |9000 | 512 GB | 800 MBps  | 2000 | 
| M128ms | 3800 GiB | 2.000 MB/s | 4800 GB | 1200 MBps |9000 | 512 GB | 800 MBps  | 2000 | 
| M208s_v2 | 2850 GiB | 1.000 MB/s | 3500 GB | 1000 MBps | 9000 | 512 GB | 500 MBps  | 2000 | 
| M208ms_v2 | 5700 GiB | 1.000 MB/s | 7200 GB | 1000 MBps | 9000 | 512 GB | 500 MBps  | 2000 | 
| M416s_v2 | 5700 GiB | 2.000 MB/s | 7200 GB | 1500 M Bps | 9000 | 512 GB | 800 MBps  | 2000 | 
| M416ms_v2 | 11400 GiB | 2.000 MB/s | 14400 GB | 1500 MBps | 9000 | 512 GB | 800 MBps  | 2000 |   

Tipi di VM M416xx_v2 non vengono ancora resi disponibili da Microsoft al pubblico. I valori elencati sono progettati per essere un punto di partenza e devono essere valutati rispetto alle esigenze reali. Il vantaggio con unità SSD extra di Azure è che i valori di IOPS e velocità effettiva possono essere adattati senza la necessità di arrestare la macchina virtuale o l'arresto del carico di lavoro applicati al sistema.   

> [!NOTE]
> Finora, gli snapshot di archiviazione con archiviazione UltraSSD non è disponibile. Questa opzione si impedisce l'uso di snapshot delle macchine Virtuali con i servizi di Backup di Azure

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni, vedere:

- [Guida alla disponibilità elevata di SAP HANA per macchine virtuali di Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview).