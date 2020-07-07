---
title: Ottimizzare Apache Hive con Apache Ambari in Azure HDInsight
description: Usare l'interfaccia utente Web di Apache Ambari per configurare e ottimizzare Apache Hive.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: ce3916ef1155224a91c0736c3dabe907ae8d2611
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82796369"
---
# <a name="optimize-apache-hive-with-apache-ambari-in-azure-hdinsight"></a>Ottimizzare Apache Hive con Apache Ambari in Azure HDInsight

Apache Ambari è un'interfaccia Web per la gestione e il monitoraggio di cluster HDInsight. Per un'introduzione all'interfaccia utente Web di Ambariri, vedere [gestire i cluster HDInsight tramite l'interfaccia utente Web di Apache Ambari](hdinsight-hadoop-manage-ambari.md).

Le sezioni seguenti descrivono le opzioni di configurazione per ottimizzare le prestazioni generali di Apache Hive.

1. Per modificare i parametri di configurazione di Hive, selezionare **Hive** dalla barra laterale Services (Servizi).
1. Passare alla scheda **Configs** (Configurazioni).

## <a name="set-the-hive-execution-engine"></a>Impostare il motore di esecuzione di Hive

Hive offre due motori di esecuzione: Apache Hadoop MapReduce e Apache TEZ. Tez è più veloce di MapReduce. I cluster Linux in HDInsight usano Tez come motore di esecuzione predefinito. Per cambiare il motore di esecuzione:

1. Nella scheda **Configs** (Configurazioni) di Hive digitare **execution engine** nella casella di filtro.

    ![Motore di esecuzione della ricerca di Apache Ambari](./media/optimize-hive-ambari/ambari-search-execution.png)

1. Il valore predefinito della proprietà **Optimization** (Ottimizzazione) è **Tez**.

    ![Ottimizzazione-motore Apache Tez](./media/optimize-hive-ambari/optimization-apache-tez.png)

## <a name="tune-mappers"></a>Ottimizzare i mapper

Hadoop prova a dividere (*eseguire il mapping*) di un singolo file in più file e a elaborare i file risultanti in parallelo. Il numero di mapper dipende dal numero di suddivisioni. I due parametri di configurazione seguenti determinano il numero di suddivisioni per il motore di esecuzione:

* `tez.grouping.min-size`: limite minimo delle dimensioni di una suddivisione raggruppata, con un valore predefinito di 16 MB (16.777.216 byte).
* `tez.grouping.max-size`: limite massimo delle dimensioni di una suddivisione raggruppata, con un valore predefinito di 1 GB (1.073.741.824 byte).

Come linea guida per le prestazioni, abbassare entrambi i parametri per migliorare la latenza, aumentando la velocità effettiva.

Ad esempio, per impostare quattro attività di mapper per dimensioni dei dati pari a 128 MB, impostare entrambi i parametri su 32 MB ognuno (33.554.432 byte).

1. Per modificare i parametri limite, passare alla scheda **Configs** (Configurazioni) del servizio Tez. Espandere il pannello **General** (Generale) e individuare i parametri `tez.grouping.max-size` e `tez.grouping.min-size`.

1. Impostare entrambi i parametri su **33.554.432** byte (32 MB).

    ![Dimensioni di raggruppamento di Apache Ambari Tez](./media/optimize-hive-ambari/apache-tez-grouping-size.png)

Queste modifiche interessano tutti i processi Tez nel server. Per ottenere un risultato ottimale, scegliere valori appropriati per i parametri.

## <a name="tune-reducers"></a>Ottimizzare i riduttori

Apache ORC e Snappy offrono entrambi prestazioni elevate. Hive potrebbe tuttavia avere un numero troppo basso di riduttori per impostazione predefinita e causare quindi colli di bottiglia.

Si supponga, ad esempio, di avere dati di input di dimensioni pari a 50 GB. Tali dati nel formato ORC con la compressione Snappy sono pari a 1 GB. Hive stima il numero di riduttori necessari nel modo seguente: (numero di byte di input per i mapper / `hive.exec.reducers.bytes.per.reducer`).

