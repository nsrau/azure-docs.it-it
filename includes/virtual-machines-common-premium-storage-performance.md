---
title: file di inclusione
description: file di inclusione
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/08/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 289100afe825c14ce9964f39e3f583078f51da1d
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73182237"
---
## <a name="application-performance-indicators"></a>Indicatori di prestazioni dell'applicazione

È possibile valutare se le prestazioni di un'applicazione sono adeguate o meno usando indicatori di prestazioni, ad esempio la velocità di elaborazione di una richiesta utente da parte di un'applicazione, la quantità di dati elaborata da un'applicazione per ogni richiesta, il numero di richieste elaborate da un'applicazione in un periodo specifico, la durata dell'attesa da parte di un utente per ottenere una risposta dopo l'invio della richiesta. I termini tecnici per definire questi indicatori di prestazioni sono IOPS, velocità effettiva o larghezza di banda e latenza.

In questa sezione verranno illustrati gli indicatori di prestazioni comuni nel contesto dell'Archiviazione Premium. Nella sezione seguente, Recupero dei requisiti dell'applicazione, verrà illustrato come misurare questi indicatori di prestazioni dell'applicazione. In Ottimizzazione delle prestazioni dell'applicazione verranno descritti infine i fattori che influiscono su questi indicatori di prestazioni e verranno fornite indicazioni utili per l'ottimizzazione degli indicatori.

## <a name="iops"></a>Operazioni di I/O al secondo

IOPS, o operazioni di input/output al secondo, indica il numero di richieste inviate dall'applicazione ai dischi di archiviazione in un secondo. Un'operazione di input/output può essere di lettura, scrittura, sequenziale o casuale. Le applicazioni OLTP (Online Transaction Processing) come un sito Web per la rivendita online devono elaborare immediatamente molte richieste utente concomitanti. Le richieste utente sono transazioni di database con un numero elevato di inserimenti e aggiornamenti, che devono essere elaborate rapidamente dall'applicazione. Le applicazioni OLTP richiedono quindi valori molto elevati per IOPS. Queste applicazioni gestiscono milioni di richieste I/O di piccole dimensioni e casuali. Se si crea un'applicazione di questo tipo, sarà necessario progettare l'infrastruttura dell'applicazione in modo che sia ottimizzata per IOPS. Nella sezione successiva, *Ottimizzazione delle prestazioni dell'applicazione*, verranno illustrati in modo dettagliato tutti i fattori da valutare per ottenere valori elevati per IOPS.

Quando si collega un disco di Archiviazione Premium alla VM a scalabilità elevata, Azure effettua automaticamente il provisioning di un numero garantito di IOPS, in base alla specifica del disco. Ad esempio, un disco P50 effettua il provisioning di 7500 IOPS. Ogni dimensione di VM a scalabilità elevata prevede anche un limite di IOPS specifico sostenibile. Ad esempio, una VM GS5 Standard ha un limite di 80.000 IOPS.

## <a name="throughput"></a>Velocità effettiva

La velocità effettiva, o larghezza di banda, è la quantità di dati che l'applicazione invia ai dischi di archiviazione in un intervallo specificato. Se l'applicazione esegue operazioni di input/output con dimensioni elevate per le unità I/O, richiederà una velocità effettiva elevata. Le applicazioni di tipo data warehouse tendono a emettere operazioni che richiedono quantità elevate di analisi, accedono a grandi porzioni di dati ed eseguono in genere operazione in blocco. In altri termini, queste applicazioni richiedono una velocità effettiva più elevata. Se si crea un'applicazione di questo tipo, sarà necessario progettarne l'infrastruttura in modo che sia ottimizzata per la velocità effettiva. Nella sezione successiva verranno illustrati in modo dettagliato i fattori da ottimizzare per ottenere questo risultato.

Quando si collega un disco di Archiviazione Premium a una macchina virtuale a scalabilità elevata, Azure effettua automaticamente il provisioning della velocità effettiva in base alla specifica del disco. Ad esempio, un disco P50 effettua il provisioning di una velocità effettiva di 250 MB al secondo per il disco. Ogni dimensione di macchina virtuale a scalabilità elevata prevede anche un limite di velocità effettiva specifico sostenibile. Ad esempio, una VM GS5 Standard ha una velocità effettiva massima di 2.000 MB al secondo.

Come illustrato dalla formula seguente, esiste una relazione tra la velocità effettiva e IOPS.

![Relazione tra IOPS e velocità effettiva](../articles/virtual-machines/linux/media/premium-storage-performance/image1.png)

È quindi importante determinare i valori ottimali per IOPS e velocità effettiva richiesti dall'applicazione. I tentativi di ottimizzazione di uno dei valori influiscono anche sull'altro. In una sezione successiva, *Ottimizzazione delle prestazioni dell'applicazione*, verrà illustrata in modo dettagliato l'ottimizzazione di IOPS e velocità effettiva.

## <a name="latency"></a>Latency

La latenza è il tempo necessario perché un'applicazione riceva una singola richiesta, la invii ai dischi di archiviazione e restituisca la risposta al client. Si tratta di una misura essenziale delle prestazioni di un'applicazione, oltre a IOPS e velocità effettiva. La latenza di un disco di Archiviazione Premium è il tempo necessario per recuperare le informazioni per una richiesta e restituirle all'applicazione. L'Archiviazione Premium offre latenze uniformemente basse. I dischi Premium sono progettati per offrire latenze di pochi millisecondi a cifra singola per la maggior parte delle operazioni di I/O. Se si abilita la memorizzazione nella cache host ReadOnly nei dischi di Archiviazione Premium, sarà possibile ottenere una latenza di lettura molto più bassa. La memorizzazione nella cache dei dischi sarà illustrata in modo più dettagliato nella sezione successiva, *Ottimizzazione delle prestazioni dell'applicazione*.

Quando si ottimizza l'applicazione per ottenere valori più elevati per IOPS e velocità effettiva, ciò influirà sulla latenza dell'applicazione. Dopo il perfezionamento delle prestazioni dell'app, è necessario valutare sempre la latenza dell'applicazione per evitare comportamenti imprevisti con latenza elevata.

Le operazioni del piano di controllo seguenti su Managed Disks possono comportare lo spostamento del disco da una posizione di archiviazione a un'altra. Questa operazione viene orchestrata tramite una copia in background dei dati che può richiedere diverse ore, in genere meno di 24 ore, in base alla quantità di dati nei dischi. Durante questo periodo, l'applicazione può riscontrare una latenza di lettura superiore alla consueta, perché alcune letture possono essere reindirizzate al percorso originale e il completamento può richiedere più tempo. Non è previsto alcun impatto sulla latenza di scrittura durante questo periodo.

- Aggiornare il tipo di archiviazione.
- Scollegare e connettere un disco da una macchina virtuale a un'altra.
- Creare un disco gestito da un disco rigido virtuale.
- Creare un disco gestito da uno snapshot.
- Convertire i dischi non gestiti in Managed Disks.

# <a name="performance-application-checklist-for-disks"></a>Elenco di controllo per le prestazioni dell'applicazione per i dischi

Il primo passaggio nella progettazione di applicazioni a prestazioni elevate in esecuzione nell'Archiviazione Premium di Azure consiste nel comprendere i requisiti relativi alle prestazioni dell'applicazione. Dopo aver raccolto i requisiti relativi alle prestazioni, sarà possibile ottimizzare l'applicazione per ottenere le prestazioni più elevate possibili.

