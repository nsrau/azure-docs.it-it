---
title: Risolvere i problemi relativi alle prestazioni di Apache HBase in Azure HDInsight
description: Diverse linee guida e suggerimenti per l'ottimizzazione delle prestazioni di Apache HBase per ottenere prestazioni ottimali in Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 09/24/2019
ms.openlocfilehash: c67f21a6ed8a7697977bb7737f0e46348efb2530
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2019
ms.locfileid: "71266653"
---
# <a name="troubleshoot-apache-hbase-performance-issues-on-azure-hdinsight"></a>Risolvere i problemi relativi alle prestazioni di Apache HBase in Azure HDInsight

Questo documento descrive le varie linee guida per l'ottimizzazione delle prestazioni di Apache HBase e suggerimenti per ottenere prestazioni ottimali in Azure HDInsight. Molti di questi suggerimenti dipendono dal carico di lavoro specifico e dal modello di lettura/scrittura/analisi. Verificare accuratamente le modifiche apportate alla configurazione prima di applicare in un ambiente di produzione.

## <a name="hdinsight-hbase-performance-insights"></a>Informazioni dettagliate sulle prestazioni di HDInsight HBase

Il primo collo di bottiglia nella maggior parte dei carichi di lavoro HBase è il log write-ahead (WAL). Influire gravemente sulle prestazioni di scrittura. HDInsight HBase ha un modello di calcolo di archiviazione separato, ovvero i dati vengono archiviati in remoto in archiviazione di Azure, ma i server di area sono ospitati nelle VM. Fino a poco tempo fa, anche il log write-ahead è stato scritto in archiviazione di Azure, in modo da amplificare questo collo di bottiglia nel caso di HDInsight. La funzionalità di scrittura [accelerata](./apache-hbase-accelerated-writes.md) è progettata per risolvere questo problema, scrivendo il log write-ahead in dischi gestiti di unità SSD Premium di Azure. In questo modo, le prestazioni di scrittura sono molto numerose e contribuiscono a molti problemi riscontrati da alcuni dei carichi di lavoro a elevato utilizzo di scrittura.

Usare l'[account di archiviazione BLOB in blocchi Premium](https://azure.microsoft.com/blog/azure-premium-block-blob-storage-is-now-generally-available/) come risorsa di archiviazione remota per ottenere un miglioramento significativo nelle operazioni di lettura. Questa opzione è disponibile solo se è abilitata la funzionalità WAL.

## <a name="compaction"></a>Compattazione

La compattazione è un altro potenziale collo di bottiglia, fondamentalmente concordato nella community.  Per impostazione predefinita, la compattazione principale è disabilitata nei cluster HBase di HDInsight. Ciò è dovuto al fatto che si tratta di un processo che richiede un utilizzo intensivo di risorse, che consente ai clienti la massima flessibilità di pianificazione in base alle caratteristiche del carico di lavoro, ovvero durante gli orari di minore attività. Dato che la risorsa di archiviazione è remota (supportata da archiviazione di Azure) anziché HDFS locale, che è comune nella maggior parte delle istanze locali, la località dei dati non è un problema, ovvero uno degli obiettivi principali della compattazione principale.

Il presupposto è che il cliente si occupi di pianificare la compattazione principale in base alla loro convenienza. Se la manutenzione non viene eseguita, la compattazione influirà in modo significativo sulle prestazioni di lettura a lungo termine.

Per le operazioni di analisi in particolare, le latenze medie molto più elevate di 100 MS dovrebbero essere motivo di preoccupazione. Controllare se la compattazione principale è stata pianificata in modo accurato.

## <a name="know-your-apache-phoenix-workload"></a>Informazioni sul carico di lavoro Apache Phoenix

La risposta alle domande seguenti consentirà di comprendere meglio il carico di lavoro Apache Phoenix:

* Tutte le "letture" si traducono in analisi?
    * In tal caso, quali sono le caratteristiche di queste analisi?
    * È stato ottimizzato lo schema della tabella Phoenix per queste analisi, inclusa l'indicizzazione appropriata?
* È stata usata l'istruzione `EXPLAIN` per comprendere i piani di query generati da "Reads".
* Le Scritture "Upsert-Select"?
    * In tal caso, eseguiranno anche analisi. La latenza prevista per le analisi è dell'ordine di 100 ms in media, anziché di 10 ms per il punto in HBase.  

## <a name="test-methodology-and-metrics-monitoring"></a>Metodologia di test e monitoraggio delle metriche

Se si usano i benchmark come YCSB, JMeter o Pherf per testare e ottimizzare le prestazioni, verificare quanto segue:

