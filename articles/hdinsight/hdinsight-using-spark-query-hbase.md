---
title: Usare Spark per leggere e scrivere dati HBase - Azure HDInsight
description: Usare il connettore HBase Spark per leggere e scrivere i dati da un cluster Spark a un cluster HBase.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/20/2020
ms.openlocfilehash: e5d9d4f215752d95ee1d676e8a5b126b6d0d3ab2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82190623"
---
# <a name="use-apache-spark-to-read-and-write-apache-hbase-data"></a>Usare Apache Spark per leggere e scrivere dati Apache HBase

Le query in Apache HBase vengono in genere eseguite con l'API di basso livello corrispondente (scan, get e put) o con una sintassi SQL tramite Apache Phoenix. Apache fornisce anche il Apache Spark connettore HBase. Il connettore è un'alternativa praticabile ed efficiente per eseguire query e modificare i dati archiviati da HBase.

## <a name="prerequisites"></a>Prerequisiti

* Due cluster HDInsight distinti distribuiti nella stessa [rete virtuale](./hdinsight-plan-virtual-network-deployment.md). Una HBase e una Spark con almeno Spark 2,1 (HDInsight 3,6) installata. Per altre informazioni, vedere [Creare cluster basati su Linux in HDInsight tramite il portale di Azure](hdinsight-hadoop-create-linux-clusters-portal.md).

* Lo schema URI per l'archiviazione primaria dei cluster. Questo schema è wasb://per l'archiviazione BLOB di Azure `abfs://` , per Azure Data Lake Storage Gen2 o adl://per Azure Data Lake storage Gen1. Se il trasferimento sicuro è abilitato per l'archiviazione BLOB, l'URI `wasbs://`è.  Vedere anche l'articolo sul [trasferimento sicuro](../storage/common/storage-require-secure-transfer.md).

## <a name="overall-process"></a>Processo generale

Il processo generale per l'abilitazione di un cluster Spark per l'esecuzione di query sul cluster HDInsight è il seguente:

1. Preparare alcuni dati di esempio in HBase.
2. Acquisire il file hbase-site.xml dalla cartella di configurazione del cluster HBase (/etc/hbase/conf).
3. Posizionare una copia del file hbase-site.xml nella cartella di configurazione di Spark 2 (/etc/spark2/conf).
4. Eseguire `spark-shell` facendo riferimento al connettore HBase Spark dalle relative coordinate Maven nell'opzione `packages`.
5. Definire un catalogo corrispondente allo schema da Spark a HBase.
6. Interagire con i dati di HBase tramite le API RDD o DataFrame.

## <a name="prepare-sample-data-in-apache-hbase"></a>Preparare i dati di esempio in Apache HBase

In questo passaggio viene creata e popolata una tabella in Apache HBase che è quindi possibile eseguire una query usando Spark.

1. Usare il `ssh` comando per connettersi al cluster HBase. Modificare il comando seguente sostituendo `HBASECLUSTER` con il nome del cluster HBase, quindi immettere il comando:

    ```cmd
    ssh sshuser@HBASECLUSTER-ssh.azurehdinsight.net
    ```

2. Usare il `hbase shell` comando per avviare la shell interattiva di HBase. Immettere il comando seguente nella connessione SSH:

    ```bash
    hbase shell
    ```

3. Usare il `create` comando per creare una tabella HBase con famiglie a due colonne. Immettere il comando seguente:

    ```hbase
    create 'Contacts', 'Personal', 'Office'
    ```

4. Utilizzare il `put` comando per inserire i valori in una colonna specificata in una riga specificata di una determinata tabella. Immettere il comando seguente:

    ```hbase
    put 'Contacts', '1000', 'Personal:Name', 'John Dole'
    put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
    put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
    put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
    put 'Contacts', '8396', 'Personal:Name', 'Calvin Raji'
    put 'Contacts', '8396', 'Personal:Phone', '230-555-0191'
    put 'Contacts', '8396', 'Office:Phone', '230-555-0191'
    put 'Contacts', '8396', 'Office:Address', '5415 San Gabriel Dr.'
    ```

