---
title: Configurare le impostazioni di Spark - Azure HDInsight
description: Come visualizzare e configurare le impostazioni di Apache Spark per un cluster Azure HDInsightHow to view and configure Apache Spark settings for an Azure HDInsight cluster
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/15/2020
ms.openlocfilehash: e13390067f8767e8c07b9c189264444e6d999a7a
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81411308"
---
# <a name="configure-apache-spark-settings"></a>Configurare le impostazioni di Apache Spark

Un cluster HDInsight Spark include un'installazione della libreria [Apache Spark](https://spark.apache.org/).  Ogni cluster HDInsight include parametri di configurazione predefiniti per tutti i servizi installati, incluso Spark.  Un aspetto chiave della gestione di un cluster Apache Hadoop HDInsight è il monitoraggio del carico di lavoro, inclusi i processi Spark.A key aspect of managing an HDInsight Apache Hadoop cluster is monitoring workload, including Spark Jobs. Per eseguire al meglio i processi Spark, considerare la configurazione del cluster fisico quando si determina la configurazione logica del cluster.

Il cluster HDInsight Apache Spark predefinito include i nodi seguenti: tre nodi [Apache ZooKeeper](https://zookeeper.apache.org/), due nodi head e uno o più nodi del ruolo di lavoro:

![Architettura di Spark HDInsight](./media/apache-spark-settings/spark-hdinsight-arch.png)

Il numero di macchine virtuali e le dimensioni delle macchine virtuali per i nodi nel cluster HDInsight possono influire sulla configurazione di Spark.The number of VMs, and VM sizes, for the nodes in your HDInsight cluster can affect your Spark configuration. I valori di configurazione di HDInsight non predefiniti richiedono spesso valori di configurazione di Spark non predefiniti. Quando si crea un cluster HDInsight Spark, vengono visualizzate le dimensioni consigliate per ogni componente. Attualmente le [dimensioni delle macchine virtuali Linux ottimizzate per la memoria](../../virtual-machines/linux/sizes-memory.md) per Azure sono D12 v2 o maggiori.

## <a name="apache-spark-versions"></a>Versioni di Apache Spark

Usare la versione di Spark più appropriata per il cluster.  Il servizio HDInsight include diverse versioni di Spark e di HDInsight.  Ogni versione di Spark include un set di impostazioni del cluster predefinite.  

Quando si crea un nuovo cluster, è possibile scegliere tra più versioni di Spark. Per visualizzare l'elenco completo, [componenti e versioni di HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning).

> [!NOTE]  
> La versione predefinita di Apache Spark nel servizio HDInsight può cambiare senza preavviso. In caso di dipendenza dalla versione, è consigliabile indicare la versione specifica quando si creano i cluster tramite .NET SDK, Azure PowerShell e l'interfaccia della riga di comando classica di Azure.

Apache Spark ha tre posizioni per le configurazioni di sistema:

* Le proprietà di Spark controllano la maggior parte dei parametri dell'applicazione e possono essere impostate usando un oggetto `SparkConf` o tramite le proprietà di sistema Java.
* Le variabili di ambiente possono essere usate per definire le impostazioni per computer, ad esempio l'indirizzo IP, tramite lo script `conf/spark-env.sh` in ogni nodo.
* La registrazione può essere configurata tramite `log4j.properties`.

Quando si seleziona una particolare versione di Spark, il cluster include le impostazioni di configurazione predefinite.  È possibile modificare i valori di configurazione predefiniti di Spark usando un file di configurazione di Spark personalizzato.  Di seguito è illustrato un esempio.

```
spark.hadoop.io.compression.codecs org.apache.hadoop.io.compress.GzipCodec
spark.hadoop.mapreduce.input.fileinputformat.split.minsize 1099511627776
spark.hadoop.parquet.block.size 1099511627776
spark.sql.files.maxPartitionBytes 1099511627776
spark.sql.files.openCostInBytes 1099511627776
```

L'esempio precedente esegue l'override di alcuni valori predefiniti per cinque parametri di configurazione di Spark.  Questi valori sono il codec di compressione, Apache Hadoop MapReduce dividere le dimensioni minime e le dimensioni del blocco di parquet. Inoltre, la partizione Spar SQL e i file aperti ridimensionano i valori predefiniti.  Queste modifiche alla configurazione vengono scelte perché i dati e i processi associati (in questo esempio, i dati genomici) hanno caratteristiche particolari. Queste caratteristiche faranno meglio utilizzando queste impostazioni di configurazione personalizzate.

---

## <a name="view-cluster-configuration-settings"></a>Visualizzare le impostazioni di configurazione del cluster

Verificare le impostazioni di configurazione del cluster HDInsight correnti prima di eseguire l'ottimizzazione delle prestazioni nel cluster. Avviare il dashboard di HDInsight dal portale di Azure facendo clic sul collegamento **Dashboard** nel riquadro del cluster Spark. Accedere con il nome utente e la password dell'amministratore del cluster.

Viene visualizzata l'interfaccia utente Web Apache Ambari, con un dashboard delle metriche chiave di utilizzo delle risorse del cluster.  Il dashboard di Ambari mostra la configurazione di Apache Spark e altri servizi installati. Il dashboard include una scheda **Cronologia di configurazione,** in cui è possibile visualizzare le informazioni per i servizi installati, tra cui Spark.

Per visualizzare i valori di configurazione per Apache Spark, selezionare **Config History** (Cronologia configurazione) e quindi selezionare **Spark2**.  Selezionare la scheda **Configs** (Configurazioni) e quindi fare clic sul collegamento `Spark` (o `Spark2`, a seconda della versione) nell'elenco di servizi.  Verrà visualizzato un elenco di valori di configurazione per il cluster:

![Configurazioni di Spark](./media/apache-spark-settings/spark-configurations.png)

Per visualizzare e modificare i singoli valori di configurazione di Spark, selezionare un collegamento con "spark" nel titolo.  Le configurazioni per Spark includono sia i valori di configurazione personalizzati che quelli avanzati nelle categorie seguenti:

* Custom Spark2-defaults (Impostazioni predefinite Spark2 personalizzate)
* Custom Spark2-metrics-properties (Proprietà metriche Spark2 personalizzate)
* Advanced Spark2-defaults (Impostazioni predefinite Spark2 avanzate)
* Advanced Spark2-env (Impostazioni ambiente Spark2 avanzate)
* Advanced spark2-hive-site-override (Impostazioni override sito hive Spark2 avanzate)

Se si crea un set non predefinito di valori di configurazione, la cronologia degli aggiornamenti è visibile.  Questa cronologia di configurazione può essere utile per capire quale configurazione non predefinita offre prestazioni ottimali.

> [!NOTE]  
> Per visualizzare, ma senza modificare, le impostazioni di configurazione del cluster Spark comuni, selezionare la scheda **Environment** (Ambiente) al livello superiore dell'**interfaccia utente del processo Spark**.

## <a name="configuring-spark-executors"></a>Configurazione degli executor Spark

Il diagramma seguente mostra gli oggetti Spark principali: il programma driver e il contesto Spark associato, oltre che lo strumento di gestione di cluster e i relativi *n* nodi del ruolo di lavoro.  Ogni nodo del ruolo di lavoro include un executor, una cache e *n* istanze di attività.

![Oggetti del cluster](./media/apache-spark-settings/hdi-spark-architecture.png)

I processi Spark usano risorse del ruolo di lavoro, in particolare la memoria, quindi è prassi comune modificare i valori di configurazione di Spark per gli executor del nodo del ruolo di lavoro.

I tre parametri principali che vengono spesso modificati per ottimizzare le configurazioni di Spark per migliorare i requisiti dell'applicazione sono `spark.executor.instances`, `spark.executor.cores` e `spark.executor.memory`. Un Executor è un processo avviato per un'applicazione Spark. Viene eseguito nel nodo del ruolo di lavoro ed è responsabile delle attività per l'applicazione. Il numero di nodi di lavoro e le dimensioni del nodo di lavoro determinano il numero di esecutori e le dimensioni dell'esecutore. Questi valori vengono `spark-defaults.conf` archiviati nei nodi head del cluster.  È possibile modificare questi valori in un cluster in esecuzione selezionando Impostazioni predefinite spark personalizzate nell'interfaccia utente Web di Ambari.You can edit these values in a running cluster by selecting **Custom spark-defaults** in the Ambari web UI.  Dopo avere apportato le modifiche, nell'interfaccia utente viene chiesto di**riavviare** tutti i servizi interessati.

> [!NOTE]  
> Questi tre parametri di configurazione possono essere configurati a livello di cluster, per tutte le applicazioni in esecuzione nel cluster, oppure specificati per ogni singola applicazione.

Un'altra fonte di informazioni sulle risorse utilizzate dagli esecutori Di Spark è l'interfaccia utente dell'applicazione Spark.Another source of information about resources used by Spark Executors is the Spark Application UI.  Nell'interfaccia utente, **esecutori** visualizza le visualizzazioni Riepilogo e Dettagli della configurazione e delle risorse utilizzate.  Determinare se modificare i valori degli esecutori per l'intero cluster o per un set specifico di esecuzioni dei processi.

![Executor Spark](./media/apache-spark-settings/apache-spark-executors.png)

In alternativa, è possibile usare l'API REST di Ambari per verificare a livello di programmazione le impostazioni di configurazione del cluster HDInsight e Spark.Or you can use the Ambari REST API to programmatically verify HDInsight and Spark cluster configuration settings.  Per altre informazioni, vedere le [informazioni di riferimento dell'API Apache Ambari in GitHub](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

A seconda del carico di lavoro di Spark, è possibile determinare che una configurazione non predefinita per Spark può consentire esecuzioni più ottimizzate dei processi Spark.  Eseguire test di benchmark con carichi di lavoro di esempio per convalidare eventuali configurazioni cluster non predefinite.  Alcuni dei parametri comuni che è possibile provare a modificare sono:

|Parametro |Descrizione|
|---|---|
|--num-executors|Imposta il numero di esecutori.|
|--executor-cores|Imposta il numero di core per ogni esecutore. È consigliabile usare executor di dimensioni medie, in quanto parte della memoria disponibile è già usata da altri processi.|
|--executor-memoria|Controlla la dimensione della memoria (dimensione dell'heap) di ogni esecutore in [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html)e sarà necessario lasciare memoria per l'overhead di esecuzione.|

Ecco un esempio di due nodi del ruolo di lavoro con valori di configurazione diversi:

![Configurazioni a due nodi](./media/apache-spark-settings/executor-configuration.png)

L'elenco seguente illustra i principali parametri di memoria degli executor Spark.

|Parametro |Descrizione|
|---|---|
|spark.executor.memory|Definisce la quantità totale di memoria disponibile per un esecutore.|
|spark.storage.memoryFraction| (valore predefinito circa 60%) definisce la quantità di memoria disponibile per l'archiviazione di oggetti RDD (Resilient Distributed Dataset) persistenti.|
|spark.shuffle.memoryFraction| (valore predefinito circa 20%) definisce la quantità di memoria riservata per la selezione in ordine casuale.|
|spark.storage.unrollFraction e spark.storage.safetyFraction|(per un totale del 30% della memoria totale) - questi valori vengono utilizzati internamente da Spark e non devono essere modificati.|

YARN controlla la quantità complessiva massima di memoria usata dai contenitori in ogni nodo Spark. Il diagramma seguente mostra le relazioni per nodo tra gli oggetti di configurazione YARN e gli oggetti Spark.

![Gestione della memoria Spark in YARN](./media/apache-spark-settings/hdi-yarn-spark-memory.png)

## <a name="change-parameters-for-an-application-running-in-jupyter-notebook"></a>Modificare i parametri per un'applicazione in esecuzione in un notebook Jupyter

I cluster Spark in HDInsight includono per impostazione predefinita diversi componenti. Ognuno di questi componenti include valori di configurazione predefiniti, di cui è possibile eseguire l'override se necessario.

|Componente |Descrizione|
|---|---|
|Nucleo Spark|Spark Core, Spark SQL, Spark streaming API, GraphX e Apache Spark MLlib.|
|Anaconda|Un gestore di pacchetti python.|
|[Apache Livy](https://livy.incubator.apache.org/)|API REST Apache Spark, usata per inviare processi remoti a un cluster HDInsight Spark.The Apache Spark REST API, used to submit remote jobs to an HDInsight Spark cluster.|
|[Quaderni Jupyter](https://jupyter.org/) e [Apache zeppelin](https://zeppelin.apache.org/)|Interfaccia utente interattiva basata su browser per l'interazione con il cluster Spark.|
|Driver ODBC|Connette i cluster Spark in HDInsight a strumenti di business intelligence (BI), ad esempio Microsoft Power BI e Tableau.Connects Spark clusters in HDInsight to business intelligence (BI) tools such as Microsoft Power BI and Tableau.|

Per le applicazioni in esecuzione nel notebook Jupyter, usare il comando `%%configure` per apportare modifiche alla configurazione dal notebook. Queste modifiche alla configurazione verranno applicate ai processi di Spark eseguiti dall'istanza del notebook. Apportare tali modifiche all'inizio dell'applicazione, prima di eseguire la prima cella di codice. La configurazione modificata viene applicata alla sessione di Livy al momento della creazione.

> [!NOTE]  
> Per modificare la configurazione in una fase successiva nell'applicazione, usare il parametro `-f` (force). Tuttavia, in questo modo tutte le operazioni eseguite nell'applicazione andranno perse.

Il codice riportato di seguito mostra come modificare la configurazione per un'applicazione in esecuzione in un notebook Jupyter.

```
%%configure
{"executorMemory": "3072M", "executorCores": 4, "numExecutors":10}
```

## <a name="conclusion"></a>Conclusioni

Monitorare le impostazioni di configurazione di base per garantire che i processi Spark vengano eseguiti in modo prevedibile e performante. Queste impostazioni aiutano a determinare la migliore configurazione del cluster Spark per i carichi di lavoro specifici.  Sarà inoltre necessario monitorare l'esecuzione di esecuzioni di processi Spark a esecuzione prolungata e che consumano risorse.  Le sfide più comuni riguardano la pressione della memoria proveniente da configurazioni non corrette, ad esempio esecutori di dimensioni non corrette. Inoltre, le operazioni a esecuzione prolungata e le attività, che generano operazioni cartesiane.

## <a name="next-steps"></a>Passaggi successivi

* [Componenti e versioni di Apache Hadoop disponibili in HDInsight](../hdinsight-component-versioning.md)
* [Gestire le risorse di un cluster Apache Spark in HDInsight](apache-spark-resource-manager.md)
* [Configurare i cluster di HDInsight con Apache Hadoop, Apache Spark, Apache Kafka e altro ancora](../hdinsight-hadoop-provision-linux-clusters.md)
* [Configurazione di Apache Spark](https://spark.apache.org/docs/latest/configuration.html)
* [Running Apache Spark on Apache Hadoop YARN](https://spark.apache.org/docs/latest/running-on-yarn.html) (Esecuzione di Apache Spark in Apache Hadoop YARN)
