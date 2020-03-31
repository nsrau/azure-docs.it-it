---
title: Risolvere i problemi di prestazioni di Apache HBase in Azure HDInsight
description: Diverse linee guida per l'ottimizzazione delle prestazioni di Apache HBase e suggerimenti per ottenere prestazioni ottimali in Azure HDInsight.Various Apache HBase performance tuning guidelines and tips for getting optimal performance on Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 09/24/2019
ms.openlocfilehash: 93698fadcecf190dd8bbc24a9d03978899d3c5e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75887156"
---
# <a name="troubleshoot-apache-hbase-performance-issues-on-azure-hdinsight"></a>Risolvere i problemi di prestazioni di Apache HBase in Azure HDInsight

Questo articolo descrive varie linee guida per l'ottimizzazione delle prestazioni di Apache HBase e suggerimenti per ottenere prestazioni ottimali in Azure HDInsight.This article describes various Apache HBase performance tuning guidelines and tips for getting optimal performance on Azure HDInsight. Molti di questi suggerimenti dipendono dal particolare carico di lavoro e dal modello di lettura/scrittura/analisi. Prima di applicare le modifiche di configurazione in un ambiente di produzione, testarle accuratamente.

## <a name="hbase-performance-insights"></a>Informazioni dettagliate sulle prestazioni di HBaseHBase performance insights

Il collo di bottiglia principale nella maggior parte dei carichi di lavoro HBase è il write Ahead Log (WAL). Influisce gravemente sulle prestazioni di scrittura. HDInsight HBase ha un modello di storage-compute separato. I dati vengono archiviati in remoto in Archiviazione di Azure, anche se le macchine virtuali ospitano i server dell'area. Fino a poco tempo fa, WAL veniva scritto anche in Archiviazione di Azure.Until recently, the WAL was also written to Azure Storage. In HDInsight, questo comportamento ha amplificato questo collo di bottiglia. La funzionalità [Scritture Accelerated](./apache-hbase-accelerated-writes.md) è progettata per risolvere questo problema. Scrive il WAL nei dischi gestiti con SSD di Azure Premium.It writes the WAL to Azure Premium SSD-managed disks. Ciò è estremamente vantaggioso per le prestazioni di scrittura e consente a molti problemi di alcuni dei carichi di lavoro che richiedono un utilizzo intensivo della scrittura.

Per ottenere un miglioramento significativo nelle operazioni di lettura, usare l'account di [archiviazione BLOB bloccati Premium](https://azure.microsoft.com/blog/azure-premium-block-blob-storage-is-now-generally-available/) come archivio remoto. Questa opzione è possibile solo se la funzione WAL è abilitata.

## <a name="compaction"></a>Compattazione

La compattazione è un altro potenziale collo di bottiglia che è fondamentalmente concordato nella comunità. Per impostazione predefinita, la compattazione principale è disabilitata nei cluster HBase HDInsight.By default, major compaction is disabled on HDInsight HBase clusters. La compattazione è disabilitata perché, anche se si tratta di un processo che richiede un utilizzo intensivo delle risorse, i clienti hanno la massima flessibilità per pianificarla in base ai carichi di lavoro. Ad esempio, potrebbero pianificarlo durante le ore non di punta. Inoltre, la localizzazione dei dati non è un problema perché l'archiviazione è remota (supportata da Archiviazione di Azure) anziché in un file system distribuito Hadoop locale (HDFS).

I clienti dovrebbero programmare la compattazione maggiore a loro piacimento. Se non eseguono questa manutenzione, la compattazione influirà negativamente sulle prestazioni di lettura a lungo termine.

Per le operazioni di scansione, le latenze medi che sono molto superiori a 100 millisecondi dovrebbero essere un motivo di preoccupazione. Controllare se la compattazione principale è stata programmata con precisione.

## <a name="apache-phoenix-workload"></a>Carico di lavoro Apache Phoenix

Rispondere alle seguenti domande ti aiuterà a capire meglio il tuo carico di lavoro Apache Phoenix:

