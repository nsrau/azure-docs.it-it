---
title: 'Archiviazione Premium di Azure: progettata per prestazioni elevate | Microsoft Docs'
description: Progettare applicazioni a prestazioni elevate con l'Archiviazione Premium di Azure. Archiviazione Premium offre prestazioni elevate e supporto per dischi a bassa latenza per carichi di lavoro con I/O intensivo in esecuzione su Macchine virtuali di Azure.
services: storage
documentationcenter: na
author: aungoo-msft
manager: tadb
editor: tysonn

ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2016
ms.author: aungoo

---
# <a name="azure-premium-storage:-design-for-high-performance"></a>Archiviazione Premium di Azure: progettata per prestazioni elevate
## <a name="overview"></a>Overview
Questo articolo fornisce indicazioni per lo sviluppo di applicazioni a prestazioni elevate mediante l'Archiviazione Premium di Azure. È possibile usare le istruzioni disponibili in questo documento insieme alle procedure consigliate per le prestazioni applicabili alle tecnologie usate dall'applicazione. Per illustrare le indicazioni, è stato usato SQL Server in esecuzione nell'Archiviazione Premium come esempio nell'intero documento.

In questo articolo vengono trattati scenari relativi alle prestazioni per il livello dell'archiviazione, ma sarà necessario ottimizzare il livello dell'applicazione. Ad esempio, se si ospita una farm SharePoint nell'Archiviazione Premium di Azure, è possibile usare gli esempi relativi a SQL Server di questo articolo per ottimizzare il server di database. È anche possibile ottimizzare il server Web e il server applicazioni della farm SharePoint per ottenere prestazioni migliori.

Questo articolo è utile per rispondere alle domande comuni seguenti sull'ottimizzazione delle prestazioni dell'applicazione nell'Archiviazione Premium di Azure:

* Come si misurano le prestazioni dell'applicazione?  
* Perché non si ottengono le prestazioni elevate previste?  
* Quali fattori influenzano le prestazioni dell'applicazione nell'Archiviazione Premium?  
* In che modo questi fattori influenzano le prestazioni dell'applicazione nell'Archiviazione Premium?  
* Come si ottiene l'ottimizzazione per IOPS, larghezza di banda e latenza?  

Queste indicazioni sono specifiche per l'Archiviazione Premium, perché i carichi di lavoro in esecuzione nell'Archiviazione Premium sono influenzati in modo significativo dalle prestazioni. Sono disponibili esempi nei casi appropriati. È anche possibile applicare alcune indicazioni alle applicazioni in esecuzione su VM IaaS con dischi di archiviazione Standard.

