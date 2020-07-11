---
title: Apache Spark e Apache Hive - Hive Warehouse Connector - Azure HDInsight
description: Informazioni su come integrare Apache Spark e Apache Hive con Hive Warehouse Connector in Azure HDInsight.
author: nis-goel
ms.author: nisgoel
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.date: 05/28/2020
ms.openlocfilehash: c2590a2c745969313ae73521dbcd110fbf3b7551
ms.sourcegitcommit: f844603f2f7900a64291c2253f79b6d65fcbbb0c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2020
ms.locfileid: "86221018"
---
# <a name="integrate-apache-spark-and-apache-hive-with-hive-warehouse-connector-in-azure-hdinsight"></a>Integrare Apache Spark e Apache Hive con Hive Warehouse Connector in Azure HDInsight

Apache Hive Warehouse Connector (HWC) è una libreria che facilita l'utilizzo di Apache Spark e Apache Hive. Supporta attività come il trasferimento di dati tra dataframe Spark e tabelle Hive, anche tramite l'indirizzamento dei flussi di dati Spark in tabelle Hive. Hive Warehouse Connector funge da bridge tra Spark e Hive. Supporta anche Scala, Java e Python come linguaggi di programmazione per lo sviluppo.

Hive Warehouse Connector consente di sfruttare le funzionalità esclusive di Hive e Spark per creare potenti applicazioni Big Data.