Con le impostazioni predefinite, questo esempio è quattro riduttori.

Il parametro `hive.exec.reducers.bytes.per.reducer` specifica il numero di byte elaborati per riduttore. Il valore predefinito è 64 MB. Se si diminuisce questo valore, il parallelismo aumenta e le prestazioni possono migliorare. Se lo si diminuisce troppo, potrebbero anche essere generati troppi riduttori, che possono influire negativamente sulle prestazioni. Questo parametro è basato sugli specifici requisiti per i dati, sulle impostazioni di compressione e su altri fattori ambientali.

1. Per modificare il parametro, passare alla scheda **Configs** (Configurazioni) di Hive e trovare il parametro **Data per Reducer** (Dati per riduttore) nella pagina Settings (Impostazioni).

    ![Dati Apache Ambari per riduttore](./media/optimize-hive-ambari/ambari-data-per-reducer.png)

1. Selezionare **Edit** (Modifica) per impostare il valore su 128 MB (134.217.728 byte) e quindi premere **INVIO** per salvare.

    ![Dati Ambari per riduttore-modificati](./media/optimize-hive-ambari/data-per-reducer-edited.png)
  
    Date le dimensioni di input di 1.024 MB, con 128 MB di dati per riduttore, sono presenti otto riduttori (1024/128).

1. Un valore non corretto per il parametro **Data per Reducer** (Dati per riduttore) può restituire un numero elevato di riduttori, che influisce negativamente sulle prestazioni della query. Per limitare il numero massimo di riduttori, impostare `hive.exec.reducers.max` su un valore appropriato. Il valore predefinito è 1009.

## <a name="enable-parallel-execution"></a>Abilitare l'esecuzione parallela

Una query Hive viene eseguita in una o più fasi. Se le fasi indipendenti possono essere eseguite in parallelo, le prestazioni della query miglioreranno.

1. Per abilitare l'esecuzione della query parallela, passare alla scheda **Config** (Configurazioni) di Hive e cercare la proprietà `hive.exec.parallel`. Il valore predefinito è false. Impostare il valore su true e quindi premere **INVIO** per salvare il valore.

1. Per limitare il numero di processi da eseguire in parallelo, modificare la `hive.exec.parallel.thread.number` Proprietà. Il valore predefinito è 8.

    ![Visualizzazione di Apache Hive Exec Parallel](./media/optimize-hive-ambari/apache-hive-exec-parallel.png)

## <a name="enable-vectorization"></a>Abilitare la vettorializzazione

Hive elabora i dati una riga alla volta. Con la vettorializzazione Hive elabora i dati in blocchi di 1.024 righe invece che una riga alla volta. La vettorializzazione è applicabile solo al formato di file ORC.

1. Per abilitare un'esecuzione di query vettorializzata, passare alla scheda **Configs** (Configurazioni) di Hive e cercare il parametro `hive.vectorized.execution.enabled`. Il valore predefinito è true per Hive 0.13.0 o versione successiva.

1. Per abilitare l'esecuzione vettorializzata per il lato reduce della query, impostare il parametro `hive.vectorized.execution.reduce.enabled` su true. Il valore predefinito è false.

    ![Apache Hive esecuzione vettoriale](./media/optimize-hive-ambari/hive-vectorized-execution.png)

## <a name="enable-cost-based-optimization-cbo"></a>Abilitare l'ottimizzazione basata sui costi

Per impostazione predefinita, Hive segue un set di regole per trovare un piano di esecuzione della query ottimale. L'ottimizzazione basata sui costi valuta più piani per l'esecuzione di una query. E assegna un costo a ogni piano, quindi determina il piano più economico per l'esecuzione di una query.

Per abilitare la configurazione di base di base, passare a impostazioni di configurazione di **hive**  >  **Configs**  >  **Settings** e trovare **Abilita ottimizzazione basata sui costi**, quindi impostare l'interruttore **su**attivato.

![Ottimizzatore basato sui costi di HDInsight](./media/optimize-hive-ambari/hdinsight-cbo-config.png)

