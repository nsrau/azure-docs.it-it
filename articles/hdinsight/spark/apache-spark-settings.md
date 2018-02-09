---
title: Configurare le impostazioni di Spark - Azure HDInsight | Microsoft Docs
description: Come configurare Spark per un cluster HDInsight.
services: hdinsight
documentationcenter: 
tags: azure-portal
author: maxluk
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: maxluk
ms.openlocfilehash: 1dd0ff26cdb39feacec697d7900ad7abaa5f1996
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/01/2018
---
# <a name="configure-spark-settings"></a>Configurare le impostazioni di Spark

Un cluster HDInsight Spark include un'installazione della libreria Apache Spark.  Ogni cluster HDInsight include parametri di configurazione predefiniti per tutti i servizi installati, incluso Spark.  Un aspetto fondamentale della gestione di un cluster HDInsight Hadoop è il monitoraggio del carico di lavoro, inclusi i processi Spark, per assicurarsi che siano in esecuzione in modo prevedibile. Per eseguire al meglio i processi Spark, prendere in considerazione la configurazione fisica del cluster quando si determina come ottimizzare la configurazione logica del cluster.

Il cluster HDInsight Apache Spark predefinito include i nodi seguenti: tre nodi ZooKeeper, due nodi head e uno o più nodi del ruolo di lavoro:

![Architettura di Spark HDInsight](./media/apache-spark-settings/spark-hdinsight-arch.png)

Anche il numero di macchine virtuali e le dimensioni delle macchine virtuali per i nodi nel cluster HDInsight possono influire sulla configurazione di Spark. I valori di configurazione di HDInsight non predefiniti richiedono spesso valori di configurazione di Spark non predefiniti. Quando si crea un cluster HDInsight Spark, per ognuno dei componenti vengono visualizzate le dimensioni suggerite per le macchine virtuali. Attualmente le [dimensioni delle macchine virtuali Linux ottimizzate per la memoria](../../virtual-machines/linux/sizes-memory.md) per Azure sono D12 v2 o maggiori.

## <a name="spark-versions"></a>Versioni di Spark

Prendere inoltre in considerazione la versione migliore di Spark per il cluster.  Spark 2.x offre caratteristiche di esecuzione migliori rispetto a Spark 1.x. Spark 2.x include numerose ottimizzazioni delle prestazioni, ad esempio Tungsten, Catalyst Query Optimization e molte altre.  Il servizio HDInsight include più versioni di Spark e di HDInsight.  Ogni versione di Spark include un set di impostazioni del cluster predefinite.  Quando si crea un nuovo cluster, è possibile scegliere tra le seguenti versioni correnti di Spark:

![Versioni di Spark](./media/apache-spark-settings/spark-version.png)

> [!NOTE]
> La versione predefinita di Apache Spark nel servizio HDInsight può cambiare senza preavviso. In caso di dipendenza dalla versione, è consigliabile indicare la versione specifica quando si creano i cluster tramite .NET SDK, Azure PowerShell e l'interfaccia della riga di comando di Azure.

Apache Spark ha tre posizioni per le configurazioni di sistema:

* Le proprietà di Spark controllano la maggior parte dei parametri dell'applicazione e possono essere impostate usando un oggetto `SparkConf` o tramite le proprietà di sistema Java.
* Le variabili di ambiente possono essere usate per definire le impostazioni per computer, ad esempio l'indirizzo IP, tramite lo script `conf/spark-env.sh` in ogni nodo.
* La registrazione può essere configurata tramite `log4j.properties`.

Quando si seleziona una particolare versione di Spark, il cluster include le impostazioni di configurazione predefinite.  È possibile modificare i valori di configurazione predefiniti di Spark fornendo un file di configurazione di Spark personalizzato.  Di seguito è illustrato un esempio.

```
    spark.hadoop.io.compression.codecs org.apache.hadoop.io.compress.GzipCodec
    spark.hadoop.mapreduce.input.fileinputformat.split.minsize 1099511627776
    spark.hadoop.parquet.block.size 1099511627776
    spark.sql.files.maxPartitionBytes 1099511627776
    spark.sql.files.openCostInBytes 1099511627776
```

L'esempio precedente esegue l'override di alcuni valori predefiniti per cinque parametri di configurazione di Spark.  Si tratta dei valori predefiniti relativi a codec di compressione, dimensioni minime di divisione MapReduce Hadoop, dimensioni del blocco Parquet, partizione Spark SQL e dimensioni dei file aperti.  Queste modifiche alla configurazione vengono scelte in quanto i dati e i processi associati (in questo esempio, i dati di genomica) hanno caratteristiche particolari che offrono prestazioni migliori se si usano queste impostazioni di configurazione personalizzate.

---

## <a name="view-cluster-configuration-settings"></a>Visualizzare le impostazioni di configurazione del cluster

