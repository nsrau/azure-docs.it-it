---
title: Integrazione di Apache Spark e Apache Hive con il connettore di Warehouse di Hive
description: Descrive come integrare Apache Spark e Apache Hive con il connettore di Warehouse di Hive in HDInsight di Azure.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 04/18/2019
ms.openlocfilehash: f5c4b07326bfd469720ab07b522aefb9ed074cbb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60484792"
---
# <a name="integrate-apache-spark-and-apache-hive-with-the-hive-warehouse-connector"></a>Integrazione di Apache Spark e Apache Hive con il connettore di Warehouse di Hive

Apache Hive Warehouse connettore (HWC) è una libreria che consente di semplificare il lavoro con Apache Spark e Apache Hive mediante il supporto di attività, ad esempio lo spostamento dei dati tra i relativi Dataframe e tabelle Hive e indirizzare anche Spark streaming dei dati nelle tabelle Hive. Hive Warehouse connettore funziona come un bridge tra Spark e Hive. Supporta Java, Scala e Python per lo sviluppo.

Il connettore di Warehouse Hive consente di sfruttare i vantaggi delle esclusive funzionalità di Hive e Spark per compilare potenti applicazioni big data. Apache Hive offre supporto per le transazioni di database che sono Atomic, Consistent, Isolated e durevole (ACID). Per ulteriori informazioni sulle proprietà ACID e le transazioni in Hive, vedere [Hive transazioni](https://cwiki.apache.org/confluence/display/Hive/Hive+Transactions). Hive offre anche i controlli di sicurezza dettagliate tramite Apache Ranger e bassa latenza di elaborazione analitica non è disponibile in Apache Spark.

Apache Spark, ha un'API di Streaming strutturato che offre funzionalità di streaming non è disponibile in Apache Hive. Iniziare con Hortonworks Data Platform (HDP) 3.0, Apache Spark e Apache Hive sono i Metastore separati, questo possono complicare l'interoperabilità. Il connettore di Warehouse Hive rende più semplice usare Spark e Hive insieme. La libreria HWC carica i dati dal daemon LLAP per gli executor di Spark in parallelo, rendendo più efficiente e scalabile rispetto all'uso di una connessione JDBC standard tra Spark e Hive.

![Architettura](./media/apache-hive-warehouse-connector/hive-warehouse-connector-architecture.png)

Sono elencate alcune delle operazioni supportate dal connettore di Warehouse di Hive:

* Che descrive una tabella
* Creazione di una tabella per i dati in formato ORC
* Selezione di dati Hive e il recupero di un frame di dati
* La scrittura di un frame di dati in Hive in batch
* Esecuzione di un'istruzione update di Hive
* La lettura di dati della tabella da Hive, li trasforma in Spark e scriverla in una nuova tabella Hive
* La scrittura di un flusso Spark o frame di dati in Hive tramite HiveStreaming

## <a name="hive-warehouse-connector-setup"></a>Configurazione connettore di Warehouse di hive

Seguire questi passaggi per configurare il connettore di Warehouse Hive tra un cluster Spark e Interactive Query in HDInsight di Azure:

1. Creare un cluster HDInsight Spark 4.0 usando il portale di Azure con un account di archiviazione e una rete virtuale di Azure personalizzata. Per informazioni sulla creazione di un cluster in una rete virtuale di Azure, vedere [aggiungere HDInsight a una rete virtuale esistente](../../hdinsight/hdinsight-extend-hadoop-virtual-network.md#existingvnet).
1. Creare un cluster HDInsight Interactive Query (LLAP) 4.0 usando il portale di Azure con lo stesso account di archiviazione e rete virtuale di Azure del cluster Spark.
1. Copiare il contenuto del `/etc/hosts` file nel nodo head 0 del cluster Interactive Query il `/etc/hosts` file nel nodo head 0 del cluster Spark. Questo passaggio consentirà il cluster Spark risolvere gli indirizzi IP dei nodi nel cluster Interactive Query. Visualizzare il contenuto del file aggiornato con `cat /etc/hosts`. L'output dovrebbe essere simile a quello mostrato nello screenshot seguente.

    ![visualizzazione del file host](./media/apache-hive-warehouse-connector/hive-warehouse-connector-hosts-file.png)

1. Configurare le impostazioni del cluster Spark eseguendo i passaggi seguenti: 
    1. Passare al portale di Azure, selezionare i cluster HDInsight e quindi fare clic sul nome del cluster.
    1. Sul lato destro, sotto **dashboard Cluster**, selezionare **Ambari home**.
    1. Nell'interfaccia utente web Ambari, fare clic su **SPARK2** > **CONFIGS** > **Custom spark2-defaults**.

        ![Configurazione di Ambari Spark2](./media/apache-hive-warehouse-connector/hive-warehouse-connector-spark2-ambari.png)

    1. Impostare `spark.hadoop.hive.llap.daemon.service.hosts` sullo stesso valore della proprietà **nome dell'app LLAP** sotto **avanzate hive-interactive-env**. Ad esempio: `@llap0`

    1. Impostare `spark.sql.hive.hiveserver2.jdbc.url` alla stringa di connessione JDBC, che si connette a Hiveserver2 nel cluster Interactive Query. La stringa di connessione per il cluster sarà simile a URI riportato di seguito. `CLUSTERNAME` è il nome del cluster Spark e il `user` e `password` parametri vengono impostati i valori corretti per il cluster.

        ```
        jdbc:hive2://LLAPCLUSTERNAME.azurehdinsight.net:443/;user=admin;password=PWD;ssl=true;transportMode=http;httpPath=/hive2
        ```

        >[!Note] 
        > L'URL di JDBC deve contenere le credenziali per la connessione a Hiveserver2 incluso un nome utente e password.

    1. Impostare `spark.datasource.hive.warehouse.load.staging.dir` una directory di gestione temporanea compatibile con HDFS adatto. Se si dispone di due cluster diversi, la directory di gestione temporanea deve essere una cartella nella directory di gestione temporanea dell'account di archiviazione del cluster LLAP in modo che HiveServer2 possa accedervi. Ad esempio, `wasb://STORAGE_CONTAINER_NAME@STORAGE_ACCOUNT_NAME.blob.core.windows.net/tmp` in cui `STORAGE_ACCOUNT_NAME` è il nome dell'account di archiviazione in uso da parte del cluster, e `STORAGE_CONTAINER_NAME` è il nome del contenitore di archiviazione.

    1. Impostare `spark.datasource.hive.warehouse.metastoreUri` con il valore del metastore URI del cluster Interactive Query. Per trovare il metastoreUri per il cluster LLAP, cercare il **hive.metastore.uris** del cluster in proprietà nella UI di Ambari per il LLAP **Hive** > **avanzate**  >  **Generali**. Il valore avrà un aspetto simile all'URI seguente:

        ```
        thrift://hn0-hwclla.0iv2nyrmse1uvp2caa4e34jkmf.cx.internal.cloudapp.net:9083,
        thrift://hn1-hwclla.0iv2nyrmse1uvp2caa4e34jkmf.cx.internal.cloudapp.net:9083
        ```

    1. Impostare `spark.security.credentials.hiveserver2.enabled` a `false` per client YARN distribuisce la modalità.
    1. Impostare `spark.hadoop.hive.zookeeper.quorum` per il quorum di Zookeeper del Cluster LLAP. Per trovare il quorum di Zookeeper per il cluster LLAP, cercare il **hive.zookeeper.quorum** del cluster in proprietà nella UI di Ambari per il LLAP **Hive** > **avanzate**  >  **Avanzate hive-site**. Il valore avrà un aspetto simile alla stringa seguente:

        ```
        zk1-nkhvne.0iv2nyrmse1uvp2caa4e34jkmf.cx.internal.cloudapp.net:2181,
        zk4-nkhvne.0iv2nyrmse1uvp2caa4e34jkmf.cx.internal.cloudapp.net:2181,
        zk6-nkhvne.0iv2nyrmse1uvp2caa4e34jkmf.cx.internal.cloudapp.net:2181
        ```

Per testare la configurazione del connettore Warehouse di Hive, seguire i passaggi nella sezione [le query in esecuzione e che si connette](#connecting-and-running-queries).

## <a name="using-the-hive-warehouse-connector"></a>Uso del connettore di Warehouse di Hive

### <a name="connecting-and-running-queries"></a>La connessione ed esecuzione di query

È possibile scegliere tra diversi metodi per connettersi al cluster Interactive Query ed eseguire query usando il connettore di Warehouse di Hive. Metodi supportati includono i seguenti strumenti:

* [spark-shell](../spark/apache-spark-shell.md)
* PySpark
* spark-submit
* [Zeppelin](../spark/apache-spark-zeppelin-notebook.md)
* [Livy](../spark/apache-spark-livy-rest-interface.md)

Tutti gli esempi forniti in questo articolo verranno eseguiti tramite spark-shell.

Per avviare una sessione di spark-shell, eseguire i passaggi seguenti:

1. SSH nel nodo head del cluster. Per altre informazioni sulla connessione al cluster con SSH, vedere [Connetti a HDInsight (Apache Hadoop) usando SSH](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).
1. Passare alla directory corretta digitando `cd /usr/hdp/current/hive_warehouse_connector` oppure specificare il percorso completo a tutti i file con estensione jar usati come parametri nel comando shell di spark.
1. Immettere il comando seguente per avviare la shell di spark:

    ```bash
    spark-shell --master yarn \
    --jars /usr/hdp/3.0.1.0-183/hive_warehouse_connector/hive-warehouse-connector-assembly-1.0.0.3.0.1.0-183.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    ```

1. Verrà visualizzato un messaggio di benvenuto e un `scala>` prompt in cui è possibile immettere i comandi.

1. Dopo l'avvio della shell di spark, può essere avviata un'istanza di Connector Warehouse di Hive usando i comandi seguenti:

    ```scala
    import com.hortonworks.hwc.HiveWarehouseSession
    val hive = HiveWarehouseSession.session(spark).build()
    ```

### <a name="connecting-and-running-queries-on-enterprise-security-package-esp-clusters"></a>La connessione ed esecuzione di query nei cluster Enterprise Security Package (ESP)

Il pacchetto di sicurezza aziendale (ESP) fornisce funzionalità di livello aziendale come autenticazione basata su Active Directory, supporto multiutente e controllo di accesso basato sui ruoli per cluster Apache Hadoop in HDInsight di Azure. Per altre informazioni su ESP, vedere [Introduzione alla sicurezza di Apache Hadoop con Enterprise Security Package](../domain-joined/apache-domain-joined-introduction.md).

1. Seguire i passaggi iniziali 1 e 2 sotto [le query in esecuzione e che si connette](#connecting-and-running-queries).
1. Tipo `kinit` e accedere con un utente di dominio.
1. Avviare spark-shell usando l'elenco completo dei parametri di configurazione come illustrato di seguito. Tutti i valori in tutte le lettere maiuscole tra parentesi quadre specificare base del cluster. Se è necessario individuare i valori di input per uno qualsiasi dei parametri seguenti, vedere la sezione sul [il programma di installazione di connettore Warehouse Hive](#hive-warehouse-connector-setup).:

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

### <a name="creating-spark-dataframes-from-hive-queries"></a>Creazione di Dataframe di Spark da query Hive

I risultati di tutte le query che usano la libreria HWC vengono restituiti come frame di dati. Gli esempi seguenti illustrano come creare una query di base.

```scala
hive.setDatabase("default")
val df = hive.executeQuery("select * from hivesampletable")
df.filter("state = 'Colorado'").show()
```

I risultati della query sono nei Dataframe di Spark, che può essere usato con le librerie di Spark come MLIB e SparkSQL.

### <a name="writing-out-spark-dataframes-to-hive-tables"></a>La scrittura nelle tabelle Hive nei Dataframe di Spark

Spark in modo nativo non supporta la scrittura in tabelle ACID gestite di Hive. Tramite HWC, tuttavia, è possibile scrivere qualsiasi frame di dati in una tabella Hive. È possibile visualizzare questa funzionalità durante il lavoro nell'esempio seguente:

1. Creare una tabella denominata `sampletable_colorado` e specificare le colonne usando il comando seguente:

    ```scala
    hive.createTable("sampletable_colorado").column("clientid","string").column("querytime","string").column("market","string").column("deviceplatform","string").column("devicemake","string").column("devicemodel","string").column("state","string").column("country","string").column("querydwelltime","double").column("sessionid","bigint").column("sessionpagevieworder","bigint").create()
    ```

2. Filtrare la tabella `hivesampletable` in cui la colonna `state` è uguale a `Colorado`. Questa query della tabella Hive viene restituita come un DataFrame di Spark. Quindi il frame di dati viene salvata nella tabella Hive `sampletable_colorado` utilizzando il `write` (funzione).
    
    ```scala
    hive.table("hivesampletable").filter("state = 'Colorado'").write.format(HiveWarehouseSession.HIVE_WAREHOUSE_CONNECTOR).option("table","sampletable_colorado").save()
    ```

È possibile visualizzare la tabella risultante nella schermata seguente.

![Mostra la tabella risulta](./media/apache-hive-warehouse-connector/hive-warehouse-connector-show-hive-table.png)

### <a name="structured-streaming-writes"></a>Structured streaming scritture

Connettore Warehouse di Hive, è possibile usare Spark streaming per scrivere dati nelle tabelle Hive.

Attenersi alla procedura seguente per creare un connettore di Warehouse Hive di esempio che inserisce dati da un flusso Spark sulla porta localhost 9999 in una tabella Hive.

1. Aprire un terminale nel cluster Spark.
1. Avviare lo streaming di spark con il comando seguente:

    ```scala
    val lines = spark.readStream.format("socket").option("host", "localhost").option("port",9988).load()
    ```

1. Generare dati per il flusso Spark che è stato creato, eseguendo le operazioni seguenti:
    1. Aprire un altro terminale nello stesso cluster di Spark.
    1. Al prompt dei comandi digitare `nc -lk 9999`. Questo comando Usa l'utilità netcat per inviare i dati dalla riga di comando per la porta specificata.
    1. Digitare le parole che si desidera lo streaming di Spark per l'inserimento, seguito dal carattere di ritorno.
        ![dati di input per lo streaming spark](./media/apache-hive-warehouse-connector/hive-warehouse-connector-spark-stream-data-input.png)
1. Creare una nuova tabella Hive per contenere i dati di streaming. In shell di spark, digitare i comandi seguenti:

    ```scala
    hive.createTable("stream_table").column("value","string").create()
    ```

1. Scrivere il flusso di dati nella tabella appena creata usando il comando seguente:

    ```scala
    lines.filter("value = 'HiveSpark'").writeStream.format(HiveWarehouseSession.STREAM_TO_STREAM).option("database", "default").option("table","stream_table").option("metastoreUri",spark.conf.get("spark.datasource.hive.warehouse.metastoreUri")).option("checkpointLocation","/tmp/checkpoint1").start()
    ```

    >[!Important]
    > Il `metastoreUri` e `database` opzioni devono essere impostate manualmente a causa di un problema noto di Apache Spark. Per altre informazioni su questo problema, vedere [SPARK-25460](https://issues.apache.org/jira/browse/SPARK-25460).

1. È possibile visualizzare i dati inseriti nella tabella con il comando seguente:

    ```scala
    hive.table("stream_table").show()
    ```

### <a name="securing-data-on-spark-esp-clusters"></a>Protezione dei dati nei cluster Spark ESP

1. Creare una tabella `demo` con alcuni dati di esempio immettendo i comandi seguenti:

    ```scala
    create table demo (name string);
    INSERT INTO demo VALUES ('HDinsight');
    INSERT INTO demo VALUES ('Microsoft');
    INSERT INTO demo VALUES ('InteractiveQuery');
    ```

1. Visualizzare il contenuto della tabella con il comando seguente. Prima di applicare i criteri, il `demo` tabella visualizza la colonna completa.

    ```scala
    hive.executeQuery("SELECT * FROM demo").show()
    ```

    ![tabella demo prima di applicare criteri di ranger](./media/apache-hive-warehouse-connector/hive-warehouse-connector-table-before-ranger-policy.png)

1. Applicare una colonna che mostra solo gli ultimi quattro caratteri della colonna di criteri di mascheramento.  
    1. Passare all'interfaccia utente di amministrazione di Ranger in `https://CLUSTERNAME.azurehdinsight.net/ranger/`.
    1. Fare clic sul servizio Hive per cluster **Hive**.
        ![tabella demo prima di applicare criteri di ranger](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-service-manager.png)
    1. Fare clic sui **maschera** scheda e quindi **Aggiungi nuovo criterio** ![elenco dei criteri](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-hive-policy-list.png)
    1. Specificare un nome di criterio desiderato. Selezionare il database: **Default**, nella tabella Hive: **demo**, colonna Hive: **nome**, utente: **rsadmin2**, i tipi di accesso: **selezionare**e **Maschera parziale: Mostra ultimo 4** dal **seleziona un'opzione di maschera** menu. Fare clic su **Aggiungi**.
                ![elenco criteri](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-create-policy.png)
1. Visualizzare di nuovo il contenuto della tabella. Dopo aver applicato i criteri di ranger, possiamo vedere solo gli ultimi quattro caratteri della colonna.

    ![tabella demo dopo l'applicazione dei criteri di ranger](./media/apache-hive-warehouse-connector/hive-warehouse-connector-table-after-ranger-policy.png)

## <a name="next-steps"></a>Passaggi successivi

* [Usare Interactive Query con HDInsight](https://docs.microsoft.com/azure/hdinsight/interactive-query/apache-interactive-query-get-started)
* [Esempi dell'interazione con connettore di Warehouse di Hive con Zeppelin, Livy, spark-submit, pyspark e](https://community.hortonworks.com/articles/223626/integrating-apache-hive-with-apache-spark-hive-war.html)
