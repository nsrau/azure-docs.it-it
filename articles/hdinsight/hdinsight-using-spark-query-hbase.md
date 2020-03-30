---
title: Usare Spark per leggere e scrivere dati HBase - Azure HDInsight
description: Usare il connettore HBase Spark per leggere e scrivere i dati da un cluster Spark a un cluster HBase.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/24/2020
ms.openlocfilehash: 888f24e13ce67c878592068927383dd8cbfefa60
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77623095"
---
# <a name="use-apache-spark-to-read-and-write-apache-hbase-data"></a>Usare Apache Spark per leggere e scrivere dati Apache HBase

Le query in Apache HBase vengono in genere eseguite con l'API di basso livello corrispondente (scan, get e put) o con una sintassi SQL tramite Apache Phoenix. Apache offre anche il connettore HBase di Apache Spark, che rappresenta una comoda ed efficace alternativa per eseguire query e modificare i dati archiviati da HBase.

## <a name="prerequisites"></a>Prerequisiti

* Due cluster HDInsight separati distribuiti nella stessa [rete virtuale.](./hdinsight-plan-virtual-network-deployment.md) Un HBase e uno Spark con almeno Spark 2.1 (HDInsight 3.6) installato. Per altre informazioni, vedere [Creare cluster basati su Linux in HDInsight tramite il portale di Azure](hdinsight-hadoop-create-linux-clusters-portal.md).