Verificare le impostazioni di configurazione correnti del cluster HDInsight prima di eseguire l'ottimizzazione delle prestazioni nel cluster. Avviare il dashboard di HDInsight dal portale di Azure facendo clic sul collegamento **Dashboard** nel riquadro del cluster Spark. Accedere usando nome utente e password dell'amministratore del cluster.

Viene visualizzata l'interfaccia utente Web Ambari, con una vista del dashboard delle metriche principali di utilizzo delle risorse del cluster.  Il dashboard di Ambari mostra la configurazione di Apache Spark e altri servizi installati. Il dashboard include una scheda **Config History** (Cronologia configurazione), dove è possibile visualizzare le informazioni di configurazione per tutti i servizi installati, incluso Spark.

Per visualizzare i valori di configurazione per Apache Spark, selezionare **Config History** (Cronologia configurazione) e quindi selezionare **Spark2**.  Selezionare la scheda **Configs** (Configurazioni) e quindi fare clic sul collegamento `Spark` (o `Spark2`, a seconda della versione) nell'elenco di servizi.  Verrà visualizzato un elenco di valori di configurazione per il cluster:

![Configurazioni di Spark](./media/apache-spark-settings/spark-config.png)

Per visualizzare e modificare i singoli valori di configurazione di Spark, selezionare un collegamento con la parola "spark" nel titolo.  Le configurazioni per Spark includono sia i valori di configurazione personalizzati che quelli avanzati nelle categorie seguenti:

* Custom Spark2-defaults (Impostazioni predefinite Spark2 personalizzate)
* Custom Spark2-metrics-properties (Proprietà metriche Spark2 personalizzate)
* Advanced Spark2-defaults (Impostazioni predefinite Spark2 avanzate)
* Advanced Spark2-env (Impostazioni ambiente Spark2 avanzate)
* Advanced spark2-hive-site-override (Impostazioni override sito hive Spark2 avanzate)

Se si crea un set non predefinito di valori di configurazione, è anche possibile visualizzare la cronologia degli aggiornamenti della configurazione.  Questa cronologia di configurazione può essere utile per capire quale configurazione non predefinita offre prestazioni ottimali.

> [!NOTE]
> Per visualizzare, ma senza modificare, le impostazioni di configurazione del cluster Spark comuni, selezionare la scheda **Environment** (Ambiente) al livello superiore dell'**interfaccia utente del processo Spark**.

## <a name="configuring-spark-executors"></a>Configurazione degli executor Spark

Il diagramma seguente mostra gli oggetti Spark principali: il programma driver e il contesto Spark associato, oltre che lo strumento di gestione di cluster e i relativi *n* nodi del ruolo di lavoro.  Ogni nodo del ruolo di lavoro include un executor, una cache e *n* istanze di attività.

![Oggetti del cluster](./media/apache-spark-settings/spark-arch.png)

I processi Spark usano risorse del ruolo di lavoro, in particolare la memoria, quindi è prassi comune modificare i valori di configurazione di Spark per gli executor del nodo del ruolo di lavoro.

I tre parametri principali che vengono spesso modificati per ottimizzare le configurazioni di Spark per migliorare i requisiti dell'applicazione sono `spark.executor.instances`, `spark.executor.cores` e `spark.executor.memory`. Un Executor è un processo avviato per un'applicazione Spark. Viene eseguito nel nodo del ruolo di lavoro ed è responsabile delle attività per l'applicazione. Il numero predefinito di executor e le relative dimensioni per ogni cluster vengono calcolati in base al numero di nodi del ruolo di lavoro e alle relative dimensioni. Questi dati vengono archiviati in `spark-defaults.conf` nei nodi head del cluster.  È possibile modificare questi valori in un cluster in esecuzione selezionando il collegamento **Custom spark-defaults** (Impostazioni predefinite Spark personalizzate) nell'interfaccia utente Web Ambari.  Dopo avere apportato le modifiche, nell'interfaccia utente viene chiesto di**riavviare** tutti i servizi interessati.

> [!NOTE]
> Questi tre parametri di configurazione possono essere configurati a livello di cluster, per tutte le applicazioni in esecuzione nel cluster, oppure specificati per ogni singola applicazione.

Un'altra fonte di informazioni sulle risorse in uso dagli executor Spark è l'interfaccia utente dell'applicazione Spark.  Nell'interfaccia utente di Spark selezionare la scheda **Executor** per visualizzare le viste di riepilogo e dei dettagli della configurazione e delle risorse utilizzate dagli executor.  Queste viste aiutano a determinare se modificare i valori predefiniti per gli executor Spark per l'intero cluster o un set specifico di esecuzioni del processo.

![Executor Spark](./media/apache-spark-settings/spark-executors.png)

