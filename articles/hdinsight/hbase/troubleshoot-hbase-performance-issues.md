---
title: Risolvere i problemi relativi alle prestazioni di Apache HBase in Azure HDInsight
description: Diverse linee guida e suggerimenti per l'ottimizzazione delle prestazioni di Apache HBase per ottenere prestazioni ottimali in Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 09/24/2019
ms.openlocfilehash: 0466b08e551a5fa9da37afe2e5ad175ef28c804e
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72529562"
---
# <a name="troubleshoot-apache-hbase-performance-issues-on-azure-hdinsight"></a>Risolvere i problemi relativi alle prestazioni di Apache HBase in Azure HDInsight

Questo articolo descrive diverse linee guida e suggerimenti per l'ottimizzazione delle prestazioni di Apache HBase per ottenere prestazioni ottimali in Azure HDInsight. Molti di questi suggerimenti dipendono dal carico di lavoro specifico e dal modello di lettura/scrittura/analisi. Prima di applicare le modifiche alla configurazione in un ambiente di produzione, testarle accuratamente.

## <a name="hbase-performance-insights"></a>Informazioni dettagliate sulle prestazioni di HBase

Il primo collo di bottiglia nella maggior parte dei carichi di lavoro HBase è il log write-ahead (WAL). Influire gravemente sulle prestazioni di scrittura. HDInsight HBase ha un modello di calcolo di archiviazione separato. I dati vengono archiviati in remoto in archiviazione di Azure, anche se le macchine virtuali ospitano i server di area. Fino a poco tempo fa, WAL è stato scritto anche in archiviazione di Azure. In HDInsight questo comportamento ha amplificato questo collo di bottiglia. La funzionalità di [scrittura accelerata](./apache-hbase-accelerated-writes.md) è progettata per risolvere questo problema. Scrive il WAL in Azure SSD Premium Managed Disks. Si tratta di un vantaggio estremamente vantaggioso per le prestazioni di scrittura e contribuisce a molti problemi affrontati da alcuni dei carichi di lavoro a elevato utilizzo di scrittura.

Per ottenere un miglioramento significativo nelle operazioni di lettura, usare l' [account di archiviazione BLOB in blocchi Premium](https://azure.microsoft.com/blog/azure-premium-block-blob-storage-is-now-generally-available/) come archiviazione remota. Questa opzione è disponibile solo se la funzionalità WAL è abilitata.

## <a name="compaction"></a>Compattazione

La compattazione è un altro potenziale collo di bottiglia che è fondamentalmente concordato nella community. Per impostazione predefinita, la compattazione principale è disabilitata nei cluster HBase di HDInsight. La compattazione è disabilitata perché, anche se si tratta di un processo a elevato utilizzo di risorse, i clienti hanno la massima flessibilità per pianificarla in base ai carichi di lavoro. Ad esempio, potrebbero pianificarlo durante le fasce orarie di minore traffico. Inoltre, la località dei dati non è un problema perché l'archiviazione è remota (supportata da archiviazione di Azure) anziché a una Hadoop Distributed File System locale (HDFS).

I clienti devono pianificare una compattazione principale con praticità. Se questa operazione non viene eseguita, la compattazione influirà negativamente sulle prestazioni di lettura a lungo termine.

Per le operazioni di analisi, le latenze medie che sono molto più elevate di 100 millisecondi dovrebbero essere motivo di preoccupazione. Controllare se la compattazione principale è stata pianificata in modo accurato.

## <a name="apache-phoenix-workload"></a>Carico di lavoro Apache Phoenix

La risposta alle domande seguenti consentirà di comprendere meglio il carico di lavoro Apache Phoenix:

* Tutte le "letture" si traducono in analisi?
    * In tal caso, quali sono le caratteristiche di queste analisi?
    * È stato ottimizzato lo schema della tabella Phoenix per queste analisi, inclusa l'indicizzazione appropriata?
* È stata usata l'istruzione `EXPLAIN` per comprendere i piani di query generati da "Reads"?
* Le Scritture "Upsert-Select"?
    * In tal caso, eseguiranno anche analisi. Latenza prevista per le analisi medie circa 100 millisecondi, rispetto a 10 millisecondi per il punto di HBase.  

## <a name="test-methodology-and-metrics-monitoring"></a>Metodologia di test e monitoraggio delle metriche

Se si usano i benchmark come Yahoo! Benchmark per il cloud, JMeter o Pherf per testare e ottimizzare le prestazioni, assicurarsi che:

- I computer client non diventano un collo di bottiglia. A tale scopo, controllare l'utilizzo della CPU nei computer client.

- Le configurazioni lato client, ad esempio il numero di thread, sono ottimizzate in modo appropriato per saturare la larghezza di banda dei client.

- I risultati dei test vengono registrati in modo accurato e sistematico.

Se le query hanno improvvisamente iniziato a peggiorare molto peggio, verificare la presenza di potenziali bug nel codice dell'applicazione. Si sta generando improvvisamente grandi quantità di dati non validi? In tal caso, è possibile aumentare le latenze di lettura.

## <a name="migration-issues"></a>Problemi di migrazione

Se si sta eseguendo la migrazione ad Azure HDInsight, assicurarsi che la migrazione venga eseguita in modo sistematico e accurato, preferibilmente tramite l'automazione. Evitare la migrazione manuale. Assicurarsi che:

- Gli attributi della tabella vengono migrati in modo accurato. Gli attributi possono includere come compressione, filtri Bloom e così via.

- Le impostazioni di salting nelle tabelle Phoenix vengono mappate in modo appropriato alle nuove dimensioni del cluster. Il numero di bucket di tipo Salt, ad esempio, deve essere un multiplo del numero di nodi del ruolo di lavoro nel cluster. Ed è necessario usare un multiplo che rappresenta un fattore della quantità di spot a caldo.

