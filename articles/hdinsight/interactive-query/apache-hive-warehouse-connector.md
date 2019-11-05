---
title: Apache Spark & connettore del warehouse hive hive-Azure HDInsight
description: Informazioni su come integrare Apache Spark e Apache Hive con il connettore del warehouse di hive in Azure HDInsight.
author: nakhanha
ms.author: nakhanha
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/08/2019
ms.openlocfilehash: 2448550cf35f92bc8d91bc6ad9d5b22cc90b5ae0
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494303"
---
# <a name="integrate-apache-spark-and-apache-hive-with-the-hive-warehouse-connector"></a>Integrare Apache Spark e Apache Hive con il connettore del warehouse di hive

Il connettore Apache Hive warehouse (HWC) è una libreria che consente di lavorare più facilmente con Apache Spark e Apache Hive supportando attività come lo spostamento dei dati tra i frame di dati Spark e le tabelle hive e l'indirizzamento dei dati di Spark streaming nelle tabelle hive. Il connettore del warehouse di hive funziona come un bridge tra Spark e hive. Supporta scala, Java e Python per lo sviluppo.

Il connettore del warehouse di hive ti permette di sfruttare le funzionalità esclusive di hive e Spark per creare potenti applicazioni Big-Data. Apache Hive offre supporto per le transazioni di database atomiche, coerenti, isolate e durevoli (ACID). Per altre informazioni su ACID e sulle transazioni in hive, vedere [transazioni hive](https://cwiki.apache.org/confluence/display/Hive/Hive+Transactions). Hive offre anche controlli di sicurezza dettagliati tramite Apache Ranger e l'elaborazione analitica a bassa latenza non disponibile in Apache Spark.

Apache Spark dispone di un'API di streaming strutturata che fornisce funzionalità di streaming non disponibili in Apache Hive. A partire da HDInsight 4,0, Apache Spark 2.3.1 e Apache Hive 3.1.0 hanno Metastore distinti, che possono rendere difficile l'interoperabilità. Il connettore del warehouse di hive rende più semplice l'uso combinato di Spark e hive. La libreria HWC carica i dati dai daemon LLAP in esecutori Spark in parallelo, rendendoli più efficienti e scalabili rispetto all'uso di una connessione JDBC standard da Spark ad hive.

![architettura del connettore warehouse di hive](./media/apache-hive-warehouse-connector/hive-warehouse-connector-architecture.png)

Di seguito sono riportate alcune delle operazioni supportate dal connettore del warehouse di hive:

* Descrizione di una tabella
* Creazione di una tabella per i dati in formato ORC
* Selezione di dati hive e recupero di un frame di dati
* Scrittura di un frame di frame in hive in batch
* Esecuzione di un'istruzione Update di hive
* Lettura dei dati della tabella da hive, trasformazione in Spark e scrittura in una nuova tabella hive
* Scrittura di un frame di frame o di un flusso di Spark in hive tramite HiveStreaming

## <a name="hive-warehouse-connector-setup"></a>Installazione del connettore warehouse di hive

Seguire questa procedura per configurare il connettore del warehouse di hive tra un cluster Spark e un cluster Interactive query in Azure HDInsight:

### <a name="create-clusters"></a>Creare i cluster

1. Creare un cluster HDInsight Spark **4,0** con un account di archiviazione e una rete virtuale di Azure personalizzata. Per informazioni sulla creazione di un cluster in una rete virtuale di Azure, vedere [aggiungere HDInsight a una rete virtuale esistente](../../hdinsight/hdinsight-plan-virtual-network-deployment.md#existingvnet).

1. Creare un cluster LLAP (HDInsight Interactive query) **4,0** con lo stesso account di archiviazione e la stessa rete virtuale di Azure del cluster Spark.

### <a name="modify-hosts-file"></a>Modificare il file degli host

Copiare le informazioni sul nodo dal file `/etc/hosts` in headnode0 del cluster Interactive query e concatenare le informazioni al file `/etc/hosts` nel headnode0 del cluster Spark. Questo passaggio consente al cluster Spark di risolvere gli indirizzi IP dei nodi nel cluster Interactive query. Visualizzare il contenuto del file aggiornato con `cat /etc/hosts`. L'output finale dovrebbe avere un aspetto simile a quello mostrato nella schermata seguente.

![il connettore del warehouse di hive ospita il file](./media/apache-hive-warehouse-connector/hive-warehouse-connector-hosts-file.png)

### <a name="gather-preliminary-information"></a>Raccogli informazioni preliminari

#### <a name="from-your-interactive-query-cluster"></a>Dal cluster Interactive query

1. Passare al home page Apache Ambari del cluster usando `https://LLAPCLUSTERNAME.azurehdinsight.net` dove `LLAPCLUSTERNAME` è il nome del cluster Interactive query.

1. Passare a **hive** > **configs** > **Advanced** > **Advanced hive-site** > **hive. Zookeeper. quorum** e prendere nota del valore. Il valore può essere simile a: `zk0-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181,zk1-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181,zk4-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181`.

1. Passare a **Hive** > **configs** > **Advanced** > **generale** > **hive. Metastore. Uri** e prendere nota del valore. Il valore può essere simile a: `thrift://hn0-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:9083,thrift://hn1-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:9083`.

#### <a name="from-your-apache-spark-cluster"></a>Dal cluster Apache Spark

1. Passare al home page Apache Ambari del cluster usando `https://SPARKCLUSTERNAME.azurehdinsight.net` dove `SPARKCLUSTERNAME` è il nome del cluster Apache Spark.

1. Passare a **hive** > **configs** > **Advanced** > **advanced hive-Interactive-site** > **hive. LLAP. daemon. Service. hosts** e prendere nota del valore. Il valore può essere simile a: `@llap0`.

### <a name="configure-spark-cluster-settings"></a>Configurare le impostazioni del cluster Spark

Dall'interfaccia utente Web di Spark Ambari passare a **Spark2** > **configs** > **Custom Spark2-defaults**.

![Configurazione di Apache Ambari Spark2](./media/apache-hive-warehouse-connector/hive-warehouse-connector-spark2-ambari.png)

Selezionare **Aggiungi proprietà** in base alle esigenze per aggiungere/aggiornare quanto segue:

| Chiave | Valore |
|----|----|
|`spark.hadoop.hive.llap.daemon.service.hosts`|Valore ottenuto in precedenza da **hive. LLAP. daemon. Service. hosts**.|
|`spark.sql.hive.hiveserver2.jdbc.url`|`jdbc:hive2://LLAPCLUSTERNAME.azurehdinsight.net:443/;user=admin;password=PWD;ssl=true;transportMode=http;httpPath=/hive2`. Impostare sulla stringa di connessione JDBC, che si connette a Hiveserver2 nel cluster Interactive query. SOSTITUIRE `LLAPCLUSTERNAME` con il nome del cluster Interactive query. Sostituire `PWD` con la password effettiva.|
|`spark.datasource.hive.warehouse.load.staging.dir`|`wasbs://STORAGE_CONTAINER_NAME@STORAGE_ACCOUNT_NAME.blob.core.windows.net/tmp`. Impostare su una directory di gestione temporanea compatibile con HDFS appropriata. Se si dispone di due cluster diversi, la directory di staging deve essere una cartella nella directory di staging dell'account di archiviazione del cluster LLAP in modo che HiveServer2 possa accedervi.  Sostituire `STORAGE_ACCOUNT_NAME` con il nome dell'account di archiviazione usato dal cluster e `STORAGE_CONTAINER_NAME` con il nome del contenitore di archiviazione.|
|`spark.datasource.hive.warehouse.metastoreUri`|Valore ottenuto in precedenza da **hive. Metastore. Uri**.|
|`spark.security.credentials.hiveserver2.enabled`|`false` per la modalità di distribuzione del client YARN.|
|`spark.hadoop.hive.zookeeper.quorum`|Valore ottenuto in precedenza da **hive. Zookeeper. quorum**.|

Salvare le modifiche e riavviare i componenti in base alle esigenze.

## <a name="using-the-hive-warehouse-connector"></a>Uso di hive warehouse Connector

### <a name="connecting-and-running-queries"></a>Connessione ed esecuzione di query

È possibile scegliere tra alcuni metodi diversi per connettersi al cluster Interactive query ed eseguire query usando il connettore del warehouse di hive. I metodi supportati includono gli strumenti seguenti:

* [Shell Spark](../spark/apache-spark-shell.md)
* PySpark
* Spark-Submit
* [Zeppelin](../spark/apache-spark-zeppelin-notebook.md)
* [Livy](../spark/apache-spark-livy-rest-interface.md)

Tutti gli esempi forniti in questo articolo verranno eseguiti tramite Spark-Shell.

Per avviare una sessione di Spark-Shell, seguire questa procedura:

1. Usare SSH nel nodo head per il cluster Apache Spark. Per altre informazioni sulla connessione al cluster con SSH, vedere [connettersi a HDInsight (Apache Hadoop) tramite SSH](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

1. Immettere il comando seguente per avviare la shell di Spark:

    ```bash
    spark-shell --master yarn \
    --jars /usr/hdp/current/hive_warehouse_connector/hive-warehouse-connector-assembly-1.0.0.3.0.2.1-8.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    ```

    Verrà visualizzato un messaggio di benvenuto e un prompt `scala>` in cui è possibile immettere i comandi.

1. Dopo l'avvio di Spark-Shell, è possibile avviare un'istanza del connettore del warehouse di hive usando i comandi seguenti:

    ```scala
    import com.hortonworks.hwc.HiveWarehouseSession
    val hive = HiveWarehouseSession.session(spark).build()
    ```

### <a name="connecting-and-running-queries-on-enterprise-security-package-esp-clusters"></a>Connessione ed esecuzione di query in cluster Enterprise Security Package (ESP)

Il Enterprise Security Package (ESP) fornisce funzionalità di livello aziendale, ad esempio l'autenticazione basata su Active Directory, il supporto multiutente e il controllo degli accessi in base al ruolo per i cluster Apache Hadoop in Azure HDInsight. Per altre informazioni su ESP, vedere [usare Enterprise Security Package in HDInsight](../domain-joined/apache-domain-joined-architecture.md).

1. Usare SSH nel nodo head per il cluster Apache Spark. Per altre informazioni sulla connessione al cluster con SSH, vedere [connettersi a HDInsight (Apache Hadoop) tramite SSH](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

1. Digitare `kinit` e accedere con un utente di dominio.

1. Avviare Spark-Shell con l'elenco completo dei parametri di configurazione, come illustrato di seguito. Tutti i valori di tutte le lettere maiuscole tra parentesi angolari devono essere specificati in base al cluster. Se è necessario trovare i valori da inserire per uno dei parametri seguenti, vedere la sezione relativa all'installazione del [connettore del warehouse di hive](#hive-warehouse-connector-setup).:

    ```bash
    spark-shell --master yarn \
    --jars /usr/hdp/3.0.1.0-183/hive_warehouse_connector/hive-warehouse-connector-assembly-1.0.0.3.0.1.0-183.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    --conf spark.hadoop.hive.llap.daemon.service.hosts='<LLAP_APP_NAME>'
    --conf spark.sql.hive.hiveserver2.jdbc.url='jdbc:hive2://<ZOOKEEPER_QUORUM>;serviceDiscoveryMode=zookeeper;zookeeperNamespace=hiveserver2-interactive'
    --conf spark.datasource.hive.warehouse.load.staging.dir='<STAGING_DIR>'
    --conf spark.datasource.hive.warehouse.metastoreUri='<METASTORE_URI>'
    --conf spark.hadoop.hive.zookeeper.quorum='<ZOOKEEPER_QUORUM>'
   ```

### <a name="creating-spark-dataframes-from-hive-queries"></a>Creazione di frame di frame di Spark da query hive

I risultati di tutte le query che usano la libreria HWC vengono restituiti come frame di dati. Negli esempi seguenti viene illustrato come creare una query di base.

```scala
hive.setDatabase("default")
val df = hive.executeQuery("select * from hivesampletable")
df.filter("state = 'Colorado'").show()
```

I risultati della query sono i dataframe di Spark, che possono essere usati con le librerie Spark, ad esempio MLIB e SparkSQL.

### <a name="writing-out-spark-dataframes-to-hive-tables"></a>Scrittura di frame di frame di Spark in tabelle hive

Spark non supporta in modo nativo la scrittura nelle tabelle ACID gestite di hive. Usando HWC, tuttavia, è possibile scrivere qualsiasi dataframe in una tabella hive. Questa funzionalità è visibile nell'esempio seguente:

1. Creare una tabella denominata `sampletable_colorado` e specificarne le colonne usando il comando seguente:

    ```scala
    hive.createTable("sampletable_colorado").column("clientid","string").column("querytime","string").column("market","string").column("deviceplatform","string").column("devicemake","string").column("devicemodel","string").column("state","string").column("country","string").column("querydwelltime","double").column("sessionid","bigint").column("sessionpagevieworder","bigint").create()
    ```

1. Filtrare la tabella `hivesampletable` in cui la colonna `state` è uguale a `Colorado`. Questa query della tabella hive viene restituita come dataframe di Spark. Il frame di frame viene quindi salvato nella tabella hive `sampletable_colorado` usando la funzione `write`.

    ```scala
    hive.table("hivesampletable").filter("state = 'Colorado'").write.format(HiveWarehouseSession.HIVE_WAREHOUSE_CONNECTOR).option("table","sampletable_colorado").save()
    ```

1. Visualizzare i risultati con il comando seguente:

    ```scala
    hive.table("sampletable_colorado").show()
    ```
    
    ![connettore warehouse hive Mostra tabella hive](./media/apache-hive-warehouse-connector/hive-warehouse-connector-show-hive-table.png)

### <a name="structured-streaming-writes"></a>Scritture di streaming strutturato

Con hive warehouse Connector è possibile usare Spark streaming per scrivere dati nelle tabelle hive.

Attenersi alla procedura seguente per creare un esempio di connettore del warehouse di hive che inserisce i dati da un flusso Spark sulla porta localhost 9999 in una tabella hive.

1. Attenersi alla procedura descritta in [connessione ed esecuzione di query](#connecting-and-running-queries).

1. Avviare il flusso Spark con il comando seguente:

    ```scala
    val lines = spark.readStream.format("socket").option("host", "localhost").option("port",9999).load()
    ```

1. Generare i dati per il flusso Spark creato, seguendo questa procedura:
    1. Aprire una seconda sessione SSH nello stesso cluster Spark.
    1. Al prompt dei comandi digitare `nc -lk 9999`. Questo comando usa l'utilità netcat per inviare i dati dalla riga di comando alla porta specificata.

1. Tornare alla prima sessione SSH e creare una nuova tabella hive per conservare i dati di streaming. In Spark-Shell immettere il comando seguente:

    ```scala
    hive.createTable("stream_table").column("value","string").create()
    ```

1. Quindi scrivere i dati di streaming nella tabella appena creata usando il comando seguente:

    ```scala
    lines.filter("value = 'HiveSpark'").writeStream.format(HiveWarehouseSession.STREAM_TO_STREAM).option("database", "default").option("table","stream_table").option("metastoreUri",spark.conf.get("spark.datasource.hive.warehouse.metastoreUri")).option("checkpointLocation","/tmp/checkpoint1").start()
    ```

    >[!Important]
    > È necessario impostare manualmente le opzioni `metastoreUri` e `database` a causa di un problema noto in Apache Spark. Per ulteriori informazioni su questo problema, vedere [Spark-25460](https://issues.apache.org/jira/browse/SPARK-25460).

1. Tornare alla seconda sessione SSH e immettere i valori seguenti:

    ```bash
    foo
    HiveSpark
    bar
    ```

1. Tornare alla prima sessione SSH e prendere nota dell'attività breve. Usare il comando seguente per visualizzare i dati:

    ```scala
    hive.table("stream_table").show()
    ```

Usare **CTRL + C** per arrestare netcat nella seconda sessione SSH. Usare `:q` per uscire da Spark-Shell nella prima sessione SSH.

### <a name="securing-data-on-spark-esp-clusters"></a>Protezione dei dati nei cluster ESP Spark

1. Creare una tabella `demo` con alcuni dati di esempio immettendo i comandi seguenti:

    ```scala
    create table demo (name string);
    INSERT INTO demo VALUES ('HDinsight');
    INSERT INTO demo VALUES ('Microsoft');
    INSERT INTO demo VALUES ('InteractiveQuery');
    ```

1. Visualizzare il contenuto della tabella con il comando seguente. Prima di applicare il criterio, nella tabella `demo` viene visualizzata la colonna full.

    ```scala
    hive.executeQuery("SELECT * FROM demo").show()
    ```

    ![tabella demo prima di applicare i criteri Ranger](./media/apache-hive-warehouse-connector/hive-warehouse-connector-table-before-ranger-policy.png)

1. Applicare un criterio di mascheramento delle colonne che mostra solo gli ultimi quattro caratteri della colonna.  
    1. Passare all'interfaccia utente di amministrazione di Ranger a `https://CLUSTERNAME.azurehdinsight.net/ranger/`.
    1. Fare clic sul servizio Hive per il cluster in **hive**.
        ](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-service-manager.png) di gestione del servizio ![Ranger
    1. Fare clic sulla scheda **maschera** e quindi su **Aggiungi nuovo criterio**

        ![elenco dei criteri hive del connettore warehouse di hive](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-hive-policy-list.png)

    a. Specificare un nome di criterio desiderato. Selezione database: **predefinito**, tabella hive: **demo**, colonna hive: **nome**, utente: **rsadmin2**, tipi di accesso: **Seleziona**e **maschera parziale: Mostra gli ultimi 4** dal menu **Opzioni di selezione maschera** . Fare clic su **Aggiungi**.
                ![Crea criterio](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-create-policy.png)
1. Visualizzare di nuovo il contenuto della tabella. Dopo aver applicato i criteri Ranger, è possibile visualizzare solo gli ultimi quattro caratteri della colonna.

    ![tabella demo dopo l'applicazione dei criteri Ranger](./media/apache-hive-warehouse-connector/hive-warehouse-connector-table-after-ranger-policy.png)

## <a name="next-steps"></a>Passaggi successivi

* [Usare Interactive Query in HDInsight](https://docs.microsoft.com/azure/hdinsight/interactive-query/apache-interactive-query-get-started)
* [Esempi di interazione con il connettore del warehouse di hive con Zeppelin, Livio, Spark-Submit e pyspark](https://community.hortonworks.com/articles/223626/integrating-apache-hive-with-apache-spark-hive-war.html)