Prima di iniziare, se non si ha alcuna esperienza dell'Archiviazione Premium, leggere l'articolo [Archiviazione Premium: archiviazione ad alte prestazioni per carichi di lavoro delle macchine virtuali di Azure](storage-premium-storage.md) e [Obiettivi di scalabilità e prestazioni per Archiviazione Premium di Azure](storage-scalability-targets.md#premium-storage-accounts).

## <a name="application-performance-indicators"></a>Indicatori di prestazioni dell'applicazione
È possibile valutare se le prestazioni di un'applicazione sono adeguate o meno usando indicatori di prestazioni, ad esempio la velocità di elaborazione di una richiesta utente da parte di un'applicazione, la quantità di dati elaborata da un'applicazione per ogni richiesta, il numero di richieste elaborate da un'applicazione in un periodo specifico, la durata dell'attesa da parte di un utente per ottenere una risposta dopo l'invio della richiesta. I termini tecnici per definire questi indicatori di prestazioni sono IOPS, velocità effettiva o larghezza di banda e latenza.

In questa sezione verranno illustrati gli indicatori di prestazioni comuni nel contesto dell'Archiviazione Premium. Nella sezione seguente, Recupero dei requisiti dell'applicazione, verrà illustrato come misurare questi indicatori di prestazioni dell'applicazione. In Ottimizzazione delle prestazioni dell'applicazione verranno descritti infine i fattori che influiscono su questi indicatori di prestazioni e verranno fornite indicazioni utili per l'ottimizzazione degli indicatori.

## <a name="iops"></a>IOPS
IOPS indica il numero di richieste inviate dall'applicazione ai dischi di archiviazione in un secondo. Un'operazione di input/output può essere di lettura o di scrittura, sequenziale o casuale. Le applicazioni OLTP, ad esempio un sito Web per la rivendita online, devono elaborare immediatamente molte richieste utente concomitanti. Le richieste utente sono transazioni di database con un numero elevato di inserimenti e aggiornamenti, che devono essere elaborate rapidamente dall'applicazione. Le applicazioni OLTP richiedono quindi valori molto elevati per IOPS. Queste applicazioni gestiscono milioni di richieste I/O di piccole dimensioni e casuali. Se si crea un'applicazione di questo tipo, sarà necessario progettare l'infrastruttura dell'applicazione in modo che sia ottimizzata per IOPS. Nella sezione successiva, *Ottimizzazione delle prestazioni dell'applicazione*, verranno illustrati in modo dettagliato tutti i fattori da valutare per ottenere valori elevati per IOPS.

Quando si collega un disco di Archiviazione Premium alla VM a scalabilità elevata, Azure effettua automaticamente il provisioning di un numero garantito di IOPS, in base alla specifica del disco. Ad esempio, un disco P30 effettua il provisioning di 5000 IOPS. Ogni dimensione di VM a scalabilità elevata prevede anche un limite di IOPS specifico sostenibile. Ad esempio, una VM GS5 Standard ha un limite di 80.000 IOPS.

## <a name="throughput"></a>Velocità effettiva
La velocità effettiva o larghezza di banda è la quantità di dati che l'applicazione invia ai dischi di archiviazione in un intervallo specificato. Se l'applicazione esegue operazioni di input/output con dimensioni elevate per le unità I/O, richiederà una velocità effettiva elevata. Le applicazioni di tipo data warehouse tendono a emettere operazioni che richiedono quantità elevate di analisi, accedono a grandi porzioni di dati ed eseguono in genere operazione in blocco. In altri termini, queste applicazioni richiedono una velocità effettiva più elevata. Se si crea un'applicazione di questo tipo, sarà necessario progettare l'infrastruttura dell'applicazione in modo che sia ottimizzata per la velocità effettiva. Nella sezione successiva verranno illustrati in modo dettagliato i fattori da ottimizzare per ottenere questo risultato.

Quando si collega un disco di Archiviazione Premium a una VM a scalabilità elevata, Azure effettua automaticamente il provisioning della velocità effettiva in base alla specifica del disco. Ad esempio, un disco P30 effettua il provisioning di una velocità effettiva di 200 MB al secondo per il disco. Ogni dimensione di VM a scalabilità elevata prevede anche un limite di velocità effettiva specifico sostenibile. Ad esempio, una VM GS5 Standard ha una velocità effettiva massima di 2.000 MB al secondo.

Come illustrato dalla formula seguente, esiste una relazione tra la velocità effettiva e IOPS.

![](media/storage-premium-storage-performance/image1.png)

È quindi importante determinare i valori ottimali per la velocità effettiva e IOPS richiesti dall'applicazione. I tentativi di ottimizzazione di uno dei valori influiscono anche sull'altro. In una sezione successiva, *Ottimizzazione delle prestazioni dell'applicazione*, verrà illustrata in modo dettagliato l'ottimizzazione di IOPS e velocità effettiva.

## <a name="latency"></a>Latency
La latenza è il tempo necessario perché un'applicazione riceva una singola richiesta, la invii ai dischi di archiviazione e restituisca la risposta al client. Si tratta di una misura essenziale delle prestazioni di un'applicazione, oltre a IOPS e velocità effettiva. La latenza di un disco di Archiviazione Premium è il tempo necessario per recuperare le informazioni per una richiesta e restituirle all'applicazione. L'Archiviazione Premium offre latenze uniformemente basse. Se si abilita la memorizzazione nella cache host ReadOnly nei dischi di Archiviazione Premium, sarà possibile ottenere una latenza di lettura molto più bassa. La memorizzazione nella cache dei dischi sarà illustrata in modo più dettagliato nella sezione successiva, *Ottimizzazione delle prestazioni dell'applicazione*.

Quando si ottimizza l'applicazione per ottenere valori più elevati per IOPS e velocità effettiva, ciò influirà sulla latenza dell'applicazione. Dopo il perfezionamento delle prestazioni dell'app, è necessario valutare sempre la latenza dell'applicazione per evitare comportamenti imprevisti con latenza elevata.

## <a name="gather-application-performance-requirements"></a>Recuperare i requisiti dell'applicazione
Il primo passaggio nella progettazione di applicazioni a prestazioni elevate in esecuzione nell'Archiviazione Premium di Azure consiste nel comprendere i requisiti relativi alle prestazioni dell'applicazione. Dopo la raccolta dei requisiti relativi alle prestazioni, sarà possibile ottimizzare l'applicazione per ottenere le prestazioni più elevate possibili.

Nella sezione precedente sono stati illustrati gli indicatori di prestazioni comuni, ovvero IOPS, velocità effettiva e latenza. È necessario identificare gli indicatori di prestazioni essenziali per consentire all'applicazione di offrire l'esperienza utente desiderata. Ad esempio, un valore elevato per IOPS è molto importante per le applicazioni OLTP che elaborano milioni di transazioni al secondo. Una velocità effettiva elevata, invece, è essenziale per applicazioni di tipo data warehouse che elaborano quantità elevate di dati in un secondo. Una latenza estremamente bassa è critica per applicazioni in tempo reale come siti Web per lo streaming di video.

È quindi necessario misurare i requisiti massimi relativi alle prestazioni per l'intero ciclo di vita dell'applicazione. Per iniziare, usare l'elenco di controllo di esempio seguente. Registrare i requisiti massimi relativi alle prestazioni durante i periodi di carico di lavoro normale, di picco e in orari di minore attività. L'identificazione dei requisiti per tutti i livelli di carico di lavoro consente di determinare i requisiti complessivi relativi alle prestazioni per l'applicazione. Ad esempio, il carico di lavoro normale di un sito Web per l'e-commerce sarà costituito dalle transazioni gestite durante la maggior parte dei giorni in un anno. Il carico di lavoro di picco del sito Web sarà costituito dalle transazioni gestite durante le festività o in caso di svendite speciali. Il carico di lavoro di picco è in genere relativo a un periodo limitato, ma può richiedere un ridimensionamento dell'applicazione pari a due o più volte i livelli necessari per il funzionamento normale. Occorre trovare i requisiti relativi al 50° percentile, al 90° percentile e al 99° percentile. Ciò consente di escludere tramite filtri eventuali outlier nei requisiti relativi alle prestazioni e di concentrarsi sull'ottimizzazione dei valori corretti.

**Elenco di controllo per i requisiti relativi alle prestazioni dell'applicazione**

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

> **Nota importante:**  
> è consigliabile prendere in considerazione il ridimensionamento di questi numeri sulla base della crescita futura prevista per l'applicazione. È consigliabile prepararsi con anticipo alla crescita dell'applicazione, perché in seguito potrebbe risultare più difficile modificare l'infrastruttura per migliorare le prestazioni.
> 
> 

Se si ha già un'applicazione e si vuole passare all'Archiviazione Premium, creare prima di tutto l'elenco di controllo precedente per l'applicazione esistente. Creare quindi un prototipo dell'applicazione nell'Archiviazione Premium e progettare l'applicazione in base alle indicazioni disponibili nella sezione *Ottimizzazione delle prestazioni dell'applicazione* più avanti in questo documento. La sezione successiva illustra gli strumenti disponibili per raccogliere le misurazioni relative alle prestazioni.

Creare un elenco di controllo analogo a quello per l'applicazione esistente per il prototipo. Usando gli strumenti di benchmarking è possibile simulare i carichi di lavoro e misurare le prestazioni nel prototipo dell'applicazione. Per altre informazioni, vedere la sezione [Benchmarking](#benchmarking) . Sarà quindi possibile determinare se l'Archiviazione Premium può soddisfare o sorpassare i requisiti relativi alle prestazioni per l'applicazione. Si potranno quindi implementare le stesse indicazioni per l'applicazione di produzione.

### <a name="counters-to-measure-application-performance-requirements"></a>Contatori per misurare i requisiti relativi alle prestazioni per l'applicazione
Il modo migliore per misurare i requisiti relativi alle prestazioni per l'applicazione consiste nell'usare gli strumenti per il monitoraggio delle prestazioni forniti dal sistema operativo del server. È possibile usare PerfMon per Windows e iostat per Linux. Questi strumenti acquisiscono i contatori corrispondenti a ogni misura illustrata nella sezione precedente. È necessario acquisire i valori di questi contatori quando l'applicazione esegue i carichi di lavoro normali, di picco e di orari di minore attività.

Sono disponibili contatori di PerfMon per il processore, la memoria e ogni disco logico e fisico del server. Quando si usano dischi di Archiviazione Premium con una VM, i contatori per dischi fisici sono relativi a ogni disco di Archiviazione Premium e i contatori per dischi logici sono relativi a ogni volume creato nei dischi di Archiviazione Premium. È necessario acquisire i valori per i dischi che ospitano il carico di lavoro dell'applicazione. Se è disponibile il mapping uno a uno tra i dischi logici e i dischi fisici, sarà possibile fare riferimento ai contatori per i dischi fisici. In caso contrario, fare riferimento ai contatori per i dischi logici. In Linux il comando iostat genera un report relativo all'utilizzo della CPU e dei dischi. Il report di utilizzo dischi fornisce statistiche relative al singolo dispositivo o alla singola partizione. Se è presente un server di database con dati e log in dischi separati, raccogliere questi dati per entrambi i dischi. La tabella seguente illustra i contatori per dischi, processore e memoria:

| Contatore | Descrizione | PerfMon | Iostat |
| --- | --- | --- | --- |
| **IOPS o transazioni al secondo** |Numero di richieste I/O rilasciate al disco di archiviazione a secondo. |Letture disco/sec  <br>  Scritture disco/sec |tps  <br> r/s  <br>  w/s |
| **Letture e scritture del disco** |% di operazioni di lettura e scrittura eseguite sul disco. |% Tempo lettura disco  <br>  % Tempo scrittura disco |r/s  <br>  w/s |
| **Velocità effettiva** |Quantità di dati letti da o scritti nel disco al secondo. |Byte letti da disco/sec  <br>  Byte scritti su disco/sec |kB_read/s <br> kB_wrtn/s |
| **Latency** |Tempo totale necessario per completare una richiesta I/O per il disco. |Media letture disco/sec  <br>  Media scritture disco/sec |await  <br>  svctm |
| **Dimensioni I/O** |Dimensioni delle richieste I/O rilasciate ai dischi di archiviazione. |Media byte letti da disco  <br>  Media byte scritti su disco |avgrq-sz |
| **Profondità coda** |Numero di richieste I/O in attesa che devono essere lette da o scritte nel disco di archiviazione. |Lunghezza corrente coda su disco |avgqu-sz |
| **Max. memoria** |Quantità di memoria necessaria per eseguire correttamente un'applicazione. |% byte vincolati in uso |Use vmstat |
| **Max. CPU** |Quantità di CPU necessaria per eseguire correttamente un'applicazione. |% tempo processore |%util |

Altre informazioni su [iostat](http://linuxcommand.org/man_pages/iostat1.html) e [PerfMon](https://msdn.microsoft.com/library/aa645516.aspx).

## <a name="optimizing-application-performance"></a>Ottimizzazione delle prestazioni dell'applicazione
I fattori principali che influenzano le prestazioni di un'applicazione in esecuzione nell'Archiviazione Premium sono costituiti dalla natura delle richieste I/O, dalle dimensioni della VM e del disco, dal numero di dischi, dalla memorizzazione nella cache del disco, dal multithreading e dalla profondità della coda. È possibile controllare alcuni di questi fattori con manopole fornite dal sistema. È possibile che la maggior parte delle applicazioni non consenta di modificare direttamente le dimensioni di I/O e la profondità della coda. Ad esempio, se si usa SQL Server, non sarà possibile scegliere le dimensioni di I/O e la profondità della coda. SQL Server sceglie i valori ottimali per le dimensioni di I/O e la profondità della coda per ottenere le prestazioni migliori possibili. È importante comprendere gli effetti di entrambi i tipi di fattori sulle prestazioni dell'applicazione, in modo da effettuare il provisioning delle risorse appropriate per soddisfare le esigenze relative alle prestazioni.

In questa sezione è consigliabile vedere l'elenco di controllo creato relativo ai requisiti dell'applicazione per identificare le esigenze di ottimizzazione per le prestazioni dell'applicazione. L'elenco di controllo consentirà di determinare i fattori da perfezionare tra quelli illustrati in questa sezione. Per verificare gli effetti di ogni fattore sulle prestazioni dell'applicazione, eseguire gli strumenti di benchmarking sulla configurazione dell'applicazione. Per informazioni sui passaggi necessari per eseguire gli strumenti di benchmarking comuni in VM Windows e Linux, vedere la sezione [Benchmarking](#Benchmarking) alla fine dell'articolo.

### <a name="optimizing-iops,-throughput-and-latency-at-a-glance"></a>Breve panoramica sull'ottimizzazione di IOPS, velocità effettiva e latenza
Questa tabella riepiloga tutti i fattori relativi alle prestazioni e i passaggi necessari per ottimizzare IOPS, velocità effettiva e latenza. Le sezioni successive al riepilogo illustreranno ogni fattore in modo più dettagliato.

|  | **IOPS** | **Velocità effettiva** | **Latency** |
| --- | --- | --- | --- |
| **Scenario di esempio** |Applicazione OLTP aziendale che richiede una frequenza molto elevata di transazioni al secondo. |Applicazione aziendale di tipo data warehouse che elabora quantità elevate di dati. |Applicazioni quasi in tempo reale che necessitano di risposte immediate alle richieste degli utenti, ad esempio i giochi online. |
| Fattori relativi alle prestazioni | | | |
| **Dimensioni I/O** |Dimensioni I/O ridotte producono valori superiori per IOPS. |Dimensioni I/O maggiori producono una velocità effettiva superiore. | |
| **Dimensioni macchina virtuale** |Usare una dimensione di VM che offre IOPS superiori ai requisiti dell'applicazione. Per informazioni sulle dimensioni delle VM e i relativi limiti di IOPS, vedere qui. |Usare una dimensione di VM con un limite di velocità effettiva superiore ai requisiti dell'applicazione. Per informazioni sulle dimensioni delle VM e i relativi limiti di velocità effettiva, vedere qui. |Usare una dimensione di VM che offre limiti di ridimensionamento superiori ai requisiti dell'applicazione. Per informazioni sulle dimensioni delle VM e i relativi limiti, vedere qui. |
| **Dimensioni disco** |Usare una dimensione di disco che offre IOPS superiori ai requisiti dell'applicazione. Per informazioni sulle dimensioni dei dischi e i relativi limiti di IOPS, vedere qui. |Usare una dimensione di disco con un limite di velocità effettiva superiore ai requisiti dell'applicazione. Per informazioni sulle dimensioni dei dischi e i relativi limiti di velocità effettiva, vedere qui. |Usare una dimensione di disco che offre limiti di ridimensionamento superiori ai requisiti dell'applicazione. Per informazioni sulle dimensioni dei dischi e i relativi limiti, vedere qui. |
| **Limiti relativi al ridimensionamento di VM e dischi** |Il limite di IOPS della dimensione di VM scelta deve essere superiore al totale di IOPS basato sul disco di Archiviazione Premium collegato alla VM. |Il limite di velocità effettiva della dimensione di VM scelta deve essere superiore al totale di velocità effettiva basato sul disco di Archiviazione Premium collegato alla VM. |I limiti di ridimensionamento della dimensione di VM scelta devono essere superiori al totale dei limiti di ridimensionamento dei dischi di Archiviazione Premium collegati. |
| **Memorizzazione nella cache del disco** |Abilitare la cache ReadOnly nei dischi di Archiviazione Premium con operazioni con numero elevato di letture per ottenere un valore di IOPS di lettura più elevato. | |Abilitare la cache ReadOnly nei dischi di Archiviazione Premium con operazioni con numero elevato di letture per ottenere un valore molto basso per le latenze di lettura. |
| **Striping del disco** |Usare più dischi ed eseguirne lo striping per ottenere un limite combinato più elevato per IOPS e velocità effettiva. Si noti che il limite combinato per ogni VM deve essere superiore ai limiti combinati dei dischi Premium collegati. | | |
| **Dimensioni di striping** |Dimensioni di striping ridotte per modelli di I/O casuali presenti in applicazioni OLTP. Ad esempio, usare dimensioni di striping pari a 64 KB per un'applicazione OLTP di SQL Server. |Dimensioni di striping superiori per modelli di I/O sequenziali di grandi dimensioni presenti in applicazioni di tipo data warehouse. Ad esempio, usare dimensioni di striping pari a 256 KB per applicazioni SQL Server di tipo data warehouse. | |
| **Multithreading** |Usare il multithreading per effettuare il push di un numero più elevato di richieste nell'Archiviazione Premium, in modo da ottenere valori più elevati per IOPS e velocità effettiva. Ad esempio, in SQL Server impostare un valore elevato per MAXDOP, in modo da allocare un numero maggiore di CPU a SQL Server. | | |
| **Profondità coda** |Una profondità maggiore per la coda genera valori più elevati per IOPS. |Una profondità maggiore per la coda genera valori più elevati per la velocità effettiva. |Una profondità minore per la coda genera latenze più basse. |

## <a name="nature-of-io-requests"></a>Natura delle richieste I/O
Una richiesta I/O è un'unità di operazioni di input/output che verrà eseguita dall'applicazione. L'identificazione della natura delle richieste I/O, ovvero casuali o sequenziali, di lettura o scrittura, grandi o piccole, consentirà di determinare i requisiti relativi alle prestazioni per l'applicazione. È molto importante comprendere la natura delle richieste I/O, in modo da pendere le decisioni corrette durante la progettazione dell'infrastruttura dell'applicazione.

La dimensione di I/O è uno dei fattori più importanti. Le dimensioni di I/O sono le dimensioni della richiesta di operazioni di input/output generata dall'applicazione. Le dimensioni di I/O hanno un impatto significativo sulle prestazioni, in particolare sui valori di IOPS e larghezza di banda che l'applicazione è in grado di ottenere. La formula seguente illustra la relazione tra IOPS, dimensioni di I/O e larghezza di banda/velocità effettiva.  
    ![](media/storage-premium-storage-performance/image1.png)

Alcune applicazioni consentono di modificare le relative dimensioni di I/O, mentre altre applicazioni non lo consentono. Ad esempio, SQL Server determina automaticamente le dimensioni di I/O ottimali e non fornisce agli utenti manopole per la modifica. D'altra parte, Oracle fornisce un parametro denominato [DB\_BLOCK\_SIZE](https://docs.oracle.com/cd/B19306_01/server.102/b14211/iodesign.htm#i28815) che consente di configurare la dimensione delle richieste I/O del database.

Se si usa un'applicazione che non consente la modifica delle dimensioni di I/O, usare le indicazioni disponibili in questo articolo per ottimizzare l'indicatore KPI relativo alle prestazioni più rilevante per l'applicazione. Ad esempio,

* Un'applicazione OLTP genera milioni di richieste I/O piccole e casuali. Per gestire questo tipo di richieste I/O, è necessario progettare l'infrastruttura dell'applicazione in modo da ottenere valori di IOPS più elevati.  
* Un'applicazione di tipo data warehouse genera richieste I/O di grandi dimensioni e sequenziali. Per gestire questo tipo di richieste I/O, è necessario progettare l'infrastruttura dell'applicazione in modo da ottenere valori di larghezza di banda e velocità effettiva più elevati.

Se si usa un'applicazione che consente la modifica delle dimensioni di I/O, usare questa regola generica per le dimensioni di I/O, oltre ad altre indicazioni relative alle prestazioni.

* Dimensioni di I/O minori per ottenere valori di IOPS più elevati. Ad esempio, 8 KB per un'applicazione OLTP.  
* Dimensioni di I/O maggiori per ottenere valori di larghezza di banda/velocità effettiva più elevati. Ad esempio, 1024 KB per un'applicazione di tipo data warehouse.

Ecco un esempio di come è possibile calcolare i valori di IOPS e larghezza di banda/velocità effettiva per l'applicazione. Prendere in considerazione un'applicazione che usa un disco P30. Il valore massimo di IOPS e larghezza di banda/velocità effettiva che può essere raggiunto da un disco P30 è pari a 5000 IOPS e 200 MB al secondo, rispettivamente. Se l'applicazione richiede il valore di IOPS massimo dal disco P30 e si usano dimensioni di I/O minori, ad esempio 8 KB, il valore di larghezza di banda risultante che si potrà ottenere è pari a 40 MB al secondo. Se l'applicazione richiede il valore massimo di larghezza di banda/velocità effettiva dal disco P30 e si usano dimensioni di I/O maggiori, ad esempio 1024 KB, il valore di IOPS risultante sarà più basso, ad esempio 200 IOPS. È quindi necessario perfezionare le dimensioni di I/O in modo che soddisfino i requisiti relativi a IOPS e velocità effettiva/larghezza di banda dell'applicazione. La tabella seguente riepiloga le diverse dimensioni di I/O e i valori di IOPS e velocità effettiva corrispondenti per un disco P30.

| **Requisiti dell'applicazione** | **Dimensioni di I/O** | **IOPS** | **Velocità effettiva/Larghezza di banda** |
| --- | --- | --- | --- |
| Operazioni di I/O al secondo max |8 KB |5.000 |40 MB al secondo |
| Velocità effettiva massima |1024 KB |200 |200 MB al secondo |
| Velocità effettiva massima + IOPS elevati |64 KB |3.200 |200 MB al secondo |
| IOPS massimi + Velocità effettiva elevata |32 KB |5.000 |160 MB al secondo |

Per ottenere valori di IOPS e larghezza di banda più elevati rispetto al valore massimo di un singolo disco di Archiviazione Premium, usare più dischi Premium con striping. Ad esempio, effettuare lo striping di due dischi P30 per ottenere un valore di IOPS combinato di 10.000 IOPS o un valore di velocità effettiva combinato di 400 MB al secondo. Come illustrato nella sezione successiva, è necessario usare una dimensione di VM che supporta i valori combinati di IOPS e velocità effettiva.

> **Nota:**  
> poiché se si aumenta il valore di IOPS o di velocità effettiva aumenterà anche l'altro valore, è necessario assicurarsi di non raggiungere i limiti di velocità effettiva o IOPS del disco o della VM in caso di aumento di uno dei valori.
> 
> 

Per verificare gli effetti delle dimensioni di I/O sulle prestazioni dell'applicazione, è possibile eseguire gli strumenti di benchmarking sulle VM e sui dischi. Creare più esecuzioni dei test e usare diverse dimensioni di I/O per ogni esecuzione per verificarne l'impatto. Per altri dettagli, vedere la sezione [Benchmarking](#Benchmarking) alla fine di questo articolo.

## <a name="high-scale-vm-sizes"></a>Dimensioni di VM a scalabilità elevata
Quando si inizia a progettare un'applicazione, uno dei primi passaggi da eseguire consiste nel scegliere una VM in cui ospitare l'applicazione. L'Archiviazione Premium include dimensioni di VM a scalabilità elevata in grado di eseguire applicazioni che richiedono capacità di calcolo elevate e prestazioni di I/O elevate per il disco locale. Queste VM forniscono processori più veloci, un rapporto più elevato tra memoria e core e un'unità SSD (Solid-State Drive) per il disco locale. Esempi di VM a scalabilità elevata che supportano l'Archiviazione Premium sono le VM serie DS, DSv2 e GS.

Le VM a scalabilità elevata sono disponibili in dimensioni diverse con un numero diverso di core CPU, memoria, sistema operativo e dimensioni del disco temporaneo. Ogni dimensione di VM prevede anche un numero massimo di dischi dati che possono essere collegati alla VM. La dimensione di VM scelta influirà quindi sulla quantità di elaborazione, memoria e capacità di archiviazione disponibile per l'applicazione. Influisce anche sui costi di calcolo e archiviazione. Ad esempio, sono riportate di seguito le specifiche per la VM di dimensione massima per le serie DS, DSv2 e GS:

| Dimensioni macchina virtuale | Core CPU | Memoria | Dimensioni di disco della VM | Max. dischi dati | Dimensioni cache | IOPS | Limiti di I/O della cache della larghezza di banda |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS14 |16 |112 GB |Sistema operativo = 1023 GB  <br>  SSD locale = 224 GB |32 |576 GB |50.000 IOPS  <br>  512 MB al secondo |4.000 IOPS e 33 MB al secondo |
| Standard_GS5 |32 |448 GB |Sistema operativo = 1023 GB  <br>  SSD locale = 896 GB |64 |4224 GB |80.000 IOPS  <br>  2.000 MB al secondo |5.000 IOPS e 50 MB al secondo |

Per visualizzare un elenco completo di tutte le dimensioni delle VM di Azure disponibili, vedere [Dimensioni per le macchine virtuali Windows](../virtual-machines/virtual-machines-windows-sizes.md) o [Dimensioni per le macchine virtuali Linux](../virtual-machines/virtual-machines-linux-sizes.md). Scegliere una dimensione di VM in grado di soddisfare e adeguarsi ai requisiti relativi alle prestazioni dell'applicazione. Quando si scelgono le dimensioni delle VM, è inoltre necessario esaminare le importanti considerazioni seguenti.

*Limiti di scalabilità*  
 I limiti massimi per IOPS per ogni VM e ogni disco sono diversi e indipendenti gli uni dagli altri. Assicurarsi che l'applicazione gestisca i valori di IOPS entro i limiti della VM e dei dischi Premium collegati alla VM. In caso contrario, le prestazioni dell'applicazione verranno limitate.

Si supponga, ad esempio, che i requisiti dell'applicazione siano pari a 4.000 IOPS. Per ottenere questo risultato, effettuare il provisioning di un disco P30 su una VM DS1. Il disco P30 può raggiungere fino a 5.000 IOPS. La VM DS1 è tuttavia limitata a 3.200 IOPS. Le prestazioni dell'applicazione verranno quindi vincolate dal limite della VM di 3.200 IOPS e le prestazioni risulteranno danneggiate. Per evitare questa situazione, scegliere dimensioni per VM e disco in grado di soddisfare entrambi i requisiti dell'applicazione.

*Costo operativo*  
 In molti casi è possibile che il costo operativo complessivo con l'Archiviazione Premium sia inferiore al costo dell'uso dell'Archiviazione Standard.

Ad esempio, si consideri un'applicazione che richiede 16.000 IOPS. Per ottenere queste prestazioni sarà necessaria una VM IaaS di Azure di tipo Standard\_StandardD14, che può offrire un valore massimo di IOPS pari a 16.000 usando 32 dischi di archiviazione Standard da 1 TB. Ogni disco di archiviazione Standard da 1 TB può raggiungere un valore massimo di 500 IOPS. Il costo stimato di questa VM al mese sarà pari a $ 1.570. Il costo mensile di 32 dischi di archiviazione Standard sarà pari a $ 1.638. Il costo totale mensile stimato sarà pari a $ 3.208.

Se tuttavia la stessa applicazione è ospitata nell'Archiviazione Premium, sarà necessaria una dimensione di VM minore e saranno necessari meno dischi di Archiviazione Premium. Ciò consente una riduzione del costo complessivo. Una VM Standard\_StandardDS13 può soddisfare il requisito di 16.000 IOPS con quattro dischi P30. La VM DS13 offre un valore massimo di IOPS pari a 25.600 e ogni disco P30 ha un valore massimo di IOPS pari a 5.000. Questa configurazione consente complessivamente di ottenere 5.000 x 4 = 20.000 IOPS. Il costo stimato di questa VM al mese sarà pari a $ 1.003. Il costo mensile di quattro dischi di Archiviazione Premium P30 sarà pari a $ 544,34. Il costo totale mensile stimato sarà pari a $ 1.544.

La tabella seguente riepiloga la scomposizione costi di questo scenario per l'Archiviazione Premium e Standard.

|  | **Standard** | **Premium** |
| --- | --- | --- |
| **Costo di VM al mese** |$1.570,58 (Standard\_D14) |$1.003,66 (Standard\_DS13) |
| **Costo di dischi al mese** |$ 1.638,40 (32 x dischi da 1 TB) |$ 544,34 (4 x dischi P30) |
| **Costo complessivo al mese** |$ 3.208,98 |$ 1.544,34 |

*Distribuzioni Linux*  

Archiviazione Premium di Azure offre lo stesso livello di prestazioni per le macchine virtuali che eseguono Windows e Linux. Sono supportati molti tipi di distribuzioni di Linux. L'elenco completo è disponibile [qui](../virtual-machines/virtual-machines-linux-endorsed-distros.md). È importante notare che diverse distribuzioni sono ottimali per diversi tipi di carichi di lavoro. Si otterranno livelli diversi di prestazioni in base alla distribuzione in cui è in esecuzione il carico di lavoro. Testare le distribuzioni di Linux con l'applicazione e scegliere quella migliore per le esigenze specifiche.

Quando si esegue Linux con l'Archiviazione Premium, verificare se sono disponibili aggiornamenti recenti per i driver necessari, in modo da assicurare prestazioni elevate.

## <a name="premium-storage-disk-sizes"></a>Dimensioni dei dischi di Archiviazione Premium
L'Archiviazione Premium di Azure offre attualmente tre dimensioni di disco. Ogni dimensione di disco ha un limite di scala diverso per IOPS, larghezza di banda e archiviazione. Scegliere la dimensione del disco di Archiviazione Premium appropriata, in base ai requisiti dell'applicazione e le dimensioni delle VM a scalabilità elevata. La tabella seguente illustra le tre dimensioni di disco e le relative capacità.

| **Tipo di disco** | **P10** | **P20** | **P30** |
| --- | --- | --- | --- |
| Dimensioni disco |128 GiB |512 GiB |1024 GiB (1 TB) |
| IOPS per disco |500 |2300 |5000 |
| Velocità effettiva per disco |100 MB al secondo |150 MB al secondo |200 MB al secondo |

Il numero di dischi scelto dipende dalla dimensione scelta per il disco. È possibile usare un singolo disco P30 o più dischi P10 per soddisfare i requisiti dell'applicazione. Valutare le considerazioni elencate di seguito quando si effettua la scelta.

*Limiti di scalabilità (IOPS e velocità effettiva)*  
 I limiti di IOPS e velocità effettiva di ogni dimensione di disco Premium sono diversi e non dipendono dai limiti di scalabilità delle VM. Assicurarsi che il valore totale di IOPS e velocità effettiva rientri nei limiti di scalabilità della dimensione scelta per la VM.

Ad esempio, si supponga che un requisito di applicazione preveda una velocità effettiva massima di 250 MB/sec e si usi una VM DS4 con un singolo disco P30. La VM DS4 può offrire una velocità effettiva massima di 256 MB/sec. Un singolo disco P30 ha tuttavia un limite di velocità effettiva pari a 200 MB/sec. L'applicazione sarà quindi limitata a 200 MB/sec a causa di questo limite del disco. Per superare questo limite, effettuare il provisioning di più dischi dati nella VM.

> **Nota:**  
> le operazioni di lettura fornite dalla cache non sono incluse nei valori di IOPS e velocità effettiva, quindi non sono soggette ai limiti del disco. La cache ha un limite di IOPS e velocità effettiva specifico per ogni VM.
> 
> Ad esempio, le operazioni di lettura e scrittura iniziali sono rispettivamente pari a 60 MB/sec e 40 MB/sec. Nel corso del tempo, la cache migliora la propria operatività e fornisce un numero sempre maggiore di operazioni di lettura dalla cache. Sarà quindi possibile ottenere una velocità effettiva di lettura superiore dal disco.
> 
> 

*Numero di dischi*  
 Determinare il numero di dischi necessari esaminando i requisiti dell'applicazione. Ogni dimensione di VM prevede anche un limite per il numero di dischi che possono essere collegati alla VM. In genere, questo valore corrisponde al doppio dei core. Assicurarsi che la dimensione di VM scelta sia in grado di supportare il numero di dischi necessari.

Occorre ricordare che i dischi di Archiviazione Premium hanno capacità di prestazioni più elevate rispetto ai dischi di Archiviazione Standard. Se quindi si esegue la migrazione dell'applicazione da una VM IaaS di Azure usando il passaggio dall'Archiviazione Standard all'Archiviazione Premium, è probabile che sia necessario un numero inferiore di dischi per ottenere le stesse prestazioni o prestazioni più elevate per l'applicazione.

## <a name="disk-caching"></a>Memorizzazione nella cache del disco
Le VM a scalabilità elevata che sfruttano i vantaggi dell'Archiviazione Premium di Azure includono una tecnologia di memorizzazione nella cache multilivello denominata BlobCache. BlobCache usa una combinazione della RAM della macchina virtuale e dell'unità SSD locale per la memorizzazione nella cache. Questa cache è disponibile per i dischi persistenti di Archiviazione Premium e i dischi locali della VM. Per impostazione predefinita, questa impostazione della cache viene configurata su Read/Write per dischi del sistema operativo e ReadOnly per i dischi dati ospitati nell'Archiviazione Premium. Quando la memorizzazione nella cache su disco è abilitata nei dischi di Archiviazione Premium, le VM a scalabilità elevata possono raggiungere livelli estremamente elevati di prestazioni che superano le prestazioni sottostanti del disco.

> [!WARNING]
> La modifica dell'impostazione della cache di un disco di Azure scollega e ricollega il disco di destinazione. Se si tratta del disco del sistema operativo, la VM viene riavviata. Arrestare tutte le applicazioni/i servizi che potrebbero essere interessati da questa interruzione prima di modificare l'impostazione della cache del disco.
> 
> 

Per altre informazioni sul funzionamento di BlobCache, vedere il post di blog relativo all' [Archiviazione Premium di Azure](https://azure.microsoft.com/blog/azure-premium-storage-now-generally-available-2/) .

È importante abilitare la cache sul set corretto di dischi. L'abilitazione o meno della memorizzazione nella cache su disco in un disco Premium dipende dal modello di carichi di lavoro che verranno gestiti dal disco. La tabella seguente illustra le impostazioni predefinite della cache per i dischi sistema operativo e i dischi dati.

| **Tipo di disco** | **Impostazione predefinita per la cache** |
| --- | --- |
| Disco del sistema operativo |ReadWrite |
| Disco dati |None |

Ecco le impostazioni consigliate per la cache su disco per i dischi dati:

| **Impostazione per la memorizzazione nella cache su disco** | **Indicazione sull'uso di questa impostazione** |
| --- | --- |
| None |Configurare la cache host come None per dischi di sola scrittura e dischi con numero elevato di operazioni di scrittura. |
| ReadOnly |Configurare la cache host come ReadOnly per dischi di sola lettura e di lettura-scrittura. |
| ReadWrite |Configurare la cache host come ReadWrite solo se l'applicazione gestisce correttamente la scrittura di dati memorizzati nella cache in dischi persistenti, quando necessario. |

*ReadOnly*  
 Configurando la memorizzazione nella cache ReadOnly nei dischi dati di Archiviazione Premium, è possibile ottenere una latenza di lettura bassa e valori molto elevati di IOPS e velocità effettiva di lettura per l'applicazione. Questo è dovuto ai due motivi seguenti:

1. Le letture eseguite dalla cache, che si trova nella memoria della VM e nell'unità SSD locale, sono più veloci rispetto alle letture dal disco dati, che si trova nell'archivio BLOB di Azure.  
2. L'Archiviazione Premium non include le operazioni di lettura fornite dalla cache nel calcolo dei valori di IOPS e velocità effettiva del disco. L'applicazione è quindi in grado di ottenere valori totali di IOPS e velocità effettiva più elevati.

*ReadWrite*  
 Per impostazione predefinita, la memorizzazione nella cache ReadWrite è abilitata nei dischi sistema operativo. È stato recentemente aggiunto il supporto per la memorizzazione nella cache ReadWrite anche sui dischi dati. Se si usa la memorizzazione nella cache ReadWrite, è necessario scrivere in modo corretto i dati dalla cache ai dischi persistenti. Ad esempio, SQL Server gestisce automaticamente la scrittura di dati memorizzati nella cache nei dischi di archiviazione permanente. L'uso della cache di tipo ReadWrite con un'applicazione che non gestisce la persistenza dei dati necessari può provocare la perdita dei dati, in caso di arresto anomalo della VM.

È ad esempio possibile applicare queste indicazioni a SQL Server in esecuzione sull'Archiviazione Premium seguendo questa procedura:

1. Configurare la cache "ReadOnly" nei dischi di Archiviazione Premium che ospitano i file di dati.  
   a.  Le operazioni rapide di lettura dalla cache riducono il tempo necessario per le query di SQL Server, poiché le pagine di dati vengono recuperate in modo molto più veloce dalla cache rispetto dal recupero diretto dai dischi dati.  
   b.  Le fornitura delle letture dalla cache consente di rendere disponibile velocità effettiva aggiuntiva dai dischi dati Premium. SQL Server può usare questa velocità effettiva aggiuntiva per recuperare un numero superiore di pagine di dati e altre operazioni come il backup/ripristino, i carichi in batch e le ricompilazioni degli indici.  
2. Configurare la cache "None" nei dischi di Archiviazione Premium che ospitano i file di log.  
   a.  I file di log hanno principalmente operazioni intensive a livello di lettura. Non ottengono quindi alcun vantaggio dalla cache ReadOnly.

## <a name="disk-striping"></a>Striping del disco
Quando una VM a scalabilità elevata è collegata ad alcuni dischi persistenti di Archiviazione Premium, è possibile effettuare lo striping dei dischi per aggregare le relative capacità di IOPS, larghezza di banda e archiviazione.

In Windows è possibile usare gli spazi di archiviazione per lo striping dei dischi. È necessario configurare una colonna per ogni disco in un pool. In caso contrario, le prestazioni complessive di un volume con striping possono essere inferiori al previsto, a causa della distribuzione non uniforme del traffico nei dischi.

Importante: l'uso dell'interfaccia utente di Gestione server consente di impostare il numero totale di colonne fino a un massimo di 8 per un volume con striping. Quando si collegano più di 8 dischi, usare PowerShell per creare il volume. L'uso di PowerShell consente di impostare un numero di colonne uguale al numero di dischi. Ad esempio, se un singolo set di striping include 16 dischi, specificare 16 colonne nel parametro *NumberOfColumns* del cmdlet *New-VirtualDisk* di PowerShell.

In Linux usare l'utilità MDADM per lo striping dei dischi. Per informazioni dettagliate sulla procedura di striping dei dischi su Linux, vedere [Configurare RAID software in Linux](../virtual-machines/virtual-machines-linux-configure-raid.md).

*Dimensioni di striping*  
 Un elemento importante della configurazione dello striping del disco è la dimensione di striping. La dimensione di striping o la dimensione del blocco è il blocco più piccolo di dati che l'applicazione può gestire in un volume con striping. La dimensione di striping configurabile dipende dal tipo di applicazione e dal relativo modello di richieste. Se si sceglie la dimensione di striping errata, è possibile che si ottenga un allineamento di I/O non corretto, che porta a prestazioni degradate per l'applicazione.

Ad esempio, se una richiesta I/O generata dall'applicazione è maggiore della dimensione di striping del disco, il sistema di archiviazione la scrive oltre i limiti di unità di striping in più dischi. Quando è necessario accedere ai dati, occorrerà cercarli in più unità di striping per completare la richiesta. L'effetto cumulativo di questo comportamento può portare a una riduzione significativa delle prestazioni. D'altra parte, se la dimensione della richiesta I/O è minore della dimensione di striping ed è di tipo casuale, è possibile che le richieste I/P si concentrino sullo stesso disco, provocando un collo di bottiglia e danneggiando le prestazioni di I/O.

Scegliere una dimensione di striping appropriata in base a tipo di carico di lavoro eseguito dall'applicazione. Per richieste I/O di piccole dimensioni e casuali, usare una dimensione di striping minore. Per richieste I/O di grandi dimensioni e sequenziali, usare una dimensione di striping maggiore. Esaminare le indicazioni relative alle dimensioni di striping per l'applicazione da eseguire nell'Archiviazione Premium. Per SQL Server configurare dimensioni di striping pari a 64 KB per carichi di lavoro OLTP e 256 KB per carichi di lavoro di tipo data warehouse. Per altre informazioni, vedere [Procedure consigliate per le prestazioni per SQL Server in Macchine virtuali di Azure](../virtual-machines/virtual-machines-windows-sql-performance.md#disks-and-performance-considerations) .

> **Nota:**  
> è possibile effettuare lo striping di un massimo di 32 dischi di Archiviazione Premium in una VM di serie DS e di 64 dischi di Archiviazione Premium in una VM di serie GS.
> 
> 

## <a name="multi-threading"></a>Multithreading
Azure ha progettato la piattaforma di Archiviazione Premium in modo che sia notevolmente parallela. Un'applicazione multithreading ottiene prestazioni molto più elevate rispetto a un'applicazione a thread singolo. Un'applicazione multithreading suddivide le proprie attività in più thread e aumenta l'efficienza dell'esecuzione utilizzando al massimo la VM e le risorse del disco.

Ad esempio, se l'applicazione è in esecuzione su una VM a core singolo con due thread, la CPU può passare da un thread all'altro per ottenere l'efficienza. Mentre un thread attende il completamento di un'operazione I/O su disco, la CPU può passare all'altro thread. In questo modo, due thread possono ottenere molto di più rispetto a un singolo thread. Se la VM ha più di un core, riduce ulteriormente il tempo di esecuzione, perché ogni core può eseguire attività in parallelo.

È possibile che non si riesca a cambiare il modo in cui un'applicazione non modificabile implementa la modalità a thread singolo o il multithreading. Ad esempio, SQL Server è in grado di gestire più CPU e più core. SQL Server decide tuttavia in che condizioni sfruttare i vantaggi di uno o più thread per elaborare una query. Può eseguire query e compilare indici usando il multithreading. Per una query che comporta l'unione di più tabelle e l'ordinamento dei dati prima di restituire risultati all'utente, SQL Server userà probabilmente più thread. Un utente non può tuttavia controllare se SQL Server esegue una query usando un singolo thread o più thread.

È possibile modificare alcune impostazioni di configurazione per influenzare l'elaborazione multithreading o parallela di un'applicazione. Ad esempio, nel caso di SQL Server è possibile modificare la configurazione Massimo grado parallelismo. Questa impostazione, denominata MAXDOP, consente di configurare il numero massimo di processori che SQL Server può usare durante l'elaborazione parallela. È possibile configurare MAXDOP per singole query o per operazioni sull'indice. Questa configurazione risulta utile per bilanciare le risorse del sistema per un'applicazione essenziale per le prestazioni.

Ad esempio, si supponga che l'applicazione che usa SQL Server stia eseguendo contemporaneamente una query di dimensioni elevate e un'operazione sull'indice. Si supponga di volere migliorare le prestazioni dell'operazione sull'indice rispetto alla query di grandi dimensioni. In questo caso è possibile impostare il valore MAXDOP dell'operazione sull'indice in modo che sia superiore al valore MAXDOP per la query. In questo modo SQL Server avrà un numero maggiore di processori da sfruttare per l'operazione sull'indice rispetto al numero di processori da dedicare alla query di grandi dimensioni. Occorre ricordare che non si può controllare il numero di thread usati da SQL Server per ogni operazione. È possibile controllare il numero massimo di processori dedicati al multithreading.

Altre informazioni sui [Gradi di parallelismo](https://technet.microsoft.com/library/ms188611.aspx) in SQL Server. Individuare le impostazioni che influenzano il multithreading nell'applicazione e le relative configurazioni per ottimizzare le prestazioni.

## <a name="queue-depth"></a>Profondità coda
Il valore per la profondità, la lunghezza o la dimensione della coda indica il numero di richieste I/O in sospeso nel sistema. Il valore della profondità della coda determina il numero di operazioni di I/O che possono essere accodate dall'applicazione e che verranno elaborate dai dischi di archiviazione. Influisce su tutti e tre gli indicatori di prestazioni illustrati in questo articolo, ovvero IOPS, velocità effettiva e latenza.

La profondità della coda e il multithreading sono strettamente correlati. Il valore della profondità della coda indica la quantità di multithreading che l'applicazione può ottenere. Se il valore della profondità della coda è elevato, l'applicazione potrà eseguire più applicazioni contemporaneamente, ovvero potrà ottenere un multithreading maggiore. Se il valore della profondità della coda è ridotto, anche se l'applicazione è abilitata al multithreading, il numero di richieste accodate non sarà sufficiente per l'esecuzione simultanea.

In genere, le applicazioni standard non consentono di modificare la profondità della coda, perché un'impostazione non corretta risulterebbe eccessivamente dannosa. Le applicazioni imposteranno automaticamente il valore di profondità della coda corretto per ottenere prestazioni ottimali. È tuttavia importante comprendere questo concetto, in modo da risolvere i problemi delle prestazioni nell'applicazione. È anche possibile osservare gli effetti della profondità della coda eseguendo gli strumenti di benchmarking nel sistema.

Alcune applicazioni forniscono le impostazioni necessarie per influenzare la profondità della coda, ad esempio, l'impostazione MAXDOP (Massimo grado parallelismo) in SQL Server illustrata nella sezione precedente. MAXDOP consente di influenzare la profondità della coda e il multithreading, anche se non modifica direttamente il valore Profondità coda di SQL Server.

*Profondità elevata della coda*  
 Un valore elevato per la profondità della coda consente di allineare più operazioni sul disco. Il disco conosce in anticipo la richiesta successiva nella coda. Di conseguenza, il disco può pianificare in anticipo le operazioni ed elaborarle nella sequenza ottimale. Poiché l'applicazione invia più richieste al disco, il disco potrà elaborare un numero maggiore di I/O paralleli. L'applicazione sarà infine in grado di ottenere valori più elevati per IOPS. Poiché l'applicazione elabora un numero maggiore di richieste, aumenterà anche la velocità effettiva dell'applicazione.

In genere, un'applicazione può ottenere una velocità effettiva massima con 8-16+ I/O in attesa per ogni disco collegato. Se il valore della profondità della coda è pari a uno, l'applicazione non effettua il push di un numero sufficiente di I/O nel sistema e ne elaborerà una quantità inferiore in un periodo specifico. In altri termini, si otterrà una velocità effettiva minore.

Ad esempio, in SQL Server l'impostazione del valore MAXDOP per una query su "4" indica a SQL Server che può usare al massimo quattro core per eseguire la query. SQL Server determinerà il valore migliore per la profondità della coda e il numero di core per l'esecuzione della query.

*Profondità ottimale della coda*  
 Un valore molto elevato per la coda può avere alcuni svantaggi. Se il valore della profondità della coda è troppo alto, l'applicazione proverà a effettuare un numero molto elevato di IOPS. A meno che un'applicazione non abbia dischi persistenti con un numero sufficiente di IOPS con provisioning, ciò può influire negativamente sulle latenze dell'applicazione. La formula seguente illustra la relazione tra IOPS, latenza e profondità della coda.  
    ![](media/storage-premium-storage-performance/image6.png)

È consigliabile non configurare la profondità della coda su un valore elevato, specificando invece un valore ottimale, in grado di offrire un numero di IOPS sufficiente per l'applicazione, senza influire sulle latenze. Ad esempio, se la latenza dell'applicazione deve essere pari a 1 millisecondo, la profondità della coda necessaria per ottenere 5.000 IOPS sarà QD = 5000 x 0,001 = 5.

*Profondità della coda per un volume con striping*  
 Per un volume con striping è consigliabile mantenere una profondità della coda sufficientemente elevata da consentire a ogni disco di avere individualmente un picco di profondità della coda. Ad esempio, si consideri un'applicazione che effettua il push di una profondità della coda pari a 2 e lo striping include 4 dischi. Le due richieste I/O verranno trasmesse a due dischi e i due dischi rimanenti saranno inattivi. È quindi consigliabile configurare la profondità della coda in modo che tutti i dischi siano occupati. La formula seguente illustra come determinare la profondità della coda dei volumi con striping.  
    ![](media/storage-premium-storage-performance/image7.png)

## <a name="throttling"></a>Limitazione
L'Archiviazione Premium di Azure effettua il provisioning di un numero specificato di IOPS e di velocità effettiva in base alle dimensioni delle VM e alle dimensioni dei dischi scelte. Ogni volta che l'applicazione prova a superare i limiti di IOPS o velocità effettiva che possono essere gestiti dalla VM o dal disco, l'Archiviazione Premium limiterà l'applicazione. Questo problema si manifesta sotto forma di una riduzione delle prestazioni dell'applicazione. Ciò può comportare una latenza più alta, una velocità effettiva minore o valori di IOPS più bassi. Se l'Archiviazione Premium non applica la limitazione, è possibile che si verifichi un errore irreversibile dell'applicazione a causa del superamento dei limiti delle risorse. Per evitare problemi di prestazioni dovuti alla limitazione, è necessario effettuare sempre il provisioning di un numero di risorse sufficiente per l'applicazione. Prendere in considerazione i concetti illustrati nelle sezioni precedenti relative alle dimensioni delle VM e dei dischi. Il benchmarking è il modo migliore per determinare le risorse necessarie per l'hosting dell'applicazione.

## <a name="benchmarking"></a>Benchmarking
Il benchmarking è il processo di simulazione di diversi carichi di lavoro sull'applicazione e di misurazione delle prestazioni dell'applicazione per ogni carico di lavoro. Eseguendo la procedura illustrata in una sezione precedente, sono stati raccolti i requisiti relativi alle prestazioni dell'applicazione. Eseguendo gli strumenti di benchmarking nelle VM che ospitano l'applicazione sarà possibile determinare i livelli di prestazioni che l'applicazione è in grado di ottenere con l'Archiviazione Premium. In questa sezione sono disponibili esempi di benchmarking per una VM DS14 Standard con provisioning con dischi di Archiviazione Premium di Azure.

Sono stati usati gli strumenti di benchmarking comuni Iometer e FIO, rispettivamente per Windows e Linux. Questi strumenti generano più thread che simulano un carico di lavoro analogo a quello di produzione e misurano le prestazioni del sistema. Questi strumenti consentono anche di configurare i parametri quali la dimensione dei blocchi e la profondità della coda, che in genere non possono essere modificati per un'applicazione. Ciò offre maggiore flessibilità per ottenere il livello massimo di prestazioni su una VM a scalabilità elevata con provisioning con dischi Premium per diversi tipi di carichi di lavoro dell'applicazione. Per altre informazioni su ogni strumento di benchmarking, vedere [Iometer](http://www.iometer.org/) e [FIO](http://freecode.com/projects/fio).

Per eseguire gli esempi seguenti, creare una VM DS14 Standard e collegare 11 dischi di Archiviazione Premium alla VM. Degli 11 dischi, configurare 10 dischi con memorizzazione nella cache dell'host impostata su "None" ed effettuarne lo striping in un volume denominato NoCacheWrites. Configurare la memorizzazione nella cache dell'host come "ReadOnly" sul disco rimanente e creare un volume denominato CacheReads con questo disco. Usando questa configurazione, sarà possibile vedere le prestazioni massime di lettura e scrittura da una VM DS14 Standard. Per informazioni dettagliate sulla creazione di una VM DS14 con dischi Premium, vedere [Creare e usare un account di Archiviazione Premium per un disco dati della macchina virtuale](storage-premium-storage.md#create-and-use-a-premium-storage-account-for-a-virtual-machine-data-disk).

*Preparare la cache*  
 Il disco con memorizzazione nella cache dell'host di tipo ReadOnly sarà in grado di ottenere valori di IOPS più elevati rispetto al limite del disco. Per ottenere queste prestazioni di lettura massime dalla cache dell'host, è prima di tutto necessario preparare la cache del disco. Ciò assicura che le operazioni di I/O di lettura che lo strumento di benchmarking eseguirà sul volume CacheReads raggiungano effettivamente la cache e non direttamente il disco. I riscontri nella cache producono IOPS aggiuntivi da un singolo disco abilitato per la cache.

> **Importante:**  
> è necessario preparare la cache prima di eseguire il benchmarking, ogni volta che la VM viene riavviata.
> 
> 

#### <a name="iometer"></a>Iometer
[Scaricare lo strumento Iometer](http://sourceforge.net/projects/iometer/files/iometer-stable/2006-07-27/iometer-2006.07.27.win32.i386-setup.exe/download) nella VM.

*File di test*  
 Iometer usa un file di test archiviato nel volume in cui eseguire i test di benchmarking. Gestisce le operazioni di lettura e scrittura sul file di test per misurare i valori di IOPS e velocità effettiva del disco. Iometer crea questo file di test se non ne è stato fornito uno. Creare un file di test di 200 GB denominato iobw.tst nei volumi CacheReads e NoCacheWrites.

*Specifiche di accesso*  
 Le specifiche, la dimensione della richiesta I/O, la percentuale di letture/scritture e la percentuale di operazioni casuali/sequenziali vengono configurate mediante la scheda "Access Specifications" in Iometer. Creare una specifica di accesso per ogni scenario illustrato di seguito. Creare le specifiche di accesso, quindi salvarle con un nome appropriato, ad esempio RandomWrites\_8K, RandomReads\_8K. Selezionare la specifica corrispondente durante l'esecuzione dello scenario di test.

Un esempio di specifiche di accesso per uno scenario con valori massimi di IOPS di scrittura è riportato di seguito,   
    ![](media/storage-premium-storage-performance/image8.png)

*Specifiche per il valore massimo di IOPS di test*  
 Per illustrare il valore massimo di IOPS, usare una dimensione minore della richiesta. Usare una dimensione di richiesta pari a 8 K e creare specifiche per letture e scritture casuali.

| Specifica di accesso | Dimensione della richiesta | % di casuali | % di letture |
| --- | --- | --- | --- |
| RandomWrites\_8K |8 K |100 |0 |
| RandomReads\_8K |8 K |100 |100 |

*Specifiche per il valore massimo di velocità effettiva di test*  
 Per illustrare il valore massimo di velocità effettiva, usare una dimensione maggiore della richiesta. Usare una dimensione di richiesta pari a 64 K e creare specifiche per letture e scritture casuali.

| Specifica di accesso | Dimensione della richiesta | % di casuali | % di letture |
| --- | --- | --- | --- |
| RandomWrites\_64K |64 K |100 |0 |
| RandomReads\_64K |64 K |100 |100 |

*Esecuzione del test di Iometer*  
 Seguire questa procedura per preparare la cache.

1. Creare le specifiche di accesso con i valori seguenti.
   
   | Nome | Dimensione della richiesta | % di casuali | % di letture |
   | --- | --- | --- | --- |
   | RandomWrites\_1MB |1 MB |100 |0 |
   | RandomReads\_1MB |1 MB |100 |100 |
2. Eseguire il test di Iometer per l'inizializzazione del disco della cache con i parametri seguenti. Usare tre thread di lavoro per il volume di destinazione e una profondità della coda pari a 128. Impostare la durata relativa al tempo di esecuzione del test su 2 ore nella scheda "Test Setup".
   
   | Scenario | Volume di destinazione | Nome | Durata |
   | --- | --- | --- | --- |
   | Inizializzare il disco della cache |CacheReads |RandomWrites\_1MB |2 ore |
3. Eseguire il test di Iometer per la preparazione del disco della cache con i parametri seguenti. Usare tre thread di lavoro per il volume di destinazione e una profondità della coda pari a 128. Impostare la durata relativa al tempo di esecuzione del test su 2 ore nella scheda "Test Setup".
   
   | Scenario | Volume di destinazione | Nome | Durata |
   | --- | --- | --- | --- |
   | Preparare il disco della cache |CacheReads |RandomReads\_1MB |2 ore |

Dopo la preparazione del disco della cache, procedere con gli scenari di test elencati di seguito. Per eseguire il test di Iometer, usare almeno tre thread di lavoro per **ogni** volume di destinazione. Per ogni thread di lavoro selezionare il volume di destinazione, impostare la profondità della coda e selezionare una delle specifiche di test salvate, come illustrato nella tabella seguente, per eseguire lo scenario di test corrispondente. La tabella illustra anche i risultati previsti per IOPS e velocità effettiva quando si eseguono questi test. Per tutti gli scenari vengono usati una dimensione di I/O ridotta pari a 8 KB e un valore elevato per la profondità della coda, pari a 128.

| Scenario di test | Volume di destinazione | Nome | Risultato |
| --- | --- | --- | --- |
| Max. IOPS di lettura |CacheReads |RandomWrites\_8K |50.000 IOPS |
| Max. IOPS di scrittura |NoCacheWrites |RandomReads\_8K |64.000 IOPS |
| Max. IOPS combinate |CacheReads |RandomWrites\_8K |100.000 IOPS |
| NoCacheWrites |RandomReads\_8K | | |
| Max. letture MB/sec |CacheReads |RandomWrites\_64K |524 MB/sec |
| Max. scritture MB/sec |NoCacheWrites |RandomReads\_64K |524 MB/sec |
| MB/sec combinati |CacheReads |RandomWrites\_64K |1000 MB/sec |
| NoCacheWrites |RandomReads\_64K | | |

Le schermate seguenti illustrano i risultati dei test di Iometer per scenari combinati di IOPS e velocità effettiva.

*Valori massimi per IOPS di letture e scritture combinate*  
![](media/storage-premium-storage-performance/image9.png)

*Velocità effettiva massima di letture e scritture combinate*  
![](media/storage-premium-storage-performance/image10.png)

### <a name="fio"></a>FIO
FIO è uno strumento popolare per il benchmarking dell'archiviazione sulle VM Linux. Offre la flessibilità necessaria per selezionare diverse dimensioni di I/O e letture e scritture sequenziali o casuali. Genera thread di lavoro o processi per l'esecuzione delle operazioni I/O specificate. È possibile specificare il tipo di operazioni I/O che ogni thread di lavoro deve eseguire usando i file processo. È stato creato un file processo per ogni scenario illustrato negli esempi seguenti. È possibile cambiare le specifiche di questi file processo per il benchmarking di diversi carichi di lavoro in esecuzione sull'Archiviazione Premium. Negli esempi viene usata una VM DS 14 Standard che esegue **Ubuntu**. Usare la stessa configurazione illustrata all'inizio della [sezione Benchmarking](#Benchmarking) e preparare la cache prima di eseguire i test di benchmarking.

Prima di iniziare, [scaricare FIO](https://github.com/axboe/fio) e installarlo nella macchina virtuale.

Eseguire il comando seguente per Ubuntu:

        apt-get install fio

Verranno usati quattro thread di lavoro per la gestione delle operazioni di scrittura e quattro thread di lavoro per la gestione delle operazioni di lettura sui dischi. I thread di lavoro di scrittura indirizzeranno il traffico sul volume "nocache", che include 10 dischi con cache impostata su "None". I thread di lavoro di scrittura indirizzeranno il traffico sul volume "readcache", che include un disco con cache impostata su "ReadOnly".

*IOPS massime di scrittura*  
 Creare il file processo con le specifiche seguenti per ottenere il valore massimo per le operazioni IOPS di scrittura. Assegnare al file il nome "fiowrite.ini".

```
[global]
size=30g
direct=1
iodepth=256
ioengine=libaio
bs=8k

[writer1]
rw=randwrite
directory=/mnt/nocache
[writer2]
rw=randwrite
directory=/mnt/nocache
[writer3]
rw=randwrite
directory=/mnt/nocache
[writer4]
rw=randwrite
directory=/mnt/nocache
```

Notare gli elementi chiave seguenti conformi alle indicazioni di progettazione illustrate nelle sezioni precedenti. Queste specifiche sono essenziali per ottenere il valore massimo per IOPS:  

* Profondità della coda elevata pari a 256.  
* Dimensione di blocco ridotta pari a 8 KB.  
* Più thread che eseguono scritture casuali.

Eseguire il comando seguente per attivare il test FIO per 30 secondi:  

    sudo fio --runtime 30 fiowrite.ini

Durante l'esecuzione del test, sarà possibile visualizzare il numero di operazioni IOPS di scrittura gestite dalla VM e dai dischi Premium. Come illustrato nell'esempio seguente, la VM DS14 fornisce il limite massimo di IOPS di scrittura pari a 50.000 IOPS.  
    ![](media/storage-premium-storage-performance/image11.png)

*IOPS massime di lettura*  
 Creare il file processo con le specifiche seguenti per ottenere il valore massimo per le operazioni IOPS di lettura. Assegnare al file il nome "fioread.ini".

```
[global]
size=30g
direct=1
iodepth=256
ioengine=libaio
bs=8k

[reader1]
rw=randread
directory=/mnt/readcache
[reader2]
rw=randread
directory=/mnt/readcache
[reader3]
rw=randread
directory=/mnt/readcache
[reader4]
rw=randread
directory=/mnt/readcache
```

Notare gli elementi chiave seguenti conformi alle indicazioni di progettazione illustrate nelle sezioni precedenti. Queste specifiche sono essenziali per ottenere il valore massimo per IOPS:

* Profondità della coda elevata pari a 256.  
* Dimensione di blocco ridotta pari a 8 KB.  
* Più thread che eseguono scritture casuali.

Eseguire il comando seguente per attivare il test FIO per 30 secondi:

    sudo fio --runtime 30 fioread.ini

Durante l'esecuzione del test, sarà possibile visualizzare il numero di operazioni IOPS di lettura gestite dalla VM e dai dischi Premium. Come illustrato nell'esempio seguente, la VM DS14 fornisce un valore superiore a 64.000 IOPS di lettura. Ciò dipende da una combinazione delle prestazioni del disco e della cache.  
    ![](media/storage-premium-storage-performance/image12.png)

*IOPS massime di lettura e scrittura*  
 Creare il file processo con le specifiche seguenti per ottenere il valore massimo per le operazioni IOPS combinate di lettura e scrittura. Assegnare al file il nome "fioreadwrite.ini".

```
[global]
size=30g
direct=1
iodepth=128
ioengine=libaio
bs=4k

[reader1]
rw=randread
directory=/mnt/readcache
[reader2]
rw=randread
directory=/mnt/readcache
[reader3]
rw=randread
directory=/mnt/readcache
[reader4]
rw=randread
directory=/mnt/readcache

[writer1]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
[writer2]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
[writer3]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
[writer4]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
```

Notare gli elementi chiave seguenti conformi alle indicazioni di progettazione illustrate nelle sezioni precedenti. Queste specifiche sono essenziali per ottenere il valore massimo per IOPS:

* Profondità della coda elevata pari a 128.  
* Dimensione di blocco ridotta pari a 4 KB.  
* Più thread che eseguono scritture e letture casuali.

Eseguire il comando seguente per attivare il test FIO per 30 secondi:

    sudo fio --runtime 30 fioreadwrite.ini

Durante l'esecuzione del test, sarà possibile visualizzare il numero di operazioni IOPS combinate di lettura e scrittura gestite dalla VM e dai dischi Premium. Come illustrato nell'esempio seguente, la VM DS14 fornisce un valore superiore a 100.000 IOPS combinate di lettura e scrittura. Ciò dipende da una combinazione delle prestazioni del disco e della cache.  
    ![](media/storage-premium-storage-performance/image13.png)

*Velocità effettiva massima combinata*  
 Per ottenere la velocità effettiva massima combinata di lettura e scrittura, usare una dimensione di blocco superiore e una profondità della coda elevata con più thread che eseguono letture e scritture. È possibile usare una dimensione di blocco pari a 64 KB e una profondità della coda pari a 128.

## <a name="next-steps"></a>Passaggi successivi
Altre informazioni sull'Archiviazione Premium di Azure:

* [Archiviazione Premium: archiviazione ad alte prestazioni per carichi di lavoro delle macchine virtuali di Azure](storage-premium-storage.md)  

Per gli utenti di SQL Server sono disponibili articoli sulle procedure consigliate per le prestazioni per SQL Server:

* [Procedure consigliate per le prestazioni per SQL Server in Macchine virtuali di Azure](../virtual-machines/virtual-machines-windows-sql-performance.md)
* [L'Archiviazione Premium di Azure offre le prestazioni più elevate per SQL Server in VM di Azure](http://blogs.technet.com/b/dataplatforminsider/archive/2015/04/23/azure-premium-storage-provides-highest-performance-for-sql-server-in-azure-vm.aspx) 

<!--HONumber=Oct16_HO2-->