1. I computer client non diventano un collo di bottiglia (controllare l'utilizzo della CPU nei computer client).

1. Le configurazioni lato client, ad esempio il numero di thread e così via, vengono ottimizzate in modo appropriato per saturare la larghezza di banda dei client.

1. I risultati dei test vengono registrati in modo accurato e sistematico.

Se le query hanno improvvisamente iniziato a peggiorare molto peggio, verificare la presenza di potenziali bug nel codice dell'applicazione, generando improvvisamente grandi quantità di dati non validi, aumentando naturalmente le latenze di lettura?

## <a name="migration-issues"></a>Problemi di migrazione

Se si esegue la migrazione ad Azure HDInsight, assicurarsi che la migrazione venga eseguita in modo sistematico e accurato, preferibilmente tramite l'automazione. Evitare la migrazione manuale. Verificare quanto segue:

1. Gli attributi della tabella, ad esempio la compressione, i filtri Bloom e così via, devono essere migrati in modo accurato.

1. Per le tabelle Phoenix, le impostazioni di salting devono essere mappate in modo appropriato alle nuove dimensioni del cluster. È consigliabile, ad esempio, che il numero di bucket di tipo Salt sia multiplo del numero di nodi del ruolo di lavoro nel cluster, il multiplo specifico è un fattore della quantità di spot a caldo osservato.  

## <a name="server-side-config-tunings"></a>Ottimizzazioni della configurazione lato server

In HDInsight HBase, HFiles vengono archiviati nell'archiviazione remota. in questo modo, in caso di mancato riscontro nella cache, il costo delle letture sarà sicuramente superiore rispetto ai sistemi locali, che hanno dati supportati da HDFS locali grazie alla latenza di rete. Per la maggior parte degli scenari, l'uso intelligente delle cache HBase (cache a blocchi e cache di bucket) è progettato per aggirare questo problema. Tuttavia, esistono casi occasionali in cui questo potrebbe costituire un problema per i clienti. L'uso di un account BLOB in blocchi Premium è stato utile. Tuttavia, il BLOB di WASB (driver di archiviazione di Microsoft Azure) si basa su determinate proprietà, ad esempio `fs.azure.read.request.size` per recuperare i dati in blocchi in base a ciò che determina la modalità di lettura (sequenziale e casuale). si potrebbero continuare a vedere istanze di latenze più elevate con letture. Sono stati rilevati esperimenti empirici che consentono di impostare le dimensioni del blocco della richiesta di lettura (`fs.azure.read.request.size`) su 512 KB e di abbinare le dimensioni del blocco delle tabelle HBase in modo che corrispondano ai risultati migliori in pratica.

HDInsight HBase, per la maggior parte dei cluster di nodi di dimensioni maggiori, fornisce `bucketcache` come file nell'unità SSD locale collegata alla VM, che esegue il `regionservers`. In alcuni casi, l'utilizzo di off cache heap può dare un certo miglioramento. Questa operazione prevede la limitazione dell'utilizzo della memoria disponibile e di dimensioni potenzialmente inferiori rispetto alla cache basata su file, pertanto questo potrebbe non essere sempre la scelta migliore.

Alcuni degli altri parametri specifici che sono stati ottimizzati sembravano avere contribuito a variare i gradi con una logica come la seguente:

1. Aumento delle dimensioni di @no__t 0 da 128 MB predefiniti a 256 MB. questa impostazione è generalmente consigliata per uno scenario di scrittura intensivo.

1. Aumento del numero di thread dedicati per la compattazione, dal valore predefinito da 1 a 4. Questa impostazione è pertinente se si osservano compattazioni secondarie frequenti.

1. Evitare il blocco dello scaricamento `memstore` a causa del limite del negozio. `Hbase.hstore.blockingStoreFiles` può essere aumentato a 100 per fornire questo buffer.

1. Per il controllo degli Scaricamenti, le impostazioni predefinite possono essere risolte come indicato di seguito:

    1. `Hbase.regionserver.maxlogs` può essere aumentato a 140 da 32 (evitando gli scaricamenti a causa dei limiti di WAL).

    1. `Hbase.regionserver.global.memstore.lowerLimit` = 0,55.

    1. `Hbase.regionserver.global.memstore.upperLimit` = 0,60.

1. Configurazioni specifiche di Phoenix per l'ottimizzazione del pool di thread:

    1. `Phoenix.query.queuesize` può essere aumentato a 10000.

    1. `Phoenix.query.threadpoolsize` può essere aumentato a 512.

1. Altre configurazioni specifiche di Phoenix:

    1. `Phoenix.rpc.index.handler.count` può essere impostato su 50 se sono presenti grandi o numerose ricerche nell'indice.

    1. `Phoenix.stats.updateFrequency`: il valore predefinito di 15 minuti può essere aumentato a 1 ora.

    1. `Phoenix.coprocessor.maxmetadatacachetimetolivems`: può essere aumentato a 1 ora da 30 minuti.

    1. `Phoenix.coprocessor.maxmetadatacachesize`: può essere incrementato a 50 MB da 20 MB.

1. Timeout RPC: timeout RPC HBase, timeout scanner client HBase e timeout query Phoenix possono essere aumentati fino a 3 minuti. È importante notare che il parametro `hbase.client.scanner.caching` è impostato su un valore che corrisponde a End Server e client end. In caso contrario, questa impostazione genera errori correlati a `OutOfOrderScannerException` al termine del client. Questa impostazione deve essere impostata su un valore basso per le analisi di grandi dimensioni. Questo valore viene impostato su 100.

## <a name="other-considerations"></a>Altre considerazioni

Altri parametri da considerare per l'ottimizzazione:

1. `Hbase.rs.cacheblocksonwrite`: questa impostazione è impostata su true per impostazione predefinita in HDI.

1. Impostazioni che consentono di rinviare la compattazione secondaria per un momento successivo.

1. Impostazioni sperimentali, ad esempio la regolazione delle percentuali delle code riservate per le richieste di lettura e scrittura.

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

- Ottieni risposte dagli esperti di Azure tramite il [supporto della community di Azure](https://azure.microsoft.com/support/community/).

- Connettersi con [@AzureSupport](https://twitter.com/azuresupport) : l'account Microsoft Azure ufficiale per migliorare l'esperienza del cliente. Connessione della community di Azure alle risorse appropriate: risposte, supporto ed esperti.

- Se è necessaria ulteriore assistenza, è possibile inviare una richiesta di supporto dal [portale di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selezionare **supporto** dalla barra dei menu o aprire l'hub **Guida e supporto** . Per informazioni più dettagliate, vedere [come creare una richiesta di supporto di Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). L'accesso alla gestione delle sottoscrizioni e al supporto per la fatturazione è incluso nella sottoscrizione di Microsoft Azure e il supporto tecnico viene fornito tramite uno dei [piani di supporto di Azure](https://azure.microsoft.com/support/plans/).
