---
title: Usare Spark per leggere e scrivere dati HBase - Azure HDInsight | Microsoft Docs
description: Usare il connettore HBase Spark per leggere e scrivere i dati da un cluster Spark a un cluster HBase.
services: hdinsight
documentationcenter: 
author: maxluk
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2018
ms.author: maxluk
ms.openlocfilehash: ccbcd1d9cb45da7076d73f71a2ed692e71816650
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/19/2018
---
# <a name="use-spark-to-read-and-write-hbase-data"></a>Usare Spark per leggere e scrivere dati HBase

Le query in Apache HBase vengono in genere eseguite con l'API di basso livello corrispondente (scan, get e put) o con una sintassi SQL tramite Phoenix. Apache offre anche il connettore HBase Spark, che rappresenta una comoda ed efficace alternativa per eseguire query e modificare i dati archiviati da HBase.

## <a name="prerequisites"></a>Prerequisiti

* Installazione di due cluster HDInsight separati, uno HBase e uno Spark con Spark 2.1 (HDInsight 3.6).
* Il cluster Spark deve comunicare direttamente con il cluster HBase con latenza minima, pertanto la configurazione consigliata prevede la distribuzione di entrambi i cluster nella stessa rete virtuale. Per altre informazioni, vedere [Creare cluster basati su Linux in HDInsight tramite il portale di Azure](hdinsight-hadoop-create-linux-clusters-portal.md).
* Accesso SSH a ogni cluster.
* Accesso all'archiviazione predefinita di ogni cluster.

## <a name="overall-process"></a>Processo generale

Il processo generale per l'abilitazione di un cluster Spark per l'esecuzione di query sul cluster HDInsight è il seguente:

1. Preparare alcuni dati di esempio in HBase.
2. Acquisire il file hbase-site.xml dalla cartella di configurazione del cluster HBase (/etc/hbase/conf).
3. Posizionare una copia del file hbase-site.xml nella cartella di configurazione di Spark 2 (/etc/spark2/conf).
4. Eseguire `spark-shell` facendo riferimento al connettore HBase Spark dalle relative coordinate Maven nell'opzione `packages`.
5. Definire un catalogo corrispondente allo schema da Spark a HBase.
6. Interagire con i dati di HBase tramite le API RDD o DataFrame.

## <a name="prepare-sample-data-in-hbase"></a>Preparare i dati di esempio in HBase

In questo passaggio, viene creata e popolata una semplice tabella in HBase, sulla quale è poi possibile eseguire query tramite Spark.

1. Connettersi al nodo head del cluster HBase tramite SSH. Per altre informazioni, vedere [Connettersi a HDInsight con SSH](hdinsight-hadoop-linux-use-ssh-unix.md).
2. Eseguire la shell HBase:

        hbase shell

3. Creare una tabella `Contacts` con le famiglie di colonne `Personal` e `Office`:

        create 'Contacts', 'Personal', 'Office'

4. Caricare alcune righe di dati di esempio:

        put 'Contacts', '1000', 'Personal:Name', 'John Dole'
        put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
        put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
        put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
        put 'Contacts', '8396', 'Personal:Name', 'Calvin Raji'
        put 'Contacts', '8396', 'Personal:Phone', '230-555-0191'
        put 'Contacts', '8396', 'Office:Phone', '230-555-0191'
        put 'Contacts', '8396', 'Office:Address', '5415 San Gabriel Dr.'

## <a name="acquire-hbase-sitexml-from-your-hbase-cluster"></a>Acquisire hbase-site.xml dal cluster HBase

1. Connettersi al nodo head del cluster HBase tramite SSH.
2. Copiare il file hbase-site.xml dall'archiviazione locale alla radice dello spazio di archiviazione predefinito del cluster HBase:

        hdfs dfs -copyFromLocal /etc/hbase/conf/hbase-site.xml /

