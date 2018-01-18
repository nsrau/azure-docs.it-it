---
title: Ottimizzare le configurazioni cluster con Ambari - Azure HDInsight | Microsoft Docs
description: Usare l'interfaccia utente Web Ambari per configurare e ottimizzare i cluster HDInsight.
documentationcenter: 
author: ashishthaps
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/09/2018
ms.author: ashish
ms.openlocfilehash: 5b3700580f593e7590360792f2b76dee79608896
ms.sourcegitcommit: e19f6a1709b0fe0f898386118fbef858d430e19d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/13/2018
---
# <a name="use-ambari-to-optimize-hdinsight-cluster-configurations"></a>Usare Ambari per ottimizzare le configurazioni cluster HDInsight

HDInsight fornisce i cluster Apache Hadoop per le applicazioni di elaborazione dei dati su larga scala. La gestione, il monitoraggio e l'ottimizzazione di questi complessi cluster a più nodi possono essere difficili. [Apache Ambari](http://ambari.apache.org/) è un'interfaccia Web per la gestione e il monitoraggio dei cluster Linux in HDInsight.  Per i cluster Windows, usare l'[API REST](hdinsight-hadoop-manage-ambari-rest-api.md) di Ambari.

Per un'introduzione all'uso dell'interfaccia utente Web Ambari, vedere [Gestire i cluster HDInsight usando l'interfaccia utente Web Ambari](hdinsight-hadoop-manage-ambari.md)

Accedere ad Ambari all'indirizzo `https://CLUSTERNAME.azurehdidnsight.net` con le credenziali del cluster. La schermata iniziale visualizza un dashboard generale.

![Dashboard di Ambari](./media/hdinsight-changing-configs-via-ambari/ambari-dashboard.png)

L'interfaccia utente Web Ambari può essere usata per gestire host, servizi, avvisi, configurazioni e visualizzazioni. Ambari non può essere usato per creare un cluster HDInsight, aggiornare i servizi, gestire stack e versioni, ritirare o ripristinare gli host o aggiungere servizi al cluster.

## <a name="manage-your-clusters-configuration"></a>Gestire la configurazione del cluster

Le impostazioni di configurazione consentono di ottimizzare un determinato servizio. Per modificare le impostazioni di configurazione di un servizio, selezionare il servizio dalla barra laterale **Services** (Servizi) a sinistra e quindi passare alla scheda **Configs** (Configurazioni) nella pagina dei dettagli del servizio.

![Barra laterale dei servizi](./media/hdinsight-changing-configs-via-ambari/services-sidebar.png)

### <a name="modify-namenode-java-heap-size"></a>Modificare le dimensioni dell'heap Java NameNode

Le dimensioni dell'heap Java NameNode dipendono da diversi fattori, ad esempio il carico nel cluster, il numero di file e il numero di blocchi. Le dimensioni predefinite, pari a 1 GB, vanno bene per la maggior parte dei cluster, anche se alcuni carichi di lavoro richiedono più o meno memoria. 

Per modificare le dimensioni dell'heap Java NameNode:

1. Selezionare **HDFS** dalla barra laterale Services (Servizi) e passare alla scheda **Configs** (Configurazioni).

    ![Configurazione HDFS](./media/hdinsight-changing-configs-via-ambari/hdfs-config.png)