5. Usare il `exit` comando per arrestare la shell interattiva di HBase. Immettere il comando seguente:

    ```hbase
    exit
    ```

## <a name="copy-hbase-sitexml-to-spark-cluster"></a>Copiare HBase-site. XML nel cluster Spark

Copiare HBase-site. XML dalla risorsa di archiviazione locale alla radice della risorsa di archiviazione predefinita del cluster Spark.  Modificare il comando seguente per riflettere la configurazione.  Quindi, dalla sessione SSH aperta al cluster HBase, immettere il comando:

| Valore di sintassi | Nuovo valore|
|---|---|
|[Schema URI](hdinsight-hadoop-linux-information.md#URI-and-scheme) | Modificare per riflettere l'archiviazione.  La sintassi seguente è relativa all'archiviazione BLOB con trasferimento sicuro abilitato.|
|`SPARK_STORAGE_CONTAINER`|Sostituire con il nome del contenitore di archiviazione predefinito usato per il cluster Spark.|
|`SPARK_STORAGE_ACCOUNT`|Sostituire con il nome dell'account di archiviazione predefinito usato per il cluster Spark.|

```bash
hdfs dfs -copyFromLocal /etc/hbase/conf/hbase-site.xml wasbs://SPARK_STORAGE_CONTAINER@SPARK_STORAGE_ACCOUNT.blob.core.windows.net/
```

Uscire quindi dalla connessione SSH al cluster HBase.

```bash
exit
```

## <a name="put-hbase-sitexml-on-your-spark-cluster"></a>Posizionare il file hbase-site.xml nel cluster Spark

1. Connettersi al nodo head del cluster Spark tramite SSH. Modificare il comando seguente sostituendo `SPARKCLUSTER` con il nome del cluster Spark e quindi immettere il comando:

    ```cmd
    ssh sshuser@SPARKCLUSTER-ssh.azurehdinsight.net
    ```

2. Immettere il comando seguente per copiare `hbase-site.xml` dalla risorsa di archiviazione predefinita del cluster Spark alla cartella di configurazione di Spark 2 nell'archivio locale del cluster:

    ```bash
    sudo hdfs dfs -copyToLocal /hbase-site.xml /etc/spark2/conf
    ```

## <a name="run-spark-shell-referencing-the-spark-hbase-connector"></a>Eseguire la shell di Spark facendo riferimento al connettore HBase Spark

1. Dalla sessione SSH aperta al cluster Spark, immettere il comando seguente per avviare una shell di Spark:

    ```bash
    spark-shell --packages com.hortonworks:shc-core:1.1.1-2.1-s_2.11 --repositories https://repo.hortonworks.com/content/groups/public/
    ```  

2. Tenere aperta l'istanza della shell di Spark e continuare con il passaggio successivo.

## <a name="define-a-catalog-and-query"></a>Definire un catalogo ed eseguire una query

In questo passaggio, definire un oggetto catalogo corrispondente allo schema da Apache Spark ad Apache HBase.  

1. In Open Spark Shell immettere le istruzioni seguenti `import` :

    ```scala
    import org.apache.spark.sql.{SQLContext, _}
    import org.apache.spark.sql.execution.datasources.hbase._
    import org.apache.spark.{SparkConf, SparkContext}
    import spark.sqlContext.implicits._
    ```  

1. Immettere il comando seguente per definire un catalogo per la tabella Contacts creata in HBase:

    ```scala
    def catalog = s"""{
        |"table":{"namespace":"default", "name":"Contacts"},
        |"rowkey":"key",
        |"columns":{
        |"rowkey":{"cf":"rowkey", "col":"key", "type":"string"},
        |"officeAddress":{"cf":"Office", "col":"Address", "type":"string"},
        |"officePhone":{"cf":"Office", "col":"Phone", "type":"string"},
        |"personalName":{"cf":"Personal", "col":"Name", "type":"string"},
        |"personalPhone":{"cf":"Personal", "col":"Phone", "type":"string"}
        |}
    |}""".stripMargin
    ```

    Il codice esegue le azioni seguenti:  

     a. Definire uno schema del catalogo per la tabella HBase denominata `Contacts`.  
     b. Identificare l'elemento rowkey come `key` ed eseguire il mapping dei nomi di colonna usati in Spark alla famiglia, al nome e al tipo di colonna usati in HBase.  
     c. L'elemento rowkey deve inoltre essere definito in dettaglio come colonna denominata (`rowkey`), con la famiglia di colonna specifica `cf``rowkey`.  

1. Immettere il comando seguente per definire un metodo che fornisce un frame di frame attorno `Contacts` alla tabella in HBase:

    ```scala
    def withCatalog(cat: String): DataFrame = {
        spark.sqlContext
        .read
        .options(Map(HBaseTableCatalog.tableCatalog->cat))
        .format("org.apache.spark.sql.execution.datasources.hbase")
        .load()
     }
    ```

1. Creare un'istanza del DataFrame:

    ```scala
    val df = withCatalog(catalog)
    ```  

1. Recuperare il DataFrame:

    ```scala
    df.show()
    ```

    Dovrebbero essere visualizzate due righe di dati:

    ```output
    +------+--------------------+--------------+-------------+--------------+
    |rowkey|       officeAddress|   officePhone| personalName| personalPhone|
    +------+--------------------+--------------+-------------+--------------+
    |  1000|1111 San Gabriel Dr.|1-425-000-0002|    John Dole|1-425-000-0001|
    |  8396|5415 San Gabriel Dr.|  230-555-0191|  Calvin Raji|  230-555-0191|
    +------+--------------------+--------------+-------------+--------------+
    ```

1. Registrare una tabella temporanea in modo che sia possibile eseguire query nella tabella HBase tramite Spark SQL:

    ```scala
    df.createTempView("contacts")
    ```

1. Eseguire una query SQL sulla tabella `contacts`:

    ```scala
    spark.sqlContext.sql("select personalName, officeAddress from contacts").show
    ```

    Dovrebbero essere visualizzati risultati simili ai seguenti:

    ```output
    +-------------+--------------------+
    | personalName|       officeAddress|
    +-------------+--------------------+
    |    John Dole|1111 San Gabriel Dr.|
    |  Calvin Raji|5415 San Gabriel Dr.|
    +-------------+--------------------+
    ```

## <a name="insert-new-data"></a>Inserire nuovi dati

1. Per inserire un nuovo record di contatto, definire una classe `ContactRecord`:

    ```scala
    case class ContactRecord(
        rowkey: String,
        officeAddress: String,
        officePhone: String,
        personalName: String,
        personalPhone: String
        )
    ```

1. Creare un'istanza di `ContactRecord` e inserirlo in una matrice:

    ```scala
    val newContact = ContactRecord("16891", "40 Ellis St.", "674-555-0110", "John Jackson","230-555-0194")

    var newData = new Array[ContactRecord](1)
    newData(0) = newContact
    ```

1. Salvare la matrice dei nuovi dati in HBase:

    ```scala
    sc.parallelize(newData).toDF.write.options(Map(HBaseTableCatalog.tableCatalog -> catalog, HBaseTableCatalog.newTable -> "5")).format("org.apache.spark.sql.execution.datasources.hbase").save()
    ```

1. Esaminare i risultati:

    ```scala  
    df.show()
    ```

    Verrà visualizzato un output simile al seguente:

    ```output
    +------+--------------------+--------------+------------+--------------+
    |rowkey|       officeAddress|   officePhone|personalName| personalPhone|
    +------+--------------------+--------------+------------+--------------+
    |  1000|1111 San Gabriel Dr.|1-425-000-0002|   John Dole|1-425-000-0001|
    | 16891|        40 Ellis St.|  674-555-0110|John Jackson|  230-555-0194|
    |  8396|5415 San Gabriel Dr.|  230-555-0191| Calvin Raji|  230-555-0191|
    +------+--------------------+--------------+------------+--------------+
    ```

1. Chiudere la shell di Spark immettendo il comando seguente:

    ```scala
    :q
    ```

## <a name="next-steps"></a>Passaggi successivi

* [Connettore HBase di Apache Spark](https://github.com/hortonworks-spark/shc)
