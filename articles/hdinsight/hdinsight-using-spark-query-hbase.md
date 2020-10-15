---
title: Usare Spark per leggere e scrivere dati HBase - Azure HDInsight
description: Usare il connettore HBase Spark per leggere e scrivere i dati da un cluster Spark a un cluster HBase.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 08/12/2020
ms.openlocfilehash: eb62cf099d7ccc133a207a843a8be3debf5c5454
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91308419"
---
# <a name="use-apache-spark-to-read-and-write-apache-hbase-data"></a>Usare Apache Spark per leggere e scrivere dati Apache HBase

Le query in Apache HBase vengono in genere eseguite con l'API di basso livello corrispondente (scan, get e put) o con una sintassi SQL tramite Apache Phoenix. Apache fornisce anche il Apache Spark connettore HBase. Il connettore rappresenta un'alternativa pratica ed efficiente per eseguire query e modificare i dati archiviati da HBase.

## <a name="prerequisites"></a>Prerequisiti

* Due cluster HDInsight distinti distribuiti nella stessa [rete virtuale](./hdinsight-plan-virtual-network-deployment.md). Una HBase e una Spark con almeno Spark 2,1 (HDInsight 3,6) installata. Per altre informazioni, vedere [Creare cluster basati su Linux in HDInsight tramite il portale di Azure](hdinsight-hadoop-create-linux-clusters-portal.md).

* Lo schema URI per l'archiviazione primaria dei cluster. Questo schema è wasb://per l'archiviazione BLOB di Azure, `abfs://` per Azure Data Lake storage Gen2 o ADL://per Azure Data Lake storage Gen1. Se il trasferimento sicuro è abilitato per l'archiviazione BLOB, l'URI è `wasbs://` .  Vedere anche l'articolo sul [trasferimento sicuro](../storage/common/storage-require-secure-transfer.md).

## <a name="overall-process"></a>Processo generale

Il processo di alto livello per consentire al cluster Spark di eseguire query sul cluster HBase è il seguente:

1. Preparare alcuni dati di esempio in HBase.
2. Acquisire il file di hbase-site.xml dalla cartella di configurazione del cluster HBase (/etc/HBase/conf) e inserire una copia di hbase-site.xml nella cartella di configurazione di Spark 2 (/etc/spark2/conf). (Facoltativo: usare lo script fornito dal team di HDInsight per automatizzare questo processo)
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
    
## <a name="run-scripts-to-set-up-connection-between-clusters"></a>Eseguire script per configurare la connessione tra cluster

Per configurare la comunicazione tra i cluster, seguire questa procedura per eseguire due script nei cluster. Questi script consentono di automatizzare il processo di copia dei file descritto nella sezione "configurare manualmente la comunicazione" riportata di seguito. 

* Lo script eseguito dal cluster HBase caricherà `hbase-site.xml` e HBase le informazioni di mapping IP nella risorsa di archiviazione predefinita collegata al cluster Spark. 
* Lo script che viene eseguito dal cluster Spark configura due processi cron per eseguire periodicamente due script Helper:  
    1.  Processo cron HBase: scaricare `hbase-site.xml` i nuovi file e il mapping IP HBase dall'account di archiviazione predefinito Spark al nodo locale
    2.  Processo cron Spark: controlla se si è verificato un ridimensionamento Spark e se il cluster è protetto. In tal caso, modificare `/etc/hosts` in modo da includere il mapping IP HBase archiviato localmente

__Nota__: prima di procedere, assicurarsi di aver aggiunto l'account di archiviazione del cluster Spark al cluster HBase come account di archiviazione secondario. Assicurarsi che gli script siano ordinati come indicato di seguito.


1. Usare l' [azione script](hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster) nel cluster HBase per applicare le modifiche con le considerazioni seguenti: 


    |Proprietà | Valore |
    |---|---|
    |URI script Bash|`https://hdiconfigactions.blob.core.windows.net/hbasesparkconnectorscript/connector-hbase.sh`|
    |Tipo/i di nodo|Region|
    |Parametri|`-s SECONDARYS_STORAGE_URL`|
    |Persisted|sì|

    * `SECONDARYS_STORAGE_URL` è l'URL dell'archiviazione predefinita lato Spark. Esempio di parametro: `-s wasb://sparkcon-2020-08-03t18-17-37-853z@sparkconhdistorage.blob.core.windows.net`