* Tutte le tue "letture" si stanno traducendo in scansioni?
    * In caso affermativo, quali sono le caratteristiche di queste scansioni?
    * Lo schema della tabella Phoenix è stato ottimizzato per queste scansioni, inclusa l'indicizzazione appropriata?
* Hai usato `EXPLAIN` l'istruzione per comprendere i piani di query che le tue "letture" generano?
* Le tue scritture sono "upsert-selects"?
    * Se è così, sarebbero anche fare scansioni. La latenza prevista per le scansioni ha una media di circa 100 millisecondi, rispetto a 10 millisecondi per l'aumento dei punti in HBase.  

## <a name="test-methodology-and-metrics-monitoring"></a>Monitoraggio della metodologia e delle metriche di test

Se si utilizzano benchmark come Yahoo! Cloud Serving Benchmark, JMeter o Pherf per testare e ottimizzare le prestazioni, assicurarsi che:

- I computer client non diventano un collo di bottiglia. A tale scopo, controllare l'utilizzo della CPU nei computer client.

- Le configurazioni lato client, come il numero di thread, sono sintonizzate in modo appropriato per saturare la larghezza di banda del client.

- I risultati dei test vengono registrati in modo accurato e sistematico.

Se le query hanno iniziato improvvisamente a fare molto peggio di prima, verificare la presenza di potenziali bug nel codice dell'applicazione. Sta improvvisamente generando grandi quantità di dati non validi? Se lo è, può aumentare le latenze di lettura.

## <a name="migration-issues"></a>Problemi di migrazione

Se si esegue la migrazione ad Azure HDInsight, assicurarsi che la migrazione venga eseguita in modo sistematico e accurato, preferibilmente tramite l'automazione. Evitare la migrazione manuale. Assicurarsi che:

- Gli attributi della tabella vengono migrati in modo accurato. Gli attributi possono includere come compressione, filtri di fioritura e così via.

- Le impostazioni di salatura nelle tabelle Phoenix vengono mappate in modo appropriato alla nuova dimensione del cluster. Ad esempio, il numero di bucket salt deve essere un multiplo del numero di nodi di lavoro nel cluster. E si dovrebbe usare un multiplo che è un fattore della quantità di hot spotting.

## <a name="server-side-configuration-tunings"></a>Ottimizzazioni della configurazione lato server

In HDInsight HBase, Gli HFile vengono archiviati nell'archiviazione remota. Quando si verifica un mancato riscontro nella cache, il costo delle letture è superiore a quello dei sistemi locali perché i dati nei sistemi locali sono supportati da HDFS locale. Per la maggior parte degli scenari, l'uso intelligente delle cache HBase (cache dei blocchi e cache dei bucket) è progettato per aggirare questo problema. Nei casi in cui il problema non è elusio, l'uso di un account BLOB di blocchi premium può aiutare questo problema. Il driver BLOB di Archiviazione di Windows `fs.azure.read.request.size` Azure si basa su determinate proprietà, ad esempio per recuperare i dati in blocchi in base a ciò che determina per la modalità di lettura (sequenziale o casuale), pertanto potrebbero continuare a essere istanze di latenze più elevate con letture. Attraverso esperimenti empirici, è stato rilevato che`fs.azure.read.request.size`l'impostazione della dimensione del blocco delle richieste di lettura ( ) su 512 KB e la corrispondenza tra le dimensioni del blocco delle tabelle HBase impostando le stesse dimensioni produce il risultato migliore nella pratica.

Per la maggior parte dei cluster di `bucketcache` nodi di grandi dimensioni, HDInsight HBase fornisce come file `regionservers`in un SSD Premium locale collegato alla macchina virtuale, che esegue . L'utilizzo della cache off-heap potrebbe invece fornire qualche miglioramento. Questa soluzione alternativa ha la limitazione di utilizzare la memoria disponibile e potenzialmente essere più piccolo della cache basata su file, pertanto potrebbe non essere sempre la scelta migliore.