3. Passare al cluster HBase tramite il [portale di Azure](https://portal.azure.com).
4. Selezionare Account di archiviazione. 

    ![Account di archiviazione](./media/hdinsight-using-spark-query-hbase/storage-accounts.png)

5. Selezionare l'account di archiviazione nell'elenco con un segno di spunta nella colonna Predefinito.

    ![Account di archiviazione predefinito](./media/hdinsight-using-spark-query-hbase/default-storage.png)

6. Nel riquadro Account di archiviazione selezionare il riquadro BLOB.

    ![Riquadro BLOB](./media/hdinsight-using-spark-query-hbase/blobs-tile.png)

7. Nell'elenco di contenitori selezionare il contenitore usato dal cluster HBase.
8. Nell'elenco dei file selezionare `hbase-site.xml`.

    ![HBase-site.xml](./media/hdinsight-using-spark-query-hbase/hbase-site-xml.png)

9. Nel pannello Proprietà BLOB selezionare Scarica e salvare `hbase-site.xml` in un percorso nel computer locale.

    ![Download](./media/hdinsight-using-spark-query-hbase/download.png)

## <a name="put-hbase-sitexml-on-your-spark-cluster"></a>Posizionare il file hbase-site.xml nel cluster Spark

1. Passare al cluster Spark tramite il [portale di Azure](https://portal.azure.com).
2. Selezionare Account di archiviazione.

    ![Account di archiviazione](./media/hdinsight-using-spark-query-hbase/storage-accounts.png)

3. Selezionare l'account di archiviazione nell'elenco con un segno di spunta nella colonna Predefinito.

    ![Account di archiviazione predefinito](./media/hdinsight-using-spark-query-hbase/default-storage.png)

4. Nel riquadro Account di archiviazione selezionare il riquadro BLOB.

    ![Riquadro BLOB](./media/hdinsight-using-spark-query-hbase/blobs-tile.png)

5. Nell'elenco di contenitori selezionare il contenitore usato dal cluster Spark.
6. Selezionare Carica.

    ![Caricamento](./media/hdinsight-using-spark-query-hbase/upload.png)

7. Selezionare il file `hbase-site.xml` scaricato in precedenza nel computer locale.

    ![Caricare hbase-site.xml](./media/hdinsight-using-spark-query-hbase/upload-selection.png)

8. Selezionare Carica.
9. Connettersi al nodo head del cluster Spark tramite SSH.
10. Copiare `hbase-site.xml` dallo spazio di archiviazione predefinito del cluster Spark alla cartella di configurazione di Spark 2 nello spazio di archiviazione locale del cluster:

        sudo hdfs dfs -copyToLocal /hbase-site.xml /etc/spark2/conf

## <a name="run-spark-shell-referencing-the-spark-hbase-connector"></a>Eseguire la shell di Spark facendo riferimento al connettore HBase Spark

1. Connettersi al nodo head del cluster Spark tramite SSH.
2. Avviare la shell di Spark specificando il pacchetto del connettore HBase Spark:

        spark-shell --packages com.hortonworks:shc-core:1.1.0-2.1-s_2.11

3. Tenere aperta l'istanza della shell di Spark e continuare con il passaggio successivo.

## <a name="define-a-catalog-and-query"></a>Definire un catalogo ed eseguire una query

In questo passaggio, definire un oggetto catalogo corrispondente allo schema da Spark a HBase. 

1. Nella shell di Spark aperta eseguire le istruzioni `import` seguenti:

        import org.apache.spark.sql.{SQLContext, _}
        import org.apache.spark.sql.execution.datasources.hbase._
        import org.apache.spark.{SparkConf, SparkContext}
        import spark.sqlContext.implicits._

2. Definire un catalogo per la tabella dei contatti creata in HBase:
    1. Definire uno schema del catalogo per la tabella HBase denominata `Contacts`.
    2. Identificare l'elemento rowkey come `key` ed eseguire il mapping dei nomi di colonna usati in Spark alla famiglia, al nome e al tipo di colonna usati in HBase.
    3. L'elemento rowkey deve inoltre essere definito in dettaglio come colonna denominata (`rowkey`), con la famiglia di colonna specifica `cf` `rowkey`.

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

3. Definire un metodo per fornire un DataFrame per la tabella `Contacts` in HBase:

            def withCatalog(cat: String): DataFrame = {
                spark.sqlContext
                .read
                .options(Map(HBaseTableCatalog.tableCatalog->cat))
                .format("org.apache.spark.sql.execution.datasources.hbase")
                .load()
            }

4. Creare un'istanza del DataFrame:

        val df = withCatalog(catalog)

5. Recuperare il DataFrame:

        df.show()

6. Dovrebbero essere visualizzate due righe di dati:

        +------+--------------------+--------------+-------------+--------------+
        |rowkey|       officeAddress|   officePhone| personalName| personalPhone|
        +------+--------------------+--------------+-------------+--------------+
        |  1000|1111 San Gabriel Dr.|1-425-000-0002|    John Dole|1-425-000-0001|
        |  8396|5415 San Gabriel Dr.|  230-555-0191|  Calvin Raji|  230-555-0191|
        +------+--------------------+--------------+-------------+--------------+

7. Registrare una tabella temporanea in modo che sia possibile eseguire query nella tabella HBase tramite Spark SQL:

        df.registerTempTable("contacts")

8. Eseguire una query SQL sulla tabella `contacts`:

        val query = spark.sqlContext.sql("select personalName, officeAddress from contacts")
        query.show()

9. Dovrebbero essere visualizzati risultati simili ai seguenti:

        +-------------+--------------------+
        | personalName|       officeAddress|
        +-------------+--------------------+
        |    John Dole|1111 San Gabriel Dr.|
        |  Calvin Raji|5415 San Gabriel Dr.|
        +-------------+--------------------+

## <a name="insert-new-data"></a>Inserire nuovi dati

1. Per inserire un nuovo record di contatto, definire una classe `ContactRecord`:

        case class ContactRecord(
            rowkey: String,
            officeAddress: String,
            officePhone: String,
            personalName: String,
            personalPhone: String
            )

2. Creare un'istanza di `ContactRecord` e inserirlo in una matrice:

        val newContact = ContactRecord("16891", "40 Ellis St.", "674-555-0110", "John Jackson","230-555-0194")

        var newData = new Array[ContactRecord](1)
        newData(0) = newContact

3. Salvare la matrice dei nuovi dati in HBase:

        sc.parallelize(newData).toDF.write
        .options(Map(HBaseTableCatalog.tableCatalog -> catalog))
        .format("org.apache.spark.sql.execution.datasources.hbase").save()

4. Esaminare i risultati:
    
        df.show()

5. Verrà visualizzato un output simile al seguente:

        +------+--------------------+--------------+------------+--------------+
        |rowkey|       officeAddress|   officePhone|personalName| personalPhone|
        +------+--------------------+--------------+------------+--------------+
        |  1000|1111 San Gabriel Dr.|1-425-000-0002|   John Dole|1-425-000-0001|
        | 16891|        40 Ellis St.|  674-555-0110|John Jackson|  230-555-0194|
        |  8396|5415 San Gabriel Dr.|  230-555-0191| Calvin Raji|  230-555-0191|
        +------+--------------------+--------------+------------+--------------+

## <a name="next-steps"></a>Passaggi successivi

* [Connettore HBase Spark](https://github.com/hortonworks-spark/shc)