Nella sezione precedente sono stati illustrati gli indicatori di prestazioni comuni, ovvero IOPS, velocità effettiva e latenza. È necessario identificare gli indicatori di prestazioni essenziali per consentire all'applicazione di offrire l'esperienza utente desiderata. Ad esempio, un valore elevato per IOPS è molto importante per le applicazioni OLTP che elaborano milioni di transazioni al secondo. Una velocità effettiva elevata, invece, è essenziale per applicazioni di tipo data warehouse che elaborano quantità elevate di dati in un secondo. Una latenza estremamente bassa è critica per applicazioni in tempo reale come siti Web per lo streaming di video.

È quindi necessario misurare i requisiti massimi relativi alle prestazioni per l'intero ciclo di vita dell'applicazione. Per iniziare, usare l'elenco di controllo di esempio seguente. Registrare i requisiti massimi relativi alle prestazioni durante i periodi di carico di lavoro normale, di picco e in orari di minore attività. L'identificazione dei requisiti per tutti i livelli di carico di lavoro consente di determinare i requisiti complessivi relativi alle prestazioni per l'applicazione. Ad esempio, il carico di lavoro normale di un sito Web per l'e-commerce sarà costituito dalle transazioni gestite durante la maggior parte dei giorni in un anno. Il carico di lavoro di picco del sito Web sarà costituito dalle transazioni gestite durante le festività o in caso di svendite speciali. Il carico di lavoro di picco è in genere relativo a un periodo limitato, ma può richiedere un ridimensionamento dell'applicazione pari a due o più volte i livelli necessari per il funzionamento normale. Occorre trovare i requisiti relativi al 50° percentile, al 90° percentile e al 99° percentile. Ciò consente di escludere tramite filtri eventuali outlier nei requisiti relativi alle prestazioni e di concentrarsi sull'ottimizzazione dei valori corretti.

## <a name="application-performance-requirements-checklist"></a>Elenco di controllo per i requisiti relativi alle prestazioni dell'applicazione

| **Requisiti relativi alle prestazioni** | **50° percentile** | **90° percentile** | **99° percentile** |
| --- | --- | --- | --- |
| Max. Transazioni al secondo | | | |
| % di operazioni di lettura | | | |
| % di operazioni di scrittura | | | |
| % di operazioni casuali | | | |
| % di operazioni sequenziali | | | |
| Dimensioni delle richieste I/O | | | |
| Velocità effettiva media | | | |
| Max. Velocità effettiva | | | |
| Min Latency | | | |
| Latenza media | | | |
| Max. CPU | | | |
| Utilizzo medio CPU | | | |
| Max. Memoria | | | |
| Memoria media | | | |
| Profondità coda | | | |

> [!NOTE]
> è consigliabile prendere in considerazione il ridimensionamento di questi numeri sulla base della crescita futura prevista per l'applicazione. È consigliabile prepararsi con anticipo alla crescita dell'applicazione, perché in seguito potrebbe risultare più difficile modificare l'infrastruttura per migliorare le prestazioni.

Se si ha già un'applicazione e si vuole passare all'Archiviazione Premium, creare prima di tutto l'elenco di controllo precedente per l'applicazione esistente. Creare quindi un prototipo dell'applicazione nell'Archiviazione Premium e progettare l'applicazione in base alle indicazioni disponibili nella sezione *Ottimizzazione delle prestazioni dell'applicazione* più avanti in questo documento. L'articolo successivo illustra gli strumenti disponibili per raccogliere le misurazioni relative alle prestazioni.

### <a name="counters-to-measure-application-performance-requirements"></a>Contatori per misurare i requisiti relativi alle prestazioni per l'applicazione

Il modo migliore per misurare i requisiti relativi alle prestazioni per l'applicazione consiste nell'usare gli strumenti per il monitoraggio delle prestazioni forniti dal sistema operativo del server. È possibile usare PerfMon per Windows e iostat per Linux. Questi strumenti acquisiscono i contatori corrispondenti a ogni misura illustrata nella sezione precedente. È necessario acquisire i valori di questi contatori quando l'applicazione esegue i carichi di lavoro normali, di picco e di fuori orario.

Sono disponibili contatori di PerfMon per il processore, la memoria e ogni disco logico e fisico del server. Quando si usano dischi di Archiviazione Premium con una VM, i contatori per dischi fisici sono relativi a ogni disco di Archiviazione Premium e i contatori per dischi logici sono relativi a ogni volume creato nei dischi di Archiviazione Premium. È necessario acquisire i valori per i dischi che ospitano il carico di lavoro dell'applicazione. Se è disponibile il mapping uno a uno tra i dischi logici e i dischi fisici, sarà possibile fare riferimento ai contatori per i dischi fisici. In caso contrario, fare riferimento ai contatori per i dischi logici. In Linux il comando iostat genera un report relativo all'utilizzo della CPU e dei dischi. Il report di utilizzo dischi fornisce statistiche relative al singolo dispositivo o alla singola partizione. Se si dispone di un server di database con i relativi dati e si accede a dischi distinti, raccogliere i dati per entrambi i dischi. La tabella seguente descrive i contatori per i dischi, i processori e la memoria:

| Contatore | Description | PerfMon | Iostat |
| --- | --- | --- | --- |
| **IOPS o transazioni al secondo** |Numero di richieste I/O rilasciate al disco di archiviazione a secondo. |Letture disco/sec <br> Scritture disco/sec |tps <br> r/s <br> w/s |
| **Letture e scritture del disco** |% di operazioni di lettura e scrittura eseguite sul disco. |% Tempo lettura disco <br> % Tempo scrittura disco |r/s <br> w/s |
| **Velocità effettiva** |Quantità di dati letti da o scritti nel disco al secondo. |Byte letti da disco/sec <br> Byte scritti su disco/sec |kB_read/s <br> kB_wrtn/s |
| **Latency** |Tempo totale necessario per completare una richiesta I/O per il disco. |Media letture disco/sec <br> Media scritture disco/sec |await <br> svctm |
| **Dimensioni I/O** |Dimensioni delle richieste I/O rilasciate ai dischi di archiviazione. |Media byte letti da disco <br> Media byte scritti su disco |avgrq-sz |
| **Profondità coda** |Numero di richieste I/O in attesa che devono essere lette da o scritte nel disco di archiviazione. |Lunghezza corrente coda su disco |avgqu-sz |
| **Max. memoria** |Quantità di memoria necessaria per eseguire correttamente un'applicazione. |% byte vincolati in uso |Use vmstat |
| **Max. CPU** |Quantità di CPU necessaria per eseguire correttamente un'applicazione. |% tempo processore |%util |

