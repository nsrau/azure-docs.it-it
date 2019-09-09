---
title: Integrare Apache Spark e Apache Hive con il connettore del warehouse di hive
description: Informazioni su come integrare Apache Spark e Apache Hive con il connettore del warehouse di hive in Azure HDInsight.
ms.service: hdinsight
author: nakhanha
ms.author: nakhanha
ms.reviewer: hrasheed
ms.topic: conceptual
ms.date: 04/29/2019
ms.openlocfilehash: 068dc76112db39ad8db118062656013e20cfc2ab
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2019
ms.locfileid: "70811665"
---
# <a name="integrate-apache-spark-and-apache-hive-with-the-hive-warehouse-connector"></a>Integrare Apache Spark e Apache Hive con il connettore del warehouse di hive

Il connettore Apache Hive warehouse (HWC) è una libreria che consente di lavorare più facilmente con Apache Spark e Apache Hive supportando attività come lo spostamento dei dati tra i frame di dati Spark e le tabelle hive e l'indirizzamento dei dati di Spark streaming nelle tabelle hive. Il connettore del warehouse di hive funziona come un bridge tra Spark e hive. Supporta scala, Java e Python per lo sviluppo.

Il connettore del warehouse di hive ti permette di sfruttare le funzionalità esclusive di hive e Spark per creare potenti applicazioni Big-Data. Apache Hive offre supporto per le transazioni di database atomiche, coerenti, isolate e durevoli (ACID). Per altre informazioni su ACID e sulle transazioni in hive, vedere [transazioni hive](https://cwiki.apache.org/confluence/display/Hive/Hive+Transactions). Hive offre anche controlli di sicurezza dettagliati tramite Apache Ranger e l'elaborazione analitica a bassa latenza non disponibile in Apache Spark.

Apache Spark dispone di un'API di streaming strutturata che fornisce funzionalità di streaming non disponibili in Apache Hive. A partire da HDInsight 4,0, Apache Spark 2.3.1 e Apache Hive 3.1.0 hanno Metastore distinti, che possono rendere difficile l'interoperabilità. Il connettore del warehouse di hive rende più semplice l'uso combinato di Spark e hive. La libreria HWC carica i dati dai daemon LLAP in esecutori Spark in parallelo, rendendoli più efficienti e scalabili rispetto all'uso di una connessione JDBC standard da Spark ad hive.

![Architettura](./media/apache-hive-warehouse-connector/hive-warehouse-connector-architecture.png)

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

1. Creare un cluster HDInsight Spark 4,0 usando il portale di Azure con un account di archiviazione e una rete virtuale di Azure personalizzata. Per informazioni sulla creazione di un cluster in una rete virtuale di Azure, vedere [aggiungere HDInsight a una rete virtuale esistente](../../hdinsight/hdinsight-plan-virtual-network-deployment.md#existingvnet).
1. Creare un cluster LLAP (HDInsight Interactive query) 4,0 usando il portale di Azure con lo stesso account di archiviazione e la stessa rete virtuale di Azure del cluster Spark.
1. Copiare il contenuto del `/etc/hosts` file in headnode0 del cluster `/etc/hosts` Interactive query nel file in headnode0 del cluster Spark. Questo passaggio consente al cluster Spark di risolvere gli indirizzi IP dei nodi nel cluster Interactive query. Visualizzare il contenuto del file aggiornato con `cat /etc/hosts`. L'output dovrebbe avere un aspetto simile a quello mostrato nella schermata seguente.

    ![Visualizzazione del file hosts](./media/apache-hive-warehouse-connector/hive-warehouse-connector-hosts-file.png)

1. Configurare le impostazioni del cluster Spark attenendosi alla procedura seguente: 
    1. Passare a portale di Azure, selezionare cluster HDInsight e quindi fare clic sul nome del cluster.
    1. Sul lato destro, in **Dashboard cluster**selezionare **Ambari Home**.
    1. Nell'interfaccia utente Web di Ambariri fare clic su **SPARK2** > **configs** > **Custom SPARK2-defaults**.

        ![Configurazione di Spark2 Ambari](./media/apache-hive-warehouse-connector/hive-warehouse-connector-spark2-ambari.png)

    1. Impostare `spark.hadoop.hive.llap.daemon.service.hosts` sullo stesso valore della proprietà **hive. LLAP. daemon. Service. hosts** in * * Advanced hive-Interactive-site * *. Ad esempio: `@llap0`

    1. Impostare `spark.sql.hive.hiveserver2.jdbc.url` sulla stringa di connessione JDBC, che si connette a Hiveserver2 nel cluster Interactive query. La stringa di connessione per il cluster apparirà come URI di seguito. `CLUSTERNAME`è il nome del cluster Spark e i `user` parametri e `password` sono impostati sui valori corretti per il cluster.

        ```
        jdbc:hive2://LLAPCLUSTERNAME.azurehdinsight.net:443/;user=admin;password=PWD;ssl=true;transportMode=http;httpPath=/hive2
        ```

        >[!Note] 
        > L'URL JDBC deve contenere le credenziali per la connessione a Hiveserver2, inclusi un nome utente e una password.

    1. Impostare `spark.datasource.hive.warehouse.load.staging.dir` su una directory di gestione temporanea compatibile con HDFS appropriata. Se si dispone di due cluster diversi, la directory di staging deve essere una cartella nella directory di staging dell'account di archiviazione del cluster LLAP in modo che HiveServer2 possa accedervi. Ad esempio, `wasb://STORAGE_CONTAINER_NAME@STORAGE_ACCOUNT_NAME.blob.core.windows.net/tmp` dove `STORAGE_ACCOUNT_NAME` è il nome dell'account di archiviazione usato dal cluster e `STORAGE_CONTAINER_NAME` è il nome del contenitore di archiviazione.

    1. Impostare `spark.datasource.hive.warehouse.metastoreUri` con il valore dell'URI del Metastore del cluster Interactive query. Per trovare il metastoreUri per il cluster LLAP, cercare la proprietà **hive. Metastore. Uri** nell'interfaccia utente di Ambari per il cluster LLAP in **hive** > **Advanced** > **General**. Il valore sarà simile all'URI seguente:

        ```
        thrift://hn0-hwclla.0iv2nyrmse1uvp2caa4e34jkmf.cx.internal.cloudapp.net:9083,
        thrift://hn1-hwclla.0iv2nyrmse1uvp2caa4e34jkmf.cx.internal.cloudapp.net:9083
        ```

    1. Impostare `spark.security.credentials.hiveserver2.enabled` su`false` per la modalità di distribuzione del client Yarn.
    1. Impostare `spark.hadoop.hive.zookeeper.quorum` sul quorum Zookeeper del cluster LLAP. Per trovare il quorum Zookeeper per il cluster LLAP, cercare la proprietà **hive. Zookeeper. quorum** nell'interfaccia utente di Ambari per il cluster LLAP in **hive** > **Advanced** > **Advanced hive-site**. Il valore sarà simile alla stringa seguente:

        ```
        zk1-nkhvne.0iv2nyrmse1uvp2caa4e34jkmf.cx.internal.cloudapp.net:2181,
        zk4-nkhvne.0iv2nyrmse1uvp2caa4e34jkmf.cx.internal.cloudapp.net:2181,
        zk6-nkhvne.0iv2nyrmse1uvp2caa4e34jkmf.cx.internal.cloudapp.net:2181
        ```

Per testare la configurazione del connettore del warehouse di hive, seguire la procedura descritta nella sezione [connessione ed esecuzione di query](#connecting-and-running-queries).

## <a name="using-the-hive-warehouse-connector"></a>Uso di hive warehouse Connector

### <a name="connecting-and-running-queries"></a>Connessione ed esecuzione di query

È possibile scegliere tra alcuni metodi diversi per connettersi al cluster Interactive query ed eseguire query usando il connettore del warehouse di hive. I metodi supportati includono gli strumenti seguenti:

* [spark-shell](../spark/apache-spark-shell.md)
* PySpark
* Spark-Submit
* [Zeppelin](../spark/apache-spark-zeppelin-notebook.md)
* [Livy](../spark/apache-spark-livy-rest-interface.md)

Tutti gli esempi forniti in questo articolo verranno eseguiti tramite Spark-Shell.

Per avviare una sessione di Spark-Shell, seguire questa procedura:

1. Usare SSH in nodo head per il cluster. Per altre informazioni sulla connessione al cluster con SSH, vedere [connettersi a HDInsight (Apache Hadoop) tramite SSH](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).
1. Passare alla directory corretta digitando `cd /usr/hdp/current/hive_warehouse_connector` o fornire il percorso completo di tutti i file jar usati come parametri nel comando Spark-Shell.
1. Immettere il comando seguente per avviare la shell di Spark:

    ```bash
    spark-shell --master yarn \
    --jars /usr/hdp/3.0.1.0-183/hive_warehouse_connector/hive-warehouse-connector-assembly-1.0.0.3.0.1.0-183.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    ```

1. Verrà visualizzato un messaggio di benvenuto e un `scala>` prompt in cui è possibile immettere i comandi.

1. Dopo l'avvio di Spark-Shell, è possibile avviare un'istanza del connettore del warehouse di hive usando i comandi seguenti:

    ```scala
    import com.hortonworks.hwc.HiveWarehouseSession
    val hive = HiveWarehouseSession.session(spark).build()
    ```

### <a name="connecting-and-running-queries-on-enterprise-security-package-esp-clusters"></a>Connessione ed esecuzione di query in cluster Enterprise Security Package (ESP)

Il Enterprise Security Package (ESP) fornisce funzionalità di livello aziendale, ad esempio l'autenticazione basata su Active Directory, il supporto multiutente e il controllo degli accessi in base al ruolo per i cluster Apache Hadoop in Azure HDInsight. Per altre informazioni su ESP, vedere [usare Enterprise Security Package in HDInsight](../domain-joined/apache-domain-joined-architecture.md).

1. Seguire i passaggi 1 e 2 iniziali in [connessione ed esecuzione di query](#connecting-and-running-queries).
1. Digitare `kinit` ed effettuare l'accesso con un utente di dominio.
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

2. Filtrare la tabella `hivesampletable` in cui la `state` colonna è `Colorado`uguale a. Questa query della tabella hive viene restituita come dataframe di Spark. Il frame di frame viene quindi salvato nella tabella `sampletable_colorado` hive usando la `write` funzione.
    
    ```scala
    hive.table("hivesampletable").filter("state = 'Colorado'").write.format(HiveWarehouseSession.HIVE_WAREHOUSE_CONNECTOR).option("table","sampletable_colorado").save()
    ```

È possibile visualizzare la tabella risultante nello screenshot seguente.

![Mostra tabella risultante](./media/apache-hive-warehouse-connector/hive-warehouse-connector-show-hive-table.png)

### <a name="structured-streaming-writes"></a>Scritture di streaming strutturato

Con hive warehouse Connector è possibile usare Spark streaming per scrivere dati nelle tabelle hive.

Attenersi alla procedura seguente per creare un esempio di connettore del warehouse di hive che inserisce i dati da un flusso Spark sulla porta localhost 9999 in una tabella hive.

1. Aprire un terminale nel cluster Spark.
1. Avviare il flusso Spark con il comando seguente:

    ```scala
    val lines = spark.readStream.format("socket").option("host", "localhost").option("port",9988).load()
    ```

1. Generare i dati per il flusso Spark creato, seguendo questa procedura:
    1. Aprire un altro terminale nello stesso cluster Spark.
    1. Al prompt dei comandi digitare `nc -lk 9999`. Questo comando usa l'utilità netcat per inviare i dati dalla riga di comando alla porta specificata.
    1. Digitare le parole che si desidera inserire nel flusso Spark, seguito da ritorno a capo.
        ![dati di input nel flusso Spark](./media/apache-hive-warehouse-connector/hive-warehouse-connector-spark-stream-data-input.png)
1. Creare una nuova tabella hive per conservare i dati di streaming. Nella shell di Spark digitare i comandi seguenti:

    ```scala
    hive.createTable("stream_table").column("value","string").create()
    ```

1. Scrivere i dati di streaming nella tabella appena creata usando il comando seguente:

    ```scala
    lines.filter("value = 'HiveSpark'").writeStream.format(HiveWarehouseSession.STREAM_TO_STREAM).option("database", "default").option("table","stream_table").option("metastoreUri",spark.conf.get("spark.datasource.hive.warehouse.metastoreUri")).option("checkpointLocation","/tmp/checkpoint1").start()
    ```

    >[!Important]
    > Le `metastoreUri` opzioni `database` e devono essere attualmente impostate manualmente a causa di un problema noto nel Apache Spark. Per ulteriori informazioni su questo problema, vedere [Spark-25460](https://issues.apache.org/jira/browse/SPARK-25460).

1. È possibile visualizzare i dati inseriti nella tabella con il comando seguente:

    ```scala
    hive.table("stream_table").show()
    ```

### <a name="securing-data-on-spark-esp-clusters"></a>Protezione dei dati nei cluster ESP Spark

1. Per creare una `demo` tabella con alcuni dati di esempio, immettere i comandi seguenti:

    ```scala
    create table demo (name string);
    INSERT INTO demo VALUES ('HDinsight');
    INSERT INTO demo VALUES ('Microsoft');
    INSERT INTO demo VALUES ('InteractiveQuery');
    ```

1. Visualizzare il contenuto della tabella con il comando seguente. Prima di applicare il criterio, `demo` nella tabella viene visualizzata la colonna full.

    ```scala
    hive.executeQuery("SELECT * FROM demo").show()
    ```

    ![tabella demo prima di applicare i criteri Ranger](./media/apache-hive-warehouse-connector/hive-warehouse-connector-table-before-ranger-policy.png)

1. Applicare un criterio di mascheramento delle colonne che mostra solo gli ultimi quattro caratteri della colonna.  
    1. Passare all'interfaccia utente di amministrazione di `https://CLUSTERNAME.azurehdinsight.net/ranger/`Ranger all'indirizzo.
    1. Fare clic sul servizio Hive per il cluster in **hive**.
        ![gestore del servizio Ranger](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-service-manager.png)
    1. Fare clic sulla scheda **maschera** e quindi su **Aggiungi nuovo** ![elenco criteri Hive criteri](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-hive-policy-list.png)
    1. Specificare un nome di criterio desiderato. Selezione database: **Predefinita**, tabella hive: **demo**, colonna hive: **nome**, utente: **rsadmin2**, tipi di accesso: **selezione**e **maschera parziale: Mostra ultimi 4** dal menu **Opzioni di selezione maschera** . Fare clic su **Aggiungi**.
                ![Crea criterio](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-create-policy.png)
1. Visualizzare di nuovo il contenuto della tabella. Dopo aver applicato i criteri Ranger, è possibile visualizzare solo gli ultimi quattro caratteri della colonna.

    ![tabella demo dopo l'applicazione dei criteri Ranger](./media/apache-hive-warehouse-connector/hive-warehouse-connector-table-after-ranger-policy.png)

## <a name="next-steps"></a>Passaggi successivi

* [Usare Interactive Query in HDInsight](https://docs.microsoft.com/azure/hdinsight/interactive-query/apache-interactive-query-get-started)
* [Esempi di interazione con il connettore del warehouse di hive con Zeppelin, Livio, Spark-Submit e pyspark](https://community.hortonworks.com/articles/223626/integrating-apache-hive-with-apache-spark-hive-war.html)
