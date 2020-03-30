---
title: Apache Spark & Hive - Connettore magazzino Hive - Azure HDInsight
description: Informazioni su come integrare Apache Spark e Apache Hive con Hive Warehouse Connector in Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/02/2020
ms.openlocfilehash: f386530ffb3a074a5c1db1d9f28535d28c8b1284
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78252414"
---
# <a name="integrate-apache-spark-and-apache-hive-with-the-hive-warehouse-connector"></a>Integrare Apache Spark e Apache Hive con il connettore Hive Warehouse

Apache Hive Warehouse Connector (HWC) è una libreria che consente di lavorare più facilmente con Apache Spark e Apache Hive supportando attività quali lo spostamento dei dati tra le tabelle Spark DataFrames e Hive e anche l'indirizzamento dei dati in streaming di Spark nelle tabelle Hive. Hive Warehouse Connector funziona come un ponte tra Spark e Hive. Supporta Scala, Java e Python per lo sviluppo.

Hive Warehouse Connector consente di sfruttare le caratteristiche uniche di Hive e Spark per creare potenti applicazioni di Big Data. Apache Hive offre il supporto per le transazioni di database atomiche, coerenti, isolate e durevoli (ACID). Per ulteriori informazioni su ACID e sulle transazioni in Hive, vedere [Hive Transactions](https://cwiki.apache.org/confluence/display/Hive/Hive+Transactions). Hive offre anche controlli di sicurezza dettagliati tramite Apache Ranger e Low Latency Analytical Processing non disponibili in Apache Spark.

Apache Spark, ha un'API di streaming strutturato che offre funzionalità di streaming non disponibili in Apache Hive. A partire da HDInsight 4.0, Apache Spark 2.3.1 e Apache Hive 3.1.0 dispongono di metastore separati, il che può rendere difficile l'interoperabilità. Il connettore del magazzino Hive semplifica l'utilizzo di Spark e Hive insieme. La libreria HWC carica i dati dai daemon l'lLAP agli esecutori Spark in parallelo, rendendolo più efficiente e scalabile rispetto all'utilizzo di una connessione JDBC standard da Spark a Hive.

![Architettura del connettore warehouse dell'hive](./media/apache-hive-warehouse-connector/hive-warehouse-connector-architecture.png)

Alcune delle operazioni supportate dal Hive Warehouse Connector sono:

* Descrizione di una tabellaDescribing a table
* Creazione di una tabella per dati in formato ORC
* Selezione dei dati Hive e recupero di un frame di datiSelecting Hive data and retrieving a DataFrame
* Scrittura di un frame di dati in Hive in batchWriting a DataFrame to Hive in batch
* Esecuzione di un'istruzione di aggiornamento HiveExecuting a Hive update statement
* Lettura dei dati della tabella da Hive, trasformazione in Spark e scrittura in una nuova tabella Hive
* Scrittura di un flusso Di Frame O Spark o DataFrame in Hive tramite HiveStreamingWriting a DataFrame or Spark stream to Hive using HiveStreaming

## <a name="hive-warehouse-connector-setup"></a>Configurazione del connettore warehouse di Hive

Seguire questi passaggi per configurare il connettore warehouse Hive tra un cluster Spark e Interactive Query in Azure HDInsight:Follow these steps to set up the Hive Warehouse Connector between a Spark and Interactive Query cluster in Azure HDInsight:

### <a name="create-clusters"></a>Creare i cluster

1. Creare un cluster HDInsight Spark **4.0** con un account di archiviazione e una rete virtuale di Azure personalizzata. Per informazioni sulla creazione di un cluster in una rete virtuale di Azure, vedere Aggiungere HDInsight a una [rete virtuale esistente.](../../hdinsight/hdinsight-plan-virtual-network-deployment.md#existingvnet)

1. Creare un cluster HDInsight Interactive Query (LLAP) 4.0 con lo stesso account di archiviazione e una rete virtuale di Azure del cluster Spark.Create an HDInsight Interactive Query (LLAP) **4.0** cluster with the same storage account and Azure virtual network as the Spark cluster.

### <a name="modify-hosts-file"></a>Modificare il file hosts

Copiare le informazioni `/etc/hosts` sul nodo dal file in headnode0 del cluster `/etc/hosts` Interactive Query e concatenarle al file nel nodo headnode0 del cluster Spark. Questo passaggio consentirà al cluster Spark di risolvere gli indirizzi IP dei nodi nel cluster Interactive Query. Visualizzare il contenuto del `cat /etc/hosts`file aggiornato con . L'output finale dovrebbe essere simile a quello mostrato nella schermata seguente.

![Hive warehouse connector ospita il file](./media/apache-hive-warehouse-connector/hive-warehouse-connector-hosts-file.png)

### <a name="gather-preliminary-information"></a>Raccogliere informazioni preliminari

#### <a name="from-your-interactive-query-cluster"></a>Dal cluster Query interattivo

1. Passare alla pagina Apache Ambari Hive `https://LLAPCLUSTERNAME.azurehdinsight.net/#/main/services/HIVE/configs` `LLAPCLUSTERNAME` del cluster usando il nome del cluster Interactive Query.

1. Passare a **Advanced** > **General** > **hive.metastore.uris** e prendere nota del valore. Il valore può essere `thrift://iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:9083,thrift://hn1-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:9083`simile al: .

1. Passare a **Advanced** > **Advanced hive-site** > **hive.zookeeper.quorum** e prendere nota del valore. Il valore può essere `zk0-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181,zk1-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181,zk4-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181`simile al: .

#### <a name="from-your-apache-spark-cluster"></a>Dal tuo cluster Apache Spark

1. Passare alla pagina Apache Ambari Hive `https://SPARKCLUSTERNAME.azurehdinsight.net/#/main/services/HIVE/configs` `SPARKCLUSTERNAME` del cluster usando dove è il nome del cluster Apache Spark.

1. Passare a **Advanced** > **Advanced hive-interactive-site** > **hive.llap.daemon.service.hosts** e prendere nota del valore. Il valore può essere `@llap0`simile al: .

### <a name="configure-spark-cluster-settings"></a>Configurare le impostazioni del cluster Spark

Dall'interfaccia utente Web di Spark Ambari passare a **Spark2** > **CONFIGS** > **Custom spark2-defaults**.

![Configurazione di Apache Ambari Spark2](./media/apache-hive-warehouse-connector/hive-warehouse-connector-spark2-ambari.png)

Selezionare Aggiungi proprietà... in base alle esigenze per aggiungere/aggiornare quanto segue:Select **Add Property...** as needed to add/update the following:

| Chiave | valore |
|----|----|
|`spark.hadoop.hive.llap.daemon.service.hosts`|Il valore ottenuto in precedenza da **hive.llap.daemon.service.hosts**.|
|`spark.sql.hive.hiveserver2.jdbc.url`|`jdbc:hive2://LLAPCLUSTERNAME.azurehdinsight.net:443/;user=admin;password=PWD;ssl=true;transportMode=http;httpPath=/hive2`. Impostare sulla stringa di connessione JDBC, che si connette a Hiveserver2 nel cluster Interactive Query. REPLACE `LLAPCLUSTERNAME` con il nome del cluster Interactive Query. Sostituire `PWD` con la password effettiva.|
|`spark.datasource.hive.warehouse.load.staging.dir`|`wasbs://STORAGE_CONTAINER_NAME@STORAGE_ACCOUNT_NAME.blob.core.windows.net/tmp`. Impostare su una directory di gestione temporanea compatibile con HDFS adatta. Se si dispone di due cluster diversi, la directory di gestione temporanea deve essere una cartella nella directory di gestione temporanea dell'account di archiviazione del cluster LLAP in modo che HiveServer2 abbia accesso ad esso.  Sostituire `STORAGE_ACCOUNT_NAME` con il nome dell'account di archiviazione `STORAGE_CONTAINER_NAME` utilizzato dal cluster e con il nome del contenitore di archiviazione.|
|`spark.datasource.hive.warehouse.metastoreUri`|Valore ottenuto in precedenza da **hive.metastore.uris**.|
|`spark.security.credentials.hiveserver2.enabled`|`false`per la modalità di distribuzione del client YARN.|
|`spark.hadoop.hive.zookeeper.quorum`|Il valore ottenuto in precedenza da **hive.zookeeper.quorum**.|

Salvare le modifiche e riavviare i componenti in base alle esigenze.

## <a name="using-the-hive-warehouse-connector"></a>Utilizzo del connettore Hive Warehouse

### <a name="connecting-and-running-queries"></a>Connessione ed esecuzione di query

È possibile scegliere tra diversi metodi per connettersi al cluster Interactive Query ed eseguire query utilizzando Hive Warehouse Connector. I metodi supportati includono i seguenti strumenti:

* [spark-shell](../spark/apache-spark-shell.md)
* PySpark
* spark-submit
* [Zeppelin](../spark/apache-spark-zeppelin-notebook.md)
* [Livy](../spark/apache-spark-livy-rest-interface.md)

Tutti gli esempi forniti in questo articolo verranno eseguiti tramite spark-shell.

Per avviare una sessione di spark-shell, procedere come segue:

1. SSH nel nodo head per il cluster Apache Spark. Per ulteriori informazioni sulla connessione al cluster con SSH, vedere [Connect to HDInsight (Apache Hadoop) using SSH](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

1. Immettere il seguente comando per avviare la shell spark:

    ```bash
    spark-shell --master yarn \
    --jars /usr/hdp/current/hive_warehouse_connector/hive-warehouse-connector-assembly-<STACK_VERSION>.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    ```

    Verrà visualizzato un messaggio `scala>` di benvenuto e un prompt in cui è possibile immettere i comandi.

1. Dopo aver avviato la shell spark, è possibile avviare un'istanza di Hive Warehouse Connector utilizzando i comandi seguenti:

    ```scala
    import com.hortonworks.hwc.HiveWarehouseSession
    val hive = HiveWarehouseSession.session(spark).build()
    ```

### <a name="connecting-and-running-queries-on-enterprise-security-package-esp-clusters"></a>Connessione ed esecuzione di query su cluster ESP (Enterprise Security Package)

Il pacchetto Enterprise Security (ESP) offre funzionalità di livello enterprise come l'autenticazione basata su Active Directory, il supporto multiutente e il controllo degli accessi in base al ruolo per i cluster Apache Hadoop in Azure HDInsight.The Enterprise Security Package (ESP) provides enterprise-grade capabilities like Active Directory-based authentication, multi-user support, and role-based access control for Apache Hadoop clusters in Azure HDInsight. Per ulteriori informazioni su ESP, vedere [Usare il pacchetto di sicurezza aziendale in HDInsight.](../domain-joined/apache-domain-joined-architecture.md)

1. SSH nel nodo head per il cluster Apache Spark. Per ulteriori informazioni sulla connessione al cluster con SSH, vedere [Connect to HDInsight (Apache Hadoop) using SSH](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

1. Digitare `kinit` e accedere con un utente di dominio.

1. Avviare spark-shell con l'elenco completo dei parametri di configurazione come illustrato di seguito. Tutti i valori in lettere maiuscole tra parentesi angolari devono essere specificati in base al cluster. Se è necessario individuare i valori da immettere per uno dei parametri riportati di seguito, vedere la sezione relativa all'impostazione di [Hive Warehouse Connector](#hive-warehouse-connector-setup).:

    ```bash
    spark-shell --master yarn \
    --jars /usr/hdp/current/hive_warehouse_connector/hive-warehouse-connector-assembly-<STACK_VERSION>.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    --conf spark.hadoop.hive.llap.daemon.service.hosts='<LLAP_APP_NAME>'
    --conf spark.sql.hive.hiveserver2.jdbc.url='jdbc:hive2://<ZOOKEEPER_QUORUM>;serviceDiscoveryMode=zookeeper;zookeeperNamespace=hiveserver2-interactive'
    --conf spark.datasource.hive.warehouse.load.staging.dir='<STAGING_DIR>'
    --conf spark.datasource.hive.warehouse.metastoreUri='<METASTORE_URI>'
    --conf spark.hadoop.hive.zookeeper.quorum='<ZOOKEEPER_QUORUM>'
   ```

### <a name="creating-spark-dataframes-from-hive-queries"></a>Creazione di frame di dati Spark dalle query HiveCreating Spark DataFrames from Hive queries

I risultati di tutte le query che utilizzano la libreria HWC vengono restituiti come frame di dati. Negli esempi seguenti viene illustrato come creare una query di base.

```scala
hive.setDatabase("default")
val df = hive.executeQuery("select * from hivesampletable")
df.filter("state = 'Colorado'").show()
```

I risultati della query sono Spark DataFrames, che possono essere utilizzati con le librerie Spark come MLIB e SparkSQL.

### <a name="writing-out-spark-dataframes-to-hive-tables"></a>Scrittura di frame di dati Spark nelle tabelle HiveWriting out Spark DataFrames to Hive tables

Spark non supporta in modo nativo la scrittura nelle tabelle ACID gestite di Hive. Utilizzando HWC, tuttavia, è possibile scrivere qualsiasi frame di dati in una tabella Hive.Using HWC, however, you can write out any DataFrame to a Hive table. È possibile visualizzare questa funzionalità al lavoro nell'esempio seguente:You can see this functionality at work in the following example:

1. Creare una `sampletable_colorado` tabella denominata e specificarne le colonne utilizzando il comando seguente:

    ```scala
    hive.createTable("sampletable_colorado").column("clientid","string").column("querytime","string").column("market","string").column("deviceplatform","string").column("devicemake","string").column("devicemodel","string").column("state","string").column("country","string").column("querydwelltime","double").column("sessionid","bigint").column("sessionpagevieworder","bigint").create()
    ```

1. Filtrare `hivesampletable` la tabella `state` in `Colorado`cui la colonna è uguale a . Questa query della tabella Hive viene restituita come frame di dati Spark.This query of the Hive table is returned as a Spark DataFrame. Quindi il frame di dati viene `sampletable_colorado` salvato `write` nella tabella Hive utilizzando la funzione.

    ```scala
    hive.table("hivesampletable").filter("state = 'Colorado'").write.format(HiveWarehouseSession.HIVE_WAREHOUSE_CONNECTOR).option("table","sampletable_colorado").save()
    ```

1. Visualizzare i risultati con il seguente comando:

    ```scala
    hive.table("sampletable_colorado").show()
    ```

    ![Connettore warehouse dell'hive Mostra tabella hive](./media/apache-hive-warehouse-connector/hive-warehouse-connector-show-hive-table.png)

### <a name="structured-streaming-writes"></a>Scritture di streaming strutturate

Utilizzando Hive Warehouse Connector, è possibile usare lo streaming Spark per scrivere dati nelle tabelle Hive.Using Hive Warehouse Connector, you can use Spark streaming to write data into Hive tables.

Seguire i passaggi seguenti per creare un esempio di Hive Warehouse Connector che inserisce i dati da un flusso Spark sulla porta localhost 9999 in una tabella Hive.Follow the steps below to create a Hive Warehouse Connector example that ingests data from a Spark stream on localhost port 9999 into a Hive table.

1. Seguire i passaggi descritti in [Connessione ed esecuzione di query](#connecting-and-running-queries).

1. Iniziare il flusso di spark con il seguente comando:

    ```scala
    val lines = spark.readStream.format("socket").option("host", "localhost").option("port",9999).load()
    ```

1. Generare i dati per il flusso Spark creato, attenendosi alla procedura seguente:
    1. Aprire una seconda sessione SSH nello stesso cluster Spark.
    1. Al prompt dei comandi digitare `nc -lk 9999`. Questo comando utilizza l'utilità netcat per inviare dati dalla riga di comando alla porta specificata.

1. Tornare alla prima sessione SSH e creare una nuova tabella Hive per contenere i dati di streaming. Nella shell spark, immettere il seguente comando:

    ```scala
    hive.createTable("stream_table").column("value","string").create()
    ```

1. Scrivere quindi i dati di streaming nella tabella appena creata utilizzando il comando seguente:

    ```scala
    lines.filter("value = 'HiveSpark'").writeStream.format(HiveWarehouseSession.STREAM_TO_STREAM).option("database", "default").option("table","stream_table").option("metastoreUri",spark.conf.get("spark.datasource.hive.warehouse.metastoreUri")).option("checkpointLocation","/tmp/checkpoint1").start()
    ```

    >[!Important]
    > Le `metastoreUri` `database` opzioni e devono attualmente essere impostate manualmente a causa di un problema noto in Apache Spark. Per ulteriori informazioni su questo problema, vedere [SPARK-25460](https://issues.apache.org/jira/browse/SPARK-25460).

1. Tornare alla seconda sessione SSH e immettere i seguenti valori:

    ```bash
    foo
    HiveSpark
    bar
    ```

1. Tornare alla prima sessione SSH e prendere nota della breve attività. Utilizzare il comando seguente per visualizzare i dati:

    ```scala
    hive.table("stream_table").show()
    ```

Utilizzare **Ctrl e C** per arrestare netcat nella seconda sessione SSH. Utilizzare `:q` per uscire da spark-shell nella prima sessione SSH.

### <a name="securing-data-on-spark-esp-clusters"></a>Protezione dei dati nei cluster ESP di Spark

1. Creare una `demo` tabella con alcuni dati di esempio immettendo i comandi seguenti:Create a table with some sample data by entering the following commands:

    ```scala
    create table demo (name string);
    INSERT INTO demo VALUES ('HDinsight');
    INSERT INTO demo VALUES ('Microsoft');
    INSERT INTO demo VALUES ('InteractiveQuery');
    ```

1. Visualizzare il contenuto della tabella con il comando seguente. Prima di applicare il `demo` criterio, la tabella mostra la colonna completa.

    ```scala
    hive.executeQuery("SELECT * FROM demo").show()
    ```

    ![tabella demo prima di applicare la politica ranger](./media/apache-hive-warehouse-connector/hive-warehouse-connector-table-before-ranger-policy.png)

1. Applicare un criterio di maschera di colonna che mostri solo gli ultimi quattro caratteri della colonna.  
    1. Passare all'interfaccia utente `https://CLUSTERNAME.azurehdinsight.net/ranger/`di Amministrazione Ranger all'indirizzo .
    1. Fare clic sul servizio Hive per il cluster in **Hive**.
        ![ranger service manager](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-service-manager.png)
    1. Fare clic sulla scheda **Mascheramento** e quindi **su Aggiungi nuovo criterio**

        ![Hive warehouse connector ranger hive policy](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-hive-policy-list.png)

    a. Specificare il nome del criterio desiderato. Selezionare database: **Default**, Hive table: **demo**, Hive column: **name**, User : **rsadmin2**, Access Types: **select**e **Partial mask: show last 4** from the Select **Masking Option** menu. Fare clic su **Aggiungi**.
                ![creare criteri](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-create-policy.png)
1. Visualizzare nuovamente il contenuto della tabella. Dopo aver applicato il criterio del ranger, possiamo vedere solo gli ultimi quattro caratteri della colonna.

    ![tavolo demo dopo l'applicazione di politica ranger](./media/apache-hive-warehouse-connector/hive-warehouse-connector-table-after-ranger-policy.png)

## <a name="next-steps"></a>Passaggi successivi

* [Usare Interactive Query in HDInsight](./apache-interactive-query-get-started.md)
* [Esempi di interazione con Hive Warehouse Connector utilizzando zeppelin, Livy, spark-submit e pyspark](https://community.hortonworks.com/articles/223626/integrating-apache-hive-with-apache-spark-hive-war.html)