2.  Usare l'azione script nel cluster Spark per applicare le modifiche con le considerazioni seguenti:

    |Proprietà | Valore |
    |---|---|
    |URI script Bash|`https://hdiconfigactions.blob.core.windows.net/hbasesparkconnectorscript/connector-spark.sh`|
    |Tipo/i di nodo|Head, Worker, Zookeeper|
    |Parametri|`-s "SPARK-CRON-SCHEDULE"` (facoltativo) `-h "HBASE-CRON-SCHEDULE"` opzionale|
    |Persisted|sì|


    * È possibile specificare la frequenza con cui si desidera che il cluster verifichi automaticamente se l'aggiornamento viene eseguito. Impostazione predefinita:-s "*/1 * * * *"-h 0 (in questo esempio, Spark cron viene eseguito ogni minuto, mentre il cron HBase non viene eseguito)
    * Poiché HBase cron non è configurato per impostazione predefinita, è necessario eseguire di nuovo questo script quando si esegue il ridimensionamento nel cluster HBase. Se il cluster HBase si ridimensiona spesso, è possibile scegliere di configurare automaticamente il processo HBase cron. Ad esempio: `-h "*/30 * * * *"` Configura lo script per eseguire i controlli ogni 30 minuti. Questa operazione eseguirà periodicamente la pianificazione di HBase cron per automatizzare il download delle nuove informazioni di HBase nell'account di archiviazione comune nel nodo locale.
    
    

## <a name="set-up-communication-manually-optional-if-provided-script-in-above-step-fails"></a>Configurare manualmente la comunicazione (facoltativo, se lo script specificato nel passaggio precedente ha esito negativo)

__Nota:__ Questa procedura deve essere eseguita ogni volta che uno dei cluster viene sottoposto a un'attività di ridimensionamento.