2. Trovare l'impostazione **NameNode Java heap size** (Dimensioni dell'heap Java NameNode). È anche possibile usare la casella di testo **filter** (filtro) per digitare e trovare una determinata impostazione. Selezionare l'icona **pen** (penna) accanto al nome dell'impostazione.

    ![Dimensioni dell'heap Java NameNode](./media/hdinsight-changing-configs-via-ambari/java-heap-size.png)

3. Digitare il nuovo valore nella casella di testo, quindi premere **INVIO** per salvare la modifica.

    ![Modificare le dimensioni dell'heap Java NameNode](./media/hdinsight-changing-configs-via-ambari/java-heap-size-edit.png)

4. Le dimensioni dell'heap Java NameNode passano da 1 GB a 2 GB.

    ![Dimensioni dell'heap Java NameNode modificate](./media/hdinsight-changing-configs-via-ambari/java-heap-size-edited.png)

5. Salvare le modifiche facendo clic sul pulsante verde **Save** (Salva) nella parte superiore della schermata di configurazione.

    ![Salvare le modifiche](./media/hdinsight-changing-configs-via-ambari/save-changes.png)

## <a name="hive-optimization"></a>Ottimizzazione di Hive

Le sezioni seguenti descrivono le opzioni di configurazione per ottimizzare le prestazioni generali di Hive.

1. Per modificare i parametri di configurazione di Hive, selezionare **Hive** dalla barra laterale Services (Servizi).
2. Passare alla scheda **Configs** (Configurazioni).

### <a name="set-the-hive-execution-engine"></a>Impostare il motore di esecuzione di Hive

Hive fornisce due motori di esecuzione: MapReduce e Tez. Tez è più veloce di MapReduce. I cluster Linux in HDInsight usano Tez come motore di esecuzione predefinito. Per cambiare il motore di esecuzione:

1. Nella scheda **Configs** (Configurazioni) di Hive digitare **execution engine** nella casella di filtro.

    ![Cercare il motore di esecuzione](./media/hdinsight-changing-configs-via-ambari/search-execution.png)

2. Il valore predefinito della proprietà **Optimization** (Ottimizzazione) è **Tez**.

    ![Ottimizzazione - Tez](./media/hdinsight-changing-configs-via-ambari/optimization-tez.png)

### <a name="tune-mappers"></a>Ottimizzare i mapper

Hadoop prova a dividere (*eseguire il mapping*) di un singolo file in più file e a elaborare i file risultanti in parallelo. Il numero di mapper dipende dal numero di suddivisioni. I due parametri di configurazione seguenti determinano il numero di suddivisioni per il motore di esecuzione:

* `tez.grouping.min-size`: limite minimo delle dimensioni di una suddivisione raggruppata, con un valore predefinito di 16 MB (16.777.216 byte).
* `tez.grouping.max-size`: limite massimo delle dimensioni di una suddivisione raggruppata, con un valore predefinito di 1 GB (1.073.741.824 byte).

Come regola per le prestazioni generale, diminuire entrambi i parametri per migliorare la latenza, aumentarli per una maggiore velocità effettiva.

Ad esempio, per impostare quattro attività di mapper per dimensioni dei dati pari a 128 MB, impostare entrambi i parametri su 32 MB ognuno (33.554.432 byte).

1. Per modificare i parametri limite, passare alla scheda **Configs** (Configurazioni) del servizio Tez. Espandere il pannello **General** (Generale) e individuare i parametri `tez.grouping.max-size` e `tez.grouping.min-size`.

2. Impostare entrambi i parametri su **33.554.432** byte (32 MB).

    ![Dimensioni dei raggruppamenti Tez](./media/hdinsight-changing-configs-via-ambari/tez-grouping-size.png)
 
Queste modifiche interessano tutti i processi Tez nel server. Per ottenere un risultato ottimale, scegliere valori appropriati per i parametri.

### <a name="tune-reducers"></a>Ottimizzare i riduttori

ORC e Snappy offrono entrambi prestazioni elevate. Hive potrebbe tuttavia avere un numero troppo basso di riduttori per impostazione predefinita e causare quindi colli di bottiglia.

Si supponga, ad esempio, di avere dati di input di dimensioni pari a 50 GB. Tali dati nel formato ORC con la compressione Snappy sono pari a 1 GB. Hive stima il numero di riduttori necessari nel modo seguente: (numero di byte di input per i mapper / `hive.exec.reducers.bytes.per.reducer`).

Con le impostazioni predefinite, in questo esempio i riduttori sono 4.

Il parametro `hive.exec.reducers.bytes.per.reducer` specifica il numero di byte elaborati per riduttore. Il valore predefinito è 64 MB. Se si diminuisce questo valore, il parallelismo aumenta e le prestazioni possono migliorare. Se lo si diminuisce troppo, potrebbero anche essere generati troppi riduttori, che possono influire negativamente sulle prestazioni. Questo parametro è basato sugli specifici requisiti per i dati, sulle impostazioni di compressione e su altri fattori ambientali.

1. Per modificare il parametro, passare alla scheda **Configs** (Configurazioni) di Hive e trovare il parametro **Data per Reducer** (Dati per riduttore) nella pagina Settings (Impostazioni).

    ![Data per Reducer (Dati per riduttore)](./media/hdinsight-changing-configs-via-ambari/data-per-reducer.png)
 
2. Selezionare **Edit** (Modifica) per impostare il valore su 128 MB (134.217.728 byte) e quindi premere **INVIO** per salvare.

    ![Data per Reducer (Dati per riduttore) - modificato](./media/hdinsight-changing-configs-via-ambari/data-per-reducer-edited.png)
  
    Con dimensioni di input pari a 1.024 MB e 128 MB di dati per riduttore, i riduttori sono 8 (1024/128).

3. Un valore non corretto per il parametro **Data per Reducer** (Dati per riduttore) può restituire un numero elevato di riduttori, che influisce negativamente sulle prestazioni della query. Per limitare il numero massimo di riduttori, impostare `hive.exec.reducers.max` su un valore appropriato. Il valore predefinito è 1009.

### <a name="enable-parallel-execution"></a>Abilitare l'esecuzione parallela

Una query Hive viene eseguita in una o più fasi. Se le fasi indipendenti possono essere eseguite in parallelo, le prestazioni della query miglioreranno.

1.  Per abilitare l'esecuzione della query parallela, passare alla scheda **Config** (Configurazioni) di Hive e cercare la proprietà `hive.exec.parallel`. Il valore predefinito è False. Impostare il valore su true e quindi premere **INVIO** per salvare il valore.
 
2.  Per limitare il numero di processi da eseguire in parallelo, modificare la proprietà `hive.exec.parallel.thread.number`. Il valore predefinito è 8.

    ![Esecuzione parallela di Hive](./media/hdinsight-changing-configs-via-ambari/hive-exec-parallel.png)


### <a name="enable-vectorization"></a>Abilitare la vettorializzazione

Hive elabora i dati una riga alla volta. Con la vettorializzazione Hive elabora i dati in blocchi di 1.024 righe invece che una riga alla volta. La vettorializzazione è applicabile solo al formato di file ORC.

1. Per abilitare un'esecuzione di query vettorializzata, passare alla scheda **Configs** (Configurazioni) di Hive e cercare il parametro `hive.vectorized.execution.enabled`. Il valore predefinito è true per Hive 0.13.0 o versione successiva.
 
2. Per abilitare l'esecuzione vettorializzata per il lato reduce della query, impostare il parametro `hive.vectorized.execution.reduce.enabled` su true. Il valore predefinito è False.

    ![Esecuzione vettorializzata di Hive](./media/hdinsight-changing-configs-via-ambari/hive-vectorized-execution.png)

### <a name="enable-cost-based-optimization-cbo"></a>Abilitare l'ottimizzazione basata sui costi

Per impostazione predefinita, Hive segue un set di regole per trovare un piano di esecuzione della query ottimale. L'ottimizzazione basata sui costi valuta più piani per eseguire una query e assegna un costo a ogni piano, quindi determina quello più conveniente per eseguire una query.

Per abilitare l'ottimizzazione basata sui costi, passare alla scheda **Configs** (Configurazioni) di Hive e cercare `parameter hive.cbo.enable`, quindi impostare l'interruttore su **On** (Sì).

![Configurazione dell'ottimizzazione basata sui costi](./media/hdinsight-changing-configs-via-ambari/cbo.png)

I parametri di configurazione aggiuntivi seguenti migliorano le prestazioni della query Hive quando l'ottimizzazione basata sui costi è abilitata:

* `hive.compute.query.using.stats`

    Se impostato su true, Hive usa le statistiche archiviate nel metastore per rispondere a query semplici, ad esempio `count(*)`.

    ![Statistiche di ottimizzazione basata sui costi](./media/hdinsight-changing-configs-via-ambari/hive-compute-query-using-stats.png)

* `hive.stats.fetch.column.stats`

    Quando l'ottimizzazione basata sui costi è abilitata, vengono create statistiche di colonna. Hive usa le statistiche di colonna, archiviate nel metastore, per ottimizzare le query. Recuperare le statistiche per ogni colonna richiede più tempo quando il numero di colonne è elevato. Se impostata su false, questa impostazione disabilita il recupero delle statistiche di colonna dal metastore.

    ![Statistiche di colonna impostate nelle statistiche di Hive](./media/hdinsight-changing-configs-via-ambari/hive-stats-fetch-column-stats.png)

* `hive.stats.fetch.partition.stats`

    Le statistiche della partizione di base, ad esempio numero di righe, dimensioni dei dati e dimensioni dei file, vengono archiviate nel metastore. Se impostato su true, le statistiche della partizione vengono recuperate dal metastore. Se false, le dimensioni dei file vengono recuperate dal file system e il numero di righe viene recuperato dallo schema delle righe.

    ![Statistiche della partizione impostate nelle statistiche di Hive](./media/hdinsight-changing-configs-via-ambari/hive-stats-fetch-partition-stats.png)

### <a name="enable-intermediate-compression"></a>Abilitare la compressione intermedia

Le attività mappe creano file intermedi che vengono usati dalle attività riduttori. La compressione intermedia riduce le dimensioni dei file intermedi.

I processi Hadoop presentano in genere colli di bottiglia a causa dell'I/O. La compressione dei dati può velocizzare l'I/O e il trasferimento di rete complessivo.

I tipi di compressione disponibili sono:

| Format | Strumento | Algoritmo | Estensione file | Divisibile |
| -- | -- | -- | -- | -- |
| Gzip | Gzip | DEFLATE | gz | No  |
| Bzip2 | Bzip2 | Bzip2 |bz2 | Sì |
| LZO | Lzop | LZO | lzo | Sì, se indicizzato |
| Snappy | N/D | Snappy | Snappy | No  |

Come regola generale, è importante che il metodo di compressione sia divisibile. In caso contrario, verranno creati pochissimi mapper. Se i dati di input sono costituiti da testo, `bzip2` è l'opzione migliore. Per il formato ORC, Snappy è l'opzione di compressione più rapida.

1. Per abilitare la compressione intermedia, passare alla scheda **Configs** (Configurazioni) di Hive e quindi impostare il parametro `hive.exec.compress.intermediate` su true. Il valore predefinito è False.

    ![Compressione intermedia di esecuzione di Hive](./media/hdinsight-changing-configs-via-ambari/hive-exec-compress-intermediate.png)

    > [!NOTE]
    > Per comprimere i file intermedi, scegliere un codec di compressione con costi di CPU più bassi, anche se il codec non ha un output di compressione elevato.

2. Per impostare il codec di compressione intermedia, aggiungere la proprietà personalizzata `mapred.map.output.compression.codec` al file `hive-site.xml` o `mapred-site.xml`.

3. Per aggiungere un'impostazione personalizzata:

    a. Passare alla scheda **Configs** (Configurazioni) di Hive e selezionare la scheda **Advanced** (Avanzate).

    b. Nella scheda **Advanced** (Avanzate) trovare ed espandere il riquadro **Custom hive-site** (hive-site personalizzato).

    c. Fare clic sul collegamento **Add Property** (Aggiungi proprietà) nella parte inferiore del riquadro Custom hive-site (hive-site personalizzato).

    d. Nella finestra Add Property (Aggiungi proprietà) immettere `mapred.map.output.compression.codec` come chiave e `org.apache.hadoop.io.compress.SnappyCodec` come valore.

    e. Fare clic su **Aggiungi**.

    ![Proprietà personalizzata di Hive](./media/hdinsight-changing-configs-via-ambari/hive-custom-property.png)

    Il file intermedio verrà compresso usando la compressione di Snappy. Dopo che la proprietà è stata aggiunta, viene visualizzata nel riquadro Custom hive-site (hive-site personalizzato).

    > [!NOTE]
    > Questa procedura modifica il file `$HADOOP_HOME/conf/hive-site.xml`.

### <a name="compress-final-output"></a>Comprimere l'output finale

Anche l'output di Hive finale può essere compresso.

1. Per comprimere l'output di Hive finale, passare alla scheda **Configs** (Configurazioni) di Hive e quindi impostare il parametro `hive.exec.compress.output` su true. Il valore predefinito è False.

2. Per scegliere il codec di compressione dell'output, aggiungere la proprietà personalizzata `mapred.output.compression.codec` al riquadro Custom hive-site (hive-site personalizzato), come descritto nel passaggio 3 della sezione precedente.

    ![Proprietà personalizzata di Hive](./media/hdinsight-changing-configs-via-ambari/hive-custom-property2.png)

### <a name="enable-speculative-execution"></a>Abilitare l'esecuzione speculativa

L'esecuzione speculativa avvia un determinato numero di attività duplicate per rilevare e inserire nell'elenco di elementi non consentiti l'utilità di rilevamento di attività a esecuzione lenta e contemporaneamente migliora l'esecuzione complessiva dei processi ottimizzando i risultati delle singole attività.

È consigliabile non attivare l'esecuzione speculativa per le attività MapReduce a esecuzione prolungata con grandi quantità di input.

1. Per abilitare l'esecuzione speculativa, passare alla scheda **Configs** (Configurazioni) di Hive e quindi impostare il parametro `hive.mapred.reduce.tasks.speculative.execution` su true. Il valore predefinito è False.

    ![Esecuzione speculativa di attività mapred reduce di Apache Hive](./media/hdinsight-changing-configs-via-ambari/hive-mapred-reduce-tasks-speculative-execution.png)

### <a name="tune-dynamic-partitions"></a>Ottimizzare le partizioni dinamiche

Hive consente di creare partizioni dinamiche quando si inseriscono i record in una tabella, senza definire in anticipo ogni singola partizione. Si tratta di una funzionalità avanzata, anche se può portare alla creazione di un numero elevato di partizioni e di file per ogni partizione.

1. Per creare partizioni dinamiche in Hive, il parametro `hive.exec.dynamic.partition` deve essere impostato su (impostazione predefinita).

2. Impostare la modalità di partizione dinamica su *strict*. Nella modalità strict almeno una partizione deve essere statica per impedire query senza il filtro per la partizione nella clausola WHERE. *strict* impedisce infatti le query che analizzano tutte le partizioni. Passare alla scheda **Configs** (Configurazioni) di Hive e quindi impostare `hive.exec.dynamic.partition.mode` su **strict**. Il valore predefinito è **nonstrict**.
 
3. Per limitare il numero di partizioni dinamiche da creare, modificare il parametro ``hive.exec.max.dynamic.partitions`. Il valore predefinito è 5.000.
 
4. Per limitare il numero totale di partizioni dinamiche per nodo, modificare `hive.exec.max.dynamic.partitions.pernode`. Il valore predefinito è 2.000.

### <a name="enable-local-mode"></a>Abilitare la modalità locale

La modalità locale consente a Hive di eseguire tutte le attività di un processo in un singolo computer o, a volte, in un singolo procedimento. Questo migliora le prestazioni delle query se i dati di input sono pochi e l'overhead derivante dall'avvio delle attività per le query utilizza una percentuale significativa dell'esecuzione complessiva delle query.

Per abilitare la modalità locale, aggiungere il parametro `hive.exec.mode.local.auto` al pannello Custom hive-site (hive-site personalizzato), come illustrato nel passaggio 3 della sezione [Abilitare la compressione intermedia](#enable-intermediate-compression).

![Modalità di esecuzione di Hive automatica locale](./media/hdinsight-changing-configs-via-ambari/hive-exec-mode-local-auto.png)

### <a name="set-single-mapreduce-multigroup-by"></a>Impostare una singola query MultiGROUP BY per MapReduce

Quando questa proprietà è impostata su true, una query MultiGROUP BY con chiavi group-by comuni genera un singolo processo MapReduce.  

Per abilitare questo comportamento, aggiungere il parametro `hive.multigroupby.singlereducer` al riquadro Custom hive-site (hive-site personalizzato), come illustrato nel passaggio 3 della sezione [Abilitare la compressione intermedia](#enable-intermediate-compression).

![Query MultiGROUP BY singola per MapReduce impostata in Hive](./media/hdinsight-changing-configs-via-ambari/hive-multigroupby-singlereducer.png)

### <a name="additional-hive-optimizations"></a>Ottimizzazioni di Hive aggiuntive

Le sezioni seguenti descrivono altre ottimizzazioni relative a Hive che è possibile impostare.

#### <a name="join-optimizations"></a>Ottimizzazioni del join

Il tipo di join predefinito in Hive è un *join casuale*. In Hive speciali mapper leggono l'input e generano una coppia chiave/valore di join per un file intermedio. Hadoop ordina e unisce queste coppie in una fase casuale. Questa fase casuale è costosa. La scelta del join appropriato in base ai dati può migliorare considerevolmente le prestazioni.

| Tipo di join | Se | Come | Settings di Hive | Commenti |
| -- | -- | -- | -- | -- |
| Join casuale | <ul><li>Scelta predefinita</li><li>È sempre valido</li></ul> | <ul><li>Legge da parte di una delle tabelle</li><li>Raggruppa e ordina nella chiave di join</li><li>Invia un bucket a ogni elemento reduce</li><li>Il join viene eseguito sul lato Reduce</li></ul> | Non sono necessarie impostazioni di Hive significative | Funziona sempre |
| Map Join | <ul><li>La memoria disponibile è sufficiente per una tabella</li></ul> | <ul><li>Legge una tabella di piccole dimensioni nella tabella hash della memoria</li><li>Passa attraverso parte del file di grandi dimensioni</li><li>Crea un join con ogni record della tabella hash</li><li>I join vengono eseguiti tramite il solo mapper</li></ul> | `hive.auto.confvert.join=true` | Molto veloce, ma limitato |
| Sort Merge Bucket | Se entrambe le tabelle: <ul><li>Sono ordinate allo stesso modo</li><li>Sono raggruppate allo stesso modo</li><li>Creano un join con la colonna ordinata/raggruppata</li></ul> | Ogni processo: <ul><li>Legge un bucket da ogni tabella</li><li>Elabora la riga con il valore più basso</li></ul> | `hive.auto.convert.sortmerge.join=true` | Molto efficiente |

#### <a name="execution-engine-optimizations"></a>Ottimizzazioni del motore di esecuzione

Raccomandazioni aggiuntive per ottimizzare il motore di esecuzione Hive:

| Impostazione | Consigliato | Impostazione predefinita di HDInsight |
| -- | -- | -- |
| `hive.mapjoin.hybridgrace.hashtable` | True = più sicuro, più lento; false = più veloce | false |
| `tez.am.resource.memory.mb` | Limite superiore di 4 GB nella maggior parte dei casi | Ottimizzazione automatica |
| `tez.session.am.dag.submit.timeout.secs` | 300+ | 300 |
| `tez.am.container.idle.release-timeout-min.millis` | 20000+ | 10000 |
| `tez.am.container.idle.release-timeout-max.millis` | 40000+ | 20000 |

## <a name="pig-optimization"></a>Ottimizzazione di Pig

Le proprietà di Pig possono essere modificate dall'interfaccia utente Web Ambari per ottimizzare le query di Pig. Se le proprietà di Pig vengono modificate da Ambari, vengono direttamente modificate anche nel file `/etc/pig/2.4.2.0-258.0/pig.properties`.

1. Per modificare le proprietà di Pig, passare alla scheda **Configs** (Configurazioni) di Pig e quindi espandere il riquadro **Advanced pig-properties** (Proprietà Pig avanzate).

2. Trovare, rimuovere il commento e cambiare il valore della proprietà che si vuole modificare.

3. Selezionare **Save** (Salva) nell'angolo in alto a destra della finestra per salvare il nuovo valore. Alcune proprietà possono richiedere un riavvio del servizio.

    ![Proprietà Pig avanzate](./media/hdinsight-changing-configs-via-ambari/advanced-pig-properties.png)
 
> [!NOTE]
> Le impostazioni a livello di sessione eseguono l'override dei valori delle proprietà nel file `pig.properties`.

### <a name="tune-execution-engine"></a>Ottimizzare il motore di esecuzione

Per eseguire gli script di Pig, sono disponibili due motori di esecuzione: MapReduce e Tez. Tez è un motore ottimizzato ed è molto più veloce di MapReduce.

1. Per modificare il motore di esecuzione, nel riquadro **Advanced pig-properties** (Proprietà Pig avanzate) trovare la proprietà `exectype`.

2. Il valore predefinito è **MapReduce**. Impostarlo su **Tez**.


### <a name="enable-local-mode"></a>Abilitare la modalità locale

Come in Hive, la modalità locale viene usata per velocizzare i processi con quantità relativamente piccole di dati.

1. Per abilitare la modalità locale, impostare `pig.auto.local.enabled` su **true**. Il valore predefinito è False.

2. I processi con dati di input di dimensioni inferiori al valore della proprietà `pig.auto.local.input.maxbytes` sono considerati processi di piccole dimensioni. Il valore predefinito è 1 GB.


### <a name="copy-user-jar-cache"></a>Copiare la cache JAR dell'utente

Pig copia i file JAR necessari per le funzioni definite dall'utente in una cache distribuita per renderli disponibili per i nodi attività. Questi file JAR non vengono modificati spesso. Se abilitata, l'impostazione `pig.user.cache.enabled` consente di inserire i file JAR in una cache per riutilizzarli per i processi eseguiti dallo stesso utente. Ne consegue un minor aumento delle prestazioni dei processi.

1. Per abilitarla, impostare `pig.user.cache.enabled` su true. Il valore predefinito è false.

2. Per impostare il percorso di base dei file JAR memorizzati nella cache, impostare `pig.user.cache.location` sul percorso di base. Il valore predefinito è `/tmp`.


### <a name="optimize-performance-with-memory-settings"></a>Ottimizzare le prestazioni con le impostazioni della memoria

Le impostazioni della memoria seguenti consentono di ottimizzare le prestazioni di uno script di Pig.

* `pig.cachedbag.memusage`: quantità di memoria allocata a un contenitore. Un contenitore è una raccolta di tuple. Una tupla è un set ordinato di campi e un campo è costituito da dati. Se i dati in un contenitore superano la memoria allocata, vengono distribuiti su disco. Il valore predefinito è 0,2, che rappresenta il 20% della memoria disponibile. Questa memoria viene condivisa tra tutti i contenitori di un'applicazione.

* `pig.spill.size.threshold`: i contenitori con dimensioni superiori a questa soglia di distribuzione (in byte) vengono distribuiti su disco. Il valore predefinito è 5 MB.


### <a name="compress-temporary-files"></a>Comprimere i file temporanei

Pig genera file temporanei durante l'esecuzione dei processi. La compressione dei file temporanei produce un aumento delle prestazioni durante la lettura o la scrittura di file su disco. Per comprimere i file temporanei, si possono usare le impostazioni seguenti.

* `pig.tmpfilecompression`: se true, abilita la compressione dei file temporanei. Il valore predefinito è False.

* `pig.tmpfilecompression.codec`: codec di compressione da usare per comprimere i file temporanei. I codec di compressione consigliati sono LZO e Snappy per un utilizzo della CPU più basso.

### <a name="enable-split-combining"></a>Abilitare la combinazione per la suddivisione

Se abilitata, i file di piccole dimensioni vengono combinati per ottenere un numero inferiore di attività mappe, migliorando così l'efficienza dei processi con molti file di piccole dimensioni. Per abilitarla, impostare `pig.noSplitCombination` su true. Il valore predefinito è False.


### <a name="tune-mappers"></a>Ottimizzare i mapper

Il numero di mapper viene controllato modificando la proprietà `pig.maxCombinedSplitSize`, che specifica le dimensioni dei dati che devono essere elaborati da una singola attività mappa. Il valore predefinito corrisponde alle dimensioni di blocco predefinite del file system. Aumentando questo valore, diminuisce il numero di attività mapper.


### <a name="tune-reducers"></a>Ottimizzare i riduttori

Il numero di riduttori viene calcolato in base al parametro `pig.exec.reducers.bytes.per.reducer`. Il parametro specifica il numero di byte elaborati per riduttore. L'impostazione predefinita è 1 GB. Per limitare il numero massimo di riduttori, impostare la proprietà `pig.exec.reducers.max`. L'impostazione predefinita è 999.


## <a name="hbase-optimization-with-the-ambari-web-ui"></a>Ottimizzazione di HBase con l'Interfaccia utente Web Ambari

La configurazione di HBase viene modificata dalla scheda **HBase Configs** (Configurazioni HBase). Le sezioni seguenti descrivono alcune importanti impostazioni di configurazione che influiscono sulle prestazioni di HBase.

### <a name="set-hbaseheapsize"></a>Impostare HBASE_HEAPSIZE

Le dimensioni dell'heap HBase specificano, in megabyte, la quantità massima di heap che devono essere usati dai server di *area* e *master*. Il valore predefinito è 1.000 MB. Questo valore deve essere ottimizzato per il carico di lavoro del cluster.

1. Per modificarlo, passare al riquadro **Advanced HBase-env** (Ambiente HBase avanzato) nella scheda **Configs** (Configurazioni) e quindi trovare l'impostazione `HBASE_HEAPSIZE`.

2. Impostare il valore predefinito su 5.000 MB.

    ![HBASE_HEAPSIZE](./media/hdinsight-changing-configs-via-ambari/hbase-heapsize.png)


### <a name="optimize-read-heavy-workloads"></a>Ottimizzare i carichi di lavoro con numero elevato di letture

Le configurazioni seguenti sono importanti per migliorare le prestazioni dei carichi di lavoro con numero elevato di letture.

#### <a name="block-cache-size"></a>Dimensioni della cache dei blocchi

La cache dei blocchi è la cache di lettura, le cui dimensioni sono controllate dal parametro `hfile.block.cache.size`. Il valore predefinito è 0,4, che corrisponde al 40% della memoria totale del server di area. La velocità delle letture casuali sarà direttamente proporzionale alle dimensioni della cache di blocchi.

1. Per modificare questo parametro, passare alla scheda **Settings** (Impostazioni) nella scheda **Configs** (Configurazioni) di HBase e quindi individuare **% of RegionServer Allocated to Read Buffers** (% di server di area allocata ai buffer di lettura).

    ![Dimensioni della cache dei blocchi di HBase](./media/hdinsight-changing-configs-via-ambari/hbase-block-cache-size.png)
 
2. Per modificare il valore, selezionare l'icona **Edit** (Modifica).


#### <a name="memstore-size"></a>Dimensioni del memstore

Tutte le modifiche vengono archiviate nel buffer di memoria, denominato *memstore*. In questo modo aumenta la quantità totale di dati che possono essere scritti su disco con una singola operazione e viene velocizzato l'accesso successivo alle modifiche recenti. Le dimensioni del memstore sono definite dai due parametri seguenti:

* `hbase.regionserver.global.memstore.UpperLimit`: definisce la percentuale massima del server di area che può essere usata dal memstore combinato.

* `hbase.regionserver.global.memstore.LowerLimit`: definisce la percentuale minima del server di area che può essere usata dal memstore combinato.

Per ottimizzare le letture casuali, è possibile ridurre i limiti massimo e minimo del memstore.


#### <a name="number-of-rows-fetched-when-scanning-from-disk"></a>Numero di righe recuperate quando si esegue l'analisi dal disco

L'impostazione `hbase.client.scanner.caching` definisce il numero di righe lette dal disco quando il metodo `next` viene chiamato su uno scanner.  Il valore predefinito è 100. Maggiore è il numero, meno saranno le chiamate remote eseguite dal client al server di area e le analisi saranno quindi più rapide. In questo modo tuttavia anche l'utilizzo di memoria nel client sarà più elevato.

![Numero di righe HBase recuperate](./media/hdinsight-changing-configs-via-ambari/hbase-num-rows-fetched.png)

> [!IMPORTANT]
> Non impostare il valore in modo tale che l'intervallo di tempo tra le chiamate al metodo next su uno scanner sia superiore al timeout dello scanner. La durata del timeout dello scanner è definita dalla proprietà `hbase.regionserver.lease.period`.


### <a name="optimize-write-heavy-workloads"></a>Ottimizzare i carichi di lavoro con numero elevato di scritture

Le configurazioni seguenti sono importanti per migliorare le prestazioni dei carichi di lavoro con numero elevato di scritture.


#### <a name="maximum-region-file-size"></a>Dimensioni massime del file di area

HBase archivia i dati in un formato di file interno, denominato *HFile*. La proprietà `hbase.hregion.max.filesize` definisce le dimensioni di un singolo HFile per un'area.  Un'area viene divisa in due aree se la somma di tutti gli HFile di un'area è superiore al valore di questa impostazione.
 
![Dimensioni massime del file di area di HBase](./media/hdinsight-changing-configs-via-ambari/hbase-hregion-max-filesize.png)

Maggiori sono le dimensioni del file di area, minore è il numero di suddivisioni. È possibile aumentare le dimensioni del file per determinare un valore che garantisca prestazioni in scrittura ottimali.


#### <a name="avoid-update-blocking"></a>Evitare il blocco degli aggiornamenti

* La proprietà `hbase.hregion.memstore.flush.size` definisce a quali dimensioni il memstore viene scaricato su disco. Le dimensioni predefinite sono pari a 128 MB.

* Il moltiplicatore di blocco di area di Hbase è definito da `hbase.hregion.memstore.block.multiplier`. Il valore predefinito è 4. Il valore massimo consentito è 8.

* HBase blocca gli aggiornamenti se il memstore è pari a (`hbase.hregion.memstore.flush.size` * `hbase.hregion.memstore.block.multiplier`) byte.

    Con i valori predefiniti delle dimensioni di flush e del moltiplicatore di blocco, gli aggiornamenti vengono bloccati quando le dimensioni del memstore sono pari a 128 * 4 = 512 MB. Per ridurre il conteggio dei blocchi degli aggiornamenti, aumentare il valore di `hbase.hregion.memstore.block.multiplier`.

![Moltiplicatore di blocco di area di HBase](./media/hdinsight-changing-configs-via-ambari/hbase-hregion-memstore-block-multiplier.png)


### <a name="define-memstore-size"></a>Definire le dimensioni del memstore

Le dimensioni del memstore sono definite dai parametri `hbase.regionserver.global.memstore.UpperLimit` e `hbase.regionserver.global.memstore.LowerLimit`. Se si impostano questi parametri sullo stesso valore, si riducono le pause tra un'operazione di scrittura e l'altra (aumentando anche la frequenza di flush) e migliorano le prestazioni in scrittura.


### <a name="set-memstore-local-allocation-buffer"></a>Impostare il buffer di allocazione locale del memstore

L'utilizzo del buffer di allocazione locale del memstore è determinato dalla proprietà `hbase.hregion.memstore.mslab.enabled`. Se abilitata (true), impedisce la frammentazione dell'heap durante un'operazione di scrittura intensa. Il valore predefinito è true.
 
![hbase.hregion.memstore.mslab.enabled](./media/hdinsight-changing-configs-via-ambari/hbase-hregion-memstore-mslab-enabled.png)


## <a name="see-also"></a>Vedere anche 

* [Gestire i cluster HDInsight con l'interfaccia utente Web Ambari](hdinsight-hadoop-manage-ambari.md)
* [API REST Ambari](hdinsight-hadoop-manage-ambari-rest-api.md)