## <a name="server-side-configuration-tunings"></a>Ottimizzazioni della configurazione lato server

In HDInsight HBase, HFiles vengono archiviati nella risorsa di archiviazione remota. Quando si verifica un mancato riscontro nella cache, il costo delle letture è superiore rispetto ai sistemi locali perché i dati nei sistemi locali sono supportati da HDFS locali. Per la maggior parte degli scenari, l'uso intelligente delle cache HBase (cache a blocchi e cache di bucket) è progettato per aggirare questo problema. Nei casi in cui il problema non viene aggirato, l'uso di un account BLOB in blocchi Premium può aiutare a risolvere questo problema. Il driver di Windows BLOB del servizio di archiviazione di Azure si basa su determinate proprietà, ad esempio `fs.azure.read.request.size` per recuperare i dati in blocchi in base a ciò che determina la modalità di lettura (sequenziale e casuale), in modo che sia possibile continuare a essere istanze di latenze più elevate con letture. Attraverso esperimenti empirici è stato rilevato che l'impostazione delle dimensioni del blocco della richiesta di lettura (`fs.azure.read.request.size`) su 512 KB e la corrispondenza delle dimensioni del blocco delle tabelle HBase con le stesse dimensioni produce il risultato migliore in pratica.

Per la maggior parte dei cluster di nodi di grandi dimensioni, HDInsight HBase fornisce `bucketcache` come file in una SSD Premium locale collegata alla macchina virtuale, che esegue `regionservers`. In alternativa, l'utilizzo della cache fuori heap potrebbe offrire un miglioramento. Questa soluzione alternativa prevede la limitazione dell'utilizzo della memoria disponibile e potenzialmente inferiore rispetto alla cache basata su file, quindi potrebbe non essere sempre la scelta migliore.

Di seguito sono riportati alcuni degli altri parametri specifici che sono stati ottimizzati e che sembrano essere utili per variare i gradi:

- Aumentare le dimensioni del `memstore` dall'impostazione predefinita 128 MB a 256 MB. Questa impostazione è in genere consigliata per scenari di scrittura intensivi.

- Aumentare il numero di thread dedicati per la compattazione, dall'impostazione predefinita da **1** a **4**. Questa impostazione è pertinente se si osservano compattazioni secondarie frequenti.

- Evitare il blocco dello scaricamento `memstore` a causa del limite del negozio. Per fornire questo buffer, aumentare l'impostazione del `Hbase.hstore.blockingStoreFiles` su **100**.

- Per controllare gli scaricamenti, usare le impostazioni seguenti:

    - `Hbase.regionserver.maxlogs`: **140** (evita gli scaricamenti a causa dei limiti di Wal)

    - `Hbase.regionserver.global.memstore.lowerLimit`: **0,55**

    - `Hbase.regionserver.global.memstore.upperLimit`: **0,60**

- Configurazioni specifiche di Phoenix per l'ottimizzazione del pool di thread:

    - `Phoenix.query.queuesize`: **10000**

    - `Phoenix.query.threadpoolsize`: **512**

- Altre configurazioni specifiche di Phoenix:

    - `Phoenix.rpc.index.handler.count`: **50** (se sono presenti numerose ricerche nell'indice)

    - `Phoenix.stats.updateFrequency`: **1 ora**

    - `Phoenix.coprocessor.maxmetadatacachetimetolivems`: **1 ora**

    - `Phoenix.coprocessor.maxmetadatacachesize`: **50 MB**

- Timeout RPC: **3 minuti**

   - I timeout RPC includono il timeout RPC HBase, il timeout dello scanner client HBase e il timeout delle query Phoenix. 
   - Verificare che il parametro `hbase.client.scanner.caching` sia impostato sullo stesso valore sia sul lato server sia sul lato client. Se non sono uguali, questa impostazione genera errori di fine client correlati a `OutOfOrderScannerException`. Questa impostazione deve essere impostata su un valore basso per le analisi di grandi dimensioni. Questo valore viene impostato su **100**.

## <a name="other-considerations"></a>Altre considerazioni

Di seguito sono riportati i parametri aggiuntivi da considerare per l'ottimizzazione:

- `Hbase.rs.cacheblocksonwrite`: per impostazione predefinita, in HDI questa impostazione è impostata su **true**.

- Impostazioni che consentono di rinviare la compattazione secondaria per un momento successivo.

- Impostazioni sperimentali, ad esempio la modifica delle percentuali delle code riservate per le richieste di lettura e scrittura.

## <a name="next-steps"></a>Passaggi successivi

Se il problema persiste, visitare uno dei canali seguenti per ottenere ulteriore supporto:

- Ottieni risposte dagli esperti di Azure tramite il [supporto della community di Azure](https://azure.microsoft.com/support/community/).

- Connettersi con [@AzureSupport](https://twitter.com/azuresupport). Si tratta dell'account Microsoft Azure ufficiale per migliorare l'esperienza dei clienti. Connette la community di Azure alle risorse appropriate: risposte, supporto ed esperti.

- Se è necessaria ulteriore assistenza, è possibile inviare una richiesta di supporto dal [portale di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selezionare **supporto** dalla barra dei menu o aprire l'hub **Guida e supporto** . Per informazioni più dettagliate, vedere [come creare una richiesta di supporto di Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). La sottoscrizione Microsoft Azure include l'accesso al supporto per la gestione delle sottoscrizioni e la fatturazione e il supporto tecnico viene fornito tramite uno dei [piani di supporto di Azure](https://azure.microsoft.com/support/plans/).