Altre informazioni su [iostat](https://linux.die.net/man/1/iostat) e [PerfMon](https://msdn.microsoft.com/library/aa645516.aspx).



## <a name="optimize-application-performance"></a>Ottimizzare le prestazioni delle applicazioni

I fattori principali che influiscono sulle prestazioni di un'applicazione in esecuzione nell'archiviazione Premium sono la natura delle richieste di i/o, le dimensioni della macchina virtuale, le dimensioni del disco, il numero di dischi, la memorizzazione nella cache del disco, il multithreading e la profondità È possibile controllare alcuni di questi fattori con manopole fornite dal sistema. È possibile che la maggior parte delle applicazioni non consenta di modificare direttamente le dimensioni di I/O e la profondità della coda. Ad esempio, se si usa SQL Server, non sarà possibile scegliere le dimensioni di I/O e la profondità della coda. SQL Server sceglie i valori ottimali per le dimensioni di I/O e la profondità della coda per ottenere le prestazioni migliori possibili. È importante comprendere gli effetti di entrambi i tipi di fattori sulle prestazioni dell'applicazione, in modo da effettuare il provisioning delle risorse appropriate per soddisfare le esigenze relative alle prestazioni.

In questa sezione è consigliabile vedere l'elenco di controllo creato relativo ai requisiti dell'applicazione per identificare le esigenze di ottimizzazione per le prestazioni dell'applicazione. L'elenco di controllo consentirà di determinare i fattori da perfezionare tra quelli illustrati in questa sezione. Per verificare gli effetti di ogni fattore sulle prestazioni dell'applicazione, eseguire gli strumenti di benchmarking sulla configurazione dell'applicazione. Vedere l'articolo benchmarking, collegato alla fine, per i passaggi necessari per eseguire gli strumenti di benchmarking comuni nelle VM Windows e Linux.

### <a name="optimize-iops-throughput-and-latency-at-a-glance"></a>Ottimizzazione immediata di IOPS, velocità effettiva e latenza

La tabella seguente riepiloga i fattori delle prestazioni e i passaggi necessari per ottimizzare IOPS, velocità effettiva e latenza. Le sezioni successive al riepilogo illustreranno ogni fattore in modo più dettagliato.

Per altre informazioni sulle dimensioni delle macchine virtuali e le operazioni di I/O al secondo, la velocità effettiva e la latenza disponibili per ogni tipo di VM, vedere [Dimensioni delle macchine virtuali Linux](../articles/virtual-machines/linux/sizes.md) o [Dimensioni delle macchine virtuali Windows](../articles/virtual-machines/windows/sizes.md).

| &nbsp; | **IOPS** | **Velocità effettiva** | **Latency** |
| --- | --- | --- | --- |
| **Scenario di esempio** |Applicazione OLTP aziendale che richiede una frequenza molto elevata di transazioni al secondo. |Applicazione aziendale di tipo data warehouse che elabora quantità elevate di dati. |Applicazioni quasi in tempo reale che necessitano di risposte immediate alle richieste degli utenti, ad esempio i giochi online. |
| Fattori relativi alle prestazioni | &nbsp; | &nbsp; | &nbsp; |
| **Dimensioni I/O** |Dimensioni I/O ridotte producono valori superiori per IOPS. |Dimensioni I/O maggiori producono una velocità effettiva superiore. | &nbsp;|
| **Dimensioni macchina virtuale** |Usare una dimensione di VM che offre IOPS superiori ai requisiti dell'applicazione. |Usare una dimensione di VM con un limite di velocità effettiva superiore ai requisiti dell'applicazione. |Usare una dimensione di VM che offre limiti di ridimensionamento superiori ai requisiti dell'applicazione. |
| **Dimensioni disco** |Usare una dimensione di disco che offre IOPS superiori ai requisiti dell'applicazione. |Usare una dimensione di disco con un limite di velocità effettiva superiore ai requisiti dell'applicazione. |Usare una dimensione di disco che offre limiti di ridimensionamento superiori ai requisiti dell'applicazione. |
| **Limiti relativi al ridimensionamento di VM e dischi** |Il limite di IOPS della dimensione di VM scelta deve essere superiore al totale di IOPS basato sul disco di Archiviazione Premium collegato alla VM. |Il limite di velocità effettiva della dimensione di VM scelta deve essere superiore al totale di velocità effettiva basato sul disco di Archiviazione Premium collegato alla VM. |I limiti di ridimensionamento della dimensione di VM scelta devono essere superiori al totale dei limiti di ridimensionamento dei dischi di Archiviazione Premium collegati. |
| **Memorizzazione nella cache del disco** |Abilitare la cache ReadOnly nei dischi di Archiviazione Premium con operazioni con numero elevato di letture per ottenere un valore di IOPS di lettura più elevato. | &nbsp; |Abilitare la cache ReadOnly nei dischi di Archiviazione Premium con operazioni con numero elevato di letture per ottenere un valore molto basso per le latenze di lettura. |
| **Striping del disco** |Usare più dischi ed eseguirne lo striping per ottenere un limite combinato più elevato per IOPS e velocità effettiva. Il limite combinato per macchina virtuale deve essere superiore ai limiti combinati dei dischi Premium collegati. | &nbsp; | &nbsp; |
| **Dimensioni di striping** |Dimensioni di striping ridotte per modelli di I/O casuali presenti in applicazioni OLTP. Usare, ad esempio, le dimensioni di striping di 64 KB per SQL Server applicazione OLTP. |Dimensioni di striping superiori per modelli di I/O sequenziali di grandi dimensioni presenti in applicazioni di tipo data warehouse. Ad esempio, usare dimensioni di striping di 256 KB per SQL Server applicazione data warehouse. | &nbsp; |
| **Multithreading** |Usare il multithreading per effettuare il push di un numero più elevato di richieste nell'Archiviazione Premium, in modo da ottenere valori più elevati per IOPS e velocità effettiva. Ad esempio, in SQL Server impostare un valore elevato per MAXDOP, in modo da allocare un numero maggiore di CPU a SQL Server. | &nbsp; | &nbsp; |
| **Profondità coda** |Una profondità maggiore per la coda genera valori più elevati per IOPS. |Una profondità maggiore per la coda genera valori più elevati per la velocità effettiva. |Una profondità minore per la coda genera latenze più basse. |

## <a name="nature-of-io-requests"></a>Natura delle richieste I/O

Una richiesta I/O è un'unità di operazioni di input/output che verrà eseguita dall'applicazione. L'identificazione della natura delle richieste I/O, ovvero casuali o sequenziali, di lettura o scrittura, grandi o piccole, consentirà di determinare i requisiti relativi alle prestazioni per l'applicazione. È importante comprendere la natura delle richieste di i/o, per prendere le decisioni appropriate durante la progettazione dell'infrastruttura dell'applicazione. IOs deve essere distribuito in modo uniforme per ottenere le migliori prestazioni possibili.

La dimensione di I/O è uno dei fattori più importanti. Le dimensioni di I/O sono le dimensioni della richiesta di operazioni di input/output generata dall'applicazione. Le dimensioni di I/O hanno un impatto significativo sulle prestazioni, in particolare sui valori di IOPS e larghezza di banda che l'applicazione è in grado di ottenere. La formula seguente illustra la relazione tra IOPS, dimensioni di i/o e larghezza di banda/velocità effettiva.  
    ![](media/premium-storage-performance/image1.png)

Alcune applicazioni consentono di modificare le relative dimensioni di I/O, mentre altre applicazioni non lo consentono. Ad esempio, SQL Server determina automaticamente le dimensioni di I/O ottimali e non fornisce agli utenti manopole per la modifica. D'altra parte, Oracle fornisce un parametro denominato [DB\_BLOCK\_SIZE](https://docs.oracle.com/cd/B19306_01/server.102/b14211/iodesign.htm#i28815) che consente di configurare la dimensione delle richieste I/O del database.

Se si usa un'applicazione che non consente la modifica delle dimensioni di I/O, usare le indicazioni disponibili in questo articolo per ottimizzare l'indicatore KPI relativo alle prestazioni più rilevante per l'applicazione. Ad esempio,

* Un'applicazione OLTP genera milioni di richieste I/O piccole e casuali. Per gestire questi tipi di richieste di i/o, è necessario progettare l'infrastruttura dell'applicazione per ottenere un numero maggiore di IOPS.  
* Un'applicazione di tipo data warehouse genera richieste I/O di grandi dimensioni e sequenziali. Per gestire questi tipi di richieste di i/o, è necessario progettare l'infrastruttura dell'applicazione per ottenere una maggiore larghezza di banda o velocità effettiva.

Se si usa un'applicazione che consente la modifica delle dimensioni di I/O, usare questa regola generica per le dimensioni di I/O, oltre ad altre indicazioni relative alle prestazioni.

* Dimensioni di I/O minori per ottenere valori di IOPS più elevati. Ad esempio, 8 KB per un'applicazione OLTP.  
* Dimensioni di I/O maggiori per ottenere valori di larghezza di banda/velocità effettiva più elevati. Ad esempio, 1024 KB per un'applicazione di tipo data warehouse.

Ecco un esempio di come è possibile calcolare i valori di IOPS e larghezza di banda/velocità effettiva per l'applicazione. Prendere in considerazione un'applicazione che usa un disco P30. Il valore massimo di IOPS e larghezza di banda/velocità effettiva che può essere raggiunto da un disco P30 è pari a 5000 IOPS e 200 MB al secondo, rispettivamente. Se l'applicazione richiede il valore di IOPS massimo dal disco P30 e si usano dimensioni di I/O minori, ad esempio 8 KB, il valore di larghezza di banda risultante che si potrà ottenere è pari a 40 MB al secondo. Se l'applicazione richiede il valore massimo di larghezza di banda/velocità effettiva dal disco P30 e si usano dimensioni di I/O maggiori, ad esempio 1024 KB, il valore di IOPS risultante sarà più basso, ad esempio 200 IOPS. È quindi necessario perfezionare le dimensioni di I/O in modo che soddisfino i requisiti relativi a IOPS e velocità effettiva/larghezza di banda dell'applicazione. Nella tabella seguente sono riepilogate le diverse dimensioni di i/o e il valore di IOPS e velocità effettiva corrispondenti per un disco P30.

| Requisiti dell'applicazione | Dimensioni di I/O | Operazioni di I/O al secondo | Velocità effettiva/Larghezza di banda |
| --- | --- | --- | --- |
| Operazioni di I/O al secondo max |8 KB |5\.000 |40 MB al secondo |
| Velocità effettiva massima |1024 KB |200 |200 MB al secondo |
| Velocità effettiva massima + IOPS elevati |64 KB |3\.200 |200 MB al secondo |
| IOPS massimi + Velocità effettiva elevata |32 KB |5\.000 |160 MB al secondo |

Per ottenere valori di IOPS e larghezza di banda più elevati rispetto al valore massimo di un singolo disco di Archiviazione Premium, usare più dischi Premium con striping. Ad esempio, effettuare lo striping di due dischi P30 per ottenere un valore di IOPS combinato di 10.000 IOPS o un valore di velocità effettiva combinato di 400 MB al secondo. Come illustrato nella sezione successiva, è necessario usare una dimensione di VM che supporta i valori combinati di IOPS e velocità effettiva.

> [!NOTE]
> poiché se si aumenta il valore di IOPS o di velocità effettiva aumenterà anche l'altro valore, è necessario assicurarsi di non raggiungere i limiti di velocità effettiva o IOPS del disco o della VM in caso di aumento di uno dei valori.

Per verificare gli effetti delle dimensioni di I/O sulle prestazioni dell'applicazione, è possibile eseguire gli strumenti di benchmarking sulle VM e sui dischi. Creare più esecuzioni dei test e usare diverse dimensioni di I/O per ogni esecuzione per verificarne l'impatto. Per altri dettagli, vedere l'articolo relativo al benchmarking collegato alla fine.

## <a name="high-scale-vm-sizes"></a>Dimensioni delle macchine virtuali a scalabilità elevata

Quando si inizia a progettare un'applicazione, uno dei primi passaggi da eseguire consiste nel scegliere una VM in cui ospitare l'applicazione. L'Archiviazione Premium include dimensioni di VM a scalabilità elevata in grado di eseguire applicazioni che richiedono capacità di calcolo elevate e prestazioni di I/O elevate per il disco locale. Queste VM forniscono processori più veloci, un rapporto più elevato tra memoria e core e un'unità SSD (Solid-State Drive) per il disco locale. Esempi di macchine virtuali a scalabilità elevata che supportano archiviazione Premium sono le VM serie DS, DSv2 e GS.

Le macchine virtuali a scalabilità elevata sono disponibili in dimensioni diverse con un numero diverso di core CPU, memoria, sistema operativo e dimensioni del disco temporaneo. Ogni dimensione di VM prevede anche un numero massimo di dischi dati che possono essere collegati alla VM. La dimensione di VM scelta influirà quindi sulla quantità di elaborazione, memoria e capacità di archiviazione disponibile per l'applicazione. Influisce anche sui costi di calcolo e archiviazione. Ad esempio, sono riportate di seguito le specifiche per la VM di dimensione massima per le serie DS, DSv2 e GS:

| Dimensioni VM | Core CPU | Memoria | Dimensioni di disco della VM | Max. dischi dati | Dimensioni cache | Operazioni di I/O al secondo | Limiti di I/O della cache della larghezza di banda |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS14 |16 |112 GB |Sistema operativo = 1023 GB <br> SSD locale = 224 GB |32 |576 GB |50.000 IOPS <br> 512 MB al secondo |4\.000 IOPS e 33 MB al secondo |
| Standard_GS5 |32 |448 GB |Sistema operativo = 1023 GB <br> SSD locale = 896 GB |64 |4224 GB |80.000 IOPS <br> 2\.000 MB al secondo |5\.000 IOPS e 50 MB al secondo |

Per visualizzare un elenco completo di tutte le dimensioni delle VM di Azure disponibili, vedere [Dimensioni per le macchine virtuali Windows](../articles/virtual-machines/windows/sizes.md) o [Dimensioni per le macchine virtuali Linux](../articles/virtual-machines/linux/sizes.md). Scegliere una dimensione di VM in grado di soddisfare e adeguarsi ai requisiti relativi alle prestazioni dell'applicazione. Quando si scelgono le dimensioni delle VM, è inoltre necessario esaminare le importanti considerazioni seguenti.

*Limiti di scalabilità*  
I limiti massimi per IOPS per ogni VM e ogni disco sono diversi e indipendenti gli uni dagli altri. Assicurarsi che l'applicazione gestisca i valori di IOPS entro i limiti della VM e dei dischi Premium collegati alla VM. In caso contrario, le prestazioni dell'applicazione verranno limitate.

Si supponga, ad esempio, che i requisiti dell'applicazione siano pari a 4.000 IOPS. Per ottenere questo risultato, effettuare il provisioning di un disco P30 su una VM DS1. Il disco P30 può raggiungere fino a 5.000 IOPS. La VM DS1 è tuttavia limitata a 3.200 IOPS. Le prestazioni dell'applicazione verranno quindi vincolate dal limite della VM di 3.200 IOPS e le prestazioni risulteranno danneggiate. Per evitare questa situazione, scegliere dimensioni per VM e disco in grado di soddisfare entrambi i requisiti dell'applicazione.

*Costo operativo*  
In molti casi è possibile che il costo operativo complessivo con l'Archiviazione Premium sia inferiore al costo dell'uso dell'Archiviazione Standard.

Ad esempio, si consideri un'applicazione che richiede 16.000 IOPS. Per ottenere queste prestazioni, è necessario disporre di una macchina virtuale\_D14 di Azure IaaS standard, che può fornire un numero massimo di IOPS di 16.000 usando i dischi da 1 TB di archiviazione standard 32. Ogni disco di archiviazione standard da 1 TB può ottenere un massimo di 500 IOPS. Il costo stimato di questa VM al mese sarà pari a $ 1.570. Il costo mensile di 32 dischi di archiviazione Standard sarà pari a $ 1.638. Il costo totale mensile stimato sarà pari a $ 3.208.

Se tuttavia la stessa applicazione è ospitata nell'Archiviazione Premium, sarà necessaria una dimensione di VM minore e saranno necessari meno dischi di Archiviazione Premium. Ciò consente una riduzione del costo complessivo. Una VM Standard\_StandardDS13 può soddisfare il requisito di 16.000 IOPS con quattro dischi P30. La VM DS13 offre un valore massimo di IOPS pari a 25.600 e ogni disco P30 ha un valore massimo di IOPS pari a 5.000. Questa configurazione consente complessivamente di ottenere 5.000 x 4 = 20.000 IOPS. Il costo stimato di questa VM al mese sarà pari a $ 1.003. Il costo mensile di quattro dischi di Archiviazione Premium P30 sarà pari a $ 544,34. Il costo totale mensile stimato sarà pari a $ 1.544.

La tabella seguente riepiloga la scomposizione costi di questo scenario per l'Archiviazione Premium e Standard.

| &nbsp; | **Standard** | **Premium** |
| --- | --- | --- |
| **Costo di VM al mese** |$1.570,58 (Standard\_D14) |$1.003,66 (Standard\_DS13) |
| **Costo di dischi al mese** |$1.638,40 (dischi da 1 TB 32) |$ 544,34 (4 x dischi P30) |
| **Costo complessivo al mese** |$ 3.208,98 |$ 1.544,34 |

*Distribuzioni Linux*  

Archiviazione Premium di Azure offre lo stesso livello di prestazioni per le macchine virtuali che eseguono Windows e Linux. Sono supportati molti tipi di distribuzioni di Linux. L'elenco completo è disponibile [qui](../articles/virtual-machines/linux/endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). È importante notare che diverse distribuzioni sono ottimali per diversi tipi di carichi di lavoro. Si otterranno livelli diversi di prestazioni in base alla distribuzione in cui è in esecuzione il carico di lavoro. Testare le distribuzioni di Linux con l'applicazione e scegliere quella migliore per le esigenze specifiche.

Quando si esegue Linux con l'Archiviazione Premium, verificare se sono disponibili aggiornamenti recenti per i driver necessari, in modo da assicurare prestazioni elevate.

## <a name="premium-storage-disk-sizes"></a>Dimensioni dei dischi di Archiviazione Premium

Archiviazione Premium di Azure offre un'ampia gamma di dimensioni per poter scegliere una soluzione più adatta alle proprie esigenze. Ogni dimensione del disco ha un limite di scalabilità diverso per IOPS, larghezza di banda e archiviazione. Scegliere la dimensione del disco di Archiviazione Premium appropriata, in base ai requisiti dell'applicazione e le dimensioni delle VM a scalabilità elevata. La tabella seguente illustra le dimensioni dei dischi e le relative funzionalità. Le dimensioni di disco P4, P6, P15, P60, P70 e P80 sono attualmente supportate solo per Managed Disks.

[!INCLUDE [disk-storage-premium-ssd-sizes](disk-storage-premium-ssd-sizes.md)]

Il numero di dischi scelto dipende dalla dimensione scelta per il disco. È possibile usare un singolo disco P50 o più dischi P10 per soddisfare i requisiti dell'applicazione. Valutare le considerazioni elencate di seguito quando si effettua la scelta.

*Limiti di scalabilità (IOPS e velocità effettiva)*  
I limiti di IOPS e velocità effettiva di ogni dimensione di disco Premium sono diversi e non dipendono dai limiti di scalabilità delle VM. Assicurarsi che il valore totale di IOPS e velocità effettiva rientri nei limiti di scalabilità della dimensione scelta per la VM.

Ad esempio, si supponga che un requisito di applicazione preveda una velocità effettiva massima di 250 MB/sec e si usi una VM DS4 con un singolo disco P30. La VM DS4 può offrire una velocità effettiva massima di 256 MB/sec. Un singolo disco P30 ha tuttavia un limite di velocità effettiva pari a 200 MB/sec. L'applicazione sarà quindi limitata a 200 MB/sec a causa di questo limite del disco. Per superare questo limite, effettuare il provisioning di più dischi dati nella VM oppure ridimensionare i dischi a P40 o P50.

> [!NOTE]
> le operazioni di lettura fornite dalla cache non sono incluse nei valori di IOPS e velocità effettiva, quindi non sono soggette ai limiti del disco. La cache ha un limite di IOPS e velocità effettiva specifico per ogni VM.
>
> Ad esempio, le operazioni di lettura e scrittura iniziali sono rispettivamente pari a 60 MB/sec e 40 MB/sec. Nel corso del tempo, la cache migliora la propria operatività e fornisce un numero sempre maggiore di operazioni di lettura dalla cache. Sarà quindi possibile ottenere una velocità effettiva di lettura superiore dal disco.

*Numero di dischi*  
Determinare il numero di dischi necessari esaminando i requisiti dell'applicazione. Ogni dimensione di VM prevede anche un limite per il numero di dischi che possono essere collegati alla VM. In genere, questo valore corrisponde al doppio dei core. Assicurarsi che la dimensione di VM scelta sia in grado di supportare il numero di dischi necessari.

Occorre ricordare che i dischi di Archiviazione Premium hanno capacità di prestazioni più elevate rispetto ai dischi di Archiviazione Standard. Se quindi si esegue la migrazione dell'applicazione da una VM IaaS di Azure usando il passaggio dall'Archiviazione Standard all'Archiviazione Premium, è probabile che sia necessario un numero inferiore di dischi per ottenere le stesse prestazioni o prestazioni più elevate per l'applicazione.

## <a name="disk-caching"></a>Memorizzazione nella cache del disco

Le VM a scalabilità elevata che sfruttano i vantaggi dell'Archiviazione Premium di Azure includono una tecnologia di memorizzazione nella cache multilivello denominata BlobCache. BlobCache usa una combinazione della RAM della macchina virtuale e dell'unità SSD locale per la memorizzazione nella cache. Questa cache è disponibile per i dischi persistenti di Archiviazione Premium e i dischi locali della VM. Per impostazione predefinita, questa impostazione della cache viene configurata su Read/Write per dischi del sistema operativo e ReadOnly per i dischi dati ospitati nell'Archiviazione Premium. Quando la memorizzazione nella cache su disco è abilitata nei dischi di Archiviazione Premium, le VM a scalabilità elevata possono raggiungere livelli estremamente elevati di prestazioni che superano le prestazioni sottostanti del disco.

> [!WARNING]
> La memorizzazione nella cache del disco non è supportata per i dischi 4 TiB e superiori. Se alla VM sono collegati più dischi, ogni disco con dimensioni inferiori a 4 TiB supporterà la memorizzazione nella cache.
>
> La modifica dell'impostazione della cache di un disco di Azure scollega e ricollega il disco di destinazione. Se si tratta del disco del sistema operativo, la VM viene riavviata. Arrestare tutte le applicazioni/i servizi che potrebbero essere interessati da questa interruzione prima di modificare l'impostazione della cache del disco.

Per altre informazioni sul funzionamento di BlobCache, vedere il post di blog relativo all' [Archiviazione Premium di Azure](https://azure.microsoft.com/blog/azure-premium-storage-now-generally-available-2/) .

È importante abilitare la cache sul set corretto di dischi. L'abilitazione o meno della memorizzazione nella cache su disco in un disco Premium dipende dal modello di carichi di lavoro che verranno gestiti dal disco. La tabella seguente illustra le impostazioni predefinite della cache per i dischi sistema operativo e i dischi dati.

| **Tipo di disco** | **Impostazione predefinita per la cache** |
| --- | --- |
| Disco del sistema operativo |ReadWrite |
| Disco dati |ReadOnly |

Ecco le impostazioni consigliate per la cache su disco per i dischi dati:

| **Impostazione per la memorizzazione nella cache su disco** | **Indicazione sull'uso di questa impostazione** |
| --- | --- |
| Nessuno |Configurare la cache host come None per dischi di sola scrittura e dischi con numero elevato di operazioni di scrittura. |
| ReadOnly |Configurare la cache host come ReadOnly per dischi di sola lettura e di lettura-scrittura. |
| ReadWrite |Configurare la cache host come ReadWrite solo se l'applicazione gestisce correttamente la scrittura di dati memorizzati nella cache in dischi persistenti, quando necessario. |

*ReadOnly*  
Configurando la memorizzazione nella cache ReadOnly nei dischi dati di Archiviazione Premium, è possibile ottenere una latenza di lettura bassa e valori molto elevati di IOPS e velocità effettiva di lettura per l'applicazione. Questo è dovuto ai due motivi seguenti:

1. Le letture eseguite dalla cache, che si trova nella memoria della VM e nell'unità SSD locale, sono più veloci rispetto alle letture dal disco dati, che si trova nell'archivio BLOB di Azure.  
1. L'Archiviazione Premium non include le operazioni di lettura fornite dalla cache nel calcolo dei valori di IOPS e velocità effettiva del disco. L'applicazione è quindi in grado di ottenere valori totali di IOPS e velocità effettiva più elevati.

*ReadWrite*  
Per impostazione predefinita, la memorizzazione nella cache ReadWrite è abilitata nei dischi sistema operativo. È stato recentemente aggiunto il supporto per la memorizzazione nella cache ReadWrite anche sui dischi dati. Se si usa la memorizzazione nella cache ReadWrite, è necessario scrivere in modo corretto i dati dalla cache ai dischi persistenti. Ad esempio, SQL Server gestisce automaticamente la scrittura di dati memorizzati nella cache nei dischi di archiviazione permanente. L'uso della cache di tipo ReadWrite con un'applicazione che non gestisce la persistenza dei dati necessari può provocare la perdita dei dati, in caso di arresto anomalo della VM.

*Nessuno*  
Attualmente, **None** è supportato solo nei dischi dati. Non è supportata nei dischi del sistema operativo. Se si imposta **None** su un disco del sistema operativo, quest'operazione verrà sostituita internamente e impostata su **ReadOnly**.

È ad esempio possibile applicare queste indicazioni a SQL Server in esecuzione sull'Archiviazione Premium seguendo questa procedura:

1. Configurare la cache "ReadOnly" nei dischi di Archiviazione Premium che ospitano i file di dati.  
   a.  Le operazioni rapide di lettura dalla cache riducono il tempo necessario per le query di SQL Server, poiché le pagine di dati vengono recuperate in modo molto più veloce dalla cache rispetto dal recupero diretto dai dischi dati.  
   b.  Le fornitura delle letture dalla cache consente di rendere disponibile velocità effettiva aggiuntiva dai dischi dati Premium. SQL Server può usare questa velocità effettiva aggiuntiva per recuperare un numero superiore di pagine di dati e altre operazioni come il backup/ripristino, i carichi in batch e le ricompilazioni degli indici.  
1. Configurare la cache "None" nei dischi di Archiviazione Premium che ospitano i file di log.  
   a.  I file di log hanno principalmente operazioni intensive a livello di lettura. Non ottengono quindi alcun vantaggio dalla cache ReadOnly.

## <a name="optimize-performance-on-linux-vms"></a>Ottimizzare le prestazioni nelle macchine virtuali Linux

Per tutti i dischi Ultra o le unità SSD Premium con cache impostata su **ReadOnly** o **None**, è necessario disabilitare le "barriere" in fase di montaggio del file system. Non sono necessarie barriere per questo scenario perché le scritture relative ai dischi di archiviazione Premium sono durevoli per queste impostazioni della cache. Quando la richiesta di scrittura viene completata in modo corretto, i dati sono stati scritti nell'archivio persistente. Per disabilitare le "barriere", usare uno dei metodi seguenti. Scegliere l'opzione per il file system:
  
* Per **reiserFS**, per disabilitare le barriere, usare l'opzione di montaggio `barrier=none`. (Per abilitare le barriere, usare `barrier=flush`.)
* Per **ext3/ext4**, per disabilitare le barriere, usare l'opzione di montaggio `barrier=0`. (Per abilitare le barriere, usare `barrier=1`.)
* Per **XFS**, per disabilitare le barriere, usare l'opzione di montaggio `nobarrier`. (Per abilitare le barriere, usare `barrier`.)
* Per i dischi di Archiviazione Premium con cache impostata su **ReadWrite**, abilitare le barriere per la durabilità della scrittura.
* Per far sì che le etichette di volume si mantengano dopo il riavvio della VM, è necessario aggiornare /etc/fstab con i riferimenti degli identificatori universalmente univoci (UUID) ai dischi. Per altre informazioni, vedere [Aggiungere un disco gestito a una VM Linux](../articles/virtual-machines/linux/add-disk.md).

Le distribuzioni Linux seguenti sono state convalidate per le unità SSD Premium. Per migliorare le prestazioni e la stabilità con le unità SSD Premium, è consigliabile aggiornare le macchine virtuali a una di queste versioni o a una versione più recente. 

Alcune versioni richiedono la versione più recente di Linux Integration Services (LIS) 4.0 per Azure. Per scaricare e installare una distribuzione, fare clic sul collegamento riportato nella tabella seguente. Nuove immagini vengono aggiunte all'elenco non appena viene completata la convalida. Le convalide mostrano che le prestazioni variano per ogni immagine. Le prestazioni dipendono dalle caratteristiche del carico di lavoro e dalle impostazioni. Immagini diverse sono ottimizzate per tipi di carico di lavoro diversi.

| Distribuzione | Versione | Kernel supportato | Dettagli |
| --- | --- | --- | --- |
| Ubuntu | 12,04 o versione successiva| 3.2.0-75.110+ | Ubuntu-12_04_5-LTS-amd64-server-20150119-en-us-30GB |
| Ubuntu | 14,04 o versione successiva| 3.13.0-44.73+  | Ubuntu-14_04_1-LTS-amd64-server-20150123-en-us-30GB |
| Debian | 7. x, 8. x o versione successiva| 3.16.7-ckt4-1+ | &nbsp; |
| SUSE | SLES 12 o versione successiva| 3.12.36-38.1+ | suse-sles-12-priority-v20150213 <br> suse-sles-12-v20150213 |
| SUSE | SLES 11 SP4 o versione successiva| 3.0.101-0.63.1+ | &nbsp; |
| CoreOS | 584.0.0 + o versione successiva| 3.18.4+ | CoreOS 584.0.0 |
| CentOS | 6,5, 6,6, 6,7, 7,0 o versione successiva| &nbsp; | [LIS4 richiesto](https://www.microsoft.com/download/details.aspx?id=51612) <br> *Vedere la nota nella sezione successiva* |
| CentOS | 7.1 + o versione successiva| 3.10.0-229.1.2.el7+ | [LIS4 consigliato](https://www.microsoft.com/download/details.aspx?id=51612) <br> *Vedere la nota nella sezione successiva* |
| Red Hat Enterprise Linux (RHEL) | 6.8 +, 7,2 + o versione successiva | &nbsp; | &nbsp; |
| Oracle | 6.0 +, 7,2 + o versione successiva | &nbsp; | UEK4 o RHCK |
| Oracle | 7.0-7.1 o versione successiva | &nbsp; | UEK4 or RHCK con [LIS 4.1+](https://www.microsoft.com/download/details.aspx?id=51612) |
| Oracle | 6.4-6.7 o versione successiva | &nbsp; | UEK4 or RHCK con [LIS 4.1+](https://www.microsoft.com/download/details.aspx?id=51612) |

### <a name="lis-drivers-for-openlogic-centos"></a>Driver LIS per Openlogic CentOS

Se si eseguono macchine virtuali OpenLogic CentOS, eseguire il comando seguente per installare i driver più recenti:

```
sudo rpm -e hypervkvpd  ## (Might return an error if not installed. That's OK.)
sudo yum install microsoft-hyper-v
```

Per attivare i nuovi driver, riavviare la macchina virtuale.

## <a name="disk-striping"></a>Striping del disco

Quando una VM a scalabilità elevata è collegata ad alcuni dischi persistenti di Archiviazione Premium, è possibile effettuare lo striping dei dischi per aggregare le relative capacità di IOPS, larghezza di banda e archiviazione.

In Windows è possibile usare gli spazi di archiviazione per lo striping dei dischi. È necessario configurare una colonna per ogni disco in un pool. In caso contrario, le prestazioni complessive di un volume con striping possono essere inferiori al previsto, a causa della distribuzione non uniforme del traffico nei dischi.

Importante: l'uso dell'interfaccia utente di Gestione server consente di impostare il numero totale di colonne fino a un massimo di 8 per un volume con striping. Quando si alleghino più di otto dischi, usare PowerShell per creare il volume. L'uso di PowerShell consente di impostare un numero di colonne uguale al numero di dischi. Ad esempio, se un singolo set di striping include 16 dischi, specificare 16 colonne nel parametro *NumberOfColumns* del cmdlet *New-VirtualDisk* di PowerShell.

In Linux usare l'utilità MDADM per lo striping dei dischi. Per informazioni dettagliate sulla procedura di striping dei dischi su Linux, vedere [Configurare RAID software in Linux](../articles/virtual-machines/linux/configure-raid.md).

*Dimensioni di striping*  
Un elemento importante della configurazione dello striping del disco è la dimensione di striping. La dimensione di striping o la dimensione del blocco è il blocco più piccolo di dati che l'applicazione può gestire in un volume con striping. La dimensione di striping configurabile dipende dal tipo di applicazione e dal relativo modello di richieste. Se si sceglie la dimensione di striping errata, è possibile che si ottenga un allineamento di I/O non corretto, che porta a prestazioni degradate per l'applicazione.

Ad esempio, se una richiesta I/O generata dall'applicazione è maggiore della dimensione di striping del disco, il sistema di archiviazione la scrive oltre i limiti di unità di striping in più dischi. Quando è necessario accedere ai dati, occorrerà cercarli in più unità di striping per completare la richiesta. L'effetto cumulativo di questo comportamento può portare a una riduzione significativa delle prestazioni. D'altra parte, se la dimensione della richiesta I/O è minore della dimensione di striping ed è di tipo casuale, è possibile che le richieste I/P si concentrino sullo stesso disco, provocando un collo di bottiglia e danneggiando le prestazioni di I/O.

Scegliere una dimensione di striping appropriata in base a tipo di carico di lavoro eseguito dall'applicazione. Per richieste I/O di piccole dimensioni e casuali, usare una dimensione di striping minore. Mentre per le richieste di i/o sequenziali di grandi dimensioni vengono utilizzate dimensioni di striping maggiori. Esaminare le indicazioni relative alle dimensioni di striping per l'applicazione da eseguire nell'Archiviazione Premium. Per SQL Server, configurare la dimensione di striping di 64 KB per i carichi di lavoro OLTP e 256 KB per i carichi di lavoro di data warehousing. Per altre informazioni, vedere [Procedure consigliate per le prestazioni per SQL Server in Macchine virtuali di Azure](../articles/virtual-machines/windows/sql/virtual-machines-windows-sql-performance.md#disks-guidance) .

> [!NOTE]
> è possibile effettuare lo striping di un massimo di 32 dischi di Archiviazione Premium in una VM di serie DS e di 64 dischi di Archiviazione Premium in una VM di serie GS.

## <a name="multi-threading"></a>Multithreading

Azure ha progettato la piattaforma di Archiviazione Premium in modo che sia notevolmente parallela. Un'applicazione multithreading ottiene prestazioni molto più elevate rispetto a un'applicazione a thread singolo. Un'applicazione multithreading suddivide le proprie attività in più thread e aumenta l'efficienza dell'esecuzione utilizzando al massimo la VM e le risorse del disco.

Ad esempio, se l'applicazione è in esecuzione su una VM a core singolo con due thread, la CPU può passare da un thread all'altro per ottenere l'efficienza. Mentre un thread attende il completamento di un'operazione I/O su disco, la CPU può passare all'altro thread. In questo modo, due thread possono ottenere molto di più rispetto a un singolo thread. Se la VM ha più di un core, riduce ulteriormente il tempo di esecuzione, perché ogni core può eseguire attività in parallelo.

È possibile che non si riesca a cambiare il modo in cui un'applicazione non modificabile implementa la modalità a thread singolo o il multithreading. Ad esempio, SQL Server è in grado di gestire più CPU e più core. SQL Server decide tuttavia in che condizioni sfruttare i vantaggi di uno o più thread per elaborare una query. Può eseguire query e compilare indici usando il multithreading. Per una query che comporta l'unione di più tabelle e l'ordinamento dei dati prima di restituire risultati all'utente, SQL Server userà probabilmente più thread. Un utente non può tuttavia controllare se SQL Server esegue una query usando un singolo thread o più thread.

È possibile modificare alcune impostazioni di configurazione per influenzare l'elaborazione multithreading o parallela di un'applicazione. Ad esempio, nel caso di SQL Server è possibile modificare la configurazione Massimo grado parallelismo. Questa impostazione, denominata MAXDOP, consente di configurare il numero massimo di processori che SQL Server può usare durante l'elaborazione parallela. È possibile configurare MAXDOP per singole query o per operazioni sull'indice. Questa configurazione risulta utile per bilanciare le risorse del sistema per un'applicazione essenziale per le prestazioni.

Ad esempio, si supponga che l'applicazione che usa SQL Server stia eseguendo contemporaneamente una query di dimensioni elevate e un'operazione sull'indice. Si supponga di volere migliorare le prestazioni dell'operazione sull'indice rispetto alla query di grandi dimensioni. In questo caso è possibile impostare il valore MAXDOP dell'operazione sull'indice in modo che sia superiore al valore MAXDOP per la query. In questo modo SQL Server avrà un numero maggiore di processori da sfruttare per l'operazione sull'indice rispetto al numero di processori da dedicare alla query di grandi dimensioni. Occorre ricordare che non si può controllare il numero di thread usati da SQL Server per ogni operazione. È possibile controllare il numero massimo di processori dedicati al multithreading.

Altre informazioni sui [Gradi di parallelismo](https://technet.microsoft.com/library/ms188611.aspx) in SQL Server. Individuare le impostazioni che influenzano il multithreading nell'applicazione e le relative configurazioni per ottimizzare le prestazioni.

## <a name="queue-depth"></a>Profondità coda

La profondità della coda o la lunghezza della coda o della coda corrisponde al numero di richieste di i/o in sospeso nel sistema. Il valore della profondità della coda determina il numero di operazioni di i/o che l'applicazione può allineare, che verrà elaborata dai dischi di archiviazione. Influiscono su tutti i tre indicatori di prestazioni dell'applicazione descritti in questo articolo, ovvero IOPS, velocità effettiva e latenza.

La profondità della coda e il multithreading sono strettamente correlati. Il valore della profondità della coda indica la quantità di multithreading che l'applicazione può ottenere. Se il valore della profondità della coda è elevato, l'applicazione potrà eseguire più applicazioni contemporaneamente, ovvero potrà ottenere un multithreading maggiore. Se il valore della profondità della coda è ridotto, anche se l'applicazione è abilitata al multithreading, il numero di richieste accodate non sarà sufficiente per l'esecuzione simultanea.

In genere, le applicazioni standard non consentono di modificare la profondità della coda, perché un'impostazione non corretta risulterebbe eccessivamente dannosa. Le applicazioni imposteranno automaticamente il valore di profondità della coda corretto per ottenere prestazioni ottimali. È tuttavia importante comprendere questo concetto, in modo da risolvere i problemi delle prestazioni nell'applicazione. È anche possibile osservare gli effetti della profondità della coda eseguendo gli strumenti di benchmarking nel sistema.

Alcune applicazioni forniscono le impostazioni necessarie per influenzare la profondità della coda, ad esempio, l'impostazione MAXDOP (Massimo grado parallelismo) in SQL Server illustrata nella sezione precedente. MAXDOP consente di influenzare la profondità della coda e il multithreading, anche se non modifica direttamente il valore Profondità coda di SQL Server.

*Profondità coda elevata*  
Un valore elevato per la profondità della coda consente di allineare più operazioni sul disco. Il disco conosce in anticipo la richiesta successiva nella coda. Di conseguenza, il disco può pianificare in anticipo le operazioni ed elaborarle nella sequenza ottimale. Poiché l'applicazione invia più richieste al disco, il disco potrà elaborare un numero maggiore di I/O paralleli. L'applicazione sarà infine in grado di ottenere valori più elevati per IOPS. Poiché l'applicazione elabora un numero maggiore di richieste, aumenterà anche la velocità effettiva dell'applicazione.

In genere, un'applicazione può ottenere una velocità effettiva massima con 8-16+ I/O in attesa per ogni disco collegato. Se è presente una profondità della coda, l'applicazione non sta effettuando il push di IOs al sistema e verrà elaborata una minore quantità di in un determinato periodo. In altri termini, si otterrà una velocità effettiva minore.

In SQL Server ,ad esempio, l'impostazione del valore MAXDOP per una query su "4" indica a SQL Server che può usare al massimo quattro core per eseguire la query. SQL Server determinerà il valore migliore per la profondità della coda e il numero di core per l'esecuzione della query.

*Profondità della coda ottimale*  
Un valore molto elevato per la coda può avere alcuni svantaggi. Se il valore della profondità della coda è troppo alto, l'applicazione proverà a effettuare un numero molto elevato di IOPS. A meno che un'applicazione non abbia dischi persistenti con un numero sufficiente di IOPS con provisioning, ciò può influire negativamente sulle latenze dell'applicazione. La formula seguente illustra la relazione tra IOPS, latenza e profondità della coda.  
    ![](media/premium-storage-performance/image6.png)

È consigliabile non configurare la profondità della coda su un valore elevato, specificando invece un valore ottimale, in grado di offrire un numero di IOPS sufficiente per l'applicazione, senza influire sulle latenze. Ad esempio, se la latenza dell'applicazione deve essere pari a 1 millisecondo, la profondità della coda necessaria per ottenere 5.000 IOPS sarà QD = 5000 x 0,001 = 5.

*Profondità della coda per un volume con striping*  
Per un volume con striping è consigliabile mantenere una profondità della coda sufficientemente elevata da consentire a ogni disco di avere individualmente un picco di profondità della coda. Si consideri, ad esempio, un'applicazione che esegue il push di una profondità della coda pari a 2 e sono presenti quattro dischi nello striping. Le due richieste I/O verranno trasmesse a due dischi e i due dischi rimanenti saranno inattivi. È quindi consigliabile configurare la profondità della coda in modo che tutti i dischi siano occupati. La formula seguente illustra come determinare la profondità della coda dei volumi con striping.  
    ![](media/premium-storage-performance/image7.png)

## <a name="throttling"></a>Limitazione

L'Archiviazione Premium di Azure effettua il provisioning di un numero specificato di IOPS e di velocità effettiva in base alle dimensioni delle VM e alle dimensioni dei dischi scelte. Ogni volta che l'applicazione prova a superare i limiti di IOPS o velocità effettiva che possono essere gestiti dalla VM o dal disco, l'Archiviazione Premium limiterà l'applicazione. Questo problema si manifesta sotto forma di una riduzione delle prestazioni dell'applicazione. Ciò può comportare latenza più elevata, velocità effettiva inferiore o IOPS inferiori. Se l'Archiviazione Premium non applica la limitazione, è possibile che si verifichi un errore irreversibile dell'applicazione a causa del superamento dei limiti delle risorse. Per evitare problemi di prestazioni dovuti alla limitazione, è necessario effettuare sempre il provisioning di un numero di risorse sufficiente per l'applicazione. Prendere in considerazione i concetti illustrati nelle sezioni precedenti relative alle dimensioni delle VM e dei dischi. Il benchmarking è il modo migliore per determinare le risorse necessarie per l'hosting dell'applicazione.

## <a name="next-steps"></a>Passaggi successivi