I parametri di configurazione aggiuntivi seguenti migliorano le prestazioni della query Hive quando l'ottimizzazione basata sui costi è abilitata:

* `hive.compute.query.using.stats`

    Se impostato su true, Hive usa le statistiche archiviate nel metastore per rispondere a query semplici, ad esempio `count(*)`.

    ![Apache Hive query di calcolo con statistiche](./media/optimize-hive-ambari/hive-compute-query-using-stats.png)

* `hive.stats.fetch.column.stats`

    Quando l'ottimizzazione basata sui costi è abilitata, vengono create statistiche di colonna. Hive usa le statistiche di colonna, archiviate nel metastore, per ottimizzare le query. Recuperare le statistiche per ogni colonna richiede più tempo quando il numero di colonne è elevato. Se impostata su false, questa impostazione disabilita il recupero delle statistiche di colonna dal metastore.

    ![Statistiche colonna set statistiche Apache Hive](./media/optimize-hive-ambari/hive-stats-fetch-column-stats.png)

* `hive.stats.fetch.partition.stats`

    Le statistiche della partizione di base, ad esempio numero di righe, dimensioni dei dati e dimensioni dei file, vengono archiviate nel metastore. Se impostato su true, le statistiche della partizione vengono recuperate dal Metastore. Se è false, le dimensioni del file vengono recuperate dal file system. Il numero di righe viene recuperato dallo schema di riga.

    ![Statistiche della partizione impostate nelle statistiche di Hive](./media/optimize-hive-ambari/hive-stats-fetch-partition-stats.png)

## <a name="enable-intermediate-compression"></a>Abilitare la compressione intermedia

Le attività mappe creano file intermedi che vengono usati dalle attività riduttori. La compressione intermedia riduce le dimensioni dei file intermedi.

I processi Hadoop presentano in genere colli di bottiglia a causa dell'I/O. La compressione dei dati può velocizzare l'I/O e il trasferimento di rete complessivo.

I tipi di compressione disponibili sono:

| Formato | Strumento | Algoritmo | Estensione nome del file | Divisibile |
| --- | --- | --- | --- | --- |
| Gzip | Gzip | DEFLATE | `.gz` | No |
| Bzip2 | Bzip2 | Bzip2 |`.bz2` | Sì |
| LZO | `Lzop` | LZO | `.lzo` | Sì, se indicizzato |
| Snappy | N/D | Snappy | Snappy | No |

Come regola generale, la possibilità di suddividere il metodo di compressione è importante. in caso contrario, verranno creati pochi Mapper. Se i dati di input sono costituiti da testo, `bzip2` è l'opzione migliore. Per il formato ORC, Snappy è l'opzione di compressione più rapida.

1. Per abilitare la compressione intermedia, passare alla scheda **Configs** (Configurazioni) di Hive e quindi impostare il parametro `hive.exec.compress.intermediate` su true. Il valore predefinito è false.

    ![' Hive Exec Compress Intermediate '](./media/optimize-hive-ambari/hive-exec-compress-intermediate.png)

    > [!NOTE]  
    > Per comprimere i file intermedi, scegliere un codec di compressione con costi di CPU più bassi, anche se il codec non ha un output di compressione elevato.

1. Per impostare il codec di compressione intermedia, aggiungere la proprietà personalizzata `mapred.map.output.compression.codec` al file `hive-site.xml` o `mapred-site.xml`.

1. Per aggiungere un'impostazione personalizzata:

    a. Passare a **hive**  >  **configs**  >  **Advanced**  >  **Custom hive-site**.

    b. Selezionare **Aggiungi proprietà** nella parte inferiore del riquadro Custom hive-site.

    c. Nella finestra Add Property (Aggiungi proprietà) immettere `mapred.map.output.compression.codec` come chiave e `org.apache.hadoop.io.compress.SnappyCodec` come valore.

    d. Selezionare **Aggiungi**.

    ![' Apache Hive proprietà personalizzata Add '](./media/optimize-hive-ambari/hive-custom-property.png)

    Questa impostazione comprimerà il file intermedio utilizzando la compressione a scatto. Dopo che la proprietà è stata aggiunta, viene visualizzata nel riquadro Custom hive-site (hive-site personalizzato).

    > [!NOTE]  
    > Questa procedura modifica il file `$HADOOP_HOME/conf/hive-site.xml`.