Di seguito sono riportati alcuni degli altri parametri specifici che abbiamo sintonizzato, e che sembravano aiutare a vari gradi:

- Aumentare `memstore` le dimensioni dal valore predefinito 128 MB a 256 MB. In genere, questa impostazione è consigliata per scenari di scrittura pesanti.

- Aumentare il numero di thread dedicati alla compattazione, dall'impostazione predefinita da **1** a **4**. Questa impostazione è rilevante se osserviamo frequenti compattazione minori.

- Evitare `memstore` di bloccare lo svuotamento a causa del limite del punto vendita. Per fornire questo buffer, aumentare l'impostazione `Hbase.hstore.blockingStoreFiles` a **100**.

- Per controllare le conquiste, utilizzare le impostazioni seguenti:To control flushes, use the following settings:

    - `Hbase.regionserver.maxlogs`: **140** (evita le vampate a causa dei limiti DI WAL)

    - `Hbase.regionserver.global.memstore.lowerLimit`: **0,55**

    - `Hbase.regionserver.global.memstore.upperLimit`: **0,60**

- Configurazioni specifiche di Phoenix per l'ottimizzazione del pool di thread:

    - `Phoenix.query.queuesize`: **10000**

    - `Phoenix.query.threadpoolsize`: **512**

- Altre configurazioni specifiche di Phoenix:

    - `Phoenix.rpc.index.handler.count`: **50** (se sono presenti ricerche di indice di grandi dimensioni o numerose)

    - `Phoenix.stats.updateFrequency`: **1 ora**

    - `Phoenix.coprocessor.maxmetadatacachetimetolivems`: **1 ora**

    - `Phoenix.coprocessor.maxmetadatacachesize`: **50 MB**

- Timeout RPC: **3 minuti**

   - I timeout RPC includono il timeout RPC di HBase, il timeout dell'utilità di analisi client HBase e il timeout delle query Phoenix. 
   - Assicurarsi che `hbase.client.scanner.caching` il parametro sia impostato sullo stesso valore sia all'estremità del server che alla fine del client. Se non sono uguali, questa impostazione comporta errori client-end correlati a `OutOfOrderScannerException`. Questa impostazione deve essere impostata su un valore basso per le scansioni di grandi dimensioni. Impostare questo valore su **100**.

## <a name="other-considerations"></a>Altre considerazioni

Di seguito sono riportati i parametri aggiuntivi per considerare l'ottimizzazione:

- `Hbase.rs.cacheblocksonwrite`– per impostazione predefinita su HDI, questa impostazione è impostata su **true**.

- Impostazioni che consentono di rinviare la compattazione secondaria per un secondo momento.

- Impostazioni sperimentali, ad esempio la regolazione delle percentuali di code riservate alle richieste di lettura e scrittura.

## <a name="next-steps"></a>Passaggi successivi

Se il problema persiste, visitare uno dei seguenti canali per ulteriore supporto:

- Ottieni risposte dagli esperti di Azure tramite il supporto della community di [Azure.](https://azure.microsoft.com/support/community/)

- Connettersi [@AzureSupport](https://twitter.com/azuresupport)con . Questo è l'account ufficiale di Microsoft Azure per migliorare l'esperienza del cliente. Connette la community di Azure alle risorse giuste: risposte, supporto ed esperti.

- Per altre informazioni, è possibile inviare una richiesta di supporto dal portale di [Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selezionare **Supporto** dalla barra dei menu o aprire l'hub **Guida e supporto** tecnico. Per informazioni più dettagliate, vedere Come creare una richiesta di supporto di Azure.For more detailed information, review [How to create an Azure support request](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). La sottoscrizione di Microsoft Azure include l'accesso al supporto per la gestione della sottoscrizione e la fatturazione e il supporto tecnico viene fornito tramite uno dei piani di supporto di [Azure.](https://azure.microsoft.com/support/plans/)