Apache Hive offre il supporto per le transazioni di database ACID, ossia atomiche, coerenti, isolate e durature. Per altre informazioni su ACID e sulle transazioni in Hive, vedere [Transazioni Hive](https://cwiki.apache.org/confluence/display/Hive/Hive+Transactions). Hive offre anche controlli di sicurezza dettagliati tramite Apache Ranger e LLAP (Low Latency Analytical Processing), non disponibili in Apache Spark.

L'API Structured Streaming di Apache Spark offre funzionalità di flusso non disponibili in Apache Hive. A partire da HDInsight 4.0, Apache Spark 2.3.1 e Apache Hive 3.1.0 hanno metastore separati, che possono ostacolare l'interoperabilità. Hive Warehouse Connector rende più semplice l'uso combinato di Spark e Hive. La libreria HWC carica i dati dai daemon LLAP agli esecutori Spark in parallelo. Questo processo la rende più efficiente e adattabile rispetto a una connessione JDBC standard da Spark a Hive.

![Architettura di Hive Warehouse Connector](./media/apache-hive-warehouse-connector/hive-warehouse-connector-architecture.png)

Alcune delle operazioni supportate da Hive Warehouse Connector sono:

* Descrizione di una tabella
* Creazione di una tabella per dati in formato ORC
* Selezione di dati Hive e recupero di un dataframe
* Scrittura di un dataframe in Hive in batch
* Esecuzione di un'istruzione di aggiornamento di Hive
* Lettura dei dati di una tabella da Hive, trasformazione in Spark e scrittura in una nuova tabella Hive
* Scrittura di un dataframe o di un flusso di Spark in Hive tramite HiveStreaming

## <a name="hive-warehouse-connector-setup"></a>Configurazione di Hive Warehouse Connector

> [!IMPORTANT]
> L'istanza interattiva di HiveServer2 installata nei cluster Spark 2,4 Enterprise Security Package non è supportata per l'uso con il connettore del warehouse di hive. Al contrario, è necessario configurare un cluster HiveServer2 interattivo separato per ospitare i carichi di lavoro interattivi di HiveServer2. Una configurazione del connettore del warehouse di hive che usa un cluster Spark 2,4 singolo non è supportata.

Hive Warehouse Connector richiede cluster distinti per i carichi di lavoro di Spark e Interactive Query. Eseguire le procedure seguenti per configurare questi cluster in Azure HDInsight.

### <a name="create-clusters"></a>Creare i cluster

1. Creare un cluster HDInsight Spark **4.0** con un account di archiviazione e una rete virtuale di Azure personalizzata. Per informazioni sulla creazione di un cluster in una rete virtuale di Azure, vedere [Aggiungere HDInsight a una rete virtuale esistente](../../hdinsight/hdinsight-plan-virtual-network-deployment.md#existingvnet).

1. Creare un cluster HDInsight Interactive Query (LLAP) **4.0** con lo stesso account di archiviazione e la stessa rete virtuale del cluster Spark.

### <a name="configure-hwc-settings"></a>Configurare le impostazioni di Hive Warehouse Connector

#### <a name="gather-preliminary-information"></a>Raccogliere le informazioni preliminari

1. In un Web browser passare a `https://LLAPCLUSTERNAME.azurehdinsight.net/#/main/services/HIVE`, dove LLAPCLUSTERNAME è il nome del cluster Interactive Query.

1. Passare a **Summary** > **HiveServer2 Interactive JDBC URL** (Riepilogo > URL JDBC interattivo per HiveServer2) e prendere nota del valore. Il valore può essere simile a: `jdbc:hive2://zk0-iqgiro.rekufuk2y2ce.bx.internal.cloudapp.net:2181,zk1-iqgiro.rekufuk2y2ce.bx.internal.cloudapp.net:2181,zk4-iqgiro.rekufuk2y2ce.bx.internal.cloudapp.net:2181/;serviceDiscoveryMode=zooKeeper;zooKeeperNamespace=hiveserver2-interactive`.

1. Passare a **Configs** > **Advanced** > **Advanced hive-site** > **hive.zookeeper.quorum** (Configurazioni > Avanzate > Impostazioni avanzate hive-site) e prendere nota del valore. Il valore può essere simile a: `zk0-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181,zk1-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181,zk4-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181`.

1. Passare a **Configs** > **Advanced** > **General** > **hive.metastore.uris** (Configurazioni > Avanzate > Generale) e prendere nota del valore. Il valore può essere simile a: `thrift://iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:9083,thrift://hn1-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:9083`.

1. Passare a **Configs** > **Advanced** > **Advanced hive-interactive-site** > **hive.llap.daemon.service.hosts** (Configurazioni > Avanzate > Impostazioni avanzate hive-interactive-site) e prendere nota del valore. Il valore può essere simile a: `@llap0`.

#### <a name="configure-spark-cluster-settings"></a>Configurare le impostazioni del cluster Spark

1. In un Web browser passare a `https://CLUSTERNAME.azurehdinsight.net/#/main/services/SPARK2/configs`, dove CLUSTERNAME è il nome del cluster Apache Spark.

1. Espandere **Custom Spark2-defaults** (Impostazioni predefinite Spark2 personalizzate).

    ![Configurazione di Apache Ambari Spark2](./media/apache-hive-warehouse-connector/hive-warehouse-connector-spark2-ambari.png)

1. Selezionare **Add Property** (Aggiungi proprietà) per aggiungere le configurazioni seguenti:

    | Configurazione | valore |
    |----|----|
    |`spark.datasource.hive.warehouse.load.staging.dir`|`wasbs://STORAGE_CONTAINER_NAME@STORAGE_ACCOUNT_NAME.blob.core.windows.net/tmp`. <br> Impostare su una directory di gestione temporanea compatibile con HDFS appropriata. Se si hanno due cluster diversi, la directory di gestione temporanea deve essere una cartella nella directory di gestione temporanea dell'account di archiviazione del cluster LLAP in modo che HiveServer2 possa accedervi.  Sostituire `STORAGE_ACCOUNT_NAME` con il nome dell'account di archiviazione usato dal cluster e `STORAGE_CONTAINER_NAME` con il nome del contenitore di archiviazione. |
    |`spark.sql.hive.hiveserver2.jdbc.url`| Valore ottenuto in precedenza da **HiveServer2 Interactive JDBC URL** |
    |`spark.datasource.hive.warehouse.metastoreUri`| Valore ottenuto in precedenza da **hive.metastore.uris**. |
    |`spark.security.credentials.hiveserver2.enabled`|`true` per la modalità cluster YARN e `false` per la modalità client YARN. |
    |`spark.hadoop.hive.zookeeper.quorum`| Valore ottenuto in precedenza da **hive.zookeeper.quorum**. |
    |`spark.hadoop.hive.llap.daemon.service.hosts`| Valore ottenuto in precedenza da **hive.llap.daemon.service.hosts**. |

1. Salvare le modifiche e riavviare tutti i componenti interessati.

### <a name="configure-hwc-for-enterprise-security-package-esp-clusters"></a>Configurare Hive Warehouse Connector per i cluster con Enterprise Security Package (ESP)

Enterprise Security Package fornisce funzionalità di livello aziendale come l'autenticazione basata su Active Directory, il supporto multiutente e il controllo degli accessi in base al ruolo per i cluster Apache Hadoop in Azure HDInsight. Per altre informazioni su ESP, vedere [Usare Enterprise Security Package in HDInsight](../domain-joined/apache-domain-joined-architecture.md).

Oltre alle configurazioni indicate nella sezione precedente, aggiungere la configurazione seguente per usare Hive Warehouse Connector nei cluster ESP.

1. Dall'interfaccia utente Web Ambari del cluster Spark passare a **Spark2** > **CONFIGS** > **Custom spark2-defaults** (Spark2 > Configurazioni > Impostazioni predefinite Spark2 personalizzate).

1. Aggiornare la proprietà seguente.

    | Configurazione | valore |
    |----|----|
    | `spark.sql.hive.hiveserver2.jdbc.url.principal`    | `hive/<llap-headnode>@<AAD-Domain>` |
    
    * Da un Web browser passare a `https://CLUSTERNAME.azurehdinsight.net/#/main/services/HIVE/summary` dove clustername è il nome del cluster Interactive query. Fare clic su **HiveServer2 Interactive**. Verrà visualizzato il nome di dominio completo (FQDN) del nodo head in cui è in esecuzione LLAP, come illustrato nella schermata. Sostituire `<llap-headnode>` con questo valore.

        ![Nodo head del connettore warehouse di hive](./media/apache-hive-warehouse-connector/head-node-hive-server-interactive.png)

    * Usare il [comando ssh](../hdinsight-hadoop-linux-use-ssh-unix.md) per connettersi al cluster Interactive query. Cercare `default_realm` il parametro nel `/etc/krb5.conf` file. Sostituire `<AAD-DOMAIN>` con questo valore come stringa maiuscola; in caso contrario, le credenziali non verranno trovate.

        ![Dominio AAD del connettore warehouse di hive](./media/apache-hive-warehouse-connector/aad-domain.png)

    * Ad esempio, `hive/hn0-ng36ll.mjry42ikpruuxgs2qy2kpg4q5e.cx.internal.cloudapp.net@PKRSRVUQVMAE6J85.D2.INTERNAL.CLOUDAPP.NET` .
    
1. Salvare le modifiche e riavviare i componenti interessati.

## <a name="hive-warehouse-connector-usage"></a>Utilizzo di Hive Warehouse Connector

È possibile scegliere tra alcuni metodi per connettersi al cluster Interactive Query ed eseguire query con Hive Warehouse Connector. I metodi supportati includono gli strumenti seguenti:

* [Spark-shell/PySpark](../spark/apache-spark-shell.md)
* [Spark-submit](#spark-submit)
* [Zeppelin](./apache-hive-warehouse-connector-zeppelin.md)


Ecco alcuni esempi di connessione a Hive Warehouse Connector da Spark.

### <a name="spark-shell"></a>Spark-shell

1. Usare il [comando ssh](../hdinsight-hadoop-linux-use-ssh-unix.md) per connettersi al cluster Apache Spark. Modificare il comando seguente sostituendo CLUSTERNAME con il nome del cluster in uso e quindi immettere il comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Nella sessione ssh eseguire il comando seguente per prendere nota della versione di `hive-warehouse-connector-assembly`:

    ```bash
    ls /usr/hdp/current/hive_warehouse_connector
    ```

1. Modificare il codice seguente con la versione di `hive-warehouse-connector-assembly` identificata in precedenza. Eseguire quindi il comando per avviare la shell Spark:

    ```bash
    spark-shell --master yarn \
    --jars /usr/hdp/current/hive_warehouse_connector/hive-warehouse-connector-assembly-<VERSION>.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    ```

1. Dopo l'avvio della shell Spark, è possibile avviare un'istanza di Hive Warehouse Connector usando i comandi seguenti:

    ```scala
    import com.hortonworks.hwc.HiveWarehouseSession
    val hive = HiveWarehouseSession.session(spark).build()
    ```

### <a name="spark-submit"></a>Spark-submit

Dopo aver compilato il codice Scala/Java insieme alle dipendenze in un file JAR di assembly, usare il comando seguente per avviare un'applicazione Spark. Sostituire `<VERSION>` e `<APP_JAR_PATH>` con i valori effettivi.

* Modalità client YARN
    
    ```scala
    spark-submit \
    --class myHwcApp \
    --master yarn \
    --deploy-mode client \
    --jars /usr/hdp/current/hive_warehouse_connector/hive-warehouse-connector-assembly-<VERSION>.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    /<APP_JAR_PATH>/myHwcAppProject.jar
    ```

* Modalità cluster YARN
    ```scala
    spark-submit \
    --class myHwcApp \
    --master yarn \
    --deploy-mode cluster \
    --jars /usr/hdp/current/hive_warehouse_connector/hive-warehouse-connector-assembly-<VERSION>.jar \
    --conf spark.security.credentials.hiveserver2.enabled=true
    /<APP_JAR_PATH>/myHwcAppProject.jar
    ```

Per Python aggiungere anche la configurazione seguente. 

    ```python
    --py-files /usr/hdp/current/hive_warehouse_connector/pyspark_hwc-<VERSION>.zip
    ```
    
## <a name="run-queries-on-enterprise-security-package-esp-clusters"></a>Eseguire query nei cluster con Enterprise Security Package (ESP)

Usare `kinit` prima di avviare il comando spark-shell o spark-submit. Sostituire USERNAME con il nome di un account di dominio con autorizzazioni di accesso al cluster, quindi eseguire il comando seguente:

```bash
kinit USERNAME
```

### <a name="securing-data-on-spark-esp-clusters"></a>Protezione dei dati nei cluster ESP Spark

1. Creare una tabella `demo` con alcuni dati di esempio immettendo i comandi seguenti:

    ```scala
    create table demo (name string);
    INSERT INTO demo VALUES ('HDinsight');
    INSERT INTO demo VALUES ('Microsoft');
    INSERT INTO demo VALUES ('InteractiveQuery');
    ```

1. Visualizzare il contenuto della tabella con il comando seguente. Prima dell'applicazione del criterio, nella tabella `demo` viene visualizzata la colonna completa.

    ```scala
    hive.executeQuery("SELECT * FROM demo").show()
    ```

    ![Tabella demo prima dell'applicazione del criterio di Ranger](./media/apache-hive-warehouse-connector/hive-warehouse-connector-table-before-ranger-policy.png)

1. Applicare un criterio di mascheramento colonna che mostra solo gli ultimi quattro caratteri della colonna.  
    1. Passare all'interfaccia utente di amministrazione di Ranger all'indirizzo `https://LLAPCLUSTERNAME.azurehdinsight.net/ranger/`.
    1. Fare clic sul servizio Hive del cluster in **Hive**.
        ![Gestione servizio Ranger](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-service-manager.png)
    1. Fare clic sulla scheda **Masking** (Mascheramento) e quindi su **Add New Policy** (Aggiungi nuovo criterio)

        ![Elenco dei criteri Hive Ranger di Hive Warehouse Connector](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-hive-policy-list.png)

    1. Specificare un nome per il criterio. Selezionare il database: **Default**, Hive table (Tabella Hive): **demo**, Hive column (Colonna Hive): **name**, User (Utente): **rsadmin2**, Access Types (Tipi di accesso): **select** e **Partial mask: show last 4** (Mascheramento parziale: mostra ultimi 4) dal menu **Select Masking Option** (Seleziona opzione di mascheramento). Scegliere **Aggiungi**.
                ![Creazione criterio](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-create-policy.png)
1. Visualizzare di nuovo il contenuto della tabella. Dopo l'applicazione del criterio Ranger, vengono visualizzati solo gli ultimi quattro caratteri della colonna.

    ![Tabella demo dopo l'applicazione del criterio di Ranger](./media/apache-hive-warehouse-connector/hive-warehouse-connector-table-after-ranger-policy.png)

## <a name="next-steps"></a>Passaggi successivi

* [Operazioni HWC e Apache Spark](./apache-hive-warehouse-connector-operations.md)
* [Usare Interactive Query in HDInsight](./apache-interactive-query-get-started.md)
* [Integrazione di HWC con Apache Zeppelin](./apache-hive-warehouse-connector-zeppelin.md)
* [Esempi di interazione con Hive Warehouse Connector tramite Zeppelin, Livy, spark-submit e pyspark](https://community.hortonworks.com/articles/223626/integrating-apache-hive-with-apache-spark-hive-war.html)