## <a name="compress-final-output"></a>Comprimere l'output finale

Anche l'output di Hive finale può essere compresso.

1. Per comprimere l'output di Hive finale, passare alla scheda **Configs** (Configurazioni) di Hive e quindi impostare il parametro `hive.exec.compress.output` su true. Il valore predefinito è false.

1. Per scegliere il codec di compressione dell'output, aggiungere la proprietà personalizzata `mapred.output.compression.codec` al riquadro Custom hive-site (hive-site personalizzato), come descritto nel passaggio 3 della sezione precedente.

    ![Apache Hive proprietà personalizzata ADD2](./media/optimize-hive-ambari/hive-custom-property2.png)

## <a name="enable-speculative-execution"></a>Abilitare l'esecuzione speculativa

L'esecuzione speculativa avvia un certo numero di attività duplicate per rilevare e negare l'elenco dello strumento di rilevamento delle attività con esecuzione prolungata. Migliorando l'esecuzione complessiva del processo, ottimizzando i risultati delle singole attività.

È consigliabile non attivare l'esecuzione speculativa per le attività MapReduce a esecuzione prolungata con grandi quantità di input.

* Per abilitare l'esecuzione speculativa, passare alla scheda **Configs** (Configurazioni) di Hive e quindi impostare il parametro `hive.mapred.reduce.tasks.speculative.execution` su true. Il valore predefinito è false.

    ![' Hive mapred ridurre le attività di esecuzione speculativa '](./media/optimize-hive-ambari/hive-mapred-reduce-tasks-speculative-execution.png)

## <a name="tune-dynamic-partitions"></a>Ottimizzare le partizioni dinamiche

Hive consente di creare partizioni dinamiche quando si inseriscono i record in una tabella, senza predefinire ogni partizione. Questa possibilità è una funzionalità potente. Sebbene possa comportare la creazione di un numero elevato di partizioni. E un numero elevato di file per ogni partizione.

1. Per creare partizioni dinamiche in Hive, il parametro `hive.exec.dynamic.partition` deve essere impostato su (impostazione predefinita).

1. Impostare la modalità di partizione dinamica su *strict*. Nella modalità strict almeno una partizione deve essere statica Questa impostazione impedisce le query senza il filtro di partizione nella clausola WHERE, ovvero *strict* impedisce le query che analizzano tutte le partizioni. Passare alla scheda **Configs** (Configurazioni) di Hive e quindi impostare `hive.exec.dynamic.partition.mode` su **strict**. Il valore predefinito è **nonstrict**.

1. Per limitare il numero di partizioni dinamiche da creare, modificare il parametro `hive.exec.max.dynamic.partitions`. Il valore predefinito è 5000.

1. Per limitare il numero totale di partizioni dinamiche per nodo, modificare `hive.exec.max.dynamic.partitions.pernode`. Il valore predefinito è 2000.

## <a name="enable-local-mode"></a>Abilitare la modalità locale

La modalità locale consente a hive di eseguire tutte le attività di un processo in un singolo computer. O a volte in un singolo processo. Questa impostazione migliora le prestazioni delle query se i dati di input sono di piccole dimensioni. E l'overhead di avvio delle attività per le query utilizza una percentuale significativa dell'esecuzione complessiva della query.