* Un client SSH. Per altre informazioni, vedere [Connettersi a HDInsight (Apache Hadoop) con SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

* Lo [schema URI](hdinsight-hadoop-linux-information.md#URI-and-scheme) per l'archiviazione primaria dei cluster. Questo schema è wasb:// per Archiviazione BLOB di Azure, abfs:// per Azure Data Lake Storage Gen2 o adl:// per Azure Data Lake Storage Gen1. Se il trasferimento sicuro è abilitato per `wasbs://`l'archiviazione BLOB, l'URI sarà .  Vedere anche l'articolo sul [trasferimento sicuro](../storage/common/storage-require-secure-transfer.md).

## <a name="overall-process"></a>Processo generale

Il processo generale per l'abilitazione di un cluster Spark per l'esecuzione di query sul cluster HDInsight è il seguente:

1. Preparare alcuni dati di esempio in HBase.
2. Acquisire il file hbase-site.xml dalla cartella di configurazione del cluster HBase (/etc/hbase/conf).
3. Posizionare una copia del file hbase-site.xml nella cartella di configurazione di Spark 2 (/etc/spark2/conf).
4. Eseguire `spark-shell` facendo riferimento al connettore HBase Spark dalle relative coordinate Maven nell'opzione `packages`.
5. Definire un catalogo corrispondente allo schema da Spark a HBase.
6. Interagire con i dati di HBase tramite le API RDD o DataFrame.

## <a name="prepare-sample-data-in-apache-hbase"></a>Preparare i dati di esempio in Apache HBase

In questo passaggio viene creata e popolata una tabella in Apache HBase su cui è possibile eseguire query tramite Spark.

1. Usare `ssh` il comando per connettersi al cluster HBase. Modificare il comando seguente `HBASECLUSTER` sostituendo con il nome del cluster HBase, quindi immettere il comando:

    ```cmd
    ssh sshuser@HBASECLUSTER-ssh.azurehdinsight.net
    ```

2. Utilizzare `hbase shell` il comando per avviare la shell interattiva HBase. Immettere il comando seguente nella connessione SSH:

    ```bash
    hbase shell
    ```

3. Utilizzare `create` il comando per creare una tabella HBase con famiglie a due colonne. Immettere il comando seguente:

    ```hbase
    create 'Contacts', 'Personal', 'Office'
    ```

4. Utilizzare `put` il comando per inserire valori in una colonna specificata in una riga specificata in una determinata tabella. Immettere il comando seguente:

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

5. Utilizzare `exit` il comando per arrestare la shell interattiva HBase. Immettere il comando seguente:

    ```hbase
    exit
    ```

## <a name="copy-hbase-sitexml-to-spark-cluster"></a>Copiare hbase-site.xml nel cluster Spark

Copiare il file hbase-site.xml dall'archiviazione locale alla radice dell'archiviazione predefinita del cluster Spark.  Modificare il comando seguente per riflettere la configurazione.  Quindi, dalla sessione SSH aperta al cluster HBase, immettere il comando:

| Valore della sintassi | Nuovo valore|
|---|---|
|[Schema URI](hdinsight-hadoop-linux-information.md#URI-and-scheme) | Modifica per riflettere lo spazio di archiviazione.  La sintassi seguente è per l'archiviazione BLOB con il trasferimento sicuro abilitato.|
|`SPARK_STORAGE_CONTAINER`|Sostituire con il nome del contenitore di archiviazione predefinito utilizzato per il cluster Spark.|
|`SPARK_STORAGE_ACCOUNT`|Sostituire con il nome dell'account di archiviazione predefinito usato per il cluster Spark.Replace with the default storage account name used for the Spark cluster.|

```bash
hdfs dfs -copyFromLocal /etc/hbase/conf/hbase-site.xml wasbs://SPARK_STORAGE_CONTAINER@SPARK_STORAGE_ACCOUNT.blob.core.windows.net/
```

Uscire quindi dalla connessione ssh al cluster HBase.

```bash
exit
```

## <a name="put-hbase-sitexml-on-your-spark-cluster"></a>Posizionare il file hbase-site.xml nel cluster Spark

1. Connettersi al nodo head del cluster Spark tramite SSH. Modificare il comando seguente `SPARKCLUSTER` sostituendo con il nome del cluster Spark, quindi immettere il comando:

    ```cmd
    ssh sshuser@SPARKCLUSTER-ssh.azurehdinsight.net
    ```

2. Immettere il comando `hbase-site.xml` seguente per copiare dall'archiviazione predefinita del cluster Spark alla cartella di configurazione di Spark 2 nell'archiviazione locale del cluster:

    ```bash
    sudo hdfs dfs -copyToLocal /hbase-site.xml /etc/spark2/conf
    ```

## <a name="run-spark-shell-referencing-the-spark-hbase-connector"></a>Eseguire la shell di Spark facendo riferimento al connettore HBase Spark

1. Dalla sessione SSH aperta al cluster Spark, immettere il comando seguente per avviare una shell di scintilla:

    ```bash
    spark-shell --packages com.hortonworks:shc-core:1.1.1-2.1-s_2.11 --repositories https://repo.hortonworks.com/content/groups/public/
    ```  

2. Tenere aperta l'istanza della shell di Spark e continuare con il passaggio successivo.

## <a name="define-a-catalog-and-query"></a>Definire un catalogo ed eseguire una query

In questo passaggio, definire un oggetto catalogo corrispondente allo schema da Apache Spark ad Apache HBase.  

1. Nella shell di apertura, immettere le seguenti `import` istruzioni:

    ```scala
    import org.apache.spark.sql.{SQLContext, _}
    import org.apache.spark.sql.execution.datasources.hbase._
    import org.apache.spark.{SparkConf, SparkContext}
    import spark.sqlContext.implicits._
    ```  

1. Immettere il comando seguente per definire un catalogo per la tabella Contatti creata in HBase:

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

    Il codice esegue le seguenti attività:  

     a. Definire uno schema del catalogo per la tabella HBase denominata `Contacts`.  
     b. Identificare l'elemento rowkey come `key` ed eseguire il mapping dei nomi di colonna usati in Spark alla famiglia, al nome e al tipo di colonna usati in HBase.  
     c. L'elemento rowkey deve inoltre essere definito in dettaglio come colonna denominata (`rowkey`), con la famiglia di colonna specifica `cf``rowkey`.  

1. Immettere il comando seguente per definire un `Contacts` metodo che fornisce un Frame di dati intorno alla tabella in HBase:Enter the command below to define a method that provides a DataFrame around your table in HBase:

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

1. Chiudere la shell dello spark immettendo il seguente comando:

    ```scala
    :q
    ```

## <a name="next-steps"></a>Passaggi successivi

* [Connettore HBase di Apache Spark](https://github.com/hortonworks-spark/shc)