1. Copiare il hbase-site.xml dalla risorsa di archiviazione locale alla radice della risorsa di archiviazione predefinita del cluster Spark.  Modificare il comando seguente per riflettere la configurazione.  Quindi, dalla sessione SSH aperta al cluster HBase, immettere il comando:

    | Valore di sintassi | Nuovo valore|
    |---|---|
    |[Schema URI](hdinsight-hadoop-linux-information.md#URI-and-scheme) | Modificare per riflettere l'archiviazione.  La sintassi seguente è relativa all'archiviazione BLOB con trasferimento sicuro abilitato.|
    |`SPARK_STORAGE_CONTAINER`|Sostituire con il nome del contenitore di archiviazione predefinito usato per il cluster Spark.|
    |`SPARK_STORAGE_ACCOUNT`|Sostituire con il nome dell'account di archiviazione predefinito usato per il cluster Spark.|

    ```bash
    hdfs dfs -copyFromLocal /etc/hbase/conf/hbase-site.xml wasbs://SPARK_STORAGE_CONTAINER@SPARK_STORAGE_ACCOUNT.blob.core.windows.net/
    ```

2. Uscire quindi dalla connessione SSH al cluster HBase.

    ```bash
    exit
    ```


3. Connettersi al nodo head del cluster Spark tramite SSH. Modificare il comando seguente sostituendo `SPARKCLUSTER` con il nome del cluster Spark e quindi immettere il comando:

    ```cmd
    ssh sshuser@SPARKCLUSTER-ssh.azurehdinsight.net
    ```

4. Immettere il comando seguente per copiare `hbase-site.xml` dalla risorsa di archiviazione predefinita del cluster Spark alla cartella di configurazione di Spark 2 nell'archivio locale del cluster:

    ```bash
    sudo hdfs dfs -copyToLocal /hbase-site.xml /etc/spark2/conf
    ```

## <a name="run-spark-shell-referencing-the-spark-hbase-connector"></a>Eseguire la shell di Spark facendo riferimento al connettore HBase Spark

Dopo aver completato il passaggio precedente, sarà possibile eseguire la shell di Spark, facendo riferimento alla versione appropriata del connettore Spark HBase. Per trovare la versione di base del connettore Spark HBase più recente per lo scenario del cluster, vedere la pagina relativa al [repository principale di SHC](https://repo.hortonworks.com/content/groups/public/com/hortonworks/shc/shc-core/).

Ad esempio, nella tabella seguente sono elencate due versioni e i comandi corrispondenti attualmente utilizzati dal team HDInsight. È possibile usare le stesse versioni per i cluster se le versioni di HBase e Spark sono identiche a quelle indicate nella tabella. 


1. Nella sessione SSH aperta al cluster Spark immettere il comando seguente per avviare una shell di Spark:

    |Versione di Spark| Versione HDI HBase  | Versione di SHC    |  Comando  |
    | :-----------:| :----------: | :-----------: |:----------- |
    |      2.1    | HDI 3,6 (HBase 1,1) | 1.1.1-2.1-s_2.11    | `spark-shell --packages com.hortonworks:shc-core:1.1.1-2.1-s_2.11 --repositories https://repo.hortonworks.com/content/groups/public/` |
    |      2.4    | HDI 4,0 (HBase 2,0) | 1.1.0.3.1.2.2-1  | `spark-shell --packages com.hortonworks.shc:shc-core:1.1.0.3.1.2.2-1 --repositories http://repo.hortonworks.com/content/groups/public/` |

2. Mantenere aperta questa istanza di Spark Shell e continuare a [definire un catalogo e una query](#define-a-catalog-and-query). Se non si trovano i file jar che corrispondono alle versioni nel repository principale di SHC, continuare a leggere. 

È possibile compilare i file jar direttamente dal ramo GitHub [Spark-HBase-Connector](https://github.com/hortonworks-spark/shc) . Ad esempio, se si esegue con Spark 2,3 e HBase 1,1, completare i passaggi seguenti:

1. Clonare il repository:

    ```bash
    git clone https://github.com/hortonworks-spark/shc
    ```
    
2. Passare al ramo-2,3:

    ```bash
    git checkout branch-2.3
    ```

3. Compilazione dal ramo (crea un file con estensione jar):

    ```bash
    mvn clean package -DskipTests
    ```
    
3. Eseguire il comando seguente (assicurarsi di modificare il nome. jar che corrisponde al file con estensione jar compilato):

    ```bash
    spark-shell --jars <path to your jar>,/usr/hdp/current/hbase-client/lib/htrace-core-3.1.0-incubating.jar,/usr/hdp/current/hbase-client/lib/hbase-client.jar,/usr/hdp/current/hbase-client/lib/hbase-common.jar,/usr/hdp/current/hbase-client/lib/hbase-server.jar,/usr/hdp/current/hbase-client/lib/hbase-protocol.jar,/usr/hdp/current/hbase-client/lib/htrace-core-3.1.0-incubating.jar
    ```
    
4. Mantenere aperta questa istanza di Spark Shell e continuare con la sezione successiva. 



## <a name="define-a-catalog-and-query"></a>Definire un catalogo e una query

In questo passaggio, definire un oggetto catalogo corrispondente allo schema da Apache Spark ad Apache HBase.  

1. In Open Spark Shell immettere le `import` istruzioni seguenti:

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

    Il codice:  

    1. Definisce uno schema del catalogo per la tabella HBase denominata `Contacts` .  
    1. Identifica RowKey come `key` ed eseguire il mapping dei nomi di colonna usati in Spark alla famiglia di colonne, al nome della colonna e al tipo di colonna usati in HBase.  
    1. Definisce RowKey in dettaglio come colonna denominata ( `rowkey` ), che ha una famiglia di colonne specifica `cf` di `rowkey` .  

1. Immettere il comando seguente per definire un metodo che fornisce un frame di frame attorno alla `Contacts` tabella in HBase:

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