Per abilitare la modalità locale, aggiungere il parametro `hive.exec.mode.local.auto` al pannello Custom hive-site (hive-site personalizzato), come illustrato nel passaggio 3 della sezione [Abilitare la compressione intermedia](#enable-intermediate-compression).

![Modalità di Apache Hive Exec auto locale](./media/optimize-hive-ambari/hive-exec-mode-local-auto.png)

## <a name="set-single-mapreduce-multigroup-by"></a>Impostare una singola query MultiGROUP BY per MapReduce

Quando questa proprietà è impostata su true, una query MultiGROUP BY con chiavi group-by comuni genera un singolo processo MapReduce.  

Per abilitare questo comportamento, aggiungere il parametro `hive.multigroupby.singlereducer` al riquadro Custom hive-site (hive-site personalizzato), come illustrato nel passaggio 3 della sezione [Abilitare la compressione intermedia](#enable-intermediate-compression).

![Query MultiGROUP BY singola per MapReduce impostata in Hive](./media/optimize-hive-ambari/hive-multigroupby-singlereducer.png)

## <a name="additional-hive-optimizations"></a>Ottimizzazioni di Hive aggiuntive

Le sezioni seguenti descrivono altre ottimizzazioni relative a Hive che è possibile impostare.

### <a name="join-optimizations"></a>Ottimizzazioni del join

Il tipo di join predefinito in Hive è un *join casuale*. In Hive speciali mapper leggono l'input e generano una coppia chiave/valore di join per un file intermedio. Hadoop ordina e unisce queste coppie in una fase casuale. Questa fase casuale è costosa. La scelta del join appropriato in base ai dati può migliorare considerevolmente le prestazioni.

| Tipo di join | Se | Come | Settings di Hive | Commenti |
| --- | --- | --- | --- | --- |
| Join casuale | <ul><li>Scelta predefinita</li><li>È sempre valido</li></ul> | <ul><li>Legge da parte di una delle tabelle</li><li>Raggruppa e ordina nella chiave di join</li><li>Invia un bucket a ogni elemento reduce</li><li>Il join viene eseguito sul lato Reduce</li></ul> | Non sono necessarie impostazioni di Hive significative | Funziona sempre |
| Map Join | <ul><li>La memoria disponibile è sufficiente per una tabella</li></ul> | <ul><li>Legge una tabella di piccole dimensioni nella tabella hash della memoria</li><li>Passa attraverso parte del file di grandi dimensioni</li><li>Crea un join con ogni record della tabella hash</li><li>I join vengono eseguiti tramite il solo mapper</li></ul> | `hive.auto.confvert.join=true` | Veloce, ma limitato |
| Sort Merge Bucket | Se entrambe le tabelle: <ul><li>Sono ordinate allo stesso modo</li><li>Sono raggruppate allo stesso modo</li><li>Creano un join con la colonna ordinata/raggruppata</li></ul> | Ogni processo: <ul><li>Legge un bucket da ogni tabella</li><li>Elabora la riga con il valore più basso</li></ul> | `hive.auto.convert.sortmerge.join=true` | Efficiente |

### <a name="execution-engine-optimizations"></a>Ottimizzazioni del motore di esecuzione

Raccomandazioni aggiuntive per ottimizzare il motore di esecuzione Hive:

| Impostazione | Implementazione consigliata | Impostazione predefinita di HDInsight |
| --- | --- | --- |
| `hive.mapjoin.hybridgrace.hashtable` | True = più sicuro, più lento; false = più veloce | false |
| `tez.am.resource.memory.mb` | limite superiore di 4 GB per la maggior parte | Ottimizzazione automatica |
| `tez.session.am.dag.submit.timeout.secs` | 300+ | 300 |
| `tez.am.container.idle.release-timeout-min.millis` | 20000+ | 10000 |
| `tez.am.container.idle.release-timeout-max.millis` | 40000+ | 20000 |

## <a name="next-steps"></a>Passaggi successivi

* [Gestire i cluster HDInsight con l'interfaccia utente Web Apache Ambari](hdinsight-hadoop-manage-ambari.md)
* [API REST Apache Ambari](hdinsight-hadoop-manage-ambari-rest-api.md)
* [Ottimizzare le query Apache Hive in Azure HDInsight](./hdinsight-hadoop-optimize-hive-query.md)
* [Ottimizzare i cluster](./optimize-hive-ambari.md)
* [Ottimizzare Apache HBase](./optimize-hbase-ambari.md)
* [Ottimizzare Apache Pig](./optimize-pig-ambari.md)