In alternativa, è possibile usare l'API REST Ambari per verificare a livello di codice le impostazioni di configurazione dei cluster HDInsight e Spark.  Per altre informazioni, vedere le [informazioni di riferimento dell'API Ambari in GitHub](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

A seconda del carico di lavoro di Spark, è possibile determinare che una configurazione non predefinita per Spark può consentire esecuzioni più ottimizzate dei processi Spark.  Eseguire test di benchmark con carichi di lavoro di esempio per convalidare le configurazioni non predefinite dei cluster.  Alcuni dei parametri comuni che è possibile provare a modificare sono:

* `--num-executors` imposta il numero di executor.
* `--executor-cores` imposta il numero di core per ogni executor. È consigliabile usare executor di dimensioni medie, in quanto parte della memoria disponibile è già usata da altri processi.
* `--executor-memory` controlla le dimensioni della memoria (dimensioni heap) di ogni executor in YARN. È necessario lasciare una certa quantità di memoria per l'overhead di esecuzione.

Ecco un esempio di due nodi del ruolo di lavoro con valori di configurazione diversi:

![Configurazioni a due nodi](./media/apache-spark-settings/executor-config.png)

L'elenco seguente illustra i principali parametri di memoria degli executor Spark.

* `spark.executor.memory` definisce la quantità totale di memoria disponibile per un executor.
* `spark.storage.memoryFraction` (valore predefinito circa 60%) definisce la quantità di memoria disponibile per l'archiviazione di oggetti RDD (Resilient Distributed Dataset) persistenti.
* `spark.shuffle.memoryFraction` (valore predefinito circa 20%) definisce la quantità di memoria riservata per la selezione in ordine casuale.
* `spark.storage.unrollFraction` e `spark.storage.safetyFraction` (per un totale di memoria pari a circa il 30%): questi valori vengono usati internamente da Spark e non devono essere modificati.

YARN controlla la quantità complessiva massima di memoria usata dai contenitori in ogni nodo Spark. Il diagramma seguente mostra le relazioni per nodo tra gli oggetti di configurazione YARN e gli oggetti Spark.

![Gestione della memoria Spark in YARN](./media/apache-spark-settings/yarn-spark-memory.png)

## <a name="change-parameters-for-an-application-running-in-jupyter-notebook"></a>Modificare i parametri per un'applicazione in esecuzione in un notebook Jupyter

I cluster Spark in HDInsight includono per impostazione predefinita diversi componenti. Ognuno di questi componenti include valori di configurazione predefiniti di cui è possibile eseguire l'override, se necessario.

* Spark Core: Spark Core, Spark SQL, API di streaming Spark, GraphX e MLlib
* Anaconda: strumento di gestione pacchetti Python
* Livy: API REST di Apache Spark, usata per inviare processi remoti a un cluster HDInsight Spark
* Notebook Jupyter e Zeppelin: interfaccia utente interattiva basata su browser per l'interazione con il cluster Spark
* Driver ODBC: connette i cluster Spark in HDInsight a strumenti di business Intelligence come Microsoft Power BI e Tableau

Per le applicazioni in esecuzione nel notebook Jupyter, usare il comando `%%configure` per apportare modifiche alla configurazione dal notebook. Queste modifiche alla configurazione verranno applicate ai processi di Spark eseguiti dall'istanza del notebook. Apportare le modifiche all'inizio dell'applicazione, prima di eseguire la prima cella di codice. La configurazione modificata viene applicata alla sessione di Livy al momento della creazione.

> [!NOTE]
> Per modificare la configurazione in una fase successiva nell'applicazione, usare il parametro `-f` (force). Tuttavia, in questo modo tutte le operazioni eseguite nell'applicazione andranno perse.

Il codice riportato di seguito mostra come modificare la configurazione per un'applicazione in esecuzione in un notebook Jupyter.

```
    %%configure
    {"executorMemory": "3072M", "executorCores": 4, "numExecutors":10}
```

## <a name="conclusion"></a>Conclusioni

Ci sono numerose impostazioni di configurazione principali che è necessario monitorare e modificare per garantire che i processi Spark vengano eseguiti in modo prevedibile e ad alte prestazioni. Queste impostazioni aiutano a determinare la migliore configurazione del cluster Spark per i carichi di lavoro specifici.  È anche necessario monitorare i processi Spark che comportano un'esecuzione prolungata e/o un consumo eccessivo di risorse.  Le sfide più comuni sono legate a un carico eccessivo sulla memoria a causa di configurazioni non corrette (in particolare in caso di executor di dimensioni errate), operazioni con esecuzione prolungata e attività che generano operazioni cartesiane.

## <a name="next-steps"></a>Passaggi successivi

* [Componenti e versioni di Hadoop disponibili in HDInsight](../hdinsight-component-versioning.md)
* [Gestire le risorse del cluster Spark in HDInsight](apache-spark-resource-manager.md)
* [Configurare i cluster di HDInsight con Hadoop, Spark, Kafka e altro ancora](../hdinsight-hadoop-provision-linux-clusters.md)
* [Apache Spark Configuration (Configurazione di Apache Spark)](https://spark.apache.org/docs/latest/configuration.html)
* [Running Spark on YARN (Esecuzione di Spark in YARN)](https://spark.apache.org/docs/latest/running-on-yarn.html)
